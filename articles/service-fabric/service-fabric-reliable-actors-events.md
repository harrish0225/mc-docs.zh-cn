---
title: 基于执行组件的 Azure Service Fabric 执行组件中的事件
description: 了解 Service Fabric Reliable Actors 的事件，这些事件提供了执行组件与客户端之间进行通信的有效方法。
author: rockboyfor
ms.topic: conceptual
origin.date: 10/06/2017
ms.date: 02/24/2020
ms.author: v-yeche
ms.openlocfilehash: ecea90aad03b60664dc85a46749704d4a344b0a5
ms.sourcegitcommit: afe972418a883551e36ede8deae32ba6528fb8dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/21/2020
ms.locfileid: "77540156"
---
# <a name="actor-events"></a>执行组件事件
执行组件事件提供了一种尽最大努力将通知从执行组件发送到客户端的方法。 执行组件事件设计用于从执行组件到客户端的通信，而不应用于从执行组件到执行组件的通信。

以下代码段演示如何在应用程序中使用执行组件事件。

定义说明由执行组件发布的事件的接口。 此接口必须派生自 `IActorEvents` 接口。 方法的参数必须为[数据协定可序列化](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)。 当事件通知是单向且为最佳效果时，方法必须返回 void。

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```

声明由执行组件在执行组件界面中发布的事件。

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```

```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```

在客户端上，实现事件处理程序。

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

在客户端上，为发布事件并订阅其事件的执行组件创建代理。

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

如果发生故障转移，执行组件可能会故障转移到不同的进程或节点。 执行组件代理管理活动的订阅，并自动重新订阅它们。 可以通过 `ActorProxyEventExtensions.SubscribeAsync<TEvent>` API 控制重新订阅的间隔。 若要取消订阅，请使用 `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` API。

在执行组件上，在事件发生时发布事件。 如果有订阅此事件的用户，那么执行组件运行时会向他们发送通知。

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```

```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```

## <a name="next-steps"></a>后续步骤
* [执行组件可重入性](service-fabric-reliable-actors-reentrancy.md)
* [执行组件诊断和性能监视](service-fabric-reliable-actors-diagnostics.md)
* [执行组件 API 参考文档](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# 示例代码](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core 示例代码](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java 代码示例](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Update_Description: update meta properties-->