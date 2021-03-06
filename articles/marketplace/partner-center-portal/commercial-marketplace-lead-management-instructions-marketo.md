---
title: 在 Marketo 中配置潜在客户管理 |Azure Marketplace
description: 为 Azure Marketplace 客户配置 Marketo 的潜在客户管理。
author: qianw211
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: b142e0ab1aaa242157e207ceecf958be51bb1721
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82133655"
---
# <a name="configure-lead-management-in-marketo"></a>在 Marketo 中配置潜在顾客管理

本文介绍如何设置 Marketo CRM 系统，以处理来自商业 marketplace 产品/服务的销售潜在顾客。

## <a name="set-up-your-marketo-crm-system"></a>设置 Marketo CRM 系统

1. 登录到 Marketo。

1. 选择“Design Studio”。****

    ![Marketo Design Studio](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-1.png)

1.  选择“新建窗体”。****

    ![Marketo New 窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-2.png)

1.  在 "**新建窗体**" 对话框中填写必填字段，然后选择 "**创建**"。

    ![Marketo 创建新窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-3.png)

1.  在 "**字段详细信息**" 页上，选择 "**完成**"。

    ![Marketo 完成窗体](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-4.png)

1.  批准并关闭。

1. 在 " **MarketplaceLeadBackend** " 选项卡上，选择 "**嵌入代码**"。 

    ![Marketo 嵌入代码](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-6.png)

1. Marketo 的“嵌入代码”将显示类似于以下示例的代码。

    ```html
    <form id="mktoForm_1179"></form>
    <script>MktoForms2.loadForm("("//app-ys12.marketo.com", "123-PQR-789", 1179);</script>
    ```

1. 复制嵌入代码窗体中显示的以下字段的值。 在下一步中，你将使用这些值来配置你的产品/服务。 使用下一个示例作为指南来获取 Marketo 嵌入代码示例所需的 Id。

    - 服务器 ID = **ys12**
    - Munchkin ID = **123-PQR-789**
    - 表单 ID = **1179**

    计算这些值的另一种方法是：

    - 在 Marketo 实例的 URL 中找到服务器 ID，例如`serverID.marketo.com`。
    - 转到 " **Munchkin 帐户 id** " 字段中的 " `https://{Munchkin ID}.mktorest.com`**管理** > **Munchkin** " 菜单，或从 Marketo REST API 主机子域的第一部分获取订阅的 Munchkin ID：。
    - 表单 ID 是在步骤7中创建的嵌入代码窗体的 ID，用于从 marketplace 路由潜在顾客。

## <a name="configure-your-offer-to-send-leads-to-marketo"></a>配置你的产品/服务以将潜在顾客发送到 Marketo

准备好在发布门户中为产品/服务配置潜在顾客管理信息时，请执行以下步骤。 

1. 请参阅产品/服务的**产品/服务设置**页。

1. 选择 "**潜在顾客管理**" 部分下的 "**连接**"。 

    ![潜在客户管理部分连接按钮](./media/commercial-marketplace-lead-management-instructions-marketo/lead-management-connect.png)

1. 在 "**连接详细信息**" 弹出窗口中，选择 " **Marketo** " 作为 "**潜在顾客" 目标**。

    ![选择潜在顾客目标](./media/commercial-marketplace-lead-management-instructions-marketo/choose-lead-destination.png)

1. 提供**服务器 id**、 **Munchkin 帐户 ID**和**表单 id**。

    > [!NOTE]
    > 您必须先完成该产品/服务的其余部分的配置，然后发布该产品/服务，才能接收该产品/服务的潜在客户。 

1. 在 "**联系人电子邮件**" 下，输入公司中应在收到新潜在客户时接收电子邮件通知的人员的电子邮件地址。 可以提供多个电子邮件地址，用分号分隔。

1. 选择“确定”  。

   若要确保已成功连接到潜在客户目标，请选择 "**验证**" 按钮。 如果成功，则在潜在客户目标中会出现测试主管。

   !["连接详细信息" 弹出窗口](./media/commercial-marketplace-lead-management-instructions-marketo/marketo-connection-details.png)
