---
title: 使用 Azure 点 Vm 创建规模集
description: 了解如何创建使用点 Vm 节省成本的 Azure 虚拟机规模集。
author: cynthn
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.openlocfilehash: a7bd22032a554c83a2ea2323ffdb3ae52dfe4faf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80545937"
---
# <a name="azure-spot-vms-for-virtual-machine-scale-sets"></a>适用于虚拟机规模集的 Azure 点 Vm 

使用规模集上的 Azure 污点，可以显著节省成本。 当 Azure 需要恢复容量时，Azure 基础结构将逐出点实例。 因此，点实例非常适合用于处理中断的工作负荷，如批处理作业、开发/测试环境、大型计算工作负荷等。

可用容量可能因大小、区域、时间等而有所不同。 在规模集上部署点实例时，只有当容量可用时，Azure 才会分配实例，但对于这些实例没有 SLA。 一个位置规模集部署在单个容错域中，不提供高可用性保证。


## <a name="pricing"></a>定价

基于区域和 SKU，专色实例的定价是可变的。 有关详细信息，请参阅适用于[Linux](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)和[Windows](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/)的定价。 


使用可变定价，可以选择设置最大价格（美元），最多可使用5个小数位数。 例如，该值`0.98765`的最大价格为 $0.98765 美元/小时。 如果将最大价格设置为`-1`，则不会根据价格收回实例。 此实例的价格将是当前的价格价格或标准实例的价格，但只要容量和配额可用即可。

## <a name="eviction-policy"></a>逐出策略

创建污点规模集时，可以将逐出策略设置为 "*解除分配*" （默认）或 "*删除*"。 

*解除分配*策略会将逐出的实例移到停止解除分配的状态，使你可以重新部署逐出的实例。 但是，不保证分配将成功。 已解除分配的 VM 将计入规模集实例配额，基础磁盘仍会产生费用。 

如果你希望在退出时删除你的位置规模集中的实例，你可以将逐出策略设置为*删除*。 将逐出策略设置为删除后，可以通过增大规模集实例计数属性来创建新的 VM。 逐出的 VM 会连同其基础磁盘一起删除，因此可以避免存储费用。 还可以使用规模集的自动缩放功能来自动尝试补偿逐出的 VM，但是，无法保证分配成功。 建议你在将逐出策略设置为 "删除" 时，仅在 "点" 规模集上使用自动缩放功能，以避免磁盘开销和达到配额限制。 

用户可以选择通过[Azure Scheduled Events](../virtual-machines/linux/scheduled-events.md)接收 VM 内通知。 这会在你的 Vm 被逐出时通知你，你将有30秒的时间完成任何作业并在逐出之前执行关闭任务。 


## <a name="deploying-spot-vms-in-scale-sets"></a>在规模集中部署专色 Vm

