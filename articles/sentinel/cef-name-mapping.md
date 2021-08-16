---
title: 通用事件格式 (CEF) 键和 CommonSecurityLog 字段映射
description: 本文将 CEF 键映射到 Azure Sentinel 的 CommonSecurityLog 中的相应字段名称。
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 04/12/2021
ms.openlocfilehash: 107d10bec77ea7bb2b67ae86f48d5b88a286869d
ms.sourcegitcommit: 49bd8e68bd1aff789766c24b91f957f6b4bf5a9b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2021
ms.locfileid: "108229497"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF 和 CommonSecurityLog 字段映射

下表将通用事件格式 (CEF) 字段名称映射到它们在 Azure Sentinel 的 CommonSecurityLog 中使用的名称，当你在 Azure Sentinel 中使用 CEF 数据源时，这些信息可能有所帮助。

有关详细信息，请参阅[使用通用事件格式连接外部解决方案](connect-common-event-format.md)。

## <a name="a---c"></a>A - C

|CEF 键名称  |CommonSecurityLog 字段名称  |说明  |
|---------|---------|---------|
| act    |    <a name="deviceaction"></a> DeviceAction     |  事件中提到的操作。       |
|   应用  |    ApplicationProtocol     |  应用程序中使用的协议，例如 HTTP、HTTPS、SSHv2、Telnet、POP、IMPA、IMAPS 等等。   |
| cnt    |    EventCount     |  与事件关联的计数，显示观察到同一事件的次数。       |
| | | |

## <a name="d"></a>D

