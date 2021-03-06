---
title: 为 Azure Marketplace 创建用户 VM 映像
description: 列出创建用户 VM 映像所需的步骤和参考。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: dsindona
ms.openlocfilehash: 9d82d50769925480d461c122096c3919d7e8940d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82146569"
---
# <a name="create-a-user-vm-image"></a>创建用户 VM 映像

> [!IMPORTANT]
> 从2020年4月13日开始，我们将开始向合作伙伴中心提供 Azure 虚拟机的移动管理。 迁移后，你将在合作伙伴中心创建和管理你的产品/服务。 按照[创建 Azure 虚拟机技术资产](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-create-offer)中的说明来管理迁移的产品/服务。

本文介绍了从通用化 VHD 创建非托管映像所需的两个常规步骤。  将提供参考资料来指导你完成每个步骤：捕获映像并通用化该映像。


## <a name="capture-the-vm-image"></a>捕获 VM 映像

遵照以下文章中的说明捕获对应于访问方法的 VM：

-  PowerShell：[如何从 Azure VM 创建非托管 VM 映像](../../../virtual-machines/windows/capture-image-resource.md)
-  Azure CLI：[如何创建虚拟机或 VHD 的映像](../../../virtual-machines/linux/capture-image.md)
-  API：[虚拟机 - 捕获](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)


## <a name="generalize-the-vm-image"></a>通用化 VM 映像

由于用户映像是从以前已通用化的 VHD 生成的，因此也应该通用化该映像。  同样，请选择对应于访问机制中的以下文章。  （捕获磁盘时可能已将其通用化。）

-  PowerShell：[通用化 VM](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Azure CLI：[步骤 2：创建 VM 映像](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API：[虚拟机 - 通用化](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="next-steps"></a>后续步骤

接下来，将[创建证书](cpp-create-key-vault-cert.md)并将其存储在新的 Azure Key Vault 中。  建立与 VM 的安全 WinRM 连接需要此证书。
