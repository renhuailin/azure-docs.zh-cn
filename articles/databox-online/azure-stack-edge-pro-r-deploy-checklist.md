---
title: 部署 Azure Stack Edge Pro R 设备的预部署清单 | Microsoft Docs
description: 本文介绍了可在部署 Azure Stack Edge Pro R 设备之前收集的信息。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 02/24/2021
ms.author: alkohli
ms.openlocfilehash: eca26934605ff70ecb26c10604fc9b493e88f2cf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "101727438"
---
# <a name="deployment-checklist-for-your-azure-stack-edge-pro-r-device"></a>Azure Stack Edge Pro R 设备的部署清单  

本文介绍了可在 Azure Stack Edge Pro R 设备的实际部署之前收集的信息。 

使用以下清单以确保在订购 Azure Stack Edge Pro R 设备之后和收到设备之前具有此信息。 

## <a name="deployment-checklist"></a>部署清单 

| 阶段                             | 参数                                                                                                                                                                                                                           | 详细信息                                                                                                           |
|-----------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------|
| 设备管理               | <li>Azure 订阅</li><li>已注册的资源提供程序</li><li>Azure 存储帐户</li>|<li>已为 Azure Stack Edge Pro/Data Box Gateway、所有者或参与者访问启用。</li><li>在 Azure 门户中，转到“主页”>“订阅”>“你的订阅”>“资源提供程序”。 搜索 `Microsoft.DataBoxEdge` 并注册。 如果部署 IoT 工作负荷，请为 `Microsoft.Devices` 重复此步骤。</li><li>需要访问凭据</li> |
| 设备安装               | 包中的电源线。 <br>针对美国，会发送一条 SVE 18/3 电缆，其额定电压和额定电流分别为 125 V 和 15 A，具有一个 NEMA 5-15P 转 C13（输入到输出）连接器。 | 有关详细信息，请参阅[按国家/地区划分的支持的电源线](azure-stack-edge-technical-specifications-power-cords-regional.md)列表  |
|                                   | <li>端口 1 至少 1 条 1-GbE RJ-45 网络电缆  </li><li> 端口 3、端口 4 至少 1 条 25-GbE SFP+ 铜缆</li>| 客户需要购买这些电缆。<br>有关设备网卡支持的网络电缆、交换机和收发器的完整列表，请参阅 [Cavium FastlinQ 41000 系列互操作性矩阵](https://www.marvell.com/documents/xalflardzafh32cfvi0z/)和 [Mellanox 双端口 25G ConnectX-4 通道网络适配器兼容产品](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products)。| 
| 首次设备连接      | <li>可以更改 IPv4 设置的便携式计算机。 此便携式计算机通过交换机或 USB 转以太网适配器连接到端口 1。  </li><!--<li> A minimum of 1 GbE switch must be used for the device once the initial setup is complete. The local web UI will not be accessible if the connected switch is not at least 1 Gbe.</li>-->|   |
| 设备登录                      | 设备管理员密码，长度在 8 到 16 个字符之间，包括以下字符类型中的三种：大写、小写、数字和特殊字符。                                            | 默认密码为“Password1”，它将在首次登录时过期。                                                     |
| 网络设置                  | 设备附带 2 个 1-GbE、4 个 25-GbE 网络端口。 <li>端口 1 仅用于配置管理设置。 可以连接和配置一个或多个数据端口。 </li><li> 至少需要将来自端口 2 到端口 6 的一个数据网络接口连接到 Internet（在连接到 Azure 的情况下）。</li><li> 支持 DHCP 和静态 IPv4 配置。 | 静态 IPv4 配置需要 IP、DNS 服务器和默认网关。   |
| 计算网络设置     | <li>Kubernetes 节点需要 2 个空闲的连续静态 IP，而 IoT Edge 服务需要 1 个静态 IP。</li><li>针对将部署的每个额外服务或模块，需要一个额外的 IP。</li>| 仅支持静态 IPv4 配置。|
| （可选）Web 代理设置     | <li>Web 代理服务器 IP/FQDN、端口 </li><li>Web 代理用户名、密码</li> |  |
| 防火墙和端口设置        | 如果使用防火墙，请确保设备 IP 允许[列出的 URL 模式和端口](azure-stack-edge-system-requirements.md#networking-port-requirements)。 |  |
| （建议）时间设置       | 配置时区、主 NTP 服务器、辅助 NTP 服务器。 | 在本地网络上配置主 NTP 服务器和辅助 NTP 服务器。<br>如果本地服务器不可用，则可配置公共 NTP 服务器。                                                    |
| （可选）更新服务器设置 | <li>需要本地网络上的更新服务器 IP 地址、到 WSUS 服务器的路径。 </li> | 默认情况下，使用公共 windows 更新服务器。|
| 设备设置 | <li>设备完全限定域名 (FQDN) </li><li>DNS 域</li> | |
| （可选）证书  | 如果使用自己的证书，包括签名链，则以适当的格式[添加证书](azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md#bring-your-own-certificates)。| 仅当更改设备名称和/或 DNS 域时才配置证书。 |
| VPN  | <!--Need VPN certificate, VPN gateway, firewall setup in Azure,  passphrase and region info VPN scripts. -->   | |
| 静态加密  | 建议使用自动生成的加密密钥。   |如果使用自己的密钥，请需要 32 个字符长度的 Base-64 编码的密钥。  |
| 激活  | 需要 Azure Stack Edge Pro/Data Box Gateway 资源中的激活密钥。    | 密钥生成后，将在 3 天后过期。 |

<!--
| (Optional) MAC Address            | If MAC address needs to be approved, get the address of the connected port from local UI of the device. |                                                                                                                   |
| (Optional) Network switch port    | Device hosts Hyper-V VMs for compute. Some network switch port configurations don’t accommodate these setups by default.                                                                                                        |                                                                                                                   |-->


## <a name="next-steps"></a>后续步骤

准备部署 [Azure Stack Edge Pro 设备](azure-stack-edge-pro-r-deploy-prep.md)。
