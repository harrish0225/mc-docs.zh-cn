---
title: "在 Azure 门户预览版中启用存储指标 | Microsoft Docs"
description: "如何为 Blob、队列、表和文件服务启用存储指标"
services: storage
documentationcenter: 
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 2fb5b229-f099-4334-92be-4e0e7dd257d7
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/03/2017
ms.author: robinsh
translationtype: Human Translation
ms.sourcegitcommit: a114d832e9c5320e9a109c9020fcaa2f2fdd43a9
ms.openlocfilehash: e7be5e3d4108aa93c0d0f58dff740b535aa82ef6
ms.lasthandoff: 04/14/2017


---
# <a name="enabling-storage-metrics-and-viewing-metrics-data"></a>启用存储指标并查看指标数据
[!INCLUDE [storage-selector-portal-enable-and-view-metrics](../../includes/storage-selector-portal-enable-and-view-metrics.md)]

## <a name="overview"></a>概述
创建新的存储帐户时默认情况下会启用存储度量。 可以使用 [Azure 经典管理门户](https://manage.windowsazure.cn)、Windows PowerShell 或以编程方式通过存储 API 配置监视。

启用存储指标时，必须为数据选择保留期：此期限用于确定存储服务保留指标并针对存储指标所需的空间向你收费的时长。 通常，由于分钟指标需要大量额外的空间，因此，应对分钟指标而非小时指标使用较短的保留期。 你应该选择恰当的保留期，以便有足够的时间分析数据，并下载任何需要保留下来进行脱机分析或报告的指标。 请记住，从存储帐户下载指标数据时，你也需要付费。

## <a name="how-to-enable-storage-metrics-using-the-azure-classic-management-portal"></a>如何使用 Azure 经典管理门户启用存储指标
在 [Azure 经典管理门户](https://manage.windowsazure.cn)中，使用存储帐户的“配置”页控制存储指标。 对于监视，你可以为每个 Blob、表和队列设置级别和保留期（以天为单位）。 在每种情况下，该级别都是以下选项之一：

* 关 - 不收集任何指标。
* 最小 - 存储指标收集为 Blob、表和队列服务聚合的一组基本指标，如传入/传出、可用性、延迟和成功百分比。
* 详细 - 除了收集服务级指标，存储指标还收集全套指标，其中包括对每个存储 API 操作都相同的指标。 通过详细监视指标可对应用程序运行期间出现的问题进行进一步分析。

请注意，Azure 经典管理门户目前不允许在存储帐户中配置分钟指标；必须通过 PowerShell 或编程方式启用分钟指标。

## <a name="how-to-enable-storage-metrics-using-powershell"></a>如何使用 PowerShell 启用存储指标
你可以使用本地计算机上的 PowerShell 在存储帐户中配置存储指标，具体方法是：使用 Azure PowerShell cmdlet Get-AzureStorageServiceMetricsProperty 检索当前设置，然后使用 cmdlet Set-AzureStorageServiceMetricsProperty 更改当前设置。

控制存储指标的 cmdlet 使用以下参数：

* MetricsType，可能值是 Hour 和 Minute。
* ServiceType 的可能值为 Blob、Queue 和 Table。
* MetricsLevel，可能值是 None（相当于 Azure 经典管理门户中的“关”）、Service（相当于 Azure 经典管理门户中的“最小”）和 ServiceAndApi（相当于 Azure 经典管理门户中的“详细”）。

例如，以下命令在保留期设为 5 天的情况下，在默认存储帐户中为 Blob 服务打开分钟指标：

```powershell
Set-AzureStorageServiceMetricsProperty -MetricsType Minute -ServiceType Blob -MetricsLevel ServiceAndApi  -RetentionDays 5
```
以下命令在默认存储帐户中为 Blob 服务检索当前的小时指标级别和保留天数：

```powershell
Get-AzureStorageServiceMetricsProperty -MetricsType Hour -ServiceType Blob
```
若要了解如何配置 Azure PowerShell cmdlet 来使用 Azure 订阅并了解如何选择要使用的默认存储帐户，请参阅：[如何安装和配置 Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs)。

## <a name="how-to-enable-storage-metrics-programmatically"></a>如何以编程方式启用存储指标
下面的 C# 代码段演示了如何使用 .NET 的存储客户端库为 Blob 服务启用指标和日志记录：

```csharp
//Parse the connection string for the storage account.
const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=account-name;AccountKey=account-key;EndpointSuffix=core.chinacloudapi.cn";
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConnectionString);

// Create service client for credentialed access to the Blob service.
CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

// Enable Storage Analytics logging and set retention policy to 10 days. 
ServiceProperties properties = new ServiceProperties();
properties.Logging.LoggingOperations = LoggingOperations.All;
properties.Logging.RetentionDays = 10;
properties.Logging.Version = "1.0";

// Configure service properties for metrics. Both metrics and logging must be set at the same time.
properties.HourMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.HourMetrics.RetentionDays = 10;
properties.HourMetrics.Version = "1.0";

properties.MinuteMetrics.MetricsLevel = MetricsLevel.ServiceAndApi;
properties.MinuteMetrics.RetentionDays = 10;
properties.MinuteMetrics.Version = "1.0";

// Set the default service version to be used for anonymous requests.
properties.DefaultServiceVersion = "2015-04-05";

// Set the service properties.
blobClient.SetServiceProperties(properties);
```

## <a name="viewing-storage-metrics"></a>查看存储指标

在配置为监视存储帐户后，存储指标将使用存储帐户在一组已知表中记录指标。 可以在 Azure 经典管理门户中使用存储帐户的“监视”页查看图表上提供的小时指标。 在 Azure 经典管理门户中，可以在此页上执行以下操作：

* 选择要在图表上绘制的指标（可用指标的选择将取决于你在“配置”页上为服务选择“详细”还是“最小”监视）。
* 为图表上显示的指标选择时间范围。
* 选择使用绝对刻度还是相对刻度来绘制指标。
* 配置电子邮件警报，以在特定指标达到某值时通知你。

如果你要为长期存储下载指标或在本地分析这些指标，则需要使用工具或编写一些代码来读取表。 你必须下载分析用的分钟指标。 如果你在存储帐户中列出所有表，则这些表不会显示，但你可以按名称直接访问它们。 很多第三方存储浏览工具都识别这些表，并允许你直接查看它们（有关可用工具的列表，请参阅博客文章 [Azure Storage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)（Azure 存储资源管理器）。

### <a name="hourly-metrics"></a>每小时指标
* $MetricsHourPrimaryTransactionsBlob
* $MetricsHourPrimaryTransactionsTable
* $MetricsHourPrimaryTransactionsQueue

### <a name="minute-metrics"></a>分钟指标
* $MetricsMinutePrimaryTransactionsBlob
* $MetricsMinutePrimaryTransactionsTable
* $MetricsMinutePrimaryTransactionsQueue

### <a name="capacity"></a>容量
* $MetricsCapacityBlob

有关这些表的完整架构详细信息，请参阅 [Storage Analytics Metrics Table Schema](https://msdn.microsoft.com/library/azure/hh343264.aspx)（存储分析指标表架构）。 以下示例行仅显示一部分可用列，但也说明了存储指标在采用相应方式保存这些指标时展现的一些重要功能：

| PartitionKey | RowKey | Timestamp | TotalRequests | TotalBillableRequests | TotalIngress | TotalEgress | Availability | AverageE2ELatency | AverageServerLatency | PercentSuccess |
| --- |:---:| ---:| --- | --- | --- | --- | --- | --- | --- | --- |
| 20140522T1100 |user;All |2014-05-22T11:01:16.7650250Z |7 |7 |4003 |46801 |100 |104.4286 |6.857143 |100 |
| 20140522T1100 |user;QueryEntities |2014-05-22T11:01:16.7640250Z |5 |5 |2694 |45951 |100 |143.8 |7.8 |100 |
| 20140522T1100 |user;QueryEntity |2014-05-22T11:01:16.7650250Z |1 |1 |538 |633 |100 |3 |3 |100 |
| 20140522T1100 |user;UpdateEntity |2014-05-22T11:01:16.7650250Z |1 |1 |771 |217 |100 |9 |6 |100 |

在这个分钟指标数据示例中，分区键按分钟使用时间。 行键可识别行中存储的信息的类型，其中包含两条信息，即访问类型和请求类型：

* 访问类型是 user 或 system，其中 user 是指用户对存储服务发出的所有请求，而 system 是指存储分析发出的请求。
* 请求类型是 all（在这种情况下是摘要行）或可识别的特定 API，如 QueryEntity 或 UpdateEntity。

上面的示例数据显示一分钟的所有记录（从上午 11:00 开始），因此 QueryEntities 请求数加 QueryEntity 请求数再加 UpdateEntity 请求数的和为 7，这是显示在 user:All 行上的总数。 同样，通过计算 ((143.8 * 5) + 3 + 9)/7，可以在 user:All 行得到平均端到端延迟为 104.4286。

应考虑在 Azure 经典管理门户中的“监视”页上设置警报，以便“存储指标”自动通知你存储服务行为中发生的任何重要更改。如果使用存储资源管理器工具下载这种采用分隔格式的指标数据，则可以使用 Microsoft Excel 分析该数据。 有关可用存储资源管理器工具的列表，请参阅博客文章 [Azure Storage Explorers](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx)（Azure 存储资源管理器）。

## <a name="accessing-metrics-data-programmatically"></a>以编程方式访问指标数据
以下列表显示示例 C# 代码，该代码用于访问分钟范围的分钟指标，并在控制台窗口中显示结果。 它使用 Azure 存储库版本 4，其中包括 CloudAnalyticsClient 类，用于简化访问存储中的指标表的过程。

```csharp
private static void PrintMinuteMetrics(CloudAnalyticsClient analyticsClient, DateTimeOffset startDateTime, DateTimeOffset endDateTime)
{
    // Convert the dates to the format used in the PartitionKey
    var start = startDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");
    var end = endDateTime.ToUniversalTime().ToString("yyyyMMdd'T'HHmm");

    var services = Enum.GetValues(typeof(StorageService));
    foreach (StorageService service in services)
    {
        Console.WriteLine("Minute Metrics for Service {0} from {1} to {2} UTC", service, start, end);
        var metricsQuery = analyticsClient.CreateMinuteMetricsQuery(service, StorageLocation.Primary);
        var t = analyticsClient.GetMinuteMetricsTable(service);
        var opContext = new OperationContext();
        var query =
          from entity in metricsQuery
          // Note, you can't filter using the entity properties Time, AccessType, or TransactionType
          // because they are calculated fields in the MetricsEntity class.
          // The PartitionKey identifies the DataTime of the metrics.
          where entity.PartitionKey.CompareTo(start) >= 0 && entity.PartitionKey.CompareTo(end) <= 0 
        select entity;

        // Filter on "user" transactions after fetching the metrics from Table Storage.
        // (StartsWith is not supported using LINQ with Azure table storage)
        var results = query.ToList().Where(m => m.RowKey.StartsWith("user"));
        var resultString = results.Aggregate(new StringBuilder(), (builder, metrics) => builder.AppendLine(MetricsString(metrics, opContext))).ToString();
        Console.WriteLine(resultString);
    }
}

private static string MetricsString(MetricsEntity entity, OperationContext opContext)
{
    var entityProperties = entity.WriteEntity(opContext);
    var entityString =
    string.Format("Time: {0}, ", entity.Time) +
    string.Format("AccessType: {0}, ", entity.AccessType) +
    string.Format("TransactionType: {0}, ", entity.TransactionType) +
    string.Join(",", entityProperties.Select(e => new KeyValuePair<string, string>(e.Key.ToString(), e.Value.PropertyAsObject.ToString())));
    return entityString;
}
```

## <a name="what-charges-do-you-incur-when-you-enable-storage-metrics"></a>在启用存储指标时，你需要支付多少费用？
为指标创建表实体的写入请求，按适用于所有 Azure 存储操作的标准费率收费。

客户端针对指标数据的读取和删除请求也按标准费率收费。 如果你已配置数据保留策略，则当 Azure 存储空间删除旧的指标数据时，你不用付费。 但是，如果你删除分析数据，则会针对删除操作向你的帐户收费。

指标表使用的容量也要付费：你可以依据以下内容估算用于存储指标数据的容量：

* 如果某个服务每小时都会利用每个服务的每个 API，则在启用服务和 API 级别摘要后，每小时约有 148KB 的数据存储在指标事务表中。
* 如果某个服务每小时都会利用每个服务的每个 API，则在仅启用服务级别摘要后，每小时约有 12KB 的数据存储在指标事务表中。
* Blob 的容量表每天添加两行（如果用户已为日志选择加入）：这表示，此表的大小每天最多以约 300 个字节的幅度增加。

## <a name="next-steps"></a>后续步骤：
[Enabling Storage Analytics Logging and Accessing Log Data](https://msdn.microsoft.com/library/dn782840.aspx)（启用存储分析日志记录和访问日志数据）