---
title: 在 Azure Data Lake Storage Gen2 中搜索（预览版）
titleSuffix: Azure Cognitive Search
description: 了解如何为 Azure Data Lake Storage Gen2 中的内容和元数据编制索引。 此功能目前以公共预览版提供
manager: nitinme
author: markheff
ms.author: v-tawe
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
origin.date: 11/04/2019
ms.date: 12/16/2019
ms.openlocfilehash: 5ecac70119a6ba1eb7599969bd9f09436e79677f
ms.sourcegitcommit: 4a09701b1cbc1d9ccee46d282e592aec26998bff
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75348579"
---
# <a name="indexing-documents-in-azure-data-lake-storage-gen2"></a>为 Azure Data Lake Storage Gen2 中的文档编制索引

> [!IMPORTANT] 
> Azure Data Lake Storage Gen2 支持目前以公共预览版提供。 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。
> 可以填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)来请求访问预览版。 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 提供了此功能。 目前不支持门户或 .NET SDK。


设置 Azure 存储帐户时，可以选择启用[分层命名空间](https://docs.azure.cn/storage/blobs/data-lake-storage-namespace)。 这样，就可以将帐户中的内容集合组织成目录和嵌套子目录的层次结构。 启用分层命名空间即可启用 [Azure Data Lake Storage Gen2](https://docs.azure.cn/storage/blobs/data-lake-storage-introduction)。

本文介绍如何开始为 Azure Data Lake Storage Gen2 中的文档编制索引。

## <a name="set-up-azure-data-lake-storage-gen2-indexer"></a>设置 Azure Data Lake Storage Gen2 索引器

需要完成几个步骤才能为 Data Lake Storage Gen2 中的内容编制索引。

### <a name="step-1-sign-up-for-the-preview"></a>步骤 1：注册预览版

填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)注册 Data Lake Storage Gen2 索引器预览版。 在我们同意你注册预览版后，你会收到确认电子邮件。

### <a name="step-2-follow-the-azure-blob-storage-indexing-setup-steps"></a>步骤 2：遵循 Azure Blob 存储索引设置步骤

收到预览版注册成功的确认消息后，便可以创建索引管道。

可以使用 [REST API 版本 2019-05-06-Preview](search-api-preview.md) 来为 Data Lake Storage Gen2 中的内容和元数据编制索引。 目前不支持门户或 .NET SDK。

为 Data Lake Storage Gen2 中的内容编制索引，与为 Azure Blob 存储中的内容编制索引相同。 若要了解如何设置 Data Lake Storage Gen2 数据源、索引和索引器，请参阅[如何使用 Azure 认知搜索为 Azure Blob 存储中的文档编制索引](search-howto-indexing-azure-blob-storage.md)。 “Blob 存储”一文还提供了支持的文档格式、提取的 Blob 元数据属性等相关信息。 此信息同样适用于 Data Lake Storage Gen2。

## <a name="access-control"></a>访问控制

Azure Data Lake Storage Gen2 实现了一个[访问控制模型](https://docs.azure.cn/storage/blobs/data-lake-storage-access-control)，该模型支持 Azure 基于角色的访问控制 (RBAC) 和类似于 POSIX 的访问控制列表 (ACL)。 为 Data Lake Storage Gen2 中的内容编制索引时，Azure 认知搜索不会从内容中提取 RBAC 和 ACL 信息。 因此，此信息不会包含在 Azure 认知搜索索引中。

如果对索引中的每个文档保持访问控制非常重要，则应由应用程序开发人员需要负责实施[安全修整](https://docs.azure.cn/search/search-security-trimming-for-azure-search)。