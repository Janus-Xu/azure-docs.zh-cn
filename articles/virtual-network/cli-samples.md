---
title: 适用于虚拟网络的 Azure CLI 示例
description: 适用于虚拟网络的 Azure CLI 示例。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 5ce10cf37b61b0269e6c8f2279b8814d9dc4a4f9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "78271202"
---
# <a name="azure-cli-samples-for-virtual-network"></a>适用于虚拟网络的 Azure CLI 示例

下表包含指向使用 Azure CLI 命令的 bash 脚本的链接：

| | |
|----|----|
| [为多层应用程序创建虚拟网络](./scripts/virtual-network-cli-sample-multi-tier-application.md) | 创建包含前端和后端子网的虚拟网络。 传入前端子网的流量仅限 HTTP 和 SSH，而传入后端子网的流量限于 MySQL、端口 3306。 |
| [两个对等虚拟网络](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | 在同一区域中创建并连接两个虚拟网络。 |
| [通过网络虚拟设备的路由流量](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | 创建包含前端和后端子网的虚拟网络，以及可在这两个子网之间路由流量的 VM。 |
| [筛选入站和出站 VM 网络流量](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | 创建包含前端和后端子网的虚拟网络。 前端子网的入站网络流量仅限于 HTTP、HTTPS 和 SSH。 不允许从后端子网到 Internet 的出站流量。 |
|[使用基本负载均衡器配置 IPv4 + IPv6 双堆栈虚拟网络](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|部署具有两个 VM 的双栈 (IPv4+IPv6) 虚拟网络和具有 IPv4 和 IPv6 公共 IP 地址的 Azure 基本负载均衡器。 |
|[使用标准负载均衡器配置 IPv4 + IPv6 双堆栈虚拟网络](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|部署具有两个 VM 的双堆栈 (IPv4+IPv6) 虚拟网络和具有 IPv4 和 IPv6 公共 IP 地址的 Azure 标准负载均衡器。 |
|[教程：创建并测试 NAT 网关 - Azure CLI](../virtual-network/tutorial-create-validate-nat-gateway-cli.md)|使用源和目标虚拟机创建并验证 NAT 网关。 |