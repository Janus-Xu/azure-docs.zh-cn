---
title: 在管理单元中添加、删除和列出用户（预览）-Azure Active Directory |Microsoft Docs
description: 在 Azure Active Directory 的管理单元中管理用户及其角色权限
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c2c5c083115440e1e4da203f39f2b32734458c3
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81684970"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>在 Azure Active Directory 中的管理单元中添加和管理用户

在 Azure Active Directory （Azure AD）中，你可以将用户添加到管理单元（AU）以获得更精细控制的管理作用域。

有关准备使用 PowerShell 和 Microsoft Graph 管理单元管理的步骤，请参阅[开始](roles-admin-units-manage.md#get-started)使用。

## <a name="add-users-to-an-au"></a>将用户添加到 AU

### <a name="azure-portal"></a>Azure 门户

可以通过两种方式将用户分配到管理单元。

1. 单个分配

    1. 可以在门户中转到 Azure AD，选择 "用户"，然后选择要分配到管理单元的用户。 然后，可以在左侧面板中选择 "管理单元"。 可以通过单击 "分配到管理单元" 并选择要在其中分配用户的管理单元，将用户分配到一个或多个管理单元。

       ![选择 "添加"，然后输入管理单元的名称](./media/roles-admin-units-add-manage-users/assign-users-individually.png)

    1. 可以在门户中转到 Azure AD，并在左窗格中选择 "管理单元"，然后选择要在其中分配用户的管理单元。 在左侧窗格中选择 "所有用户"，然后选择 "添加成员"。 然后，可以从右窗格中选择一个或多个要分配到管理单元的用户。

        ![选择管理单元，然后选择 "添加成员"](./media/roles-admin-units-add-manage-users/assign-to-admin-unit.png)

1. 批量分配

    在门户中转到 Azure AD，并选择 "管理单元"。 选择要在其中添加用户的管理单元。 单击 "所有用户"-> "从 .csv 文件添加成员"。 然后，你可以下载 CSV 模板并编辑该文件。 格式很简单，需要在每行中添加单个 UPN。 文件准备就绪后，将其保存在适当的位置，然后在步骤3中将其上传到快照中突出显示的位置。

    ![将用户批量分配到管理单元](./media/roles-admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

    $administrativeunitObj = Get-AzureADAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
    $UserObj = Get-AzureADUser -Filter "UserPrincipalName eq 'billjohn@fabidentity.onmicrosoft.com'"
    Add-AzureADAdministrativeUnitMember -ObjectId $administrativeunitObj.ObjectId -RefObjectId $UserObj.ObjectId

在上面的示例中，使用 cmdlet Add-azureadadministrativeunitmember 将用户添加到管理单元。 要添加用户的管理单元的对象 ID 和要添加的用户的对象 ID 作为参数。 突出显示部分可能根据特定环境的需要进行更改。

### <a name="microsoft-graph"></a>Microsoft Graph

    Http request
    POST /administrativeUnits/{Admin Unit id}/members/$ref
    Request body
    {
      "@odata.id":"https://graph.microsoft.com/beta/users/{id}"
    }

例如：

    {
      "@odata.id":"https://graph.microsoft.com/beta/users/johndoe@fabidentity.com"
    }

## <a name="list-administrative-units-for-a-user"></a>列出用户的管理单元

### <a name="azure-portal"></a>Azure 门户

在 Azure 门户可以通过转到 Azure AD > 用户打开用户的配置文件。 单击用户打开用户的配置文件。

![在 Azure Active Directory 中打开用户的配置文件](./media/roles-admin-units-add-manage-users/user-profile-admin-units.png)

选择左侧面板上的 "**管理单元**"，以查看已分配用户的管理单元的列表。

![列出用户的管理单元](./media/roles-admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

    Get-AzureADAdministrativeUnit | where { Get-AzureADAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $userObjId} }

### <a name="microsoft-graph"></a>Microsoft Graph

    https://graph.microsoft.com/beta/users//memberOf/$/Microsoft.Graph.AdministrativeUnit

## <a name="remove-a-single-user-from-an-au"></a>从 AU 删除单个用户

### <a name="azure-portal"></a>Azure 门户

可以通过两种方式从管理单元中删除用户。 在 Azure 门户可以通过转到**Azure AD** > **用户**来打开用户的配置文件。 选择用户以打开用户的配置文件。 选择要从中删除用户的管理单元，然后选择 "**从管理单元中删除**"。

![从用户配置文件中的管理单元中删除用户](./media/roles-admin-units-add-manage-users/user-remove-admin-units.png)

还可以通过选择要从中删除用户的管理单元，在**Azure AD** > **管理单元**中删除用户。 选择用户，然后选择 "**删除成员**"。
  
![删除管理单元级别的用户](./media/roles-admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

    Remove-AzureADAdministrativeUnitMember -ObjectId $auId -MemberId $memberUserObjId

### <a name="microsoft-graph"></a>Microsoft Graph

   https://graph.microsoft.com/beta/administrativeUnits/<adminunit-id>/members/<user-id>/$ref

## <a name="bulk-remove-more-than-one-user"></a>大容量删除多个用户

你可以前往 Azure AD > 管理单元，并选择要从中删除用户的管理单元。 单击 "批量删除成员"。 下载 CSV 模板，提供要删除的用户的列表。

用相关用户条目编辑下载的 CSV 模板。 请勿删除模板的前两行。 在每行中添加一个用户 UPN。

![编辑 CSV 文件以从管理单元批量删除用户](./media/roles-admin-units-add-manage-users/bulk-user-entries.png)

将条目保存到文件中后，请上传文件，然后选择 "**提交**"。

![提交大容量上传文件](./media/roles-admin-units-add-manage-users/bulk-user-remove.png)

## <a name="next-steps"></a>后续步骤

- [向管理单元分配角色](roles-admin-units-assign-roles.md)
- [向管理单元添加组](roles-admin-units-add-manage-groups.md)
