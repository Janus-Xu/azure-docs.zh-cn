---
title: 通过 Azure DevOps Starter 将 ASP.NET Core 应用部署到 Azure Kubernetes 服务
description: Azure DevOps Starter 可让你轻松地开始在 Azure 上入门。 通过 DevOps Starter，只需几步即可使用 Azure Kubernetes 服务（AKS）部署 ASP.NET Core 应用。
ms.author: mlearned
ms.manager: gwallace
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 03/24/2020
author: mlearned
ms.openlocfilehash: 7e298afa7efa6035b24f689d1163308ac6b14bff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82233323"
---
# <a name="deploy-aspnet-core-apps-to-azure-kubernetes-service-with-azure-devops-starter"></a>通过 Azure DevOps Starter 将 ASP.NET Core 应用部署到 Azure Kubernetes 服务

Azure DevOps Starter 提供了简化的体验，你可以在其中引入现有的代码和 Git 存储库，或者选择一个示例应用程序，以便创建到 Azure 的持续集成（CI）和持续交付（CD）管道。 

DevOps Starter 还：

* 自动创建 Azure 资源，例如 Azure Kubernetes 服务 (AKS)。
* 在 Azure DevOps 中创建并配置一个发布管道，用于设置 CI/CD 的生成和发布管道。
* 创建用于监视的 Azure Application Insights 资源。
* 启用[用于容器的 Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) 以监视 AKS 群集上容器工作负载的性能

在本教程中，将：

> [!div class="checklist"]
> * 使用 DevOps Starter 将 ASP.NET Core 应用部署到 AKS
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 AKS 群集
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

## <a name="prerequisites"></a>必备条件

* Azure 订阅。 可以通过 [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/) 免费获取一个。

## <a name="use-devops-starter-to-deploy-an-aspnet-core-app-to-aks"></a>使用 DevOps Starter 将 ASP.NET Core 应用部署到 AKS

DevOps Starter Azure Pipelines 中创建 CI/CD 管道。 可以创建新的 Azure DevOps 组织，或使用现有的组织。 DevOps Starter 还会在所选的 Azure 订阅中创建 Azure 资源，例如 AKS 群集。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 在搜索框中，键入 " **DevOps Starter**"，然后选择。 单击 "**添加**" 以创建一个新的。

    ![DevOps 入门仪表板](_img/azure-devops-starter-aks/search-devops-starter.png)

1. 依次选择“.NET”、“下一步”。  

1. 在 "**选择应用程序框架**" 下，选择**ASP.NET Core** ，然后选择 "**下一步**"。

1. 依次选择“Kubernetes 服务”、“下一步”。   

## <a name="configure-azure-devops-and-an-azure-subscription"></a>配置 Azure DevOps 和 Azure 订阅

1. 创建新的 Azure DevOps 组织，或选择现有的组织。 

1. 输入 Azure DevOps 项目的名称。 

1. 选择 Azure 订阅。

1. 若要查看其他 Azure 配置设置并确定 AKS 群集的节点数，请选择“更改”。  此窗格显示了各种用于配置 Azure 服务类型和位置的选项。
 
1. 退出 Azure 配置区域，然后选择“完成”。  配置过程在几分钟后完成。 此过程会在你的 Azure DevOps 组织的 Git 存储库中设置一个示例 ASP.NET Core 应用，创建 AKS 群集，执行 CI/CD 管道，并将应用部署到 Azure。 

    完成所有此项后，Azure DevOps Starter 仪表板将显示在 Azure 门户中。 你还可以直接从 Azure 门户中的**所有资源**中转到 DevOps 入门仪表板。 

    在此仪表板中可以查看 Azure DevOps 代码存储库、CI/CD 管道和 AKS 群集。 可在 Azure DevOps 管道中配置其他 CI/CD 选项。 在右侧，选择“浏览”即可查看正在运行的应用。 

## <a name="examine-the-aks-cluster"></a>检查 AKS 群集

DevOps Starter 自动配置可浏览和自定义的 AKS 群集。 若要了解 AKS 群集，请执行以下操作：

1. 请参阅 DevOps 入门仪表板。

1. 在右侧，选择“AKS 服务”。 此时会打开 AKS 群集的窗格。 可在此视图中执行各种操作，例如，监视容器运行状况、搜索日志、打开 Kubernetes 仪表板。

1. 在右侧，选择“查看 Kubernetes 仪表板”。  （可选）执行相关步骤打开 Kubernetes 仪表板。

## <a name="examine-the-ci-pipeline"></a>检查 CI 管道

DevOps Starter 会自动在你的 Azure DevOps 组织中配置 CI/CD 管道。 可以浏览和自定义此管道。 若要了解此管道，请执行以下操作：

1. 请参阅 DevOps 入门仪表板。

1. 在 DevOps 入门仪表板的顶部，选择 "**生成管道**"。  浏览器标签页会显示新项目的生成管道。

1. 指向 "**状态**" 字段，然后选择省略号（...）。 菜单将显示多个选项，例如将新的生成排入队列、暂停生成和编辑生成管道。

1. 选择“编辑”  。

1. 在此窗格中，可以检查生成管道的各种任务。 该生成会执行各种任务，例如，从 Git 存储库提取源、还原依赖项，以及发布用于部署的输出。

