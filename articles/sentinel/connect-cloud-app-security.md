---
title: 将 Cloud App Security 数据连接到 Azure Sentinel |Microsoft Docs
description: 了解如何将 Cloud App Security 数据连接到 Azure Sentinel。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422145"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>连接 Microsoft Cloud App Security 的数据 



[Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) （MCAS）连接器使你能够将警报和[CLOUD DISCOVERY 日志](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it)从 MCAS 流式传输到 Azure Sentinel。 这使你能够深入了解你的云应用，获取复杂分析来识别和对付以来自2200，并控制数据的传播方式。

## <a name="prerequisites"></a>必备条件

- 你的用户必须具有工作区的读取和写入权限。
- 用户必须对工作区的租户具有全局管理员或安全管理员权限。
- 若要将 Cloud Discovery 日志流式传输到 Azure Sentinel，请[在 Microsoft Cloud App Security 中启用 Azure sentinel 作为你的 SIEM](https://aka.ms/AzureSentinelMCAS)。

> [!IMPORTANT]
> 引入 Cloud Discovery 日志当前为公共预览版。
> 此功能在提供时没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。
 
## <a name="connect-to-cloud-app-security"></a>连接到 Cloud App Security

如果已有 Cloud App Security，请确保已[在网络上启用](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security)它。
如果 Cloud App Security 已部署并引入数据，则可以轻松地将警报数据流式传输到 Azure Sentinel。


1. 在 Azure Sentinel 导航菜单中，选择 "**数据连接器**"。 在连接器列表中，单击 " **Microsoft Cloud App Security** " 磁贴，然后单击右下角的 "**打开连接器页**" 按钮。

1. 选择要流式传输到 Azure Sentinel 的日志;您可以选择**警报**并**Cloud Discovery 日志**（预览）。 

1. 单击“应用更改”****。

1. 若要使用 Log Analytics 中的相关架构 Cloud App Security 警报，请`SecurityAlert`在 "查询" 窗口中键入。 对于 Cloud Discovery 日志架构，请键入`McasShadowItReporting`。

> [!NOTE]
> Cloud Discovery 通过将数据基础用户连接聚合到云应用来帮助检测和确定趋势。
>
> 由于 Cloud Discovery 的数据是按天聚合的，因此请注意，最多24小时的最新数据将不会在 Azure Sentinel 中反映出来。 如果低级调查要求更直接的数据，则应直接在原始数据所在的源设备或服务中执行此操作。

## <a name="next-steps"></a>后续步骤
本文档介绍了如何将 Microsoft Cloud App Security 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：
- 了解如何[洞悉数据和潜在威胁](quickstart-get-visibility.md)。
- 使用[内置](tutorial-detect-threats.md)或[自定义](tutorial-detect-threats-custom.md)规则开始使用 Azure Sentinel 检测威胁。
