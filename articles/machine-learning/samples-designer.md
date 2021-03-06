---
title: 设计器管道示例
titleSuffix: Azure Machine Learning
description: 使用 Azure 机器学习设计器中的示例来快速开始生成机器学习管道。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: peterclu
ms.author: peterlu
ms.date: 03/29/2020
ms.openlocfilehash: f9a8b0a4c51024d91e517db2f6ae10a4dba62384
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389335"
---
# <a name="designer-sample-pipelines"></a>设计器示例管道

使用 Azure 机器学习设计器中的内置示例，快速开始生成自己的机器学习管道。 Azure 机器学习设计器 [GitHub 存储库](https://github.com/Azure/MachineLearningDesigner)包含了可帮助你了解某些常见机器学习方案的详细文档。

## <a name="prerequisites"></a>先决条件

* Azure 订阅。 如果还没有 Azure 订阅，可以创建一个[免费帐户](https://aka.ms/AMLFree)。
* 使用企业 SKU 的 Azure 机器学习工作区。


## <a name="how-to-use-sample-pipelines"></a>如何使用示例管道

设计器会将示例管道的副本保存到工作室工作区。 你可以编辑管道，以根据自己的需求对其进行改编；还可以保存管道供自己使用。 可以使用这些示例作为起点来快速开始生成项目。

### <a name="open-a-sample-pipeline"></a>打开示例管道

1. 登录到 <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a>，选择要使用的工作区。

1. 选择“设计器”。 

1. 在“新建管道”部分下选择一个示例管道。 

    选择“显示更多示例”查看完整的示例列表。 

### <a name="submit-a-pipeline-run"></a>提交管道运行

若要运行管道，必须首先设置要在其上运行管道的默认计算目标。

1. 在画布右侧的“设置”窗格中，选择“选择计算目标”。  

1. 在出现的对话框中，选择现有的计算目标或创建新的计算目标。 选择“保存”。 

1. 选择画布顶部的“提交”  ，提交管道运行。

运行的完成可能需要一些时间，具体取决于示例管道和计算设置。 默认计算设置中的最小节点大小为 0，这意味着设计器必须在空闲后分配资源。 由于计算资源已分配，因此，重复的管道运行花费的时间会更少。 此外，设计器还对每个模块使用缓存的结果，以便进一步提高效率。


### <a name="review-the-results"></a>查看结果

管道运行完毕后，可以查看管道，还可以查看每个模块的输出，了解详细信息。

使用以下步骤查看模块输出：

1. 选择画布中的模块。

1. 在画布右侧的模块详细信息窗格中，选择“输出 + 日志”。  选择图标 ![可视化图标](./media/tutorial-designer-automobile-price-train-score/visualize-icon.png)，查看每个模块的结果。 

从示例着手，了解一些最常见的机器学习方案。

## <a name="regression-samples"></a>回归示例

详细了解内置的回归示例。

| 标题示例 | 说明 | 
| --- | --- |
| [示例 1：回归 - 汽车价格预测(基本)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-basic.md)。 | 使用线性回归预测汽车价格。 |
| [示例 2：回归 - 汽车价格预测(高级)](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-regression-automobile-price-compare-algorithms.md)。 | 使用决策林和提升决策树回归器预测汽车价格。 比较模型以找出最佳算法。

## <a name="classification-samples"></a>分类示例

详细了解内置的分类示例。 对于未提供文档链接的示例，可将其打开并查看模块注释来详细了解这些示例。

| 标题示例 | 说明 | 
| --- | --- |
| [示例 3：通过特征选择进行二元分类 - 收入预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-predict-income.md) | 使用双类提升决策树预测收入的高低。 使用皮尔逊相关选择特征。
| [示例 4：通过自定义 Python 脚本进行二元分类 - 信用风险预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-credit-risk-cost-sensitive.md) | 将信贷申请分类为高风险或低风险。 使用“执行 Python 脚本”模块为数据加权。
| [示例 5：二元分类 - 客户关系预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-churn.md) | 使用双类提升决策树预测客户流失率。 使用 SMOTE 对有偏差的数据采样。
| [示例 7：文本分类 - 维基百科 SP 500 数据集](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-text-classification.md) | 使用多类逻辑回归对维基百科文章中的公司类型进行分类。 |
| 示例 12：多类分类 - 字母识别 | 创建二元分类器的系综，对手写字母进行分类。 |

## <a name="recommender-samples"></a>推荐器示例

详细了解内置的推荐器示例。 对于未提供文档链接的示例，可将其打开并查看模块注释来详细了解这些示例。

| 标题示例 | 说明 | 
| --- | --- |
| 示例 10：推荐 - 电影评级推文 | 基于电影标题和评级生成电影推荐器引擎。 |

## <a name="utility-samples"></a>实用工具示例

详细了解用于演示机器学习实用工具和功能的示例。 对于未提供文档链接的示例，可将其打开并查看模块注释来详细了解这些示例。

| 标题示例 | 说明 | 
| --- | --- |
| [示例 6：使用自定义 R 脚本 - 航班延误预测](https://github.com/Azure/MachineLearningDesigner/blob/master/articles/samples/how-to-designer-sample-classification-flight-delay.md) |
| 示例 8：二元分类的交叉验证 - 成人收入预测 | 使用交叉验证生成用于预测成人收入的二元分类器。
| 示例 9：排列特征重要性 | 使用排列特征重要性来计算测试数据集的重要性评分。 
| 示例 11：优化二元分类的参数 - 成人收入预测 | 使用“优化模型超参数”找出用于生成二元分类器的最佳超参数。 |

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>后续步骤

参考以下文章了解如何生成和部署机器学习模型：[教程：使用设计器预测汽车价格](tutorial-designer-automobile-price-train-score.md)
