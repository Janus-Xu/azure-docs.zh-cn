---
title: SSPR 和 MFA 的组合注册 - Azure Active Directory
description: Azure AD 多重身份验证和自助服务密码重置注册
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4d4caaf8704f2ee49f8f094ad22065ae462154be
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143923"
---
# <a name="combined-security-information-registration-overview"></a>组合安全信息注册概述

在组合注册之前，用户单独注册了 Azure 多重身份验证和自助服务密码重置（SSPR）的身份验证方法。 人们搞糊涂，使用了类似的方法进行多重身份验证和 SSPR，但他们不得不注册这两种功能。 现在，通过组合注册，用户可以注册一次，并获得多重身份验证和 SSPR 的优势。

本文概述了安全注册的组合。 若要开始结合安全注册，请参阅以下文章：

> [!div class="nextstepaction"]
> [启用组合的安全注册](howto-registration-mfa-sspr-combined.md)

![我的配置文件显示用户的已注册安全信息](media/concept-registration-mfa-sspr-combined/combined-security-info-defualts-registered.png)

在启用新体验之前，请查看此以管理员为中心的文档和以用户为中心的文档，以确保你了解此功能的功能和效果。 根据[用户文档](../user-help/user-help-security-info-overview.md)的培训，为用户准备新体验，并帮助确保成功部署。

Azure AD 组合的安全信息注册目前不适用于国内云，如 Azure 美国政府版、Azure 德国或 Azure 中国世纪互联。

