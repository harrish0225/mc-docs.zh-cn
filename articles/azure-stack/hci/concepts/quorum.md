---
title: 了解 Azure Stack HCI 上的群集和池仲裁
description: 了解 Azure Stack HCI 存储空间直通中的群集和池仲裁，并通过具体的示例了解细节。
author: WenJason
ms.author: v-jay
ms.topic: article
origin.date: 02/28/2020
ms.date: 03/23/2020
ms.openlocfilehash: 6b4de21b155ca489d1dec7243ad7b091e392b73a
ms.sourcegitcommit: e500354e2fd8b7ac3dddfae0c825cc543080f476
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2020
ms.locfileid: "79547143"
---
# <a name="understanding-cluster-and-pool-quorum-on-azure-stack-hci"></a>了解 Azure Stack HCI 上的群集和池仲裁

>适用于：Windows Server 2019

[Windows Server 故障转移群集](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)为工作负荷提供高可用性。 如果托管资源的节点已启动，则认为这些资源具有高可用性；但是，群集通常需要运行一半以上的节点，才认为它具有仲裁。 

仲裁旨在防止“裂脑”的情况。当网络中存在分区，并且节点的子集无法相互通信时，就会发生这种情况。  此情况可能会导致两个节点子集尝试拥有工作负荷，并写入到到可能造成许多问题的同一磁盘。 但是，可以通过故障转移群集的仲裁概念来避免此问题，因为仲裁会强制要求这些节点组中只能有一个组继续运行，从而只有其中的一个组保持联机状态。

仲裁确定群集在仍保持联机的情况下可以承受的故障次数。 仲裁旨在处理群集节点子集之间发生通信问题时出现的情况，避免多个服务器同时尝试托管某个资源组并写入到同一磁盘。 借助此仲裁概念，群集将强制群集服务在某个节点子集中停止，以确保特定的资源组只有一个真正的所有者。 停止的节点可以再次与主要节点组通信后，这些节点将自动重新加入群集，并启动其群集服务。

Windows Server 2019 中有两个系统组件具有自身的仲裁机制：

- **群集仲裁**：此组件在群集级别运行（即，可以在丢失节点的情况下让群集保持运行状态）
- **池仲裁**：此组件在启用存储空间直通时在池级别运行（即，可以在丢失节点和驱动器的情况下让池保持运行状态）。 存储池既可在群集方案中使用，也可在非群集方案中使用，正因如此，它们采用了不同的仲裁机制。

## <a name="cluster-quorum-overview"></a>群集仲裁概述

下表提供了每种方案的群集仲裁结果的概述：

| 服务器节点 | 可以承受一次服务器节点故障 | 可以承受一次服务器节点故障，以后还可以承受一次 | 可以承受同时发生两次服务器节点故障 |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | 50/50                               | 否                                                | 否                                                 |
| 2 个 + 见证  | 是                                 | 否                                                | 否                                                 |
| 3            | 是                                 | 50/50                                             | 否                                                 |
| 3 个 + 见证  | 是                                 | 是                                               | 否                                                 |
| 4            | 是                                 | 是                                               | 50/50                                              |
| 4 个 + 见证  | 是                                 | 是                                               | 是                                                |
| 5 个或更多  | 是                                 | 是                                               | 是                                                |

### <a name="cluster-quorum-recommendations"></a>群集仲裁建议

- 如果你有两个节点，则**必须**使用见证。
- 如果你有三个或四个节点，则我们**强烈建议**使用见证。
- 如果你可以访问 Internet，请使用 **[云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)**
- 如果在包含其他计算机和文件共享的 IT 环境中操作，请使用文件共享见证

## <a name="how-cluster-quorum-works"></a>群集仲裁的工作原理

当节点发生故障或者某些节点子集与另一个子集失去联系时，幸存的节点需要确认它们可以构成群集的多数，这样才能保持联机。  如果无法确认这一点，则它们将会脱机。

