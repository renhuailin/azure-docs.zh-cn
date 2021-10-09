---
title: Azure Defender for IoT 支持的协议
description: 了解 Azure Defender for IoT 支持的协议。
ms.date: 09/20/2021
ms.topic: article
ms.openlocfilehash: 9320b43cf645e62d68baac39cdf4f06dd1825305
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644422"
---
# <a name="support-for-iot-ot-ics-and-scada-protocols"></a>对 IoT、OT、ICS 和 SCADA 协议的支持

Azure Defender for IoT 提供开放且可互操作的操作技术 (OT) 网络安全平台。 Defender for IoT 部署在许多不同的位置，对于各个垂直行业和地域中要求很高的复杂 OT 环境中的部署，它可以降低 IoT、IT 和 ICS 风险。

## <a name="supported-protocols"></a>支持的协议

Azure Defender for IoT 支持在各种企业中使用广泛的协议，并且包括所有工业门类、企业网络和建筑管理系统 (BMS) 环境中的工业自动化设备。 对于自定义协议或专有协议，Microsoft 提供了一个 SDK，用于轻松开发、测试和部署插件形式的自定义协议解析器。 该 SDK 可以在不泄露专有信息（例如协议设计方式）或共享可能包含敏感信息的 PCAP 的情况下完成所有这些操作。 下面列出了支持的协议。

### <a name="supported-protocols-passive-monitoring"></a>支持的协议（被动监视）

本部分列出了使用被动监视检测的协议。

ABB：IEC61850 MMS（包括 ABB 扩展）

ASHRAE：BACnet、BACnet BACapp、BACnet BVLC

Beckhoff：AMS (ADS)、Twincat 

Cisco：CAPWAP Control、CAPWAP Data、CDP、LWAPP

DNP.org：DNP3

Emerson：DeltaV、Emerson OpenBSI/BSAP、Ovation DCS ADMD、Ovation DCS DPUSTAT、Ovation DCS SSRPC

Emerson Fischer：ROC

Eurocontrol：ASTERIX

GE：Bentley Nevada (System 1)、EGD、GSM（GE MarkVI 和 MarkVIe）、SRTP (GE)

Honeywell：ENAP、Experion DCS CDA、Experion DCS FDA

IEC：Codesys V3、ICCP TASE.2/IEC-60870、IEC60870-5 (IEC104/101)、IEC60870-5-103（封装系列）、IEC61850 GOOSE、IEC61850 MMS、IEC61850 SMV (SAMPLED-VALUES)、LonTalk (LonWorks)

IEEE：LLC、STP、VLAN

IETF：ARP、DCE RPC、DNS、FTP（FTP_ADAT、FTP_DATA）、GSSAPI (RFC2743)、HTTP、ICMP、IPv4、IPv6、LLDP、MDNS、NBNS、NTLM（NTLMSSP 身份验证协议）、RPC、SMB / Browse / NBDGM、SMB / CIFS、SNMP、SPNEGO (RFC4178)、SSH、Syslog、Telnet、TFTP、TPKT、UDP

ISO：CLNP (ISO 8473)、COTP (ISO 8073)、ISO Industrial Protocol、MQTT (IEC 20922)

Medical：ASTM、HL7

Microsoft：Horizon 社区解析器、Horizon 专有解析器（由客户开发）。 有关详细信息，请参阅 [Horizon 专有协议解析器](references-horizon-sdk.md)。

Mitsubishi：Melsoft / Melsec (Mitsubishi Electric)

Omron：FINS

Oracle：TDS、TNS

Rockwell Automation：ENIP、EtherNet/IP CIP（包括 Rockwell 扩展）、EtherNet/IP CIP FW 版本 27 和更高版本

Schneider Electric：Modbus/TCP、Modbus TCP–Schneider Unity Extensions、OASYS (Schneider Electric Telvant) 

Schneider Electric / Invensys：Foxboro Evo、Foxboro I/A、Trident、TriGP、TriStation

Schneider Electric/Modicon：Modbus RTU

Schneider Electric / Wonderware：Wonderware Suitelink

Siemens：CAMP、PCS7、PCS7 WinCC – Historian、Profinet DCP、Profinet Realtime、Siemens PHD、Siemens S7、Siemens S7-Plus、Siemens S7-Plus、Siemens SICAM、Siemens WinCC

Toshiba：Toshiba Computer Link

Yokogawa：Centum ODEQ (Centum / ProSafe DCS)、HIS Equalize、Vnet/IP

### <a name="supported-protocols-active-monitoring"></a>支持的协议（主动监视）

本部分列出了使用主动探测（例如 ping 扫描和查询）检测的协议。

IETF：Ping Sweep、SNMP Network Layout Query、SNMP Query

Microsoft：Windows WMI Query（需要 WMI/WinRM）：硬件、BIOS、版本、硬件、补丁

Rockwell Automation：ENIP Query、ENIP Scan、EtherNet/IP CIP (CIP Query)

Siemens：Siemens S7

## <a name="quickly-add-support-for-proprietary-restricted-protocols"></a>快速添加对专有受限协议的支持

数字化正在推动数十亿 IoT 设备的部署，IT 和 OT 网络之间的连接也随之有了大幅增长。 这意味着受攻击面正在不断扩大，包括工业控制系统面临的危害性网络攻击风险比以往更大。

为了帮助减小受攻击面，可以使用 Horizon 协议 SDK 来快速安全地支持 IoT 和 ICS 环境中使用的任何协议。

Horizon 为客户和技术合作伙伴提供以下解决方案：

- 无限制完全支持通用协议、专有协议、自定义协议或者偏离任何标准的协议。

- 全新级别的 DPI 开发灵活度和范围。

- 可成倍扩展 OT 可见性和控制度的工具，无需升级 Defender for IoT 平台版本。

- 实现专有开发的安全性，不会泄漏敏感信息。

使用 Horizon SDK 可以编写插件，以针对流量启用深度数据包检查 (DPI) 并实时检测威胁。 Horizon SDK 还使额外的自定义成为可能。 例如，Horizon SDK 使资产供应商、合作伙伴或平台所有者能够本地化和自定义警报、事件与协议参数的文本。

[![使用 Horizon SDK 可以快速支持 IoT 和 ICS 环境中使用的所有协议。](media/concept-supported-protocols/sdk-horizon.png)](media/concept-supported-protocols/sdk-horizon-expanded.png#lightbox)

## <a name="collaborate-with-the-horizon-community"></a>与 Horizon 社区协作

成为引领数字化转型和全行业协议支持协作的社区中的一员。 在 Horizon ICS 社区中可以分享关键基础设施、建筑管理、生产线、运输系统和其他行业领导者中领域专家的知识。

该社区提供教程、论坛、讲师引导式培训、教育白皮书、网络研讨会等资源。

我们诚邀你加入我们的社区：<horizon-community@microsoft.com>

## <a name="next-steps"></a>后续步骤

详细了解 [Horizon 专有协议解析器](references-horizon-sdk.md)。
查看我们的 [Horizon API](references-horizon-api.md)。
