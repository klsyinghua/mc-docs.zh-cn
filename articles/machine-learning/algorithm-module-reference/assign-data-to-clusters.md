---
title: 将数据分配到群集：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用“将数据分配到群集”模块为聚类分析模型评分。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 6038525de2d9683bc140b94f5b8ff53c04153e2b
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598589"
---
# <a name="module-assign-data-to-clusters"></a>模块：将数据分配到聚类

本文介绍如何使用 Azure 机器学习设计器（预览版）中的“将数据分配到群集”模块。  该模块通过一个使用“K-means 聚类分析”  算法训练的聚类分析模型来生成预测。

“将数据分配到群集”模块会返回一个数据集，其中包含每个新数据点的可能分配。 

## <a name="how-to-use-assign-data-to-clusters"></a>如何使用“将数据分配到群集”
  
1. 在 Azure 机器学习设计器中，找到先前训练的聚类分析模型。 可以使用以下方法之一创建和训练聚类分析模型：  
  
    - 使用 [K-Means 聚类分析](k-means-clustering.md)模块配置 K-Means 聚类分析算法，并使用数据集和“训练聚类分析模型”模块（本文）来训练模型。  
  
    - 还可以从工作区中的“保存的模型”  组添加现有的已训练聚类分析模型。

2. 将训练的模型附加到“将数据分配到群集”的左侧输入端口。   

3. 将新的数据集作为输入附加。 

   在此数据集中，标签为可选。 通常情况下，聚类分析是一种无人监督的学习方法。 你不会提前知道这些类别。 但是，输入列必须与在训练聚类分析模型时使用的列相同，否则会发生错误。

    > [!TIP]
    > 若要减少从群集预测写入设计器的列数，请使用[选择数据集中的列](select-columns-in-dataset.md)，然后选择列的子集。 
    
4. 如果希望结果包含完整的输入数据集（包括显示结果的列（群集分配）），请让“选中以便进行追加，或者取消选中以便只获取结果”复选框保持选中状态。 
  
    如果清除此复选框，则仅返回结果。 将预测作为 Web 服务的一部分创建时，可以使用此选项。
  
5.  运行管道。  
  
### <a name="results"></a>结果

+  若要查看数据集中的值，请右键单击模块，选择“结果数据集”，然后选择“可视化”   。

