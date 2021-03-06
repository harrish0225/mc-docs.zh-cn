---
title: 视觉对象创作
description: 了解如何在 Azure 数据工厂中使用视觉对象创作
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: WenJason
ms.author: v-jay
ms.reviewer: ''
manager: digimobile
origin.date: 12/19/2019
ms.date: 01/06/2020
ms.openlocfilehash: 29cdc2d4f81e26999b0ab9d5eae5891cf2d66675
ms.sourcegitcommit: 6a8bf63f55c925e0e735e830d67029743d2c7c0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/03/2020
ms.locfileid: "75624314"
---
# <a name="visual-authoring-in-azure-data-factory"></a>Azure 数据工厂中的视觉对象创作

Azure 数据工厂用户界面体验 (UX) 允许你以可视方式创作和部署资源为你的数据工厂而无需编写任何代码。 通过此无代码的界面，可将活动拖放到管道画布上、执行测试运行、以迭代方式进行调试，以及部署和监视管道运行。

目前，仅在 Microsoft Edge 和 Google Chrome 中支持 Azure 数据工厂 UX。

## <a name="authoring-canvas"></a>创作画布

若要打开**创作画布**，请单击铅笔图标。 

![创作画布](media/author-visually/authoring-canvas.png)

在这里，你将创作构成工厂的管道、活动、数据集、链接服务、触发器和集成运行时。 若要开始使用创作画布构建管道，请参阅[使用复制活动复制数据](tutorial-copy-data-portal.md)。 

## <a name="expressions-and-functions"></a>表达式和函数

可以使用表达式和函数代替静态值来指定 Azure 数据工厂中的许多属性。

若要指定属性值的表达式，请选择“添加动态内容”  或在焦点在字段上时单击 **Alt + P**。

![添加动态内容](media/author-visually/dynamic-content-1.png)

这将打开**数据工厂表达式生成器**，你可以从支持的系统变量、活动输出、函数和用户指定的变量或参数构建表达式。 

![表达式生成器](media/author-visually/dynamic-content-2.png)

有关表达式语言的信息，请参阅 [Azure 数据工厂中的表达式和函数](control-flow-expression-language-functions.md)。

## <a name="provide-feedback"></a>提供反馈

选择“反馈”可发表有关功能的看法，或者告知 Azure 出现了工具问题  ：

![反馈](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>后续步骤

若要了解有关监视和管理管道的信息，请参阅[以编程方式监视和管理管道](monitor-programmatically.md)。
