---
author: craigshoemaker
ms.service: azure-functions
ms.topic: include
ms.date: 03/02/2020
ms.author: v-junlch
ms.openlocfilehash: 7287efc731ac8dc7518915952c30878d78cb9e7f
ms.sourcegitcommit: 1ac138a9e7dc7834b5c0b62a133ca5ce2ea80054
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2020
ms.locfileid: "78266120"
---
使用事件中心输出绑定将事件写入到事件流。 必须具有事件中心的发送权限才可将事件写入到其中。

在尝试实现输出绑定之前，请确保所需的包引用已准备就绪。

<a id="example" name="example"></a>

# <a name="c"></a>[C#](#tab/csharp)

以下示例演示使用方法返回值作为输出将消息写入到事件中心的 [C# 函数](../articles/azure-functions/functions-dotnet-class-library.md)：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    return $"{DateTime.Now}";
}
```

以下示例演示如何使用 `IAsyncCollector` 接口发送一批消息。 当你处理来自一个事件中心的消息并将结果发送到另一个事件中心时，这种情况很常见。

```csharp
[FunctionName("EH2EH")]
public static async Task Run(
    [EventHubTrigger("source", Connection = "EventHubConnectionAppSetting")] EventData[] events,
    [EventHub("dest", Connection = "EventHubConnectionAppSetting")]IAsyncCollector<string> outputEvents,
    ILogger log)
{
    foreach (EventData eventData in events)
    {
        // do some processing:
        var myProcessedEvent = DoSomething(eventData);

        // then send the message
        await outputEvents.AddAsync(JsonConvert.SerializeObject(myProcessedEvent));
    }
}
```

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [C# 脚本函数](../articles/azure-functions/functions-reference-csharp.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x 及更高版本，第二个示例适用于 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可创建一条消息的 C# 脚本代码：

```cs
using System;
using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, out string outputEventHubMessage, ILogger log)
{
    String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    log.LogInformation(msg);   
    outputEventHubMessage = msg;
}
```

下面是可创建多条消息的 C# 脚本代码：

```cs
public static void Run(TimerInfo myTimer, ICollector<string> outputEventHubMessage, ILogger log)
{
    string message = $"Message created at: {DateTime.Now}";
    log.LogInformation(message);
    outputEventHubMessage.Add("1 " + message);
    outputEventHubMessage.Add("2 " + message);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

以下示例演示 *function.json* 文件中的一个事件中心触发器绑定以及使用该绑定的 [JavaScript 函数](../articles/azure-functions/functions-reference-node.md)。 该函数将消息写入事件中心。

以下示例显示了 *function.json* 文件中的事件中心绑定数据。 第一个示例适用于 Functions 2.x 及更高版本，第二个示例适用于 Functions 1.x。 

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "eventHubName": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

```json
{
    "type": "eventHub",
    "name": "outputEventHubMessage",
    "path": "myeventhub",
    "connection": "MyEventHubSendAppSetting",
    "direction": "out"
}
```

下面是可发送一条消息的 JavaScript 代码：

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    context.log('Message created at: ', timeStamp);   
    context.bindings.outputEventHubMessage = "Message created at: " + timeStamp;
    context.done();
};
```

下面是可发送多条消息的 JavaScript 代码：

```javascript
module.exports = function(context) {
    var timeStamp = new Date().toISOString();
    var message = 'Message created at: ' + timeStamp;

    context.bindings.outputEventHubMessage = [];

    context.bindings.outputEventHubMessage.push("1 " + message);
    context.bindings.outputEventHubMessage.push("2 " + message);
    context.done();
};
```

# <a name="java"></a>[Java](#tab/java)

以下示例演示一个将包含当前时间的消息写入到事件中心的 Java 函数。

```java
@FunctionName("sendTime")
@EventHubOutput(name = "event", eventHubName = "samples-workitems", connection = "AzureEventHubConnection")
public String sendTime(
   @TimerTrigger(name = "sendTimeTrigger", schedule = "0 */5 * * * *") String timerInfo)  {
     return LocalDateTime.now().toString();
 }
