---
title: 导入数据：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“导入数据”模块将数据从现有的云数据服务载入机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: v-yiso
origin.date: 10/22/2019
ms.date: 03/09/2020
ms.openlocfilehash: 47add6c2ce302279fcc34a3824608eaab178ded2
ms.sourcegitcommit: d202f6fe068455461c8756b50e52acd4caf2d095
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78155037"
---
# <a name="import-data-module"></a>“导入数据”模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块可将数据从现有的云数据服务载入机器学习管道。 

> [!Note]
> 此模块提供的所有功能可以通过工作区登陆页中的**数据存储**和**数据集**来实现。 建议使用**数据存储**和**数据集**，因为它们包括数据监视等附加功能。 有关详细信息，请参阅文章[如何访问数据](../how-to-access-data.md)和[如何注册数据集](../how-to-create-register-datasets.md)。
> 注册数据集后，可以在设计器界面中的“数据集” -> “我的数据集”类别中找到它。   此模块是为工作室（经典版）用户保留的，以便为他们提供熟悉的体验。 
>

“导入数据”模块支持从以下源读取数据： 

- 通过 HTTP 从 URL 读取
- 通过[**数据存储**](../how-to-access-data.md))从 Azure 云存储
    - Azure Blob 容器
    - Azure 文件共享
    - Azure Data Lake
    - Azure Data Lake Gen2
    - Azure SQL 数据库
    - Azure PostgreSQL    

在使用云存储之前，需要先在 Azure 机器学习工作区中注册一个数据存储。 有关详细信息，请参阅[如何访问数据](../how-to-access-data.md)。 

定义所需的数据并连接到源后， **[导入数据](./import-data.md)** 将根据每个列包含的值推断该列的数据类型，并将数据载入设计器管道。 “导入数据”的输出是可在任何设计器管道中使用的数据集。 

如果源数据发生更改，可以通过重新运行[导入数据](./import-data.md)来刷新数据集并添加新数据。

## <a name="how-to-configure-import-data"></a>如何配置“导入数据”

1. 将“导入数据”模块添加到管道。  可以在设计器的“数据输入和输出”类别中找到此模块。 

1. 单击“启动数据导入向导”以使用向导配置数据源。 

    该向导将获取帐户名称和凭据，并帮助你配置其他选项。 如果你正在编辑现有的配置，该向导将先加载当前值。

1. 选择“数据源”，然后选择数据源类型。  该类型可以是 HTTP 或数据存储。

    如果选择数据存储，则可以选择已注册到 Azure 机器学习工作区的现有数据存储，或创建新的数据存储。 然后，定义数据在数据存储中的导入路径。 可以通过单击“浏览路径”来轻松浏览路径 ![import-data-path](media/module/import-data-path.png) 

1. 选择“预览架构”以筛选要包含的列。 还可以在“分析”选项中定义高级设置，例如“分隔符”。

    ![import-data-preview](media/module/import-data.png)



1. 运行管道。

    当“导入数据”将数据载入设计器时，它会根据每个列包含的值推断该列的数据类型：数字或分类。

    如果标题存在，则使用该标题来命名输出数据集的列。

    如果数据中没有现有的列标题，将使用以下格式生成新的列名称：col1, col2,…。 , coln*。

## <a name="results"></a>结果

导入完成后，请单击输出数据集，然后选择“可视化”查看是否已成功导入数据。 

如果希望保存数据以供重用，而不是每次运行管道时都导入一组新数据，请在模块右侧面板的“输出”  选项卡下选择“注册数据集”  图标。 选择数据集的名称。 保存的数据集将保留单击保存时存在的数据；重新运行管道时不会更新数据集，即使管道中的数据集发生更改，也是如此。 这有助于创建数据快照。

导入数据后，可能需要对它进行一些额外的准备，才能将它用于建模和分析：

- 使用[编辑元数据](./edit-metadata.md)更改列名、将列处理为不同的数据类型，或指示某些列是标签或特征。

- 使用[选择数据集中的列](./select-columns-in-dataset.md)选择要转换的或要在建模中使用的列子集。 使用[添加列](./add-columns.md)模块可以轻松地将已转换的或已删除的列重新加入原始数据集。  

- 使用[分区和采样](./partition-and-sample.md)来分割数据集、执行采样或获取排名靠前的 n 行。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
