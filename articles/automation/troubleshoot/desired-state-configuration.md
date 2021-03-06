---
title: Azure 自动化状态配置（DSC）疑难解答
description: 本文提供了有关 Azure 自动化状态配置（DSC）疑难解答的信息。
services: automation
ms.service: automation
ms.subservice: ''
author: mgoedtel
ms.author: magoedte
ms.date: 04/16/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4c045e110e21ed201278dcd84f38cb4a376ae8db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81679308"
---
# <a name="troubleshoot-issues-with-azure-automation-state-configuration-dsc"></a>排查 Azure 自动化状态配置（DSC）的问题

本文介绍有关在 Azure 自动化状态配置（DSC）中编译或部署配置时出现的问题的疑难解答信息。

>[!NOTE]
>本文进行了更新，以便使用新的 Azure PowerShell Az 模块。 你仍然可以使用 AzureRM 模块，至少在 2020 年 12 月之前，它将继续接收 bug 修补程序。 若要详细了解新的 Az 模块和 AzureRM 兼容性，请参阅[新 Azure Powershell Az 模块简介](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0)。 有关混合 Runbook 辅助角色上的 Az 模块安装说明，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0)。 对于自动化帐户，可参阅[如何更新 Azure 自动化中的 Azure PowerShell 模块](../automation-update-azure-modules.md)，将模块更新到最新版本。

## <a name="diagnosing-an-issue"></a>诊断问题

当你收到用于配置的编译或部署错误时，以下是一些帮助你诊断问题的步骤。

### <a name="1-ensure-that-your-configuration-compiles-successfully-on-the-local-machine"></a>1. 确保你的配置在本地计算机上成功编译