|CEF 键名称  |CommonSecurityLog 名称  |说明  |
|---------|---------|---------|
|设备供应商     |  DeviceVendor       | 与设备产品和版本定义一起用于唯一标识发送方设备类型的字符串。       |
|设备产品     |   DeviceProduct      |   与设备供应商和版本定义一起用于唯一标识发送方设备类型的字符串。        |
|设备版本     |   DeviceVersion      |      与设备产品和供应商定义一起用于唯一标识发送方设备类型的字符串。     |
| destinationDnsDomain    | DestinationDnsDomain        |   完全限定的域名 (FQDN) 的 DNS 部分。      |
| destinationServiceName | DestinationServiceName | 事件所针对的服务。 例如，`sshd`。|
| destinationTranslatedAddress | DestinationTranslatedAddress | 标识 IP 网络中由事件引用的已转换目标，用作 IPv4 IP 地址。 |
| destinationTranslatedPort | DestinationTranslatedPort | 转换后的端口（例如防火墙）。 <br>有效端口号：`0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | 有关已观察到的通信所采用的方向的任何信息。 有效值： <br>- `0` = 入站 <br>- `1` = 出站 |
| deviceDnsDomain | DeviceDnsDomain | 完全限定的域名 (FQDN) 的 DNS 域部分 |
|DeviceEventClassID     |   DeviceEventClassID     |   充当每个事件类型的唯一标识符的字符串或整数。      |
| deviceExternalID | DeviceExternalID | 一个用于唯一标识生成事件的设备的名称。 |
| deviceFacility | DeviceFacility | 生成事件的设施。|
| deviceInboundInterface | DeviceInboundInterface |数据包或数据进入设备时所用的接口。  |
| deviceNtDomain | DeviceNtDomain | 设备地址的 Windows 域 |
| deviceOutboundInterface | DeviceOutboundInterface |数据包或数据离开设备时所用的接口。 |
| devicePayloadId |DevicePayloadId |与事件关联的有效负载的唯一标识符。 |
| deviceProcessName | ProcessName | 与事件关联的进程名称。 <br><br>例如，在 UNIX 中，这是生成 syslog 条目的进程。 |
| deviceTranslatedAddress | DeviceTranslatedAddress | 标识事件在 IP 网络中引用的已转换设备地址。 <br><br>格式为 IPv4 地址。 |
| dhost |DestinationHostName | 事件在 IP 网络中引用的目标。  <br>格式应是与目标节点（如果节点可用）关联的 FQDN。 例如，`host.domain.com` 或 `host`。 |
| dmac | DestinationMacAddress | 目标 MAC 地址 (FQDN) |
| dntdom | DestinationNTDomain | 目标地址的 Windows 域名。|
| dpid | DestinationProcessId |与事件关联的目标进程的 ID。|
| dpriv | DestinationUserPrivileges | 定义目标用户的特权。 <br>有效值：`Admninistrator`、`User`、`Guest` |
| dproc | DestinationProcessName | 事件目标进程的名称，例如 `telnetd` 或 `sshd.` |
| dpt | DestinationPort | 目标端口。 <br>有效值：`*0` - `65535` |
| dst | DestinationIP | 事件在 IP 网络中引用的目标 IPv4 地址。 |
| dtz | DeviceTimeZon | 生成事件的设备的时区 |
| duid |DestinationUserId | 按 ID 标识目标用户。 |
| duser | DestinationUserName |按名称标识目标用户。|
| dvc | DeviceAddress | 生成事件的设备的 IPv4 地址。 |
| dvchost | DeviceName | 与设备节点（如果节点可用）关联的 FQDN。 例如，`host.domain.com` 或 `host`。| 
| dvcmac | DeviceMacAddress | 生成事件的设备的 MAC 地址。 |
| dvcpid | 进程 ID | 定义生成事件的设备上进程的 ID。 |

## <a name="e---i"></a>E - I

|CEF 键名称  |CommonSecurityLog 名称  |说明  |
|---------|---------|---------|
|externalId    |   ExternalID      | 发起设备使用的 ID。 通常，这些值具有一些递增值，其中每个递增值均与某个事件相关联。        |
|fileCreateTime     |  FileCreateTime      | 文件的创建时间。        |
|FileHash     |   FileHash      |   文件的哈希。      |
|fileId     |   FileID      |与文件关联的 ID，例如 inode。         |
| fileModificationTime | FileModificationTime |文件的上次修改时间。 |
| filePath | 文件路径 | 文件的完整路径，包括文件名。 例如 `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` 或 `/usr/bin/zip`。|
| filePermission |FilePermission |文件的权限。 |
| fileType | FileType | 文件类型，例如管道、套接字等。|
|fname | FileName| 文件的名称，不包括路径。 |
| fsize | FileSize | 文件的大小。 |
|主机    |  Computer       | Syslog 中的主机        |
|in     |  ReceivedBytes      |入站传输的字节数。         |
| | | |

## <a name="m---p"></a>M - P

|CEF 键名称  |CommonSecurityLog 名称  |说明  |
|---------|---------|---------|
|msg   |  消息       | 一条消息，提供有关事件的更多详细信息。        |
|名称     |  活动       |   一个字符串，表示用户可读且可理解的事件说明。     |
|oldFileCreateTime     |  OldFileCreateTime       | 旧文件的创建时间。        |
|oldFileHash     |   OldFileHash      |   旧文件的哈希。      |
|oldFileId     |   OldFileId     |   与旧文件相关联的 ID，例如 inode。      |
| oldFileModificationTime | OldFileModificationTime |旧文件的上次修改时间。 |
| oldFileName |  OldFileName |旧文件的名称。 |
| oldFilePath | OldFilePath | 旧文件的完整路径，包括文件名。 <br>例如，`C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` 或 `/usr/bin/zip`。|
| oldFilePermission | OldFilePermission |旧文件的权限。 |
|oldFileSize | OldFileSize | 旧文件的大小。|
| oldFileType | OldFileType | 旧文件的文件类型，例如管道、套接字等。|
| out | SentBytes | 出站传输的字节数。 |
| 业务成效 | 业务成效 | 事件的结果，例如 `success` 或 `failure`。|
|proto    |  协议       | 用于标识所用第 4 层协议的传输协议。 <br><br>可能的值包括协议名称，例如 `TCP` 或 `UDP`。        |
| | | |

## <a name="r---t"></a>R - T

|CEF 键名称  |CommonSecurityLog 名称  |说明  |
|---------|---------|---------|
|请求     |   RequestURL      | 为 HTTP 请求访问的 URL，包括协议。 例如： `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   与请求关联的用户代理。      |
| requestContext | RequestContext | 描述从中发起请求的内容，例如 HTTP 引用网站。 |
| requestCookies | RequestCookies |与请求关联的 Cookie。 |
| requestMethod | RequestMethod | 用于访问 URL 的方法。 <br><br>有效值包括 `POST`、`GET` 等方法。 |
| rt | ReceiptTime | 收到活动相关事件的时间。 |
|严重性     |  <a name="logseverity"></a> LogSeverity       |  用于描述事件重要性的字符串或整数。<br><br> 有效的字符串值：`Unknown`、`Low`、`Medium`、`High`、`Very-High` <br><br>有效的整数值为：<br> - `0`-`3` = 低 <br>- `4`-`6` = 中<br>- `7`-`8` = 高<br>- `9`-`10` = 很高 |
| shost    | SourceHostName        |标识事件在 IP 网络中引用的源。 格式应是与源节点（如果节点可用）关联的完全限定域名 (DQDN)。 例如，`host` 或 `host.domain.com`。 |
| smac | SourceMacAddress | 源 MAC 地址。 |
| sntdom | SourceNTDomain | 源地址的 Windows 域名。 |
| sourceDnsDomain | SourceDnsDomain | 完整 FQDN 的 DNS 域部分。 |
| sourceServiceName | SourceServiceName | 负责生成事件的服务。 |
| sourceTranslatedAddress | SourceTranslatedAddress | 标识事件在 IP 网络中引用的已转换源。 |
| sourceTranslatedPort | SourceTranslatedPort | 转换后的源端口（例如防火墙）。 <br>有效端口号为 `0` - `65535`。 |
| spid | SourceProcessId | 与事件关联的源进程的 ID。|
| spriv | SourceUserPrivileges | 源用户的特权。 <br><br>有效值包括：`Administrator`、`User`、`Guest` |
| sproc | SourceProcessName | 事件的源进程名称。|
| spt | SourcePort | 源端口号。 <br>有效端口号为 `0` - `65535`。 |
| src | SourceIP |事件在 IP 网络中引用的源，用作 IPv4 地址。 |
| suid | SourceUserID | 按 ID 标识源用户。 |
| suser | SourceUserName | 按名称标识源用户。 |
| 类型 | EventType | 事件类型。 有效值包括： <br>- `0`：基本事件 <br>- `1`：聚合 <br>- `2`：关联事件 <br>- `3`：操作事件 <br><br>注意：对于基本事件，可以省略此事件。 |
| | | |

