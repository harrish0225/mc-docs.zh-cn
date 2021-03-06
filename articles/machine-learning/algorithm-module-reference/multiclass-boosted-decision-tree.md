---
title: 多类提升决策树：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“多类提升决策树”模块来创建使用标记数据的分类器。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 7fd159f58bf6bea94253985de1e9b9b5f2ebd741
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598429"
---
# <a name="multiclass-boosted-decision-tree"></a>多类提升决策树

本文介绍 Azure 机器学习设计器（预览版）中的模块。

使用此模块，可以根据提升决策树算法创建机器学习模型。

提升决策树是一种集成学习方法，在此方法中，第二个树将针对第一个树的误差进行纠正，第三个树将针对第一个和第二个树的误差进行纠正，依此类推。 预测基于树的集合。

## <a name="how-to-configure"></a>如何配置 

此模块创建一个未训练的分类模型。 由于分类是一种监督式学习方法，所以，你需要一个标记的数据集，其中包含一个标签列，该列在所有行中都有一个值  。

可以使用[训练模型](././train-model.md)来训练这种类型的模型。 

1.  将“多类提升决策树”模块添加到管道  。

1.  通过设置“创建训练程序模式”选项，指定所希望的模型训练方式  。

    + **单个参数**：如果知道自己想要如何配置模型，可以提供一组特定的值作为参数。


    *  “每个树的最大叶数”限制可在任何树中创建的终端节点（叶）的最大数目  。
    
        如果增大此值，则可能会增加树的大小并达到更高的精度，但会有过度拟合和训练时间较长的风险。
  
    * “每个叶节点的最少样本数”指示在树中创建任何终端节点（叶）所需的事例数  。  

         通过增加此值，可以增加用于创建新规则的阈值。 例如，默认值为 1 时，即使是单个案例也可能导致创建新规则。 如果将值增加到 5，则训练数据将必须包含至少五个满足相同条件的案例。

    * “学习速率”定义学习时的步幅  。 请输入介于 0 到 1 之间的数字。

         学习速率决定了学习器向最佳解决方案趋近的速度。 如果步幅太大，则可能超出最佳解决方案。 如果步幅太小，训练将花费更长的时间来趋近最佳解决方案。

    * “构造的树数”指示要在集成中创建的决策树的总数  。 通过创建更多决策树，你可能会获得更好的覆盖范围，但训练时间将会增加。

    *  “随机数种子”可以选择性地设置非负整数作为随机种子值  。 指定种子可以确保具有相同数据和参数的运行之间的可再现性。  

         默认情况下，随机种子设置为 42。 使用不同随机种子的后续运行会产生不同的结果。

> [!Note]
> 如果将“创建训练程序模式”设置为“单个参数”，请连接标记的数据集和[训练模型](./train-model.md)模块   。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 
