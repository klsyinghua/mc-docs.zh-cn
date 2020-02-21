---
title: 交叉验证模型：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“交叉验证模型”模块，通过对数据进行分区来交叉验证分类或回归模型的参数估算值。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 10/10/2019
ms.openlocfilehash: 38c7edb77ef588fa5590eb9ff3f4f418bbbb1320
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598809"
---
# <a name="cross-validate-model"></a>交叉验证模型

本文介绍如何使用 Azure 机器学习设计器（预览版）中的“交叉验证模型”模块。 “交叉验证”技术通常在机器学习中用来评估数据集的可变性，以及通过该数据训练的任何模型的可靠性。   

“交叉验证模型”模块将带标签的数据集用作输入，与未训练的分类或回归模型一起工作。 它将数据集分割成某个数量的子集（折），在每个折上生成一个模型，然后为每个折返回一组准确度统计信息。  通过比较所有折的准确度统计信息，可以解释数据集的质量。 然后，可以了解模型是否容易受到数据变化的影响。  

“交叉验证模型”还返回数据集的预测结果和概率，使你能够评估预测的可靠性。  

### <a name="how-cross-validation-works"></a>交叉验证的工作原理

1. 交叉验证会将训练数据随机分割成折。 

   如果未事先将数据集分区，则算法默认使用 10 个折。 若要将数据集分割成不同数量的折，可以使用[分区和采样](partition-and-sample.md)模块并指定要使用的折数。  

2.  模块将留出折 1 中的数据以用于验证。 （此折有时称为“维持数据折”。）  模块使用剩余的折来训练模型。 

    例如，如果创建了五个折，模块将在交叉验证过程中生成五个模型。 模块使用 4/5 的数据来训练每个模型。 它基于 1/5 的剩余数据测试每个模型。  

3.  在测试每个折的模型期间，模块将评估多项准确度统计信息。 模块要使用哪些统计信息取决于要评估的模型类型。 不同的统计信息用于评估分类模型和回归模型。  

4.  完成所有折的生成和评估过程后，“交叉验证模型”将生成一组性能指标，以及所有数据的评分结果。 查看这些指标可以确定是否有任何一个折的准确度偏高或偏低。 

### <a name="advantages-of-cross-validation"></a>交叉验证的优势

评估模型的另一种常用方法是使用[拆分数据](split-data.md)将数据分割成训练集和测试集，然后基于训练数据验证模型。 但是，交叉验证提供一些优势：  

-   交叉验证使用更多的测试数据。

    交叉验证使用较大数据空间中指定的参数来度量模型的性能。 即，交叉验证将整个训练数据集（而不是其一部分）用于训练和评估。 相比之下，如果使用随机拆分后生成的数据来验证模型，则通常只会基于 30% 或更少的可用数据来评估模型。  

    但是，由于交叉验证基于较大的数据集训练和验证模型多次，因此其计算密集度要高得多。 与基于随机拆分的验证相比，其花费的时间也要长得多。  

-   交叉验证同时评估数据集和模型。

    交叉验证不只是度量模型的准确度。 它还可让你大致了解数据集的代表性高低，以及模型对数据变化的敏感性。  

## <a name="how-to-use-cross-validate-model"></a>如何使用“交叉验证模型”

如果数据集很大，则运行交叉验证可能需要较长的时间。  因此，可以在生成和测试模型的初始阶段使用“交叉验证模型”。 在此阶段，可以评估模型参数的好坏（假设容许任何计算时间）。 然后，可以在[训练模型](train-model.md)和[评估模型](evaluate-model.md)模块中使用建立的参数来训练和评估模型。

在此方案中，将使用“交叉验证模型”同时训练和测试模型。

1. 将“交叉验证模型”模块添加到管道。 可以在 Azure 机器学习设计器中的“模型评分和评估”类别中找到此模块。  

2. 连接任何分类或回归模型的输出。 

    例如，如果使用“双类贝叶斯点机”进行分类，请使用所需的参数配置模型。  然后，将分类器的“未训练的模型”端口中的连接器拖放到“交叉验证模型”的匹配端口。  

    > [!TIP] 
    > 无需训练模型，因为“交叉验证模型”会在评估过程中自动训练模型。  
3.  在“交叉验证模型”的“数据集”端口上，连接任何带标签的训练数据集。   

4.  在“交叉验证模型”的“属性”窗格中，选择“启动列选择器”。   选择包含类标签的单个列，或选择可预测的值。 

5. 若要在针对相同数据的连续运行中重复交叉验证的结果，请为“随机种子”参数设置一个值。   

6. 运行管道。

7. 有关报告的说明，请参阅[结果](#results)部分。

    若要获取模型的副本供今后重复使用，请右键单击包含算法的模块（例如“双类贝叶斯点机”）的输出。  然后选择“另存为已训练的模型”。 

## <a name="results"></a>结果

所有迭代完成后，“交叉验证模型”将为整个数据集创建评分。 它还会创建可用于评估模型质量的性能指标。

### <a name="scored-results"></a>评分结果

模块的第一个输出提供每个行的源数据，以及一些预测值和相关概率。 

若要查看这些结果，请在管道中右键单击“交叉验证模型”模块。 依次选择“评分结果”、“可视化”。  

| 新列名      | 说明                              |
| -------------------- | ---------------------------------------- |
| 评分标签        | 此列添加在数据集的末尾。 其中包含每个行的预测值。 |
| 评分概率 | 此列添加在数据集的末尾。 它指示“评分标签”中的值的估计概率。  |
| 折编号          | 指示在交叉验证过程中每个数据行分配到的折的从零开始的索引。 |

 ### <a name="evaluation-results"></a>评估结果

第二份报告已按折分组。 请记住，在执行过程中，“交叉验证模型”会将训练数据随机拆分为 *n* 折（默认为 10）。 每次迭代数据集时，“交叉验证模型”将使用一折作为验证数据集。 它使用剩余的 *n-1* 折来训练模型。 将会根据所有其他折中的数据测试 *n* 个模型中的每个模型。

在此报告中，将按索引值的升序列出折。  若要根据任何其他列进行排序，可将结果另存为数据集。

若要查看这些结果，请在管道中右键单击“交叉验证模型”模块。 依次选择“按折列出的评估结果”、“可视化”。  


|列名称| 说明|
|----|----|
|折编号| 每个折的标识符。 如果创建了 5 个折，则会有 5 个数据子集，其编号为 0 到 4。
|折中的示例数|分配给每个折的行数。 它们应大致相等。 |


根据要评估的模型类型，该模块还会包含每个折的以下指标： 

+ **分类模型**：精准率、召回率、F 评分、AUC、准确度  

+ **回归模型**：平均绝对误差、平均根方根误差、相对绝对误差、相对平方误差和决定系数。


## <a name="technical-notes"></a>技术说明  

+ 在将数据集用于交叉验证之前，最佳做法是规范化数据集。 

+ “交叉验证模型”的计算密集度要高得多，与使用随机分割的数据集验证模型相比，其完成时间更长。 原因在于，“交叉验证模型”需要训练并验证模型多次。

+ 使用交叉验证来度量模型的准确度时，无需将数据集拆分为训练集和测试集。 


## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 
