---
title: 在 Azure AD 中阻止旧版身份验证协议
description: 了解组织应如何阻止旧身份验证协议
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 55ce0233fdefb8360376e94c0baafabe4c62ced7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81309209"
---
# <a name="blocking-legacy-authentication"></a>阻止旧身份验证
 
为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 术语“旧式身份验证”是指通过以下方式发出的身份验证请求：

- 不使用新式身份验证的早期 Office 客户端（例如 Office 2010 客户端）
- 使用 IMAP/SMTP/POP3 等旧式邮件协议的任何客户端

当今，大部分透露信息的登录企图都来自旧式身份验证。 旧式身份验证不支持多重身份验证 (MFA)。 即使在目录中启用 MFA 策略，恶意行动者也可以使用旧式协议进行身份验证并绕过 MFA。 防范旧式协议发出的恶意身份验证请求入侵帐户的最佳方法是完全阻止这些企图。

## <a name="identify-legacy-authentication-use"></a>识别旧式身份验证的用法

需要先了解用户是否有使用旧式身份验证的应用，以及它如何影响整个目录，然后才能在目录中阻止旧式身份验证。 Azure AD 登录日志可用于了解你是否正在使用旧身份验证。

1. 导航到 **Azure 门户** > **Azure Active Directory** Azure Active Directory> **登录**。
1. 如果未通过单击 **"客户端**应用" **列** >来显示 "**客户端应用**" 列，则添加它。
1. 按 **客户端应用** 筛选> 检查显示的所有**旧版身份验证客户端**选项。
1. 按**状态** > **成功**筛选。 
1. 如果需要，请使用**日期**筛选器扩展日期范围。

筛选只会显示所选旧身份验证协议进行的成功登录尝试。 单击每个单独的登录尝试将显示其他详细信息。 选择单个数据行后，"基本信息" 选项卡下的 "客户端应用" 列或 "客户端应用" 字段将指示使用的是旧身份验证协议。 这些日志将指示哪些用户仍然依赖于旧式身份验证，以及哪些应用程序使用旧式协议发出身份验证请求。 对于未出现在这些日志中并且已确认不使用旧身份验证的用户，请执行条件性访问策略或启用基线策略：仅阻止这些用户的旧身份验证。

## <a name="moving-away-from-legacy-authentication"></a>弃用旧式身份验证 

在更好地了解谁正在你的目录中使用旧式身份验证以及哪些应用程序依赖于这种身份验证后，下一步是将用户升级为使用新式身份验证。 新式身份验证是一种标识管理方法，提供更安全的用户身份验证和授权。 如果在目录中实施了 MFA 策略，则新式身份验证可确保根据需要提示用户执行 MFA。 这是旧式身份验证协议的更安全替代协议。

本部分逐步概述如何将环境更新到新式身份验证。 在组织中启用旧式身份验证阻止策略之前，请仔细阅读以下步骤。

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>步骤1：在目录中启用新式身份验证

启用新式身份验证的第一步是确保目录支持新式身份验证。 对于在 2017 年 8 月 1 日或之后创建的目录，默认已启用新式身份验证。 如果你的目录是在此日期之前创建的，则需要使用以下步骤手动为你的目录启用新式身份验证：

