---
title: Azure HDInsight 中的“hbase hbck”命令超时
description: 修复区域分配时出现“hbase hbck”命令超时问题
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: v-yiso
origin.date: 08/16/2019
ms.date: 11/11/2019
ms.openlocfilehash: 1a446a44f442e295a6c054a9fb00b5e5a8cc6f5b
ms.sourcegitcommit: 642a4ad454db5631e4d4a43555abd9773cae8891
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/01/2019
ms.locfileid: "73426059"
---
# <a name="scenario-timeouts-with-hbase-hbck-command-in-azure-hdinsight"></a>方案：Azure HDInsight 中的“hbase hbck”命令超时

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

修复区域分配时遇到 `hbase hbck` 命令超时。

## <a name="cause"></a>原因

使用 `hbck` 命令时出现超时问题的可能原因是多个区域长时间处于“正在转换”状态。 在 HBase Master UI 中可以看到这些区域显示为脱机。 由于有大量区域正在尝试进行转换，因此，HBase Master 可能会超时并且无法使那些区域恢复联机。

## <a name="resolution"></a>解决方法

1. 使用 SSH 登录到 HDInsight HBase 群集。

1. 运行 `hbase zkcli` 命令连接 Apache ZooKeeper shell。

1. 运行 `rmr /hbase/regions-in-transition` 或 `rmr /hbase-unsecure/regions-in-transition` 命令。

1. 使用 `exit` 命令从 `hbase zkcli` shell 退出。

1. 在 Apache Ambari UI 中，重启 Active HBase Master 服务。

1. 运行 `hbase hbck -fixAssignments` 命令。

1. 监视 HBase Master UI 的“转换中的区域”部分，以确保没有区域停滞。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道以获取更多支持：


* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 在 Microsoft Azure 订阅中可以访问订阅管理和计费支持；通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
