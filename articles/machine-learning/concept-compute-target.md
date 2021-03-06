---
title: 什么是计算目标
titleSuffix: Azure Machine Learning
description: 定义要在何处使用 Azure 机器学习训练或部署你的模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sgilley
author: sdgilley
ms.date: 03/30/2020
ms.openlocfilehash: f9ca75943eaec2ae018b54145d872fc09294035e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80398183"
---
#  <a name="what-are-compute-targets-in-azure-machine-learning"></a>什么是 Azure 机器学习中的计算目标? 

“计算目标”  是用来运行训练脚本或托管服务部署的指定计算资源/环境。 此位置可以是你的本地计算机，也可以是基于云的计算资源。 如果使用计算目标，以后可以轻松地更改计算环境，不需更改代码。  

在典型的模型开发生命周期中，你可以：
1. 首先，基于少量数据进行开发和试验。 在此阶段，我们建议使用本地环境（本地计算机或基于云的 VM）作为计算目标。 
2. 纵向扩展到更多的数据，或者使用这些[训练计算目标](#train)之一进行分布式训练。  
3. 在模型准备就绪后，使用这些[部署计算目标](#deploy)之一将其部署到 Web 托管环境或 IoT 设备。

你用于计算目标的计算资源附加到[工作区](concept-workspace.md)。 本地计算机以外的计算资源由工作区的用户共享。

## <a name="training-compute-targets"></a><a name="train"></a> 训练计算目标

Azure 机器学习为不同的计算资源提供不同的支持。  你也可以附加自己的计算资源，不过，为各种方案提供的支持可能有所不同。

[!INCLUDE [aml-compute-target-train](../../includes/aml-compute-target-train.md)]

详细了解[设置并使用计算目标进行模型训练](how-to-set-up-training-targets.md)。

## <a name="deployment-targets"></a><a name="deploy"></a>部署目标

以下计算资源可用来托管模型部署。

[!INCLUDE [aml-compute-target-deploy](../../includes/aml-compute-target-deploy.md)]

了解[在何处以及如何将模型部署到计算目标](how-to-deploy-and-where.md)。

<a name="amlcompute"></a>
## <a name="azure-machine-learning-compute-managed"></a>Azure 机器学习计算（托管）

托管计算资源是由 Azure 机器学习创建和管理的。 此计算针对机器学习工作负荷进行了优化。 Azure 机器学习计算群集和[计算实例](concept-compute-instance.md)是仅有的托管计算。 将来可能会添加其他托管计算资源。

可以从以下内容创建 Azure 机器学习计算实例（预览版）或计算群集：
* Azure 机器学习工作室
* Azure 门户
* Python SDK [ComputeInstance](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.computeinstance(class)?view=azure-ml-py)和[AmlCompute](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute(class)?view=azure-ml-py)类
* [R SDK](https://azure.github.io/azureml-sdk-for-r/reference/index.html#section-compute-targets)
* 资源管理器模板

你还可以使用[机器学习扩展为 Azure CLI](tutorial-train-deploy-model-cli.md#create-the-compute-target-for-training)创建计算群集。

创建时，这些计算资源会自动成为工作区的一部分，这与其他类型的计算目标不同。

### <a name="compute-clusters"></a>计算群集

你可以使用 Azure 机器学习计算群集进行训练以及批量推断（预览版）。  使用此计算资源，你将实现：

* 单节点或多节点群集
* 每次提交运行时自动缩放 
* 自动化群集管理和作业计划 
* 为 CPU 和 GPU 资源提供支持



## <a name="unmanaged-compute"></a>非托管计算

非托管计算目标不是由 Azure 机器学习托管的。** 你将在 Azure 机器学习外部创建此类型的计算目标，然后将其附加到工作区。 对于非托管计算资源，可能需要执行额外的步骤才能保持或提高机器学习工作负荷的性能。

## <a name="next-steps"></a>后续步骤

了解如何操作：
* [设置用来训练模型的计算目标](how-to-set-up-training-targets.md)
* [将模型部署到计算目标](how-to-deploy-and-where.md)