但是“多数”的概念仅在群集中的节点总数是奇数（例如，五节点群集中的三个节点）时才起作用。  那么，如果群集的节点数目是偶数呢（例如四节点群集）？

有两种方式可让群集将投票总数变成奇数： 

1. 首先，可以通过添加见证来增加一个投票。   这需要用户进行设置。
2. 或者，可以通过将一个不幸运的节点归零来减去投票（在需要时会自动发生）。 

当幸存的节点成功确认其属于多数时，“多数”的定义将在这些幸存的节点中更新。   这可以让群集失去一个节点，然后再失去一个节点，依此类推。 这种在连续故障后会自动调整的“投票总数”概念称作“动态仲裁”。    

### <a name="dynamic-witness"></a>动态见证

动态见证切换见证的投票，以确保投票总数为奇数。  如果投票数为奇数，则见证不会获得投票。 如果投票数为偶数，则见证获得投票。 动态见证大幅降低了群集因见证故障而中断的风险。 群集根据群集中可用的投票节点数目，确定是否要使用见证投票。

动态仲裁按下面所述与动态见证配合工作。

### <a name="dynamic-quorum-behavior"></a>动态仲裁行为

- 如果节点数目为**偶数**且没有见证，则某个节点会将其投票归零。  例如，四个节点中只有三个获得投票，因此投票总数为三个，而获得投票的两个幸存节点被视为多数。 
- 如果节点数目为**奇数**且没有见证，则这些节点都获得投票。 
- 如果有**偶数**数目的节点再加上见证（见证投票），则节点总计为奇数。 
- 如果有**奇数**数目的节点再加上见证，则见证不会获得投票。 

使用动态仲裁可将投票动态分配给节点，以避免失去多数投票，并且可允许群集使用一个节点（称为“幸存到最后的节点”）运行。 让我们以一个四节点群集为例。 假设仲裁需要 3 个投票。 

在此情况下，如果失去两个节点，群集就关闭。

![显示四群集节点的示意图，其每个节点获得投票](media/quorum/dynamic-quorum-base.png)

但是，动态仲裁可防止这种情况发生。 仲裁所需的投票总数现在根据可用节点数来确定。  因此，使用动态仲裁时，即使失去三个节点，群集也仍能保持运行。

![显示四群集节点的示意图，其中每次有一个节点发生故障，每次故障后会调整所需的投票数。](media/quorum/dynamic-quorum-step-through.png)