若要在规模集上部署点 Vm，你可以将新的 "*优先级*" 标志设置为 "*发现*"。 规模集中的所有 Vm 都将设置为 "发现"。 若要使用专色 Vm 创建规模集，请使用以下方法之一：
- [Azure 门户](#portal)
- [Azure CLI](#azure-cli)
- [Azure PowerShell](#powershell)
- [Azure Resource Manager 模板](#resource-manager-templates)

## <a name="portal"></a>门户

创建使用专色 Vm 的规模集的过程与入门[文章](quick-create-portal.md)中详述的过程相同。 部署规模集时，可以选择设置点标志和逐出策略： ![使用污点 vm 创建规模集](media/virtual-machine-scale-sets-use-spot/vmss-spot-portal-max-price.png)


## <a name="azure-cli"></a>Azure CLI

使用专色 Vm 创建规模集的过程与[入门文章](quick-create-cli.md)中详述的过程相同。 只需添加 "--Priority 污点"，并添加`--max-price`。 在此示例中，我们`-1`将`--max-price`用于，因此不会根据价格收回实例。

```azurecli
az vmss create \
    --resource-group myResourceGroup \
    --name myScaleSet \
    --image UbuntuLTS \
    --upgrade-policy-mode automatic \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 
```

## <a name="powershell"></a>PowerShell

使用专色 Vm 创建规模集的过程与[入门文章](quick-create-powershell.md)中详述的过程相同。
只需添加 "-Priority 污点"，并`-max-price`向[AzVmssConfig](/powershell/module/az.compute/new-azvmssconfig)提供。

```powershell
$vmssConfig = New-AzVmssConfig `
    -Location "East US 2" `
    -SkuCapacity 2 `
    -SkuName "Standard_DS2" `
    -UpgradePolicyMode Automatic `
    -Priority "Spot" `
    --max-price -1
```

## <a name="resource-manager-templates"></a>资源管理器模板

创建使用点 Vm 的规模集的过程与适用于[Linux](quick-create-template-linux.md)或[Windows](quick-create-template-windows.md)的入门文章中详述的过程相同。 

对于部署点模板，请`"apiVersion": "2019-03-01"`使用或更高版本。 将`priority`、 `evictionPolicy`和`billingProfile`属性添加到模板`"virtualMachineProfile":`中的部分： 

```json
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
```

若要在逐出实例后将其删除，请将`evictionPolicy`参数更改`Delete`为。

## <a name="faq"></a>FAQ

**问：** 创建后，点实例是否与标准实例相同？

**答：** 是，但没有用于发现点 Vm 的 SLA，可以随时将其逐出。


**问：** 当你收回但仍需要容量时，该怎么办？

**答：** 如果需要立即使用容量，建议使用标准 Vm，而不是使用虚拟机。


**问：** 如何对配额进行位置管理？

**答：** 污点实例和标准实例将具有不同的配额池。 将在 Vm 与规模集实例之间共享点配额。 有关详细信息，请参阅 [Azure 订阅和服务限制、配额与约束](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits)。


**问：** 能否为查找附加的配额？

**答：** 是的，你将能够提交请求，以通过[标准配额请求过程](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests)增加对污点 vm 的配额。


**问：** 是否可以将现有规模集转换为污点规模集？

**答：** 不，仅在`Spot`创建时支持设置标志。


**问：** 如果使用`low`的是低优先级规模集，是否需要开始使用`Spot` ？

**答：** 目前，和`low` `Spot`都将起作用，但你应使用`Spot`开始转换为。


**问：** 是否可以同时创建具有常规 Vm 和虚拟机的规模集？

**答：** 不能，规模集不能支持多个优先级类型。


**问：** 能否对污点规模集使用自动缩放？

**答：** 是的，你可以在你的位置规模集上设置自动缩放规则。 如果 Vm 被逐出，自动缩放可尝试创建新的位置 Vm。 但记住，不保证会创建成功。 


**问：** 自动缩放是否适用于逐出策略（解除分配和删除）？

**答：** 建议在使用自动缩放时将逐出策略设置为删除。 这是因为解除分配的实例将计入规模集的容量计数。 如果使用自动缩放，由于实例已解除分配且已收回，实例很可能会快速达到目标数。 


**问：** 哪些通道支持污点 Vm？

**答：** 有关点 VM 可用性，请参阅下表。

<a name="channel"></a>

| Azure 通道               | Azure 点 Vm 可用性       |
|------------------------------|-----------------------------------|
| 企业协议         | 是                               |
| Pay As You Go                | 是                               |
| 云服务提供商 (CSP) | [联系你的合作伙伴](https://docs.microsoft.com/partner-center/azure-plan-get-started) |
| 优点                     | 不可用                     |
| 赞助                    | 不可用                     |
| 免费试用版                   | 不可用                     |


**问：** 可以在何处发布问题？

**答：** 您可以使用`azure-spot` [Q&](https://docs.microsoft.com/answers/topics/azure-spot.html)来发布和标记问题。 

## <a name="next-steps"></a>后续步骤

有关价格详细信息，请查看[虚拟机规模集定价页](https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/)。
