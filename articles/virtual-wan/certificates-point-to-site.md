---
title: 为用户 VPN 连接生成和导出证书 |Azure 虚拟 WAN
description: 在 Windows 10 或 Windows Server 2016 上使用 PowerShell 创建自签名根证书、导出公钥和生成客户端证书。
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: cherylmc
ms.openlocfilehash: 0303bac88f34c895a4a680cd5bff0e9d1513d2e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80059905"
---
# <a name="generate-and-export-certificates-for-user-vpn-connections"></a>为用户 VPN 连接生成和导出证书

用户 VPN （点到站点）连接使用证书进行身份验证。 本文介绍如何在 Windows 10 或 Windows Server 2016 上使用 PowerShell 创建自签名根证书并生成客户端证书。

必须在运行 Windows 10 或 Windows Server 2016 的计算机上执行本文中的步骤。 用于生成证书的 PowerShell cmdlet 是操作系统的一部分，在其他版本的 Windows 上不正常工作。 只需 Windows 10 或 Windows Server 2016 计算机即可生成证书。 生成证书后，可上传证书，或在任何支持的客户端操作系统上安装该证书。

[!INCLUDE [Export public key](../../includes/vpn-gateway-generate-export-certificates-include.md)]

## <a name="next-steps"></a>后续步骤

继续阅读[用户 VPN 连接的虚拟 WAN 步骤](virtual-wan-about.md)