上述方案适用于未启用存储空间直通的普通群集。 但是，启用存储空间直通时，群集只能支持两个节点发生故障。 [池仲裁部分](#pool-quorum-overview)对此做了更详细的解释。

### <a name="examples"></a>示例

#### <a name="two-nodes-without-a-witness"></a>两个节点且没有见证。
一个节点的投票已归零，因此多数投票由总数 **1 票**所确定。  如果非投票节点意外关闭，幸存的节点将获得 1 个投票中的 1 票，而群集将会幸存。 如果非投票节点意外关闭，幸存的节点将获得 1 个投票中的 0 票，而群集将会关闭。 如果投票节点正常关闭，则投票将转移到另一个节点，而群集将会幸存。 正因如此，配置见证至关重要。

![解释有两个节点但没有见证时的仲裁](media/quorum/2-node-no-witness.png)

- 可以承受一次服务器故障：**50% 的几率**。
- 可以承受一次服务器故障，以后还可以承受一次：**不**。
- 可以同时承受两次服务器故障：**不**。

#### <a name="two-nodes-with-a-witness"></a>两个节点加一个见证。
这两个节点都可投票，再加上见证投票，因此“多数”由总数 **3 票**来确定。  如果任一节点关闭，则幸存的节点会获得 3 个投票中的 2 票，而群集将会幸存。

![解释有两个节点和一个见证时的仲裁](media/quorum/2-node-witness.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**不**。
- 可以同时承受两次服务器故障：**不**。

#### <a name="three-nodes-without-a-witness"></a>三个节点且没有见证。
所有节点都可投票，因此“多数”由总数 **3 票**来确定。  如果任一节点关闭，则幸存的节点会获得 3 个投票中的 2 票，而群集将会幸存。 群集包含两个节点但没有见证 – 此时，你将进入方案 1。

![解释有三个节点但没有见证时的仲裁](media/quorum/3-node-no-witness.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**50% 的几率**。
- 可以同时承受两次服务器故障：**不**。

#### <a name="three-nodes-with-a-witness"></a>三个节点加一个见证。
所有节点都可投票，因此见证最初不会投票。 “多数”由总数 **3 票**来确定。  发生一次故障后，群集将包含两个节点和一个见证 – 此时将返回到方案 2。 因此，现在两个节点和见证都可投票。

![解释有三个节点和一个见证时的仲裁](media/quorum/3-node-witness.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**是**。
- 可以同时承受两次服务器故障：**不**。

#### <a name="four-nodes-without-a-witness"></a>四个节点且没有见证。
一个节点的投票已归零，因此“多数”由总数 **3 票**所确定。  发生一次故障后，群集将包含三个节点，此时，你将进入方案 3。

![解释有四个节点但没有见证时的仲裁](media/quorum/4-node-no-witness.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**是**。
- 可以同时承受两次服务器故障：**50% 的几率**。

#### <a name="four-nodes-with-a-witness"></a>四个节点加一个见证。
所有节点和见证都可投票，因此“多数”由总数 **5 票**来确定。  发生一次故障后，你将进入方案 4。 同时发生两次故障后，你将直接跳到方案 2。

![解释有四个节点和一个见证时的仲裁](media/quorum/4-node-witness.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**是**。
- 可以同时承受两次服务器故障：**是**。 

#### <a name="five-nodes-and-beyond"></a>五个或更多节点。
所有节点都可投票，或者只有其中的一个不能投票，无论总数是如何变成奇数的。 存储空间直通无法处理两个以上节点关闭的情况，因此，此时不需要见证，它也发挥不了作用。

![解释有五个或更多节点时的仲裁](media/quorum/5-nodes.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**是**。
- 可以同时承受两次服务器故障：**是**。 

了解仲裁的工作原理后，接下来让我们看看仲裁见证的类型。

### <a name="quorum-witness-types"></a>仲裁见证的类型

故障转移群集支持三种类型的仲裁见证：

- **[云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)** - Azure 中可供群集所有节点访问的 Blob 存储。 它在 witness.log 文件中维护群集信息，但不存储群集数据库的副本。
- **文件共享见证** – 在运行 Windows Server 的文件服务器上配置的 SMB 文件共享。 它在 witness.log 文件中维护群集信息，但不存储群集数据库的副本。
- **磁盘见证** - 群集可用存储组中的小型群集磁盘。 此磁盘具有高可用性，并且可在节点之间故障转移。 它包含群集数据库的副本。  存储空间直通不支持磁盘见证。

## <a name="pool-quorum-overview"></a>池仲裁概述

前面介绍了在群集级别运行的群集仲裁。 现在，让我们深入了解在池级别运行的池仲裁（即，可以在丢失节点和驱动器的情况下让池保持运行状态）。 存储池既可在群集方案中使用，也可在非群集方案中使用，正因如此，它们采用了不同的仲裁机制。

下表提供了每种方案的池仲裁结果的概述：

| 服务器节点 | 可以承受一次服务器节点故障 | 可以承受一次服务器节点故障，以后还可以承受一次 | 可以承受同时发生两次服务器节点故障 |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | 否                                  | 否                                                | 否                                                 |
| 2 个 + 见证  | 是                                 | 否                                                | 否                                                 |
| 3            | 是                                 | 否                                                | 否                                                 |
| 3 个 + 见证  | 是                                 | 否                                                | 否                                                 |
| 4            | 是                                 | 否                                                | 否                                                 |
| 4 个 + 见证  | 是                                 | 是                                               | 是                                                |
| 5 个或更多  | 是                                 | 是                                               | 是                                                |

## <a name="how-pool-quorum-works"></a>池仲裁的工作原理

当驱动器发生故障或者某些驱动器子集与另一个子集失去联系时，幸存的驱动器需要确认它们可以构成池的多数，这样才能保持联机。  如果无法确认这一点，则它们将会脱机。 池是根据其磁盘是否足以用于仲裁 (50% + 1) 而确定脱机或保持联机状态的实体。 池资源所有者（主动群集节点）可以是 +1 的那一个。

但是，池仲裁的工作原理在以下方面不同于群集仲裁：

- 池使用群集中的一个节点作为见证，该节点决定了在一半驱动器关闭时池是否可以幸存（此节点是池资源所有者）
- 池没有动态仲裁
- 池不会实现自身的删除投票机制

### <a name="examples"></a>示例

#### <a name="four-nodes-with-a-symmetrical-layout"></a>采用对称布局的四个节点。 
16 个驱动器各自获得一个投票，节点 2 也获得一个投票（因为它是池资源所有者）。 “多数”由总数 **16 票**来确定。  如果节点 3 和 4 关闭，则幸存的子集包含 8 个驱动器和池资源所有者，即，获得了 16 个投票中的 9 票。 因此，池将会幸存。

![池仲裁 1](media/quorum/pool-1.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**是**。
- 可以同时承受两次服务器故障：**是**。 

#### <a name="four-nodes-with-a-symmetrical-layout-and-drive-failure"></a>采用对称布局的四个节点，且发生驱动器故障。 
16 个驱动器各自获得一个投票，节点 2 也获得一个投票（因为它是池资源所有者）。 “多数”由总数 **16 票**来确定。  首先，驱动器 7 会关闭。 如果节点 3 和 4 关闭，则幸存的子集包含 7 个驱动器和池资源所有者，即，获得了 16 个投票中的 8 票。 因此，池不会获得多数投票，从而会关闭。

![池仲裁 2](media/quorum/pool-2.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**不**。
- 可以同时承受两次服务器故障：**不**。 

#### <a name="four-nodes-with-a-non-symmetrical-layout"></a>采用非对称布局的四个节点。 
24 个驱动器各自获得一个投票，节点 2 也获得一个投票（因为它是池资源所有者）。 “多数”由总数 **24 票**来确定。  如果节点 3 和 4 关闭，则幸存的子集包含 8 个驱动器和池资源所有者，即，获得了 24 个投票中的 9 票。 因此，池不会获得多数投票，从而会关闭。

![池仲裁 3](media/quorum/pool-3.png)

- 可以承受一次服务器故障：**是**。
- 可以承受一次服务器故障，以后还可以承受一次：**视情况而定**（如果节点 3 和 4 都关闭，则池无法幸存，但在其他情况下都可以幸存）。
- 可以同时承受两次服务器故障：**视情况而定**（如果节点 3 和 4 都关闭，则池无法幸存，但在其他情况下都可以幸存）。

### <a name="pool-quorum-recommendations"></a>池仲裁建议

- 确保群集中的每个节点采用对称布局（每个节点的驱动器数目相同）
- 启用三向镜像或双重奇偶校验，以便可以承受节点故障，并使虚拟磁盘保持联机状态。 
- 如果两个以上的节点关闭，或者两个节点以及另一个节点上的磁盘关闭，则卷可能无法访问这些节点的数据的所有三个副本，因而造成脱机且不可用的情况。 建议尽快将服务器恢复正常或更换磁盘，确保卷中所有数据拥有最大的复原能力。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅以下部分：

- [配置和管理仲裁](https://docs.microsoft.com/windows-server/failover-clustering/manage-cluster-quorum)
- [部署云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)