---
title: 手动输入数据：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何使用 Azure 机器学习中的“手动输入数据”模块通过键入值来创建小型数据集。 该数据集可以有多个列。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: ec5d57399049aed18361deab880bd454806287b5
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598791"
---
# <a name="enter-data-manually-module"></a>“手动输入数据”模块

本文介绍了 Azure 机器学习设计器（预览版）中的一个模块。

使用此模块，可以通过键入值来创建小型数据集。 该数据集可以有多个列。
  
此模块可用于以下方案：  
  
- 生成一小组值用于测试  
  
- 创建简短的标签列表
  
- 键入要在数据集中插入的列名的列表

## <a name="enter-data-manually"></a>手动输入数据 
  
1.  将[手动输入数据](./enter-data-manually.md)模块添加到管道。 可以在 Azure 机器学习的**数据输入和输出**类别中找到此模块。 
  
2.  对于“DataFormat”  ，选择以下选项之一。 这些选项决定了应该如何分析你提供的数据。 每种格式的要求差别很大，因此请务必阅读相关主题。  
  
    -   **ARFF**。 Weka 使用的属性-关系文件格式。   
  
    -   **CSV**。 逗号分隔值格式。 有关详细信息，请参阅[转换为 CSV](./convert-to-csv.md)。  
  
    -   **SVMLight**。 Vowpal Wabbit 和其他机器学习框架使用的一种格式。  
  
    -   **TSV**。 制表符分隔值格式。

     如果你选择了某种格式，但是未提供满足格式规范的数据，则会发生运行时错误。
  
3.  在“数据”  文本框内单击以开始输入数据。 以下格式需要特别注意：  
  
    - **CSV**：若要创建多个列，请粘贴逗号分隔的文本，或者键入多个列并在字段之间使用逗号。
  
        如果选择“HasHeader”  选项，则可以使用第一行值作为列标题。  
  
        如果取消选择此选项，将使用列名 Col1、Col2，等等。 稍后可以使用[编辑元数据](./edit-metadata.md)来添加或更改列名。  
  
    - **TSV**：若要创建多个列，请粘贴制表符分隔的文本，或者键入多个列并在字段之间使用制表符。  
  
        如果选择“HasHeader”  选项，则可以使用第一行值作为列标题。  
  
        如果取消选择此选项，将使用列名 Col1、Col2，等等。 稍后可以使用[编辑元数据](./edit-metadata.md)来添加或更改列名。  
  
    -   **ARFF**：粘贴现有的 ARFF 格式文件。 如果直接键入值，请确保在数据的开头添加可选的标题和必需的属性字段。 
    
        例如，可以将以下标题和属性行添加到一个简单列表中。 列标题将是 `SampleText`。
    
        ```text
        % Title: SampleText.ARFF  
        % Source: Enter Data module  
        @ATTRIBUTE SampleText STRING  
        @DATA  
        \<type first data row here>  
        ```

    -   **SVMLight**：使用 SVMLight 格式键入或粘贴值。  
  
        例如，下面的示例以 SVMight 格式表示 Blood Donation 数据集的前两行：  
  
        ```text  
        # features are [Recency], [Frequency], [Monetary], [Time]  
        1 1:2 2:50 3:12500 4:98   
        1 1:0 2:13 3:3250 4:28   
        ```  
  
        运行[手动输入数据](./enter-data-manually.md)模块时，这些行将转换为列和索引值的数据集，如下所示：  
  
        |Col1|Col2|Col3|Col4|Labels|  
        |-|-|-|-|-|  
        |0.00016|0.004|0.999961|0.00784|1|  
        |0|0.004|0.999955|0.008615|1|  
  
4.  在每行后面按 Enter 键，以便另起一行。  
  
     **请确保在最后一行后按 ENTER 键。** 
     
     如果多次按 ENTER 键来添加多个空的尾随行，则会删除最后一个空行，但会将其他空行视为缺失值。  
  
     如果创建包含缺失值的行，则稍后随时可以将其筛选出来。  
  
5.  右键单击该模块，然后选择“运行所选”  以分析数据，并将其作为数据集加载到你的工作区中。  
  
     若要查看数据集，请单击输出端口并选择“可视化”  。  
## <a name="next-steps"></a>后续步骤

参阅 Azure 机器学习[可用的模块集](module-reference.md)。 