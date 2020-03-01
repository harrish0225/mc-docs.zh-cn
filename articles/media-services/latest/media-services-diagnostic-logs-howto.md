---
title: 通过 Azure Monitor 监视媒体服务诊断日志 | Microsoft Docs
description: 本文演示如何通过 Azure Monitor 路由和查看诊断日志。
services: media-services
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 07/08/2019
ms.date: 02/24/2020
ms.author: v-jay
ms.openlocfilehash: 8cd29cbff589bf85d230d2e9de89e4d86f6dee81
ms.sourcegitcommit: f5bc5bf51a4ba589c94c390716fc5761024ff353
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77494588"
---
# <a name="monitor-media-services-diagnostic-logs"></a>监视媒体服务诊断日志

> [!NOTE]
> Google Widevine 目前在中国地区不可用。

可以通过 [Azure Monitor](../../azure-monitor/overview.md) 监视指标和诊断日志，以便了解应用程序的执行情况。 有关此功能的详细说明以及使用 Azure 媒体服务指标和诊断日志的原因，请参阅[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)。

本文介绍如何将数据路由到存储帐户并查看数据。 

## <a name="prerequisites"></a>必备条件

- [创建媒体服务帐户](create-account-cli-how-to.md)。
- 参阅[监视媒体服务指标和诊断日志](media-services-metrics-diagnostic-logs.md)。

## <a name="route-data-to-the-storage-account-using-the-portal"></a>使用门户将数据路由到存储帐户

1. 在 https://portal.azure.cn 登录 Azure 门户。
1. 导航到其中的媒体服务帐户，然后单击“监视”下的“诊断设置”。   在此处查看订阅中所有资源的列表，这些资源通过 Azure Monitor 生成监视数据。 

    ![诊断设置部分](media/media-services-diagnostic-logs/logs01.png)

1. 单击“添加诊断设置”  。

   资源诊断设置是描述应从特定资源中路由哪个监视数据以及此监视数据应传输到何处的一种定义   。

1. 在显示的部分中，提供设置的“名称”，并勾选“存档到存储帐户”框   。

    选择要向其发送日志的存储帐户，然后按“确定”。 
1. 勾选“日志”和“指标”下的所有框   。 可能仅有下述选项之一，这具体取决于资源类型。 这些复选框可控制向所选目标（本例中为存储帐户）发送此资源类型可用的哪些日志和指标数据类别。

   ![诊断设置部分](media/media-services-diagnostic-logs/logs02.png)
1. 将“保留期(天)”滑块移至 30  。 此滑块设置监视数据要在存储帐户中保留的天数。 Azure Monitor 会自动删除早于所述天数的数据。 如果保留期为 0 天，则无限期存储数据。
1. 单击“保存”  。

现在，资源的监视数据将流入到存储帐户。

## <a name="route-data-to-the-storage-account-using-the-cli"></a>使用 CLI 将数据路由到存储帐户

若要允许在存储帐户中存储诊断日志，请运行以下 `az monitor diagnostic-settings` CLI 命令： 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例如：

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforams  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount" \
    --resource-group "amsResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="view-data-in-the-storage-account-using-the-portal"></a>使用门户查看存储帐户中的数据

如果已执行前述步骤，则数据已开始流向存储帐户。

可能最多需等待 5 分钟，事件即会在存储帐户中显示。

1. 在门户中，导航到左侧导航栏上的“存储帐户”部分  。
1. 找到并单击上一部分中创建的存储帐户。
1. 单击“Blob”，然后单击标记为 **insights-logs-keydeliveryrequests** 的容器。  这是包含日志的容器。 监视数据依次按资源 ID、日期和时间细分存入容器。
1. 通过单击容器中的资源 ID、日期和时间，导航到 PT1H.json 文件。 单击 PT1H.json 文件，再单击“下载”  。

 现可查看存储帐户中存储的 JSON 事件。

### <a name="examples-of-pt1hjson"></a>PT1H.json 的示例

#### <a name="clear-key-delivery-log"></a>清除密钥传送日志

```json
{
  "time": "2019-05-21T00:07:33.2820450Z",
  "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000000/RESOURCEGROUPS/amsResourceGroup/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/AMSACCOUNT",
  "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
  "operationVersion": "1.0",
  "category": "KeyDeliveryRequests",
  "resultType": "Succeeded",
  "resultSignature": "OK",
  "durationMs": 253,
  "identity": {
    "authorization": {
      "issuer": "myIssuer",
      "audience": "myAudience"
    },
    "claims": {
      "urn:microsoft:azure:mediaservices:contentkeyidentifier": "e4276e1d-c012-40b1-80d0-ac15808b9277",
      "nbf": "1558396914",
      "exp": "1558400814",
      "iss": "myIssuer",
      "aud": "myAudience"
    }
  },
  "level": "Informational",
  "location": "chinaeast2",
  "properties": {
    "requestId": "fb5c2b3a-bffa-4434-9c6f-73d689649add",
    "keyType": "Clear",
    "keyId": "e4276e1d-c012-40b1-80d0-ac15808b9277",
    "policyName": "SharedContentKeyPolicyUsedByAllAssets",
    "tokenType": "JWT",
    "statusMessage": "OK"
  }
}
```

## <a name="see-also"></a>另请参阅

* [Azure Monitor 指标](../../azure-monitor/platform/data-platform.md)
* [Azure Monitor 诊断日志](../../azure-monitor/platform/platform-logs-overview.md)
* [如何从 Azure 资源收集和使用日志数据](../../azure-monitor/platform/platform-logs-overview.md)

## <a name="next-steps"></a>后续步骤

[监视指标](media-services-metrics-howto.md)