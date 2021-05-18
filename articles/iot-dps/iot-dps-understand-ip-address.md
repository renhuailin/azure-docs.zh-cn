---
title: 了解 IoT 设备预配服务 (DPS) 实例的 IP 地址 |Microsoft Docs
description: 了解如何查询 IoT 设备预配服务 (DPS) 地址及其属性。 在某些情况下（如灾难恢复或区域故障转移）可以更改 DPS 实例的 IP 地址。
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: f6afd5c4cc5aa0215f943979ae91389b39d449f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "79284923"
---
# <a name="iot-hub-dps-ip-addresses"></a>IoT 中心 DPS IP 地址

IoT 中心设备预配服务 (DPS) 公共终结点的 IP 地址前缀会定期发布在 AzureIoTHub [服务标记](../virtual-network/service-tags-overview.md)下。 你可以使用这些 IP 地址前缀来控制 IoT DPS 实例与设备或网络资产之间的连接，以实现各种网络隔离目标：

| 目标 | 方法 |
|------|----------|
| 确保设备和服务仅与 IoT 中心 DPS 终结点通信 | 使用 AzureIoTHub 服务标记发现 IoT 中心 DPS 实例。 在设备和服务的防火墙设置中为这些 IP 地址前缀相应地配置 ALLOW 规则。 配置规则以将流量丢弃到不希望设备或服务与之通信的其他目标 IP 地址。 |
| 确保 IoT 中心 DPS 终结点仅接收来自你的设备和网络资产的连接 | 使用 IoT DPS [IP 筛选器功能](iot-dps-ip-filtering.md)为设备和 DPS 服务 API 创建筛选规则。 这些筛选规则仅用于允许来自设备和网络资产 IP 地址的连接（请参阅[限制](#limitations-and-workarounds)部分）。 | 




## <a name="best-practices"></a>最佳做法

* 在设备的防火墙配置中添加“允许”规则时，最好是提供[适用协议使用的特定端口](../iot-hub/iot-hub-devguide-protocols.md#port-numbers)。

* IoT DPS 实例的 IP 地址前缀可能会更改。 这些更改在生效之前通过服务标记定期发布。 因此，必须制定流程来定期检索并使用最新的服务标记。 可以通过[服务标记发现 API](../virtual-network/service-tags-overview.md#service-tags-on-premises) 自动执行此进程。 服务标记发现 API 仍处于预览状态，在某些情况下，可能不会生成标记和 IP 地址的完整列表。 在发现 API 推出正式版之前，请考虑使用[可下载 JSON 格式的服务标记](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files)。 

* 使用 AzureIoTHub.[region name] 标记来标识特定区域内 DPS 终结点使用的 IP 前缀。 考虑到数据中心灾难恢复或[区域故障转移](../iot-hub/iot-hub-ha-dr.md)，请确保还启用了到 DPS 实例异地对区域的 IP 前缀的连接。

* 为 DPS 实例设置防火墙规则可能会阻止针对其运行 Azure CLI 和 PowerShell 命令所需的连接。 为避免出现这些连接性情况，可以为客户端的 IP 地址前缀添加 ALLOW 规则，以重新允许 CLI 或 PowerShell 客户端与 DPS 实例通信。  


## <a name="limitations-and-workarounds"></a>限制和解决方法

* DPS IP 筛选器功能的规则限制为 100 个。 可以请求 Azure 客户支持人员提高此限制。 

* 配置的 [IP 筛选规则](iot-dps-ip-filtering.md)仅适用于 DPS 终结点，而不适用于链接的 IoT 中心终结点。 必须单独配置用于链接的 IoT 中心的 IP 筛选。 有关详细信息，请参阅 [IoT 中心 IP 筛选规则](../iot-hub/iot-hub-ip-filtering.md)。

## <a name="support-for-ipv6"></a>支持 IPv6 

IoT 中心或 DPS 目前不支持 IPv6。

## <a name="next-steps"></a>后续步骤

若要详细了解 DPS 的 IP 地址配置，请参阅：

* [配置 IP 筛选](iot-dps-ip-filtering.md)