> [!IMPORTANT]
> 同时启用了原始预览和增强组合注册体验的用户将看到新的行为。 同时启用这两种体验的用户只会看到新的 "我的个人资料" 体验。 新的 "我的个人资料" 与组合注册的外观一致，并为用户提供无缝体验。 用户可以通过转到来[https://myprofile.microsoft.com](https://myprofile.microsoft.com)查看我的个人资料。
>
> 尝试访问 "安全信息" 选项时，可能会遇到错误消息。 例如，"抱歉，我们无法登录"。 在这种情况下，请确认在 web 浏览器中没有任何配置或组策略对象阻止第三方 cookie。

根据访问页面的计算机的语言设置对配置文件页面进行本地化。 Microsoft 存储了浏览器缓存中使用的最新语言，因此后续尝试访问页面的操作将以最后使用的语言呈现。 如果清除缓存，则页面将重新呈现。 如果要强制使用特定语言，则可以将添加`?lng=<language>`到 URL 的末尾，其中`<language>`是要呈现的语言的代码。

![设置 SSPR 或其他安全验证方法](media/howto-registration-mfa-sspr-combined/combined-security-info-my-profile.png)

## <a name="methods-available-in-combined-registration"></a>组合注册中的可用方法

组合注册支持以下身份验证方法和操作：

|   | 注册 | 更改 | 删除 |
| --- | --- | --- | --- |
| Microsoft Authenticator | 是（最多5个） | 否 | 是 |
| 其他验证器应用 | 是（最多5个） | 否 | 是 |
| 硬件令牌 | 否 | 否 | 是 |
| 电话 | 是 | 是 | 是 |
| 备用号码 | 是 | 是 | 是 |
| 办公电话 | 否 | 否 | 否 |
| 电子邮件 | 是 | 是 | 是 |
| 安全提问 | 是 | 否 | 是 |
| 应用密码 | 是 | 否 | 是 |
| FIDO2 安全密钥<br />*托管模式，仅从 "[安全信息](https://mysignins.microsoft.com/security-info)" 页*| 是 | 是 | 是 |

> [!NOTE]
> 应用密码仅适用于已强制执行多重身份验证的用户。 对于通过条件性访问策略启用多重身份验证的用户，不能使用应用密码。

用户可以将以下选项之一设置为默认多重身份验证方法：

- Microsoft Authenticator –通知。
- 验证器应用或硬件令牌-代码。
- 电话呼叫。
- 短信。

由于我们继续将更多的身份验证方法添加到 Azure AD，因此这些方法将在组合注册中可用。

## <a name="combined-registration-modes"></a>组合注册模式

组合注册分为两种模式：中断和管理。

- **中断模式**是一种类似于向导的体验，当用户在登录时注册或刷新其安全信息时，它会向用户提供。
- **管理模式**是用户配置文件的一部分，允许用户管理其安全信息。

对于这两种模式，先前注册了可用于多重身份验证的方法的用户需要先执行多重身份验证，然后才能访问其安全信息。 用户必须确认其信息，然后才能继续使用以前注册的方法。 

### <a name="interrupt-mode"></a>中断模式

如果同时为你的租户启用了多因素身份验证和 SSPR 策略，则组合注册会考虑这两个策略。 这些策略控制在登录期间用户是否被中断以便注册，以及哪些方法可用于注册。

在以下几种情况下，系统可能会提示用户注册或刷新其安全信息：

- 通过 Identity Protection 强制实施多重身份验证注册：要求用户在登录时注册。 它们将注册多重身份验证方法和 SSPR 方法（如果为 SSPR 启用了用户）。
- 通过每用户多重身份验证强制实施多重身份验证注册：用户需要在登录时注册。 它们将注册多重身份验证方法和 SSPR 方法（如果为 SSPR 启用了用户）。
- 通过条件访问或其他策略强制实施多重身份验证注册：在用户使用需要多重身份验证的资源时要求用户进行注册。 它们将注册多重身份验证方法和 SSPR 方法（如果为 SSPR 启用了用户）。
- 强制 SSPR 注册：用户需要在登录时注册。 它们只注册 SSPR 方法。
- 强制执行 SSPR 刷新：用户需要在管理员设置的时间间隔内检查其安全信息。用户显示其信息，并可以确认当前信息，或在需要时进行更改。

强制执行注册后，用户会看到所需的最小方法数必须符合多重身份验证和 SSPR 策略（从最高到最安全）。

例如：

- 已为 SSPR 启用用户。 SSPR 策略需要两种方法来重置和启用移动应用代码、电子邮件和手机。
   - 此用户需要注册两个方法。
      - 默认情况下，用户显示为 "身份验证器应用和电话"。
      - 用户可以选择注册电子邮件，而不是验证身份验证应用或电话。

此流程图介绍了在登录期间中断注册时向用户显示的方法：

![组合安全信息流程图](media/concept-registration-mfa-sspr-combined/combined-security-info-flow-chart.png)

如果同时启用了多重身份验证和 SSPR，我们建议强制实施多重身份验证注册。

如果 SSPR 策略要求用户定期检查其安全信息，则用户会在登录期间中断，并显示其所有注册方法。 如果是最新的，他们可以确认当前信息，如果需要，也可以进行更改。 访问此页时，用户必须执行多重身份验证。

### <a name="manage-mode"></a>管理模式

用户可以访问管理模式，方法是[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)转到或从 "我的配置文件" 中选择 "**安全信息**"。 用户可以在该处添加方法、删除或更改现有方法、更改默认方法等。

## <a name="key-usage-scenarios"></a>关键使用方案

### <a name="set-up-security-info-during-sign-in"></a>在登录过程中设置安全信息

管理员已强制执行注册。

用户尚未设置所有所需的安全信息，并转到 Azure 门户。 输入用户名和密码后，系统会提示用户设置安全信息。 然后，用户按照向导中显示的步骤来设置所需的安全信息。 如果设置允许，则用户可以选择设置默认情况下显示的其他方法。 完成向导后，用户将查看他们设置的方法及其用于多重身份验证的默认方法。 若要完成安装过程，用户需要确认信息并继续 Azure 门户。

### <a name="set-up-security-info-from-my-profile"></a>从 "我的配置文件" 设置安全信息

管理员尚未强制执行注册。

尚未设置所有必需[https://myprofile.microsoft.com](https://myprofile.microsoft.com)的安全信息的用户将进入。 用户在左窗格中选择 "**安全信息**"。 用户从此处选择添加方法，选择可用的任何方法，然后按照步骤设置该方法。 完成后，用户会看到刚在 "安全信息" 页上设置的方法。

### <a name="delete-security-info-from-my-profile"></a>从我的配置文件中删除安全信息

之前设置了至少一个方法的用户将导航到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。 用户选择删除以前注册的方法之一。 完成后，用户将不再在 "安全信息" 页上看到该方法。

### <a name="change-the-default-method-from-my-profile"></a>更改我的配置文件中的默认方法

以前设置了至少一个可用于多重身份验证的方法的用户将导航到[https://aka.ms/mysecurityinfo](https://aka.ms/mysecurityinfo)。 用户将当前默认方法更改为其他默认方法。 完成后，用户将看到 "安全信息" 页上的新默认方法。

## <a name="next-steps"></a>后续步骤

若要开始使用，请参阅[启用自助密码重置](tutorial-enable-sspr.md)和[启用 Azure 多重身份验证](tutorial-enable-azure-mfa.md)的教程。

了解如何[在租户中启用组合注册](howto-registration-mfa-sspr-combined.md)，或[强制用户重新注册身份验证方法](howto-mfa-userdevicesettings.md#manage-user-authentication-options)。

你还可以查看[Azure 多重身份验证和 SSPR 的可用方法](concept-authentication-methods.md)。
