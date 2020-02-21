---
title: 决策林回归：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“决策林回归”模块基于决策树的集成创建回归模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: f39af45f1a57435d2009be8cec136d8f61cc6876
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598745"
---
# <a name="decision-forest-regression-module"></a>“决策林回归”模块

本文介绍了 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块，可以基于决策树的集成创建回归模型。

配置模型后，必须使用带标签的数据集和[训练模型](./train-model.md)模块来训练模型。 然后，可以使用训练后的模型进行预测。 

## <a name="how-it-works"></a>工作原理

决策树是非参数模型，可对每个实例执行一系列简单测试，遍历二元树数据结构，直至到达叶节点（决策）。

决策树具有以下优点：

- 在训练和预测期间的计算和内存使用效率都很高。

- 它们可以表示非线性决策边界。

- 它们执行集成的特征选择和分类，并且在存在干扰性特征时具有弹性。

此回归模型由决策树的集成组成。 回归决策林中的每个树都将高斯分布输出为预测。 聚合是通过树的集成执行的，用于查找与模型中所有树的组合分布最接近的高斯分布。

有关此算法及其实现的理论框架的详细信息，请参阅以下文章：[决策林：用于分类、回归、密度估算、流形学习和半监督式学习的统一框架](https://www.microsoft.com/en-us/research/publication/decision-forests-a-unified-framework-for-classification-regression-density-estimation-manifold-learning-and-semi-supervised-learning/?from=http%3A%2F%2Fresearch.microsoft.com%2Fapps%2Fpubs%2Fdefault.aspx%3Fid%3D158806#)

## <a name="how-to-configure-decision-forest-regression-model"></a>如何配置决策林回归模型

1. 将**决策林回归**模块添加到管道。 可以在设计器中在“机器学习”  、“初始化模型”  和“回归”  下找到此模块。

2. 打开模块属性，对于“重新采样方法”，请选择用于创建各个树的方法  。  你可以选择“装袋”或“复制”。  

    - **装袋**：装袋也称为“启动聚合”  。 回归决策林中的每个树都将高斯分布输出为预测。 聚合的目的是找到这样一个高斯分布：其前两个矩与通过组合各个树返回的所有分布来形成的混合高斯分布的矩相匹配。

         有关详细信息，请参阅关于[启动聚合](https://wikipedia.org/wiki/Bootstrap_aggregating)的维基百科条目。

    - **复制**：在复制中，每个树都用完全相同的输入数据进行训练。 确定每个树节点使用哪个拆分谓词仍然是随机的，并且树将多种多样。

         有关包含**复制**选项的训练过程的详细信息，请参阅[适用于计算机视觉和医疗图像分析的决策林。Criminisi 和 J. Shotton。Springer 2013。](https://research.microsoft.com/projects/decisionforests/)

3. 通过设置“创建训练程序模式”选项，指定要如何对模型进行训练  。

    - “单个参数” 

      如果你知道自己想要如何配置模型，可以提供一组特定的值作为参数。 你可能已通过试验了解了这些值，或者已采用它们作为指南。



4. 对于“决策树数目”  ，请指定要在集成中创建的决策树的总数。 通过创建更多决策树，你可能会获得更好的覆盖范围，但训练时间将会增加。

    > [!TIP]
    > 此值还控制对训练后的模型进行可视化时显示的树的数量。 如果希望查看或打印单个树，可将该值设置为 1；但是，这意味着只会生成一个树（带有初始参数集的树），而不会执行进一步的迭代。

5. 对于“决策树的最大深度”  ，请键入一个数字，以限制任何决策树的最大深度。 增加树的深度可能会提高精度，但存在过度拟合和训练时间增加的风险。

6. 对于“每个节点的随机拆分数”  ，键入在生成树的每个节点时要使用的拆分数。 “拆分”意味着对树的每个级别（节点）的要素进行随机划分  。

7. 对于“每个叶节点的最少样本数”  ，指定在树中创建任何终端节点（叶）所需的最小事例数。

     通过增加此值，可以增加创建新规则的阈值。 例如，使用默认值 1 时，即使是单个事例也可以导致创建新规则。 如果将值增加到 5，则训练数据将必须包含至少五个满足相同条件的事例。


9. 连接一个带标签的数据集，选择包含不超过两个结果的单个标签列，并连接到[训练模型](./train-model.md)。

    - 如果将“创建训练程序模式”选项设置为“单个参数”，请使用[训练模型](./train-model.md)模块来训练模型   。

   

10. 运行管道。

### <a name="results"></a>结果

在训练完成后：

+ 若要查看在每次迭代中创建的树，请右键单击训练模块的输出，并选择“可视化”  。

+ 若要查看每个节点的规则，请单击每个树并向下钻取到各个拆分。

+ 若要保存已训练模型的快照，请右键单击训练模块的输出，并选择“另存为已训练模型”  。 模型的此副本在后续运行管道时不会更新。 

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 