---
title: PowerShell 示例-无证书的应用程序代理应用
description: PowerShell 示例，其中列出了使用自定义域但尚未上传有效的 TLS/SSL 证书的所有 Azure Active Directory （Azure AD）应用程序代理应用程序。
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/05/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97dc46a34e1bb5bb85dcb771a9720a19d356e6f5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79481205"
---
# <a name="get-all-azure-ad-proxy-application-apps-published-with-no-certificate-uploaded"></a>获取已发布但未上传任何证书的所有 Azure AD 代理应用程序

此 PowerShell 脚本示例列出了使用自定义域但尚未上传有效的 TLS/SSL 证书的所有 Azure Active Directory （Azure AD）应用程序代理应用。

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

此示例需要用于图形模块（AzureAD）的[AzureAD V2 powershell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)或[graph 模块预览版本](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0-preview)（AzureADPreview）的 AzureAD v2 powershell。

## <a name="sample-script"></a>示例脚本

[!code-azurepowershell[main](~/powershell_scripts/application-proxy/get-all-custom-domain-no-cert.ps1 "Get all Azure AD Proxy application apps published with no certificate uploaded")]

## <a name="script-explanation"></a>脚本说明

| Command | 说明 |
|---|---|
|[Get-AzureADServicePrincipal](https://docs.microsoft.com/powershell/module/azuread/get-azureadserviceprincipal?view=azureadps-2.0) | 获取服务主体。 |
|[Get-AzureADApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplication?view=azureadps-2.0) | 获取 Azure AD 应用程序。 |
|[Get-AzureADApplicationProxyApplication](https://docs.microsoft.com/powershell/module/azuread/get-azureadapplicationproxyapplication?view=azureadps-2.0) | 检索为 Azure AD 中的应用程序代理配置的应用程序。 |

## <a name="next-steps"></a>后续步骤

有关 Azure AD PowerShell 模块的详细信息，请参阅[Azure AD powershell 模块概述](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0)。

有关应用程序代理的其他 PowerShell 示例，请参阅[Azure AD 应用程序代理的 Azure AD powershell 示例](../application-proxy-powershell-samples.md)。