1. 在生成管道的顶部，选择生成管道名称。

1. 将生成管道的名称更改为更具描述性的名称，选择 "**保存 & 队列**"，然后选择 "**保存**"。

1. 在生成管道名称下，选择“历史记录”。**** 此窗格显示最近针对生成所做的更改的审核线索。 Azure DevOps 会跟踪对生成管道所做的任何更改，并允许进行版本比较。

1. 选择**触发器**。 DevOps Starter 会自动创建 CI 触发器，每次提交到存储库将启动新的生成。 （可选）可以选择在 CI 过程中包括或排除分支。

1. 选择“保留期”。**** 可以根据方案指定策略，以保留或删除特定数目的生成。

## <a name="examine-the-cd-release-pipeline"></a>检查 CD 发布管道

DevOps Starter 会自动创建和配置必要的步骤，将其从 Azure DevOps 组织部署到你的 Azure 订阅。 这些步骤包括配置 Azure 服务连接，以便在 Azure 订阅中进行 Azure DevOps 身份验证。 自动化还会创建一个发布管道，该管道提供到 Azure 的 CD。 若要详细了解发布管道，请执行以下操作：

1. 选择“生成和发布”，然后选择“发布”。********  DevOps Starter 创建发布管道，以管理到 Azure 的部署。

1. 选择发布管道旁边的省略号 (...)，然后选择“编辑”。**** 发布管道包含一个管道，该*管道*定义发布过程。

1. 在“项目”下选择“删除”********。 在前述步骤中检查过的生成管道会生成用于项目的输出。 

1. 在“删除”图标的右侧，选择“持续部署触发器”。******** 此发布管道有一个已启用的 CD 触发器，每次有新的生成项目可用时，此触发器就会执行部署。 （可选）可以禁用此触发器，这样就需要手动执行部署。 

1. 在右侧选择“查看发布”，以显示发布历史记录。****

1. 选择某个发布旁边的省略号 (...)，然后选择“打开”。**** 可以浏览多个菜单，例如发布摘要、关联的工作项和测试。

1. 选择“提交”。**** 此视图显示与此部署关联的代码提交。 请对发布进行比较，查看不同部署之间的提交差异。

1. 选择 "**日志**"。 日志包含有关部署过程的有用信息。 可以在部署期间和之后查看日志。

## <a name="commit-changes-to-azure-repos-and-automatically-deploy-them-to-azure"></a>提交对 Azure Repos 所做的更改并将其自动部署到 Azure 

 > [!NOTE]
 > 以下过程通过进行简单的文本更改来测试 CI/CD 管道。

现在，可以使用可将最新工作部署到网站的 CI/CD 过程，与某个团队展开应用协作。 每次对 Git 存储库进行更改都会在 Azure DevOps 中启动一个生成，CD 管道可以执行到 Azure 的部署。 遵循本部分所述的过程，或使用其他方法提交对存储库所做的更改。 例如，可以在常用工具或 IDE 中克隆 Git 存储库，然后将更改推送到此存储库。

1. 在 Azure DevOps 菜单中，选择 "**代码** > **文件**"，然后打开存储库。

1. 转到 *Views\Home* 目录，选择 *Index.cshtml* 文件旁边的省略号 (...)，然后选择“编辑”。****

1. 对该文件进行更改，例如，在某个 div 标记内部添加一些文本。 

1. 在右上角选择“提交”，然后再次选择“提交”以推送更改。******** 片刻之后，某个生成会在 Azure DevOps 中启动，并且某个发布将会执行以部署更改。 在 DevOps 入门仪表板上或在浏览器中通过 Azure DevOps 组织监视生成状态。

1. 发布完成后，刷新应用以验证更改。

## <a name="clean-up-resources"></a>清理资源

测试后，可以清理资源，以避免产生费用。 不再需要本教程中创建的 AKS 群集和相关资源时，可将其删除。 为此，请使用 DevOps 入门仪表板上的**删除**功能。

> [!IMPORTANT]
> 以下过程会永久删除资源。 *删除*功能会销毁由项目在 Azure 和 azure DevOps 中的 DevOps 创建的数据，你将无法检索它。 只能在仔细阅读提示后才使用此过程。

1. 在 Azure 门户中，请切换到 DevOps 入门仪表板。
1. 在右上角选择“删除”****。 
1. 出现提示时，请选择“是”以永久删除这些资源。******

## <a name="next-steps"></a>后续步骤

可以选择根据所在团队的需求修改这些生成和发布管道。 也可以将此 CI/CD 模式用作其他管道的模板。 在本教程中，你了解了如何执行以下操作：

> [!div class="checklist"]
> * 使用 DevOps Starter 将 ASP.NET Core 应用部署到 AKS
> * 配置 Azure DevOps 和 Azure 订阅 
> * 检查 AKS 群集
> * 检查 CI 管道
> * 检查 CD 管道
> * 提交对 Git 所做的更改并将其自动部署到 Azure
> * 清理资源

若要详细了解如何使用 Kubernetes 仪表板，请参阅：

> [!div class="nextstepaction"]
> [使用 Kubernetes 仪表板](https://docs.microsoft.com/azure/aks/kubernetes-dashboard)
