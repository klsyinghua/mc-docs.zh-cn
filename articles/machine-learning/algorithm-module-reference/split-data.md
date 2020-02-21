---
title: 拆分数据：模块参考
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习中使用“拆分数据”模块将一个数据集拆分为两个非重复集。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 10/22/2019
ms.openlocfilehash: d7625de9f988e63d64aa347162365b3bc9c52108
ms.sourcegitcommit: 623d64ef33e80d5f84b6dcf6d1ef4120fe4b8c08
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/02/2020
ms.locfileid: "75598394"
---
# <a name="split-data-module"></a>“拆分数据”模块

本文介绍 Azure 机器学习设计器（预览版）中的一个模块。

使用本模块将一个数据集拆分为两个分重复集。

如果需要将数据拆分为训练和测试集，本模板会很有帮助。 你也可以自定义数据的拆分方式。 某些选项支持数据随机化；其他则为特定的数据类型或模型类型而定制。

## <a name="how-to-configure"></a>如何配置

> [!TIP]
> 在选择拆分模式之前，请阅读所有选项以确定所需的拆分类型。
> 如果更改拆分模式，则可重置所有其他选项。

1. 在设计器中将“拆分数据”模块添加到管道  。 你可在“示例和拆分”类别中的“数据转换”下找到此模块   。

2. **拆分模式**：选择以下模式之一，具体取决于你所拥有的数据类型，以及你希望的数据拆分方式。 每个拆分模式都有不同的选项。 单击以下主题，获取详细说明和示例。 

    - **拆分行**：如果只需将数据拆分为两个部分，请使用该选项。 你可指定要置于每个拆分中的数据的百分比，但在默认情况下，数据被划分为 50-50。

        还可随机化每组中的行选定内容，并使用分层采样。 在分层采样中，必须选择希望在两个结果数据集中平等分配值的单个数据列。  

    - **正则表达式拆分**  如果希望通过测试值的单列划分数据集，请选择该选项。

        例如，如果你正在分析情绪，可以在文本字段中查看是否存在特定的产品名称，然后将数据集拆分为具有目标产品名称的行，以及不具有目标产品名称的行。

    - **相对表达式拆分**：如果希望对数字列应用条件，请使用该选项。 该数字可以是日期/时间字段、包含年龄或金额的列，甚至可以是百分比。 例如，你可能希望根据项目的成本、按年龄范围分组人员，或按日历日期的不同数据来拆分数据集。

### <a name="split-rows"></a>拆分行

1.  在设计器中将[拆分数据](./split-data.md)模块添加到管道，并连接想要拆分的数据集。
  
2.  对于“拆分模式”，请选择“拆分行”   。 

3.  **首个输出数据集中的小部分行**。 使用此选项确定有多少行进入第一个（左侧）输出。 所有其他行将进入第二个（右侧）输出。

    此比率表示发送到第一个输出数据集的行的百分比，因此必须键入一个介于 0 和 1 之间的十进制数字。
     
     如果将 0.75 作为值键入，则将使用 75:25 的比率拆分数据集，并将 75% 的行发送到第一个输出数据集，而将 25% 发送到第二个输出数据集。
  
4. 如果要将数据选定内容随机化为两个组，请选择“随机化拆分”选项  。 这是创建训练和测试数据集时的首选项。

5.  **随机种子**:键入一个非负整数值以初始化要使用的实例的伪随机序列。 此默认种子用于生成随机数字的所有模块。 

     指定种子会使结果通常可重复。 如果需要重复执行拆分操作的结果，则应指定随机数生成器的种子。 否则，随机种子默认设置为 0，这意味着将从系统时钟获取初始种子值。 因此，每次执行拆分时，数据的分布都会略有不同。 

6. **分层拆分**：如果将此选项设置为 True，则可确保两个输出数据集包含“阶层列”或“分层键列”中值的代表性示例    。 

    使用分层采样，将对数据进行划分，以便每个输出数据集大约获取每个目标值的相同百分比。 例如，你可能希望确保你的训练和测试集在结果方面大致平衡，或者考虑其他一些列（如性别）。

7. 运行管道。


## <a name="regular-expression-split"></a>正则表达式拆分

1.  将[拆分数据](./split-data.md)模块添加到管道，并将其作为输入连接到要拆分的数据集。  
  
2.  对于“拆分模式”，请选择“正则表达式拆分”   。

3. 在“正则表达式”框中，键入有效的正则表达式  。 
  
   正则表达式应遵循 Python 正则表达式语法。


4. 运行管道。

    根据你提供的正则表达式，数据集分为两组行：值与表达式匹配的行和所有剩余行。 

## <a name="relative-expression-split"></a>相对表达式拆分。

1. 将[拆分数据](./split-data.md)模块添加到管道，并将其作为输入连接到要拆分的数据集。
  
2. 对于“拆分模式”，请选择“相对表达式拆分”   。
  
3. 在“相对表达式”文本框中，在单列上键入执行比较运算的表达式  ：


 - 数值列：
    - 该列包含任何数值数据类型的数字，包括日期/时间数据类型。

    - 表达式最多可以引用一个列名称。

    - 对“与”运算使用“&”字符 (&)，并对“或”运算使用竖线字符 (|)。

    - 支持以下运算符：`<`、`>`、`<=`、`>=`、`==`、`!=`

    - 不能使用 `(` 和 `)` 对运算进行分组。

 - 字符串列： 
    - 支持以下运算符：`==`、`!=`



4. 运行管道。

    表达式将数据集分成两组行：值满足条件的行和所有剩余行。

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 