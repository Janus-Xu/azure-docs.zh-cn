---
title: 跨租户管理体验
description: Azure 委派资源管理可实现跨租户管理体验。
ms.date: 04/24/2020
ms.topic: conceptual
ms.openlocfilehash: 51cd464da417bfd1d6d4ff52e2a2595a7ce77fe6
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82201697"
---
# <a name="cross-tenant-management-experiences"></a>跨租户管理体验

作为服务提供商，可以使用 [Azure 委派资源管理](../concepts/azure-delegated-resource-management.md)，以便在 [Azure 门户](https://portal.azure.com)中为租户内的多个客户管理 Azure 资源。 大多数任务和服务都可以跨托管租户在委派的 Azure 资源上执行。 本文介绍了一些可实现有效 Azure 委托资源管理的增强方案。

> [!NOTE]
> 还可以[在具有多个 Azure AD 租户的企业内](enterprise.md)使用 Azure 委派的资源管理来简化跨租户管理。

## <a name="understanding-customer-tenants"></a>理解客户租户

Azure Active Directory (Azure AD) 租户表示组织。 它是组织通过注册 Azure、Microsoft 365 或其他服务与 Microsoft 建立关系时接收的 Azure AD 的专用实例。 每个 Azure AD 租户都是独特的，独立于其他 Azure AD 租户，且具有其自己的租户 ID (GUID)。 有关详细信息，请参阅[什么是 Azure Active Directory？](../../active-directory/fundamentals/active-directory-whatis.md)

通常，为管理客户的 Azure 资源，服务提供商必须使用与该客户的租户相关联的帐户登录 Azure 门户，要求客户租户中的管理员为该服务提供商创建和管理用户帐户。

使用 Azure 委派资源管理时，加入过程会指定服务提供商租户中能够访问和管理客户租户中的订阅、资源组和资源的用户。 然后，这些用户可以使用自己的凭据登录到 Azure 门户。 在 Microsoft Azure 门户中，这些用户可以管理其有权访问的所有客户的资源。 为此，可以访问 Microsoft Azure 门户中的[我的客户](../how-to/view-manage-customers.md)页，或直接在该客户订阅的上下文中工作（在 Azure 门户中或通过 API）。

Azure 委派资源管理可更灵活地管理多个客户的资源，而无需登录到不同租户中的不同帐户。 例如，服务提供商可能有两个具有不同职责和访问级别的客户。 使用 Azure 委托资源管理，授权用户可以登录到服务提供商的租户来访问这些资源。

![通过一个服务提供商租户管理的客户资源](../media/azure-delegated-resource-management-service-provider-tenant.jpg)

## <a name="apis-and-management-tool-support"></a>API 和管理工具支持

可以直接在门户中对委派资源执行管理任务，也可以使用 API 和管理工具（如 Azure CLI 和 Azure PowerShell）对委派资源执行管理任务。 在处理委托的资源时，只要跨租户管理支持该功能，并且用户具有相应的权限，可以使用所有现有 API。

Azure PowerShell [AzSubscription cmdlet](https://docs.microsoft.com/powershell/module/Az.Accounts/Get-AzSubscription?view=azps-3.5.0)显示每个订阅的**tenantID** ，使你可以确定返回的订阅是属于你的服务提供商租户还是托管客户租户。

同样，Azure CLI 命令，如[az account list](https://docs.microsoft.com/cli/azure/account?view=azure-cli-latest#az-account-list)显示**homeTenantId**和**managedByTenants**属性。

> [!TIP]
> 如果使用 Azure CLI 时未看到这些值，请尝试通过运行`az account clear`后跟来`az login --identity`清除缓存。

我们还提供了特定于执行 Azure 委托资源管理任务的 Api。 有关详细信息，请参阅“参考”部分****。

## <a name="enhanced-services-and-scenarios"></a>增强的服务和方案

大多数任务和服务都可对跨托管租户的委托资源执行。 下面是可有效进行跨租户管理的一些关键方案。

[用于服务器的 Azure Arc（预览版）](../../azure-arc/servers/overview.md)：

- [将 Azure 外部的 Windows Server 或 Linux 计算机连接](../../azure-arc/servers/quickstart-onboard-portal.md)到 Azure 中的委派订阅和/或资源组
- 使用 Azure 构造（如Azure Policy 和标记）管理连接的计算机

[Azure 自动化](../../automation/index.yml)：

- 通过自动化帐户来访问和使用委派的客户资源

[Azure 备份](../../backup/index.yml)：

- 备份并还原客户租户中的客户数据
- 使用[备份资源管理器](../../backup/monitor-azure-backup-with-backup-explorer.md)来帮助查看备份项（包括尚未针对备份配置的 Azure 资源）的操作信息，以及为委托订阅监视信息（作业和警报）。 备份资源管理器当前仅对 Azure VM 数据可用。
- 在委派的订阅中使用[备份报表](../../backup/configure-reports.md)来跟踪历史趋势、分析备份存储消耗以及审核备份和还原。

[Azure Kubernetes 服务（AKS）](../../aks/index.yml)：

- 管理托管的 Kubernetes 环境并部署和管理客户租户中的容器化应用程序

[Azure Monitor](../../azure-monitor/index.yml)：

- 查看委派订阅的警报，并能够查看所有订阅的警报
- 查看委派订阅的活动日志详细信息
- Log analytics：从多个租户中的远程客户工作区查询数据
- 通过 Webhook 在服务提供商租户中创建触发自动化（例如 Azure Automation Runbook 或 Azure Functions）的客户租户警报

[Azure 网络](../../networking/networking-overview.md)：

- 部署和管理客户租户中的[Azure 虚拟网络（VNet）](../../virtual-network/index.yml)和虚拟网络接口卡（vnic）
- 部署和配置[Azure 防火墙](../../firewall/overview.md)来保护客户的虚拟网络资源
- 为客户管理连接服务，如[Azure 虚拟 WAN](../../virtual-wan/virtual-wan-about.md)、 [ExpressRoute](../../expressroute/expressroute-introduction.md)和[VPN 网关](../../vpn-gateway/vpn-gateway-about-vpngateways.md)
- 使用 Azure Lighthouse 支持[Azure 网络 MSP 计划](../../networking/networking-partners-msp.md)的关键方案


[Azure 策略](../../governance/policy/index.yml)：

- 符合性快照显示委派订阅中分配的策略的详细信息
- 在委派订阅中创建和编辑策略定义
- 在委派订阅中分配客户定义的策略定义
- 客户将看到由服务提供商和自己创建的策略
- 可以[修正 deployIfNotExists 或修改客户租户内的分配](../how-to/deploy-policy-remediation.md)

[Azure 资源关系图](../../governance/resource-graph/index.yml)：

- 现在，在返回的查询结果中包含租户 ID，以便确定订阅是属于客户租户还是服务提供商租户

[Azure 安全中心](../../security-center/index.yml)：

- 跨租户可见性
  - 监视与安全策略的符合性并确保所有租户资源的安全覆盖
  - 单个视图中跨多个客户的连续合规性监视
  - 通过安全分数计算监视、会审可操作安全建议，并设置其优先级
- 跨租户安全状况管理
  - 管理安全策略
  - 对不符合可操作安全建议的资源执行操作
  - 收集并存储安全相关数据
- 跨租户威胁检测和保护
  - 跨租户的资源检测威胁
  - 应用高级威胁防护控制（如实时 (JIT) VM 访问）
  - 通过自适应网络强化来强化网络安全组配置
  - 通过自适应应用程序控制，确保服务器仅运行适当的应用程序和进程
  - 运用文件完整性监视 (FIM) 监视对重要文件和注册表项的更改

[Azure Sentinel](../../sentinel/multiple-tenants-service-providers.md)：

- 管理[客户租户中的](../../sentinel/multiple-tenants-service-providers.md)Azure Sentinel 资源
- [跟踪攻击并查看跨多个客户租户的安全警报](https://techcommunity.microsoft.com/t5/azure-sentinel/using-azure-lighthouse-and-azure-sentinel-to-monitor-across/ba-p/1043899)

[Azure 服务运行状况](../../service-health/index.yml)：

- 通过 Azure 资源运行状况监视客户资源的运行状况
- 跟踪客户使用的 Azure 服务的运行状况

[Azure Site Recovery](../../site-recovery/index.yml)：

- 为客户租户中的 Azure 虚拟机管理灾难恢复选项（请注意，不能使用运行方式帐户复制 VM 扩展）

[Azure 虚拟机](../../virtual-machines/index.yml)：

- 使用虚拟机扩展在客户租户的 Azure VM 上提供部署后配置和自动化任务
- 使用启动诊断对客户租户中的 Azure VM 进行故障排除
- 使用客户租户中的串行控制台访问 VM
- 通过[策略](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/create-keyvault-secret)将 Vm 与 Azure KeyVault 集成以实现密码、机密或用于磁盘加密的加密密钥，从而确保机密存储在客户租户的 Key Vault 中
- 请注意，不能使用 Azure Active Directory 远程登录到客户租户中的 Vm

支持请求：

- 从 Microsoft Azure 门户中的“帮助 + 支持”边栏选项卡中，打开对委派资源的支持请求（选择对委派范围可用的支持计划）****

## <a name="current-limitations"></a>当前限制
对于所有方案，都请注意以下当前限制：

- 可以使用 Azure 委派资源管理执行 Azure 资源管理器处理的请求。 这些请求的操作 URI 都以 `https://management.azure.com` 开头。 但是，Azure 委派的资源管理不支持由资源类型的实例（如 KeyVault 的机密访问或存储数据访问）处理的请求。 这些请求的操作 URI 通常以实例特有的地址开头，例如 `https://myaccount.blob.core.windows.net` 或 `https://mykeyvault.vault.azure.net/`。 后者通常也是数据操作，而不是管理操作。 
- 角色分配必须使用基于角色的访问控制 (RBAC) [内置角色](../../role-based-access-control/built-in-roles.md)。 除了所有者或具有 [DataActions](../../role-based-access-control/role-definitions.md#dataactions) 权限的任何内置角色之外，Azure 委派资源管理当前支持其他所有内置角色。 仅在[向托管标识分配角色](../how-to/deploy-policy-remediation.md#create-a-user-who-can-assign-roles-to-a-managed-identity-in-the-customer-tenant)时才支持使用用户访问管理员角色。  不支持自定义角色和[经典订阅管理员角色](../../role-based-access-control/classic-administrators.md)。
- 虽然你可以将使用 Azure Databricks 的订阅加入其中，但管理租户中的用户目前无法在委派的订阅上启动 Azure Databricks 工作区。
- 虽然可以为具有资源锁的 Azure 委托资源管理加入订阅和资源组，但这些锁不会阻止管理租户中的用户执行操作。 [拒绝](../../role-based-access-control/deny-assignments.md)保护系统管理的资源的分配，如由 azure 托管应用程序或 azure 蓝图创建的资源（系统分配的拒绝分配），则会阻止管理租户中的用户对这些资源进行操作;但是，在这段时间内，客户租户中的用户无法创建自己的拒绝分配（用户分配的拒绝分配）。
- 管理租户中的用户将无法查看委派的客户订阅的计费信息，即使他们具有通常允许访问的内置角色。 这是因为对计费信息的访问需要的其他步骤当前仅支持同一租户中的用户。

## <a name="next-steps"></a>后续步骤

- 要将客户加入 Azure 委派资源管理，可以[使用 Azure 资源管理器模板](../how-to/onboard-customer.md)，或[将专用或公共托管服务发布到 Microsoft Azure 市场](../how-to/publish-managed-services-offers.md)。
- 在 Microsoft Azure 门户中转到“我的客户”，以[查看和管理客户](../how-to/view-manage-customers.md)****。