1. 检查目录是否已通过从 `Get-CsOAuthConfiguration`  [Skype for business Online PowerShell 模块](https://docs.microsoft.com/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell)运行来支持新式身份验证。
1. 如果命令返回空 `OAuthServers` 属性，则会禁用新式身份验证。 更新设置以使用 `Set-CsOAuthConfiguration`启用新式身份验证。 如果您 `OAuthServers` 的属性包含一项，则您可以继续。

请务必先完成此步骤，然后继续。 首先更改目录配置很重要，因为它们规定了将由所有 Office 客户端使用的协议。 即使你使用的是支持新式身份验证的 Office 客户端，如果在你的目录中禁用了新式身份验证，则它们将默认为使用旧协议。

### <a name="step-2-office-applications"></a>步骤2： Office 应用程序

在目录中启用新式身份验证后，可以通过对 Office 客户端启用新式身份验证开始更新应用程序。 默认情况下，Office 2016 或更高版本的客户端支持新式身份验证。 无需执行额外的步骤。

如果使用 Office 2013 Windows 客户端或更低版本，我们建议升级到 Office 2016 或更高版本。 即使完成了上述在目录中启用新式身份验证的步骤，早期的 Office 应用程序也仍会继续使用旧式身份验证协议。 如果你使用的是 Office 2013 客户端，但无法立即升级到 Office 2016 或更高版本，请按照以下文章中的步骤在 [Windows 设备上启用 Office 2013 新式身份验证](https://docs.microsoft.com/office365/admin/security-and-compliance/enable-modern-authentication)。 若要在使用旧身份验证时帮助保护你的帐户，我们建议你在目录中使用强密码。 请查看 [Azure AD 密码保护](../authentication/concept-password-ban-bad.md) ，禁止在目录中执行弱密码。

Office 2010 不支持新式身份验证。 需将使用 Office 2010 的任何用户升级到最新版本的 Office。 我们建议升级到 Office 2016 或更高版本，因为它默认会阻止旧式身份验证。

如果你使用的是 macOS，则建议升级到适用于 Mac 2016 或更高版本的 Office。 如果你使用的是本机邮件客户端，则需要在所有设备上安装 macOS 版本10.14 或更高版本。

### <a name="step-3-exchange-and-sharepoint"></a>步骤3： Exchange 和 SharePoint

要让基于 Windows 的 Outlook 客户端使用新式身份验证，还必须为 Exchange Online 启用新式身份验证。 如果对 Exchange Online 禁用新式身份验证，则支持新式身份验证的基于 Windows 的 Outlook 客户端（Outlook 2013 或更高版本）将使用基本身份验证连接到 Exchange Online 邮箱。

默认已经为 SharePoint Online 启用了新式身份验证。 对于在 2017 年 8 月 1 日之后创建的目录，默认已在 Exchange Online 中启用新式身份验证。 但是，如果你先前禁用了新式身份验证，或者你使用的是在此日期之前创建的目录，请按照以下文章中的步骤 [在 Exchange Online 中启用新式身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

### <a name="step-4-skype-for-business"></a>步骤4： Skype for Business

若要阻止 Skype for business 发出的旧式身份验证请求，需要为 Skype for Business Online 启用新式身份验证。 对于在 2017 年 8 月 1 日之后创建的目录，默认已经为 Skype for Business 启用了新式身份验证。

建议过渡到 Microsoft Teams，它默认支持新式身份验证。 但是，如果当前无法迁移，则需要为 Skype for Business Online 启用新式身份验证，让 Skype for Business 客户端开始使用新式身份验证。 请按照本文中的步骤 [使用新式身份验证支持的商业拓扑](https://docs.microsoft.com/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported)，为 skype for Business 启用新式身份验证。

除了为 Skype for Business Online 启用新式身份验证以外，我们还建议在为 Skype for Business 启用新式身份验证时，为 Exchange Online 启用新式身份验证。 此过程有助于同步 Exchange Online 和 Skype for Business Online 中的新式身份验证状态，并防止 Skype for Business 客户端中多次出现登录提示。

### <a name="step-5-using-mobile-devices"></a>步骤5：使用移动设备

移动设备上的应用程序也需要阻止旧式身份验证。 我们建议使用 Outlook for Mobile。 Outlook for Mobile 默认支持新式身份验证，可满足其他 MFA 基线保护策略。

若要使用本机 iOS 邮件客户端，需要运行 iOS 11.0 或更高版本，以确保更新邮件客户端来阻止旧式身份验证。

### <a name="step-6-on-premises-clients"></a>步骤6：本地客户端

对于使用本地 Exchange Server 和本地 Skype for Business 的混合产品客户，需要更新这两个服务才能启用新式身份验证。 在混合环境中使用新式身份验证时，仍会对本地用户进行身份验证。 授权用户访问资源（文件或电子邮件）的历程发生了变化。

在本地开始启用新式身份验证之前，请确保符合先决条件。 你现在已准备好在本地启用新式身份验证。

可在以下文章中找到有关启用新式身份验证的步骤：

* [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [如何在 Skype for Business 中使用新式身份验证 (ADAL)](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>后续步骤

- [如何将本地 Exchange Server 配置为使用混合新式身份验证](https://docs.microsoft.com/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [如何在 Skype for Business 中使用新式身份验证 (ADAL)](https://docs.microsoft.com/skypeforbusiness/manage/authentication/use-adal)
- [阻止传统身份验证](../conditional-access/block-legacy-authentication.md)
