---
title: Azure Defender for IoT 部署前清单
description: 本文提供信息以及准备站点时应在部署之前使用的清单。
ms.date: 07/18/2021
ms.topic: checklist
ms.openlocfilehash: ac60e574a3d61bfa0c3106375d0606b7de6d9494
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676928"
---
# <a name="pre-deployment-checklist-overview"></a>部署前清单概述

本文提供信息以及准备站点时应在部署之前使用的清单，用以确保成功加入。

- Defender for IoT 物理传感器应连接到可看到第 1 层与第 2 层（在某些情况下还包括第 3 层）之间的工业通信的受管理交换机。
- 传感器侦听交换机镜像端口（SPAN 端口）或 TAP。
- 管理端口通过 SSL 连接到企业/公司网络。

## <a name="checklist"></a>清单

工业网络示意图便于网站工程师为 Defender for IoT 设备定义正确的位置。

### <a name="1-global-network-diagram"></a>1、全局网络示意图

全局网络示意图提供工业 OT 环境图示

:::image type="content" source="media/resources-sensor-deployment-checklist/purdue-model.png" alt-text="这是在安装期间 Azure Defender for IoT 传感器的适合位置。":::

:::image type="content" source="media/resources-sensor-deployment-checklist/backbone-switch.png" alt-text="这是在 purdue 模型中 Azure Defender for IoT 传感器的适合位置。":::

> [!Note] 
> Defender for IoT 设备应连接到较低级别的交换机，该交换机可以看到其端口之间的流量。 

### <a name="2-committed-devices"></a>2、已提交设备

提供要监视的网络设备的大致数量。 将订阅加入 Azure Defender for IoT 门户时需要此信息。 在加入过程中，系统会提示你输入设备数量，以 1000 为增量。

### <a name="3-optional-subnet-list"></a>3、（可选）子网列表 

提供生产网络的子网列表。

| **#** | **子网名称** | **说明** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="4-vlans"></a>4、VLAN

提供生产网络的 VLAN 列表。

| **#** | **VLAN 名称** | **说明** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="5-switch-models-and-mirroring-support"></a>5、交换机型号和镜像支持

若要验证交换机是否具有端口镜像功能，请提供 Defender for IoT 平台应连接到的交换机型号。

| **#** | **Switch** | **Model** | **流量镜像支持（SPAN、RSPAN 或无）** |
|--|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

### <a name="6-third-party-switch-management"></a>6、第三方交换机管理

是否由第三方管理交换机？ 是或否 

如果是，由谁管理？ __________________________________ 

他们的策略是什么？ __________________________________ 

### <a name="7-serial-connection"></a>7、串行连接

是否有设备通过网络中的串行连接进行通信？ 是或否 

如果是，请指定串行通信协议：________________ 

如果是，请在网络图上指示哪些设备使用串行协议通信，以及这些设备的位置。

使用标记的串行连接添加网络示意图。

### <a name="8-vendors-and-protocols-industrial-equipment"></a>8、供应商和协议（工业设备）

提供工业设备的供应商和协议列表。 (可选)

| **#** | **供应商** | **通信协议** |
|--|--|--|
| 1 |  |  |
| 2 |  |  |
| 3 |  |  |
| 4 |  |  |

例如：

- Siemens

- Rockwell Automation – 以太网或 IP

- Emerson – DeltaV、Ovation

### <a name="9-qos"></a>9、QoS

对于 QoS，传感器的默认设置为 1.5 Mbps。 指定是否要更改此设置：________________ 

   业务单位 (BU)：________________

### <a name="10-sensor"></a>10、传感器  

传感器设备通过网络适配器连接到交换机 SPAN 端口。 它连接到客户的企业网络，以通过另一个专用网络适配器进行管理。

提供要在企业网络中连接的传感器 NIC 的地址详细信息： 

| 项 | 设备 1 | 设备 2 | 设备 3 |
|--|--|--|--|
| 设备 IP 地址 |  |  |  |
| 子网 |  |  |  |
| 默认网关 |  |  |  |
| DNS |  |  |  |
| 主机名 |  |  |  |

### <a name="11-idraciloserver-management"></a>11、iDRAC/iLO/服务器管理

| 项 | 设备 1 | 设备 2 | 设备 3 |
|--|--|--|--|
| 设备 IP 地址 |  |  |  |
| 子网 |  |  |  |
| 默认网关 |  |  |  |
| DNS |  |  |  |

### <a name="12-on-premises-management-console"></a>12、本地管理控制台  

| 项 | 可用 | 被动（使用 HA 时） |
|--|--|--|
| IP 地址 |  |  |
| 子网 |  |  |
| 默认网关 |  |  |
| DNS |  |  |

### <a name="13-snmp"></a>13、SNMP  

| 项 | 详细信息 |
|--|--|
| IP |  |
| IP 地址 |  |
| 用户名 |  |
| 密码 |  |
| 身份验证类型 | MD5 或 SHA |
| 加密 | DES 或 AES |
| 密钥 |  |
| SNMP v2 社区字符串 |

### <a name="14-ssl-certificate"></a>14、SSL 证书

你是否计划使用 SSL 证书？ 是或否

如果是，你要使用哪个服务生成证书？ 你将在证书中包含哪些特性（例如域或 IP 地址）？

### <a name="15-smtp-authentication"></a>15、SMTP 身份验证

你是否计划使用 SMTP 将警报转发到电子邮件服务器？ 是或否

如果是，你要使用哪种身份验证方法？  

### <a name="16-active-directory-or-local-users"></a>16、Active Directory 或本地用户

请与 Active Directory 管理员联系，以创建 Active Directory 站点用户组或创建本地用户。 确保用户为部署日的到来做好准备。

### <a name="17-iot-device-types-in-the-network"></a>17、网络中的 IoT 设备类型

| 设备类型 | 网络中的设备数 | 平均带宽 |
|--|--|--|
| 例如： 照相机 |  |
| 例如， X 光机 |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |
|  |  |

## <a name="next-steps"></a>后续步骤

[关于 Azure Defender for IoT 网络设置](how-to-set-up-your-network.md)

[关于 Defender for IoT 安装](how-to-install-software.md)