```

在 [Java 函数运行时库](https://docs.microsoft.com/java/api/overview/azure/functions/runtime)中，对其值将被发布到事件中心的参数使用 `@EventHubOutput` 注释。  此参数应为 `OutputBinding<T>` 类型，其中 T 是 POJO 或任何本机 Java 类型。

---

## <a name="attributes-and-annotations"></a>特性和注释

# <a name="c"></a>[C#](#tab/csharp)

对于 [C# 类库](../articles/azure-functions/functions-dotnet-class-library.md)，使用 [EventHubAttribute](https://github.com/Azure/azure-functions-eventhubs-extension/blob/master/src/Microsoft.Azure.WebJobs.Extensions.EventHubs/EventHubAttribute.cs) 特性。

该特性的构造函数使用事件中心的名称和包含连接字符串的应用设置的名称。 有关这些设置的详细信息，请参阅[输出 - 配置](#configuration)。 下面是 `EventHub` 特性的示例：

```csharp
[FunctionName("EventHubOutput")]
[return: EventHub("outputEventHubMessage", Connection = "EventHubConnectionAppSetting")]
public static string Run([TimerTrigger("0 */5 * * * *")] TimerInfo myTimer, ILogger log)
{
    ...
}
```

有关完整示例，请参阅[输出 - C# 示例](#example)。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

C# 脚本不支持特性。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript 不支持特性。

# <a name="java"></a>[Java](#tab/java)

在 [Java 函数运行时库](https://docs.microsoft.com https://docs.microsoft.com/java/api/overview/azure/functions/runtime)中，对其值将被发布到事件中心的参数使用 [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 注释。 此参数应为 `OutputBinding<T>` 类型，其中 `T` 是 POJO 或任何本机 Java 类型。

---

## <a name="configuration"></a>配置

下表解释了在 function.json  文件和 `EventHub` 特性中设置的绑定配置属性。

|function.json 属性 | Attribute 属性 |说明|
|---------|---------|----------------------|
|**type** | 不适用 | 必须设置为“eventHub”。 |
|**direction** | 不适用 | 必须设置为“out”。 在 Azure 门户中创建绑定时，会自动设置该参数。 |
|**name** | 不适用 | 函数代码中使用的表示事件的变量名称。 |
|**路径** |**EventHubName** | 仅适用于 Functions 1.x。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|**eventHubName** |**EventHubName** | Functions 2.x 及更高版本。 事件中心的名称。 当事件中心名称也出现在连接字符串中时，该值会在运行时覆盖此属性。 |
|连接  |**Connection** | 应用设置的名称，该名称中包含事件中心命名空间的连接字符串。 单击 *命名空间* （而不是事件中心本身）  的“连接信息”按钮，以复制此连接字符串。 此连接字符串必须具有发送权限才可将消息发送到事件流。|

[!INCLUDE [app settings to local.settings.json](../articles/azure-functions/../../includes/functions-app-settings-local.md)]

## <a name="usage"></a>使用情况

# <a name="c"></a>[C#](#tab/csharp)

使用 `out string paramName` 等方法参数发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

# <a name="c-script"></a>[C# 脚本](#tab/csharp-script)

使用 `out string paramName` 等方法参数发送消息。 在 C# 脚本中，`paramName` 是在 *function.json* 的 `name` 属性中指定的值。 若要编写多条消息，可以使用 `ICollector<string>` 或 `IAsyncCollector<string>` 代替 `out string`。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

使用 `context.bindings.<name>` 访问输出事件，其中 `<name>` 是在 *function.json* 的 `name` 属性中指定的值。

# <a name="java"></a>[Java](#tab/java)

可通过两个选项使用 [EventHubOutput](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.annotation.eventhuboutput) 注释从函数输出事件中心消息：

- **返回值**：通过将注释应用于函数本身，函数的返回值将持久保存为事件中心消息。

- **命令性**：若要显式设置消息值，请将注释应用于 [`OutputBinding<T>`](https://docs.microsoft.com/java/api/com.microsoft.azure.functions.OutputBinding) 类型的特定参数，其中 `T` 是 POJO 或任何本机 Java 类型。 使用此配置时，向 `setValue` 方法传递某值会将该值持久保存为事件中心消息。

---

## <a name="exceptions-and-return-codes"></a>异常和返回代码

| 绑定 | 参考 |
|---|---|
| 事件中心 | [操作指南](https://docs.microsoft.com/rest/api/eventhub/publisher-policy-operations) |

<a name="host-json"></a>  

## <a name="hostjson-settings"></a>host.json 设置

本部分介绍版本 2.x 及更高版本中可用于此绑定的全局配置设置。 下面的示例 host.json 文件仅包含此绑定的 2.x 版及更高版本设置。 若要详细了解 2.x 版及更高版本中的全局配置设置，请参阅 [Azure Functions 的 host.json 参考](../articles/azure-functions/functions-host-json.md)。

> [!NOTE]
> 有关 Functions 1.x 中 host.json 的参考，请参阅 [Azure Functions 1.x 的 host.json 参考](../articles/azure-functions/functions-host-json-v1.md)。

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            }
        }
    }
}  
```

|属性  |默认 | 说明 |
|---------|---------|---------|
|`maxBatchSize`|10 个|每个接收循环收到的最大事件计数。|
|`prefetchCount`|300|基础 `EventProcessorHost` 使用的默认预提取计数。|
|`batchCheckpointFrequency`|1|创建 EventHub 游标检查点之前要处理的事件批数。|

