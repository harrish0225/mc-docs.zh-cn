---
title: 无法在 Azure HDInsight 中读取 Apache Yarn 日志
description: 与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方法。
author: hrasheed-msft
ms.author: v-yiso
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
origin.date: 01/23/2020
ms.date: 03/02/2020
ms.openlocfilehash: 96a48ec875d37e4619e0cce7956fcba41dec469e
ms.sourcegitcommit: 46fd4297641622c1984011eac4cb5a8f6f94e9f5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77563603"
---
# <a name="scenario-unable-to-read-apache-yarn-log-in-azure-hdinsight"></a>方案：无法在 Azure HDInsight 中读取 Apache Yarn 日志

本文介绍在与 Azure HDInsight 群集交互时出现的问题的故障排除步骤和可能的解决方案。

## <a name="issue"></a>问题

从存储帐户找到的 Apache Yarn 日志用户不可读。 文件分析程序无法工作，生成以下错误消息：

```
java.io.IOException: Not a valid BCFile.
```

## <a name="cause"></a>原因

Apache Yarn 日志聚合为文件分析程序不支持的 `IndexFile` 格式。

## <a name="resolution"></a>解决方法

1. 在 Web 浏览器中，导航到 `https://CLUSTERNAME.azurehdinsight.cn`，其中 `CLUSTERNAME` 是群集的名称。

1. 在 Ambari UI 中，导航到“YARN”   > “配置”   > “高级”   > “高级 yarn-site”  。

1. 对于 WASB 存储：`yarn.log-aggregation.file-formats` 的默认值为 `IndexedFormat,TFile`。 将该值更改为 `TFile`。

1. 对于 ADLS 存储：`yarn.nodemanager.log-aggregation.compression-type` 的默认值为 `gz`。 将该值更改为 `none`。

1. 保存更改并重启所有受影响的服务。

## <a name="next-steps"></a>后续步骤

如果你的问题未在本文中列出，或者无法解决问题，请访问以下渠道以获取更多支持：


* 如果需要更多帮助，可以从 [Azure 门户](https://portal.azure.cn/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/)提交支持请求。 从菜单栏中选择“支持”  ，或打开“帮助 + 支持”  中心。 有关更多详细信息，请参阅[如何创建 Azure 支持请求](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)。 Microsoft Azure 订阅包含对订阅管理和计费支持的访问权限，并且通过 [Azure 支持计划](https://azure.microsoft.com/support/plans/)之一提供技术支持。