## <a name="custom-fields"></a>自定义字段

下表映射了 CEF 键和 CommonSecurityLog 字段的名称，客户可以将这些字段用于不在任何内置字段中应用的数据。

### <a name="custom-ipv6-address-fields"></a>自定义 IPv6 地址字段

下表映射了可用于自定义数据的 IPv6 地址字段的 CEF 键和 CommonSecurityLog 名称。

|CEF 键名称  |CommonSecurityLog 名称  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>自定义数字字段

下表映射了可用于自定义数据的数字字段的 CEF 键和 CommonSecurityLog 名称。

|CEF 键名称  |CommonSecurityLog 名称  |
|---------|---------|
|     cn1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     cn2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>自定义字符串字段

下表映射了可用于自定义数据的字符串字段的 CEF 键和 CommonSecurityLog 名称。

|CEF 键名称  |CommonSecurityLog 名称  |
|---------|---------|
|     cs1    |     DeviceCustomString1 <sup>[1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup>[1](#use-sparingly)</sup>    |
|     cs2    |     DeviceCustomString2 <sup>[1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label <sup>[1](#use-sparingly)</sup> |
|     cs3    |     DeviceCustomString3 <sup>[1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label <sup>[1](#use-sparingly)</sup> |
|     cs4    |     DeviceCustomString4 <sup>[1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label <sup>[1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup>[1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup>[1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6 <sup>[1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label <sup>[1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> 建议谨慎使用 DeviceCustomString 字段，并且尽可能使用更具体的内置字段。
> 

### <a name="custom-timestamp-fields"></a>自定义时间戳字段

下表映射了可用于自定义数据的时间戳字段的 CEF 键和 CommonSecurityLog 名称。

|CEF 键名称  |CommonSecurityLog 名称  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>自定义整数值

下表映射了可用于自定义数据的整数字段的 CEF 键和 CommonSecurityLog 名称。

|CEF 键名称  |CommonSecurityLog 名称  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>扩充字段

Azure Sentinel 添加了以下 CommonSecurityLog 字段，以丰富接收自源设备的原始事件，并且不会在 CEF 键中使用映射：

### <a name="threat-intelligence-fields"></a>威胁情报字段

|CommonSecurityLog 字段名称  |说明  |
|---------|---------|
|   **IndicatorThreatType**  |  [MaliciousIP](#MaliciousIP) 威胁类型，视威胁情报源而定。       |
| MaliciousIP<a name="MaliciousIP"></a> | 列出消息中与当前威胁情报源关联的所有 IP 地址。 |
|  MaliciousIPCountry   | [MaliciousIP](#MaliciousIP) 所在的国家/地区，视记录引入时的地理信息而定。        |
| MaliciousIPLatitude    |   [MaliciousIP](#MaliciousIP) 的所在纬度，视记录引入时的地理信息而定。      |
| MaliciousIPLongitude    |  [MaliciousIP](#MaliciousIP) 的所在经度，视记录引入时的地理信息而定。       |
| **ReportReferenceLink**    |    威胁情报报告的链接。     |
|  ThreatConfidence   |   [MaliciousIP](#MaliciousIP) 威胁的可信度，视威胁情报源而定。      |
| ThreatDescription    |   [MaliciousIP](#MaliciousIP) 威胁描述，视威胁情报源而定。      |
| ThreatSeverity | [MaliciousIP](#MaliciousIP) 威胁的严重性，视记录引入时的威胁情报源而定。 |
|     |         |

### <a name="additional-enrichment-fields"></a>其他扩充字段

|CommonSecurityLog 字段名称  |说明  |
|---------|---------|
|OriginalLogSeverity     |  始终为空，支持与 CiscoASA 集成。 <br>有关日志严重性值的详细信息，请参阅 [LogSeverity](#logseverity) 字段。       |
|**RemoteIP**     |     远程 IP 地址。 <br>此值基于 [CommunicationDirection](#communicationdirection) 字段（如果可能）。     |
|RemotePort     |   远程端口。 <br>此值基于 [CommunicationDirection](#communicationdirection) 字段（如果可能）。      |
|SimplifiedDeviceAction     |   将 [DeviceAction](#deviceaction) 值简化为一组静态值，同时在 [DeviceAction](#deviceaction) 字段中保留原始值。 <br>例如：`Denied` > `Deny`。      |
|**SourceSystem**     | 始终定义为 OpsManager。        |
|     |         |

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅[使用通用事件格式连接外部解决方案](connect-common-event-format.md)。
