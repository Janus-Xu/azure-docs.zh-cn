---
title: 本地资源的 SSO 在已加入 Azure AD 的设备上的工作原理 | Microsoft Docs
description: 了解如何配置联接到混合 Azure Active Directory 的设备。
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672705"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>本地资源的 SSO 在已加入 Azure AD 的设备上的工作原理

已加入 Azure Active Directory (Azure AD) 的设备为租户的云应用提供单一登录 (SSO) 体验可能并不意外。 如果环境具有本地 Active Directory (AD)，则你可将这些设备上的 SSO 体验扩展到该环境。

本文介绍它的工作原理。

## <a name="prerequisites"></a>先决条件

 如果 Azure AD 联接的计算机未连接到组织的网络，则需要 VPN 或其他网络基础结构。 本地 SSO 要求与本地 AD DS 域控制器进行线路通信。

## <a name="how-it-works"></a>工作原理 

因为你只需记住一个用户名和密码，因此 SSO 简化了资源访问，并提高了环境的安全性。 使用已加入 Azure AD 的设备，用户已在环境享有云应用的 SSO 体验。 如果环境具有一个 Azure AD 和一个本地 AD，建议将 SSO 体验的范围扩展到本地业务线 (LOB) 应用、文件共享和打印机。

已加入 Azure AD 的设备不了解你的本地 AD 环境，因为它们未加入其中。 但是，可以使用 Azure AD Connect 向这些设备提供本地 AD 的其他信息。

同时具有 Azure AD 和本地 AD 的环境被称为混合环境。 如果具有混合环境，很可能已部署 Azure AD Connect 以将本地标识信息同步到云。 作为同步过程的一部分，Azure AD Connect 将本地用户信息同步到 Azure AD。 当用户登录到混合环境中的已加入 Azure AD 的设备时：

1. Azure AD 将用户所属本地域的名称发送回设备。
1. 本地安全机构 (LSA) 服务在该设备允许进行 Kerberos 身份验证。

在尝试访问用户本地环境中请求 Kerberos 的资源期间，设备：

1. 向找到的 DC 发送本地域信息和用户凭据，以对用户进行身份验证。
1. 接收用于访问已加入 AD 的资源的 Kerberos [票证授予票证 (TGT)](/windows/desktop/secauthn/ticket-granting-tickets)。 如果尝试获取 AAD connect 域的 TGT 失败（相关 Dc 定位程序超时可能会导致延迟）、凭据管理器条目被尝试或用户可能会收到针对目标资源的身份验证弹出请求凭据。

当用户尝试访问针对 Windows 集成身份验证配置的所有应用时，它们将顺利进行 SSO****。

Windows Hello for Business 需要其他配置才能支持已加入 Azure AD 的设备的本地 SSO。 有关详细信息，请参阅[使用 Windows Hello for Business 配置已加入 Azure AD 的设备进行本地单一登录](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base)。 

## <a name="what-you-get"></a>用户所得

通过 SSO，在已加入 Azure AD 的设备上，可以： 

- 访问 AD 成员服务器上的 UNC 路径
- 访问为 Windows 集成安全性配置的 AD 成员 Web 服务器 

如果想要管理 Windows 设备的本地 AD，请安装[适用于 Windows 10 的远程服务器管理工具](https://www.microsoft.com/download/details.aspx?id=45520)。

可用工具如下：

- Active Directory 用户和计算机 (ADUC) 管理单元管理所有 AD 对象。 但是，需要手动指定要连接到的域。
- DHCP 管理单元用于管理已加入 AD 的 DHCP 服务器。 但是，可能需要指定 DHCP 服务器名称或地址。
 
## <a name="what-you-should-know"></a>要点

可能需要在 Azure AD Connect 中调整[基于域的筛选](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering)，以确保所需域的相关数据已同步。

依赖 Active Directory 计算机身份验证的应用和资源无法正常运行，因为已加入 Azure AD 的设备在 AD 中没有计算机对象。 

不能与已加入 Azure AD 的设备上的其他用户共享文件。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 [Azure Active Directory 中的设备管理是什么？](overview.md) 