Azure 自动化状态配置（DSC）是基于 PowerShell Desired State Configuration （DSC）构建的。 可以在 [PowerShell DSC 文档](https://docs.microsoft.com/powershell/scripting/overview)中找到 DSC 语言和语法的文档。

通过在本地计算机上编译 DSC 配置，可以发现和解决常见错误，如：

   - 缺少模块
   - 语法错误
   - 逻辑错误

### <a name="2-view-dsc-logs-on-your-node"></a>2. 查看节点上的 DSC 日志

如果配置成功编译，但在应用于节点时失败，则可以在 DSC 日志中找到详细信息。 有关在何处查找这些日志的信息，请参阅[DSC 事件日志的位置](/powershell/scripting/dsc/troubleshooting/troubleshooting#where-are-dsc-event-logs)。

[XDscDiagnostics](https://github.com/PowerShell/xDscDiagnostics)模块可以帮助你分析 DSC 日志中的详细信息。 如果联系支持人员，则需要这些日志来诊断问题。

可以按照`xDscDiagnostics` [安装稳定版本模块](https://github.com/PowerShell/xDscDiagnostics#install-the-stable-version-module)中的说明在本地计算机上安装该模块。

若要在`xDscDiagnostics` Azure 计算机上安装模块，请使用[AzVMRunCommand](https://docs.microsoft.com/powershell/module/az.compute/invoke-azvmruncommand?view=azps-3.7.0)。 还可以按照在[WINDOWS VM 中运行 PowerShell 脚本和运行命令](../../virtual-machines/windows/run-command.md)中的步骤，使用 Azure 门户中的 "**运行命令**" 选项。

有关使用**xDscDiagnostics**的信息，请参阅[使用 xDscDiagnostics 分析 DSC 日志](/powershell/scripting/dsc/troubleshooting/troubleshooting#using-xdscdiagnostics-to-analyze-dsc-logs)。 另请参阅[XDscDiagnostics cmdlet](https://github.com/PowerShell/xDscDiagnostics#cmdlets)。

### <a name="3-ensure-that-nodes-and-the-automation-workspace-have-required-modules"></a>3. 确保节点和自动化工作区具有所需的模块

DSC 依赖于节点上安装的模块。 使用 Azure 自动化状态配置时，请使用[导入模块](../shared-resources/modules.md#importing-modules)中的步骤将任何所需的模块导入到自动化帐户中。 配置还可以依赖于特定版本的模块。 有关详细信息，请参阅[模块故障排除](shared-resources.md#modules)。

## <a name="scenario-a-configuration-with-special-characters-cannot-be-deleted-from-the-portal"></a><a name="unsupported-characters"></a>方案：无法从门户中删除包含特殊字符的配置

### <a name="issue"></a>问题

尝试通过门户删除 DSC 配置时，将看到以下错误：

```error
An error occurred while deleting the DSC configuration '<name>'.  Error-details: The argument configurationName with the value <name> is not valid.  Valid configuration names can contain only letters,  numbers, and underscores.  The name must start with a letter.  The length of the name must be between 1 and 64 characters.
```

### <a name="cause"></a>原因

此错误是计划要解决的临时问题。

### <a name="resolution"></a>解决方法

使用 [AzAutomationDscConfiguration] （https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationDscConfiguration?view=azps-3.7.0 cmdlet 删除配置。

### <a name="scenario-failed-to-register-dsc-agent"></a><a name="failed-to-register-agent"></a>方案：未能注册 Dsc 代理

### <a name="issue"></a>问题

当[set-dsclocalconfigurationmanager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1)或其他 DSC cmdlet 时，你将收到以下错误：

```error
Registration of the Dsc Agent with the server
https://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000 failed. The
underlying error is: Failed to register Dsc Agent with AgentId 00000000-0000-0000-0000-000000000000 with the server htt
ps://<location>-agentservice-prod-1.azure-automation.net/accounts/00000000-0000-0000-0000-000000000000/Nodes(AgentId='00000000-0000-0000-0000-000000000000'). .
    + CategoryInfo          : InvalidResult: (root/Microsoft/...gurationManager:String) [], CimException
    + FullyQualifiedErrorId : RegisterDscAgentCommandFailed,Microsoft.PowerShell.DesiredStateConfiguration.Commands.Re
   gisterDscAgentCommand
    + PSComputerName        : <computerName>
```

### <a name="cause"></a>原因

此错误通常由防火墙、位于代理服务器后面的计算机或其他网络错误导致。

### <a name="resolution"></a>解决方法

验证你的计算机是否可以访问适用于 DSC 的正确终结点，然后重试。 有关所需端口和地址的列表，请参阅[网络规划](../automation-dsc-overview.md#network-planning)

## <a name="a-nameunauthorizedscenario-status-reports-return-response-code-unauthorized"></a><a name="unauthorized"><a/>方案：状态报告返回未授权的响应代码

### <a name="issue"></a>问题

使用 Azure 自动化状态配置注册节点时，会收到以下错误消息之一：

```error
The attempt to send status report to the server https://{your Automation account URL}/accounts/xxxxxxxxxxxxxxxxxxxxxx/Nodes(AgentId='xxxxxxxxxxxxxxxxxxxxxxxxx')/SendReport returned unexpected response code Unauthorized.
```

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC / Registration of the Dsc Agent with the server failed.
```

### <a name="cause"></a>原因

此问题是由证书损坏或过期引起的。 请参阅[证书过期和重新注册](../automation-dsc-onboarding.md#re-registering-a-node)。

此问题也可能是由于代理配置不允许访问 ***. azure-automation.net**。 有关详细信息，请参阅[专用网络的配置](../automation-dsc-overview.md#network-planning)。 

### <a name="resolution"></a>解决方法

使用以下步骤重新注册失败的 DSC 节点。

步骤 1-取消注册节点。

1. 在 Azure 门户中，导航到 " **Home** -> **automation 帐户**-> （你的自动化帐户）"->**状态配置（DSC）**。
2. 选择 "**节点**"，然后单击 "有问题的节点"。
3. 单击 "**注销**" 以注销节点。

步骤 2-从节点中卸载 DSC 扩展。

1. 在 Azure 门户中，导航到 "**家庭** -> **虚拟机**-> （失败节点）->**扩展**"。
2. 选择 **""，** 然后选择 "powershell dsc 扩展"。
3. 单击 "**卸载**" 以卸载扩展。

步骤 3-从节点中删除所有错误或过期的证书。

在权限提升的 PowerShell 提示符下，运行以下命令：

```powershell
$certs = @()
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC"}
$certs += dir cert:\localmachine\my | ?{$_.FriendlyName -like "DSC-OaaS Client Authentication"}
$certs += dir cert:\localmachine\CA | ?{$_.subject -like "CN=AzureDSCExtension*"}
"";"== DSC Certificates found: " + $certs.Count
$certs | FL ThumbPrint,FriendlyName,Subject
If (($certs.Count) -gt 0)
{ 
    ForEach ($Cert in $certs) 
    {
        RD -LiteralPath ($Cert.Pspath) 
    }
}
```

步骤 4-重新注册失败的节点。

1. 在 Azure 门户中，导航到 " **Home** -> **automation 帐户**-> （你的自动化帐户）"->**状态配置（DSC）**
2. 选择**节点**。
3. 单击“添加”  。
4. 选择 "失败" 节点。
5. 单击 "**连接**"，然后选择所需的选项。

## <a name="scenario-node-is-in-failed-status-with-a-not-found-error"></a><a name="failed-not-found"></a>场景：节点处于失败状态，出现“未找到”错误

### <a name="issue"></a>问题

该节点有一个状态为“失败”的报表，其中包含错误：

```error
The attempt to get the action from server https://<url>//accounts/<account-id>/Nodes(AgentId=<agent-id>)/GetDscAction failed because a valid configuration <guid> cannot be found.
```

### <a name="cause"></a>原因

此错误通常发生在将节点分配到配置名称（例如， **abc**，而不是节点配置（MOF 文件）名称）时，例如**abc。Web**服务器。

### <a name="resolution"></a>解决方法

* 请确保为节点分配节点配置名称，而不是配置名称。
* 可以使用 Azure 门户或 PowerShell cmdlet 将节点配置分配给节点。

  * 在 Azure 门户中，导航到 " **Home** -> **automation 帐户**-> （你的自动化帐户）->**状态配置（DSC）**"，然后选择一个节点，然后单击 "**分配节点配置**"。
  * 使用[AzAutomationDscNode](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationDscNode?view=azps-3.7.0) cmdlet。

## <a name="scenario-no-node-configurations-mof-files-were-produced-when-a-configuration-was-compiled"></a><a name="no-mof-files"></a>方案：编译配置时未生成节点配置（MOF 文件）

### <a name="issue"></a>问题

DSC 编译作业暂停，且出现错误：

```error
Compilation completed successfully, but no node configuration **.mof** files were generated.
```

### <a name="cause"></a>原因

如果 DSC 配置中的`Node`关键字后面的表达式的计算结果`$null`为，则不会生成节点配置。

### <a name="resolution"></a>解决方法

使用以下解决方案之一来解决问题：

* 请确保配置定义中`Node`关键字旁边的表达式的计算结果不是 Null。
* 如果要在编译配置时传递[ConfigurationData](../automation-dsc-compile.md) ，请确保正在传递配置数据所需的值。

### <a name="scenario-the-dsc-node-report-becomes-stuck-in-the-in-progress-state"></a><a name="dsc-in-progress"></a>场景： DSC 节点报告将停滞在 "正在进行" 状态

### <a name="issue"></a>问题

DSC 代理输出：

```error
No instance found with given property values
```

### <a name="cause"></a>原因

你已升级 Windows Management Framework （WMF）版本，并已损坏 Windows Management Instrumentation （WMI）。

### <a name="resolution"></a>解决方法

按照[DSC 已知问题和限制](https://docs.microsoft.com/powershell/scripting/wmf/known-issues/known-issues-dsc)中的说明进行操作。

## <a name="scenario-unable-to-use-a-credential-in-a-dsc-configuration"></a><a name="issue-using-credential"></a>场景：无法在 DSC 配置中使用凭据

### <a name="issue"></a>问题

你的 DSC 编译作业已挂起，但出现错误：

```error
System.InvalidOperationException error processing property 'Credential' of type <some resource name>: Converting and storing an encrypted password as plaintext is allowed only if PSDscAllowPlainTextPassword is set to true.
```

### <a name="cause"></a>原因

你使用了配置中的凭据，但没有为每`ConfigurationData`个节点`PSDscAllowPlainTextPassword`配置将其提供给 true。

### <a name="resolution"></a>解决方法

请确保为配置中提到的`ConfigurationData`每个`PSDscAllowPlainTextPassword`节点配置将正确的设置为 "true"。 请参阅[在 Azure 自动化状态配置中编译 DSC 配置](../automation-dsc-compile.md)。

## <a name="scenario-failure-processing-extension-error-when-onboarding-from-dsc-extension"></a><a name="failure-processing-extension"></a>方案：从 DSC 扩展载入时出现 "失败处理扩展" 错误

### <a name="issue"></a>问题

使用 DSC 扩展进行加入时，会发生失败，其中包含错误：

```error
VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \"DSC COnfiguration 'RegistrationMetaConfigV2' completed with error(s). Following are the first few: Registration of the Dsc Agent with the server <url> failed. The underlying error is: The attempt to register Dsc Agent with Agent Id <ID> with the server <url> return unexpected response code BadRequest. .\".
```

### <a name="cause"></a>原因

当为节点分配了服务中不存在的节点配置名称时，通常会出现此错误。

### <a name="resolution"></a>解决方法

* 请确保分配的名称与服务中的名称完全匹配。
* 你可以选择不包括节点配置名称，这将导致加入该节点但不分配节点配置。

## <a name="scenario-one-or-more-errors-occurred-error-when-registering-a-node-using-powershell"></a><a name="cross-subscription"></a>方案：使用 PowerShell 注册节点时出现 "出现一个或多个错误" 错误

### <a name="issue"></a>问题

当使用[AzAutomationDSCNode](https://docs.microsoft.com/powershell/module/az.automation/register-azautomationdscnode?view=azps-3.7.0)或[unregister-azurermautomationdscnode](https://docs.microsoft.com/powershell/module/azurerm.automation/register-azurermautomationdscnode?view=azurermps-6.13.0)注册节点时，会收到以下错误：

```error
One or more errors occurred.
```

### <a name="cause"></a>原因

当你尝试在不同于自动化帐户所使用的订阅中注册节点时，将发生此错误。

### <a name="resolution"></a>解决方法

处理跨订阅节点，就像它是为单独的云或本地定义的一样。 使用下列载入选项之一注册节点：

* Windows-[本地或 Azure/AWS 以外的云中](../automation-dsc-onboarding.md#onboarding-physicalvirtual-windows-machines)的 windows 计算机。
* Linux-[本地或 Azure 以外的云中的物理/虚拟 Linux 计算机](../automation-dsc-onboarding.md#onboarding-physicalvirtual-linux-machines)。

## <a name="scenario-error-message---provisioning-failed"></a><a name="agent-has-a-problem"></a>方案：错误消息-"预配失败"

### <a name="issue"></a>问题

注册节点时，会看到以下错误：

```error
Provisioning has failed
```

### <a name="cause"></a>原因

当节点与 Azure 之间的连接出现问题时，将出现此消息。

### <a name="resolution"></a>解决方法

确定节点是在虚拟专用网络（VPN）中，还是在连接到 Azure 时出现其他问题。 请参阅[排除载入解决方案时的错误](onboarding.md)。

## <a name="scenario-failure-with-a-general-error-when-applying-a-configuration-in-linux"></a><a name="failure-linux-temp-noexec"></a>方案：在 Linux 中应用配置时出现一般错误

### <a name="issue"></a>问题

在 Linux 中应用配置时，会出现包含以下错误的故障：

```error
This event indicates that failure happens when LCM is processing the configuration. ErrorId is 1. ErrorDetail is The SendConfigurationApply function did not succeed.. ResourceId is [resource]name and SourceInfo is ::nnn::n::resource. ErrorMessage is A general error occurred, not covered by a more specific error code..
```

### <a name="cause"></a>原因

如果 **/tmp**位置设置为`noexec`，则 DSC 的当前版本将无法应用配置。

### <a name="resolution"></a>解决方法

从/tmp `noexec`位置中删除 **/tmp**该选项。

## <a name="scenario-node-configuration-names-that-overlap-can-result-in-bad-release"></a><a name="compilation-node-name-overlap"></a>情况：重叠的节点配置名称可能会导致发布错误

### <a name="issue"></a>问题

如果使用单个配置脚本来生成多个节点配置，并且某些节点配置名称是其他名称的子集，则编译服务可能最终会分配错误的配置。 此问题仅在以下情况下发生：仅使用单个脚本生成每个节点具有配置数据的配置，并且仅当名称在字符串的开头发生重叠时才会发生。 例如，一个配置脚本用于基于使用 cmdlet 作为哈希表传递的节点数据生成配置，节点数据包括名为**server**和**1server**的服务器。

### <a name="cause"></a>原因

这是编译服务的已知问题。

### <a name="resolution"></a>解决方法

最好的解决方法是在本地或在 CI/CD 管道中进行编译，并将节点配置 MOF 文件直接上传到服务。 如果服务中的编译是必需的，则下一种最佳解决方法是拆分编译作业，使名称中不存在重叠。

## <a name="scenario-gateway-timeout-error-on-dsc-configuration-upload"></a><a name="gateway-timeout"></a>方案： DSC 配置上传网关超时错误

#### <a name="issue"></a>问题

上传 DSC `GatewayTimeout`配置时收到错误。 

### <a name="cause"></a>原因

需要很长时间编译的 DSC 配置可能导致此错误。

### <a name="resolution"></a>解决方法

可以通过显式包含任何`ModuleName` [get-dscresource](https://docs.microsoft.com/powershell/scripting/dsc/configurations/import-dscresource?view=powershell-5.1)调用的参数，使 DSC 配置更快地进行分析。

## <a name="next-steps"></a>后续步骤

如果你没有看到你的问题或无法解决你的问题，请尝试以下通道之一以获取其他支持：

* 通过[Azure 论坛](https://azure.microsoft.com/support/forums/)获取 azure 专家的解答。
* 与[@AzureSupport](https://twitter.com/azuresupport)官方 Microsoft Azure 帐户联系，通过将 Azure 社区连接到适当的资源来改进客户体验：答案、支持和专家。
* 提出 Azure 支持事件。 转到[Azure 支持站点](https://azure.microsoft.com/support/options/)并选择 "**获取支持**"。