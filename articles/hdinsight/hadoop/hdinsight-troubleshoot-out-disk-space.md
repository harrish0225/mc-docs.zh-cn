---
title: Azure HDInsight 中群集节点的磁盘空间不足
description: Azure HDInsight 中群集节点的磁盘空间不足
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: v-yiso
origin.date: 08/05/2019
ms.date: 09/23/2019
ms.openlocfilehash: 435bbcfe2fc1b7ab3b016c2cb33d939531fbb8e2
ms.sourcegitcommit: 43f569aaac795027c2aa583036619ffb8b11b0b9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/12/2019
ms.locfileid: "70921336"
---
# <a name="scenario-cluster-node-runs-out-of-disk-space-in-azure-hdinsight"></a>方案：Azure HDInsight 中群集节点的磁盘空间不足

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。

## <a name="issue"></a>问题

作业可能失败并出现如下所示的错误消息：`/usr/hdp/2.6.3.2-14/hadoop/libexec/hadoop-config.sh: fork: No space left on device.`

或者，可能收到如下所示的 Apache Ambari 警报：`local-dirs usable space is below configured utilization percentage`。

## <a name="cause"></a>原因

Apache Yarn 应用程序缓存可能占用了所有可用磁盘空间。 Spark 应用程序可能运行效率低下。

## <a name="resolution"></a>解决方法

1. 使用 Ambari UI 确定哪个节点的磁盘空间不足。

1. 确定有问题的节点中哪个文件夹占用了大部分磁盘空间。 首先通过 SSH 连接到该节点，然后运行 `df` 列出所有装入点的磁盘用量。 通常，空间占用量最大的装入点是 `/mnt`，即 OSS 使用的一个临时磁盘。 可以进入某个文件夹，然后键入 `sudo du -hs` 显示该文件夹的总文件大小。 如果看到类似于 `/mnt/resource/hadoop/yarn/local/usercache/livy/appcache/application_1537280705629_0007` 的文件夹，则表示应用程序仍在运行。 问题可能与 RDD 持久性或中间随机文件相关。

1. 若要缓解此问题，请终止应用程序，以释放该应用程序使用的磁盘空间。

1. 若要最终解决该问题，请优化应用程序。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道之一获取更多支持：

* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
