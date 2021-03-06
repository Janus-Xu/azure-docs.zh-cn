---
title: Azure 开发测试实验室和 DevOps 的集成 |Microsoft Docs
description: 了解如何在企业环境中的持续集成（CI）/持续交付（CD）管道内使用 Azure 开发测试实验室实验室。
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "67078919"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>集成 Azure 开发测试实验室和 Azure DevOps
DevOps 是一种软件开发方法，它将软件开发（开发）与系统的操作（Ops）相集成。 此系统可能会随业务目标提供新的功能、更新和修补程序。 此方法包含基于目标、使用模式和客户反馈设计新功能的所有内容;在出现问题时修复、恢复和强化系统。 此方法的一个易于识别的组件是持续集成（CI）/持续交付（CD）管道。 CI/CD 管道通过一系列步骤（包括生成、测试和部署）来获取信息、代码和资源，以生成系统。 本文重点介绍在企业环境中的管道内有效使用实验室的不同方式。 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>在 DevOps 工作流中使用实验室的好处 

### <a name="focused-access"></a>重点访问 
使用实验室作为组件允许特定生态系统与一组有限的人员关联。 通常，在公共区域或特定功能上工作的团队或组将具有分配给他们的实验室。   

### <a name="infrastructure-replication-in-the-cloud"></a>云中的基础结构复制 
开发人员可以快速设置一个开发生态系统，其中包含一个带有源代码和工具的开发人员。 开发人员还可以创建几乎与生产配置完全相同的环境。 它有助于实现更快的内部循环开发。 

### <a name="pre-production"></a>预生产 
使用 CI/CD 管道中的实验室，可以更轻松地在同一时间运行多个不同的预生产环境或虚拟机进行异步测试。 可以在实验室中部署和管理不同的支持基础结构，如生成代理。 

## <a name="devops-with-devtest-labs"></a>具有开发测试实验室的 DevOps 

### <a name="development--operation"></a>开发/操作 
实验室应侧重于在功能区域中工作的团队。 此常见焦点允许共享特定于区域的资源，例如工具、脚本或资源管理器模板。 它允许更快地进行更改，同时将负面影响限制到较小的组。 这些共享资源允许开发人员创建具有所有必需代码、工具和配置的开发 Vm。 可以动态创建它们，也可以让系统创建具有自定义的基本映像。 开发人员不仅可以创建 Vm，还可以根据必要的模板创建开发测试实验室环境，以便在实验室中创建相应的 Azure 资源。 可以对实验室环境进行任何更改或破坏性工作，而不会影响任何其他人。 考虑这种情况：产品是安装在客户计算机上的独立系统。 在这种情况下，开发测试实验室已改进了 VM 创建，包括使用项目安装其他软件和预生成客户配置，以便更快地对代码进行内部循环测试。 
  
## <a name="cicd-pipeline"></a>CI/CD 管道 
CI/CD 管道是 DevOps 中的一项关键组件，可从开发人员的拉取请求移动代码，将代码与现有代码集成，并将其部署到生产生态系统。 所有资源不需要处于实验室中。 例如，可以在实验室外将 Jenkins 主机设置为持久性资源。 下面是将实验室集成到管道的一些具体示例。 

### <a name="build"></a>构建 
生成管道重点介绍如何创建一个组件包，这些组件将一起测试以移交给发布管道。 实验室可以是生成管道的一部分作为生成代理和其他支持资源的位置。 能够动态构建基础结构，从而实现更好的控制。 由于可以在实验室中拥有多个环境，因此每个生成都可以异步运行，同时使用生成 ID 作为环境信息的一部分，以唯一标识特定生成的资源。   

对于生成代理，实验室能够限制访问会提高安全性并降低意外损坏的可能性。  

### <a name="test"></a>测试 
开发测试实验室允许 CI/CD 管道自动创建可用于自动测试和手动测试的 Azure 资源（Vm、环境）。 将使用项目或公式创建 Vm，这些项目或公式使用生成过程中的信息来创建测试所需的不同自定义配置。   

### <a name="release"></a>Release 
在部署代码之前，开发测试实验室通常用于在发布部分中进行验证。 它类似于在 "生成" 部分中进行测试。 生产资源不应部署在开发测试实验室中。 

### <a name="customization"></a>自定义 
在 Azure DevOps 中，有一些现有任务允许在特定实验室中操作 Vm 和环境。 尽管 Azure DevOps Services 是一种管理 CI/CD 管道的方法，但您可以将实验室集成到支持调用 REST Api、执行 PowerShell 脚本或使用 Azure CLI 的任何系统中。 

尽管某些 CI/CD 管道管理器具有可管理 Azure 和开发测试实验室中的资源的现有开源插件。 可能需要使用某些自定义脚本来满足管道的特定需求。  考虑到这一点，执行任务时，服务主体将与适当的角色一起使用，以获得对实验室的访问权限。 服务主体通常需要参与者角色访问实验室。 有关详细信息，请参阅[将 Azure DevTests 实验室集成到 Azure DevOps 持续集成和传递管道](devtest-lab-integrate-ci-cd-vsts.md)。 
 