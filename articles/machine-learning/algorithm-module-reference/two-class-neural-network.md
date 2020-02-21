---
title: 双类神经网络：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“双类神经网络”模块创建一个神经网络模型，该模型可用于预测只有两个值的目标。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: e7bf8ed852afb739db80f2bcec6fbba15eed6530
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598363"
---
# <a name="two-class-neural-network-module"></a>“双类神经网络”模块

本文介绍了 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块，可以创建一个神经网络模型，该模型可用于预测只有两个值的目标。

使用神经网络进行分类是一种监督式学习方法，因此需要一个“带标记的数据集”（其中包含一个标签列）。  例如，你可以使用此神经网络模型来预测二元结果，例如患者是否具有特定疾病，或者计算机是否可能在指定时间段内失败。  

在定义模型后，通过提供一个带标记的数据集和模型作为[训练模型](./train-model.md)的输入来对其进行训练。 然后，可以使用训练后的模型针对新输入来预测值。

### <a name="more-about-neural-networks"></a>有关神经网络的详细信息

神经网络是一组互连的层。 输入是第一层，并通过由加权边缘和节点组成的无环图连接到一个输出层。

在输入层和输出层之间，可以插入多个隐藏层。 大多数预测任务都可以仅使用一个或几个隐藏层轻松完成。 但是，最近的研究表明，具有许多个层的深度神经网络 (DNN) 在复杂任务（例如图像或语音识别）中可能比较有效。 后续层用于为不断增加的语义深度级别建模。

输入与输出之间的关系是通过基于输入数据对神经网络进行训练来了解的。 图形的方向是从输入到隐藏层，再到输出层。 每一层中的所有节点都通过加权边缘连接到下一层中的节点。

为了针对特定输入计算网络的输出，会在隐藏层和输出层中的每个节点上计算一个值。 此值是通过计算上一层中节点的值的加权和来设置的。 然后会向该加权和应用一个激活函数。
  
## <a name="how-to-configure"></a>如何配置

1.  向你的管道中添加**双类神经网络**模块。 可以在“机器学习”  、“初始化”  下的“分类”  类别中找到此模块。  
  
2.  通过设置“创建训练程序模式”选项，指定要如何对模型进行训练  。  
  
    -   **单个参数**：如果你已知要如何配置模型，请选择此选项。  

3.  对于“隐藏层规范”  ，请选择要创建的网络体系结构的类型。  
  
    -   **完全连接的情况**：使用为双类神经网络定义的默认神经网络体系结构，如下所述：
  
        -   有一个隐藏层。
  
        -   输出层完全连接到隐藏层，并且隐藏层完全连接到输入层。
  
        -   输入层中的节点数等于训练数据中的特征数。
  
        -   隐藏层中的节点数是由用户设置的。 默认值为 100。
  
        -   节点数等于类的数目。 对于双类神经网络，这意味着所有输入都必须映射到输出层中的两个节点之一。

5.  对于“学习比率”，请定义更正之前每次迭代要执行的步幅  。 学习比率的值越大，模型的汇聚速度就越快，但它可以超过本地最小值。

6.  对于“学习迭代数”，请指定算法处理训练事例的最大次数  。

7.  对于“初始学习权重直径”，在学习过程开始时，指定节点权重。 

8.  对于“动力”，指定在学习过程中要应用到之前迭代中的节点的权重。   

10. 选择“随机选择示例”选项可在迭代间随机选择事例。  如果取消选择此选项，则每次运行管道时，会以完全相同的顺序处理事例。
  
11. 对于“随机数种子”，键入一个值来用作种子  。
  
     如果要确保同一管道的运行可重复，则指定种子值非常有用。  否则，将使用系统时钟值作为种子，这可能会导致每次运行管道时产生略微不同的结果。
  
13. 向管道添加带标记的数据集，并连接[训练模块](module-reference.md)之一。  
  
    -   如果将“创建训练程序模式”设置为“单个参数”，请使用[训练模型](train-model.md)模块   。  
  
14. 运行管道。

## <a name="results"></a>结果

在训练完成后：

+ 若要查看模型参数以及从训练学到的特征权重的摘要，请右键单击[训练模型](./train-model.md)的输出，然后选择“可视化”  。  

+ 若要保存已训练模型的快照，请右键单击“已训练模型”输出，选择“另存为已训练模型”   。 此模型在后续运行相同管道时不会更新。


## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 