---
title: 模拟 Azure Service Fabric 应用中的故障
description: 了解如何针对常规/非常规故障强化 Azure Service Fabric 服务。
author: rockboyfor
ms.topic: conceptual
origin.date: 06/15/2017
ms.date: 02/24/2020
ms.author: v-yeche
ms.openlocfilehash: 80176606416a7a095037ba324bd7bfe9cd32cca7
ms.sourcegitcommit: afe972418a883551e36ede8deae32ba6528fb8dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77540566"
---
# <a name="simulate-failures-during-service-workloads"></a>在服务工作负荷期间模拟故障
Azure Service Fabric 中的可测试性方案可让开发人员不用再担心如何处理单个故障。 然而也存在一些方案，可能需要客户端工作负荷与故障有明显的交错。 客户端工作负荷与故障的交错确保在发生故障时，服务实际在执行某些操作。 考虑到可测试性功能提供的控制等级，这些交错应该在精确的工作负荷执行点进行。 这种在应用程序的不同状态下引入故障可以找出 bug 并提高质量。

## <a name="sample-custom-scenario"></a>自定义方案示例
此测试显示一种方案，其中业务工作负荷与[常规故障和非常规故障](service-fabric-testability-actions.md#graceful-vs-ungraceful-fault-actions)交错出现。 为了获得最佳结果，故障应在服务操作或计算的中间引入。

让我们来了解一个显示了四个工作负荷（A、B、C、D）的服务示例。每个负荷对应一组工作流程，可以是计算、存储或者二者的混合。 为简单起见，我们对示例中的工作负荷进行抽象化。 本示例中执行的不同故障为：

* RestartNode：用于模拟计算机重启的非常规故障。
* RestartDeployedCodePackage：用于模拟服务主机进程崩溃的非正常故障。
* RemoveReplica：用于模拟副本删除操作的正常故障。
* MovePrimary：用于模拟 Service Fabric 负载均衡器触发的副本移动操作的正常故障。

```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll.

using System;
using System.Fabric;
using System.Fabric.Testability.Scenario;
using System.Threading;
using System.Threading.Tasks;

class Test
{
    public static int Main(string[] args)
    {
        // Replace these strings with the actual version for your cluster and application.
        string clusterConnection = "localhost:19000";
        Uri applicationName = new Uri("fabric:/samples/PersistentToDoListApp");
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");

        Console.WriteLine("Starting Workload Test...");
        try
        {
            RunTestAsync(clusterConnection, applicationName, serviceName).Wait();
        }
        catch (AggregateException ae)
        {
            Console.WriteLine("Workload Test failed: ");
            foreach (Exception ex in ae.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("Workload Test completed successfully.");
        return 0;
    }

    public enum ServiceWorkloads
    {
        A,
        B,
        C,
        D
    }

    public enum ServiceFabricFaults
    {
        RestartNode,
        RestartCodePackage,
        RemoveReplica,
        MovePrimary,
    }

    public static async Task RunTestAsync(string clusterConnection, Uri applicationName, Uri serviceName)
    {
        // Create FabricClient with connection and security information here.
        FabricClient fabricClient = new FabricClient(clusterConnection);
        // Maximum time to wait for a service to stabilize.
        TimeSpan maxServiceStabilizationTime = TimeSpan.FromSeconds(120);

        // How many loops of faults you want to execute.
        uint testLoopCount = 20;
        Random random = new Random();

        for (var i = 0; i < testLoopCount; ++i)
        {
            var workload = SelectRandomValue<ServiceWorkloads>(random);
            // Start the workload.
            var workloadTask = RunWorkloadAsync(workload);

            // While the task is running, induce faults into the service. They can be ungraceful faults like
            // RestartNode and RestartDeployedCodePackage or graceful faults like RemoveReplica or MovePrimary.
            var fault = SelectRandomValue<ServiceFabricFaults>(random);

            // Create a replica selector, which will select a primary replica from the given service to test.
            var replicaSelector = ReplicaSelector.PrimaryOf(PartitionSelector.RandomOf(serviceName));
            // Run the selected random fault.
            await RunFaultAsync(applicationName, fault, replicaSelector, fabricClient);
            // Validate the health and stability of the service.
            await fabricClient.TestManager.ValidateServiceAsync(serviceName, maxServiceStabilizationTime);

            // Wait for the workload to finish successfully.
            await workloadTask;
        }
    }

    private static async Task RunFaultAsync(Uri applicationName, ServiceFabricFaults fault, ReplicaSelector selector, FabricClient client)
    {
        switch (fault)
        {
            case ServiceFabricFaults.RestartNode:
                await client.FaultManager.RestartNodeAsync(selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RestartCodePackage:
                await client.FaultManager.RestartDeployedCodePackageAsync(applicationName, selector, CompletionMode.Verify);
                break;
            case ServiceFabricFaults.RemoveReplica:
                await client.FaultManager.RemoveReplicaAsync(selector, CompletionMode.Verify, false);
                break;
            case ServiceFabricFaults.MovePrimary:
                await client.FaultManager.MovePrimaryAsync(selector.PartitionSelector);
                break;
        }
    }

    private static Task RunWorkloadAsync(ServiceWorkloads workload)
    {
        throw new NotImplementedException();
        // This is where you trigger and complete your service workload.
        // Note that the faults induced while your service workload is running will
        // fault the primary service. Hence, you will need to reconnect to complete or check
        // the status of the workload.
    }

    private static T SelectRandomValue<T>(Random random)
    {
        Array values = Enum.GetValues(typeof(T));
        T workload = (T)values.GetValue(random.Next(values.Length));
        return workload;
    }
}
```

<!-- Update_Description: update meta properties, wording update -->