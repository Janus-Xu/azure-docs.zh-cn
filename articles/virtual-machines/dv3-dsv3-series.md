---
title: Dv3 和 Dsv3 系列 - Azure 虚拟机
description: Dv3 和 Dsv3 系列 VM 的规范。
services: virtual-machines
author: joelpelley
ms.service: virtual-machines
ms.topic: article
ms.date: 02/03/2020
ms.author: lahugh
ms.openlocfilehash: 9ea5811fa20ea4866655de74d79ff3905ba03f16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "78164400"
---
# <a name="dv3-and-dsv3-series"></a>Dv3 和 Dsv3 系列

Dv3 系列在超线程配置中在 Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，针对最常规用途的工作负荷提供了更好的价值主张。 在磁盘和网络限制已基于核心进行了调整以适应超线程技术的同时，内存已扩展（从 ~3.5 GiB/vCPU 到 4 GiB/vCPU）。 Dv3 系列不再具有 D/Dv2 系列的高内存 VM 大小，那些已移至内存优化的 [Ev3 和 Esv3 系列](ev3-esv3-series.md)。

示例 D 系列用例包括企业级应用程序、关系数据库、内存中缓存和分析。

## <a name="dv3-series"></a>Dv3 系列

Dv3 系列大小在 Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，采用 Intel Turbo Boost Technology 2.0。 Dv3 系列大小为大多数生产工作负荷提供 vCPU、内存和临时存储组合。

数据磁盘存储与虚拟机分开计费。 若要使用高级存储磁盘，请使用 Dsv3 大小。 Dsv3 大小的定价和计费标准与 Dv3 系列相同。

Dv3 系列 VM 采用 Intel® 超线程技术。

ACU：160-190

高级存储：不支持

高级存储缓存：不支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大临时存储吞吐量：IOPS/读取 MBps/写入 MBps | 最大 NIC 数/网络带宽 |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96000/1000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96000/1000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3 系列

Dsv3 系列大小在 Intel® Xeon® 8171M 2.1GHz (Skylake)、Intel® Xeon® E5-2673 v4 2.3 GHz (Broadwell) 或 Intel® Xeon® E5-2673 v3 2.4 GHz (Haswell) 处理器上运行，采用 Intel Turbo Boost Technology 2.0 并使用高级存储。 Dsv3 系列大小为大多数生产工作负荷提供 vCPU、内存和临时存储组合。

Dsv3 系列 VM 采用 Intel® 超线程技术。

ACU：160-190

高级存储：支持

高级存储缓存：支持

实时迁移：支持

内存保留更新：支持

| 大小 | vCPU | 内存:GiB | 临时存储 (SSD) GiB | 最大数据磁盘数 | 最大缓存吞吐量和临时存储吞吐量：IOPS/MBps（以 GiB 为单位的缓存大小） | 最大非缓存磁盘吞吐量：IOPS/MBps | 最大 NIC 数/预期网络带宽 (Mbps) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 3200/48    | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 6400/96    | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 12800/192  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 25600/384  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 51200/768  | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 76800/1152 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 80000/1200 | 8/30000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>其他大小

- [常规用途](sizes-general.md)
- [内存优化](sizes-memory.md)
- [存储优化](sizes-storage.md)
- [GPU 优化](sizes-gpu.md)
- [高性能计算](sizes-hpc.md)
- [前几代](sizes-previous-gen.md)

## <a name="next-steps"></a>后续步骤

了解有关 [Azure 计算单元 (ACU)](acu.md) 如何帮助跨 Azure SKU 比较计算性能的详细信息。
