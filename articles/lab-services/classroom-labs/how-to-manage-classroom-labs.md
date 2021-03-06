---
title: 管理 Azure 实验室服务中的教室实验室 | Microsoft Docs
description: 了解如何创建和配置教室实验室、如何查看所有教室实验室、如何与实验室用户共享注册链接，或者如何删除实验室。
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/10/2020
ms.author: spelluru
ms.openlocfilehash: 46c53c99c12ade986ab913bf013b652a931a4d22
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81257736"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>管理 Azure 实验室服务中的教室实验室 
本文介绍如何创建和删除教室实验室。 它还说明如何查看实验室帐户中的所有教室实验室。 

## <a name="prerequisites"></a>必备条件
若要在实验室帐户中设置课堂实验室，你必须是实验室帐户中“实验室创建者”**** 角色的成员。 用来创建实验室帐户的帐户会自动添加到此角色。 实验室所有者可以根据[将用户添加为“实验室创建者”角色](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role)一文中的步骤将其他用户添加为“实验室创建者”角色。

## <a name="create-a-classroom-lab"></a>创建课堂实验室

1. 导航到 [Azure 实验室服务网站](https://labs.azure.com)。 目前尚不支持 Internet Explorer 11。 
2. 选择“登录”**** 并输入凭据。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码********。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 选择“新建实验室”。**** 
    
    ![创建课堂实验室](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. 在“新建实验室”窗口中，执行以下操作：****： 
    1. 指定实验室的**名称**。 
    2. 选择类所需的**虚拟机大小**。 有关可用大小列表，请参阅[VM 大小](#vm-sizes)部分。 
    3. 选择要用于教室实验室的**虚拟机映像**。 如果选择 Linux 映像，可以看到一个用来为该映像启用远程桌面连接的选项。 有关详细信息，请参阅[启用适用于 Linux 的远程桌面连接](how-to-enable-remote-desktop-linux.md)。

        如果使用实验室帐户所有者凭据登录，则会看到一个选项，可为实验室启用更多映像。 有关详细信息，请参阅[在创建实验室时启用映像](specify-marketplace-images.md#enable-images-at-the-time-of-lab-creation)。
    4. 查看页面上显示的**每小时总价格**。 
    6. 选择“保存”  。

        ![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        > [!NOTE]
        > 如果实验室帐户已配置为[允许 lab creator 选取实验室 location](allow-lab-creator-pick-lab-location.md)选项，则会显示一个用于选择实验室位置的选项。 
4. 在“虚拟机凭据”页上，指定实验室中所有 VM 的默认凭据。****
    1. 指定实验室中所有 VM 的**用户名**。
    2. 指定用户的**密码**。 

        > [!IMPORTANT]
        > 记下用户名和密码。 这些信息不会再次显示。
    3. 如果你希望学生设置自己的密码，则禁用 "**为所有虚拟机使用相同的密码**" 选项。 此步骤是**可选**的。 

        教师可以选择对实验室中的所有 Vm 使用相同的密码，或允许学生为其 Vm 设置密码。 默认情况下，所有 Windows 和 Linux 映像（Ubuntu 除外）都启用此设置。 选择**Ubuntu** VM 时，此设置会被禁用，因此在首次登录时，会提示学生设置密码。  

        ![“新建实验室”窗口](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
    4. 然后，在 "**虚拟机凭据**" 页上选择 "**下一步**"。 
5. 在 "**实验室策略**" 页上，执行以下步骤：
    1. 输入在实验室的计划时间之外为每个用户分配的小时数（**每个用户的配额**）。 
    2. 对于 "**自动关闭虚拟机**" 选项，请指定是否想要在用户断开连接时自动关闭 VM。 你还可以指定 VM 在自动关机之前应等待用户重新连接的时间。 有关详细信息，请参阅[在断开连接时启用 vm 自动关闭](how-to-enable-shutdown-disconnect.md)。
    3. 然后选择 "**完成**"。 

        ![每个用户的配额](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
    
5. 此时会看到以下屏幕，显示模板 VM 的创建状态。 在实验室中创建模板最长需要 20 分钟时间。 

    ![模板 VM 的创建状态](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. 在 "**模板**" 页上，执行以下步骤：对于本教程，这些步骤是**可选**的。

    2. 选择“连接”以连接到模板 VM。**** 如果它是 Linux 模板 VM，请选择是要使用 SSH 还是 RDP（如果已启用 RDP）来建立连接。
    1. 选择“重置密码”**** 以重置 VM 的密码。 
    1. 在模板 VM 上安装并配置软件。 
    1. **停止**VM。  
    1. 输入模板的**说明**
10. 在“模板”页上的工具栏中选择“发布”****。**** 

    ![“发布模板”按钮](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > 发布后无法取消发布。 
8. 在“发布模板”**** 页上，输入要在实验室中创建的虚拟机的数目，然后选择“发布”。**** 

    ![发布模板 - VM 数](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. 可以在页面上查看模板的**发布状态**。 此过程最长可能需要花费一小时。 

    ![发布模板 - 进度](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. 选择左侧菜单上的“虚拟机”或选择“虚拟机”磁贴，切换到“虚拟机池”页。**** **** 确认看到这些虚拟机处于“未分配”状态。 这些 VM 尚未分配给学生。 它们应该处于“已停止”状态。**** 可以在此页上启动学生 VM，连接到该 VM，停止该 VM，然后删除该 VM。 你可以在此页中启动这些 VM，也可以让你的学生启动它们。 

    ![处于已停止状态的虚拟机](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    在此页面上执行以下任务（请勿执行本教程的这些步骤。 这些步骤仅供参考。）： 
    
    1. 若要更改实验室容量（实验室中的 VM 数），请在工具栏上选择“实验室容量”。****
    2. 若要一次启动所有 VM，请在工具栏上选择“全部启动”。**** 
    3. 若要启动特定 VM，请在“状态”**** 中选择下拉箭头****，然后选择“启动”。 也可先在第一列中选择一个 VM，然后在工具栏上选择“启动”，以这种方式启动 VM。****                

### <a name="vm-sizes"></a>VM 大小  

| 大小 | 核心数 | RAM | 说明 | 
| ---- | ----- | --- | ----------- | 
| 小型 | 2 | 3.5 GB | 此大小最适用于命令行、打开 web 浏览器、低流量 web 服务器、小到中型数据库。 |
| 中 | 4 | 7 GB | 此大小最适合用于关系数据库、内存中缓存和分析 | 
| 中（嵌套虚拟化） | 4 | 16 GB | 此大小最适合用于关系数据库、内存中缓存和分析。 此大小还支持嵌套虚拟化。 <p>如果每个学生都需要多个 Vm，则可以使用此大小。 教师可以使用嵌套虚拟化在虚拟机中设置几个小大小的嵌套虚拟机。 </p> |
| 大型 | 8 | 32 GB | 此大小最适用于需要更快的 Cpu、更好的本地磁盘性能、大型数据库、大内存缓存的应用程序。 此大小还支持嵌套虚拟化 |  
| 小型 GPU （可视化效果） | 6 | 56 GB | 此大小最适合用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏、编码。 | 
| 小型 GPU （计算） | 6 | 56 GB | 此大小最适用于计算密集型和网络密集型应用程序，如人工智能和深度学习应用程序。 | 
| 中等 GPU （可视化效果） | 12 | 112 GB | 此大小最适合用于使用框架（如 OpenGL 和 DirectX）进行远程可视化、流式处理、游戏、编码。 | 

> [!NOTE]
> 使用 GPU 映像创建实验室时，Azure 实验室服务会自动安装和配置所需的 GPU 驱动程序。  

## <a name="view-all-classroom-labs"></a>查看所有教室实验室
1. 导航到 [Azure 实验室服务门户](https://labs.azure.com)。
2. 选择“登录”  。 选择或输入属于实验室帐户中“实验室创建者”角色成员的“用户 ID”，然后输入密码********。 Azure 实验室服务支持组织帐户和 Microsoft 帐户。 
3. 确认在所选实验室帐户中看到所有实验室。 在实验室的磁贴上，你会看到实验室中的虚拟机数和每个用户的配额（在计划时间之外）。

    ![所有实验室](../media/how-to-manage-classroom-labs/all-labs.png)
3. 使用顶部的下拉列表选择另一实验室帐户。 此时会在所选实验室帐户中看到实验室。 

## <a name="delete-a-classroom-lab"></a>删除教室实验室
1. 在实验室的磁贴上，选择角中的三个点（...），然后选择 "**删除**"。 

    ![“删除”按钮](../media/how-to-manage-classroom-labs/delete-button.png)
3. 在 "**删除实验室**" 对话框中，选择 "**删除**" 以继续删除操作。 

## <a name="switch-to-another-classroom-lab"></a>切换到另一教室实验室
若要从当前教室实验室切换到另一教室实验室，请在顶部的实验室帐户中选择实验室下拉列表。

![从顶部的下拉列表中选择实验室](../media/how-to-manage-classroom-labs/switch-lab.png)

你还可以使用此下拉列表中的**新实验室**创建新的实验室。 

> [!NOTE]
> 还可以使用 LabServices PowerShell 模块（预览版）管理实验室。 有关详细信息，请参阅[GitHub 上的 LabServices 主页](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)。

若要切换到其他实验室帐户，请选择实验室帐户旁边的下拉箭头，然后选择其他实验室帐户。 

## <a name="next-steps"></a>后续步骤
请参阅以下文章：

- [以实验室所有者身份设置并发布模板](how-to-create-manage-template.md)
- [以实验室所有者身份配置并控制实验室的使用](how-to-configure-student-usage.md)
- [以实验室用户身份访问教室实验室](how-to-use-classroom-lab.md)

