---
title: Azure Sentinel 信息模型 (ASIM) 内容 | Microsoft Docs
description: 本文概述了使用 Azure Sentinel 信息模型 (ASIM) 的 Azure Sentinel 内容
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: 6ff0d40fbfa75eb6ae40766a4be42891c11156dd
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123432191"
---
# <a name="azure-sentinel-information-model-asim-security-content--public-preview"></a>Azure Sentinel 信息模型 (ASIM) 安全内容（公共预览版）

Azure Sentinel 中的规范化安全内容包括分析规则、搜寻查询以及使用与源无关的规范化分析器的工作簿。

<a name="builtin"></a>可在 Azure Sentinel 库和[解决方案](sentinel-solutions-catalog.md)中查找规范化的内置内容、自行创建规范化内容或修改现有内容以使用规范化数据。

本文列出了已配置为支持 ASIM 的内置 Azure Sentinel 内容。  虽然下面提供了 Azure Sentinel GitHub 存储库的链接作为参考，但也可在 [Azure Sentinel Analytics 规则库](detect-threats-built-in.md)中找到这些规则。 请使用链接的 GitHub 页复制任何相关的搜寻查询。

> [!IMPORTANT]
> ASIM 当前为预览版。 [Azure 预览版补充条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。
>

## <a name="authentication-security-content"></a>身份验证安全内容

ASIM 规范化支持以下内置身份验证内容。

### <a name="analytics-rules"></a>分析规则

 - [潜在的密码喷涂攻击（使用身份验证规范化）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [对用户凭据进行的暴力攻击（使用身份验证规范化）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [3 小时内来自不同国家/地区的用户登录（使用身份验证规范化）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [尝试登录已禁用帐户的 IP 登录（使用身份验证规范化）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


## <a name="dns-query-security-content"></a>DNS 查询安全内容

ASIM 规范化支持以下内置 DNS 查询内容。

### <a name="analytics-rules"></a>分析规则

 - [NXDOMAIN DNS 查询过多（规范化 DNS）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [与挖掘池相关的 DNS 事件（规范化 DNS）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [与 ToR 代理相关的 DNS 事件（规范化 DNS）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [已知的 Barium 域](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [已知的 Barium IP 地址](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [2021 年 3 月 IoC 匹配中披露的 Exchange Server 漏洞](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [已知 GALLIUM 域和哈希](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [已知 IRIDIUM IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - 域和 IP IOC - 2021 年 3 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [已知 Phosphorus 组域/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [已知的 STRONTIUM 组域 - 2019 年 7 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate 网络信标](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [包含在 DCU 撤销中的 THALLIUM 域](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [已知的 ZINC Comebacker 和 Klackring 恶意软件哈希](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="file-activity-security-content"></a>文件活动安全内容

ASIM 规范化支持以下内置文件活动内容。

### <a name="analytic-rules"></a>分析规则

- [SUNBURST 和 SUPERNOVA 后门哈希（规范化文件事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [2021 年 3 月 IoC 匹配中披露的 Exchange Server 漏洞](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [HAFNIUM UM 服务写入可疑文件](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM - 域、哈希和 IP IOC - 2021 年 5 月](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT 日志文件创建](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [已知的 ZINC Comebacker 和 Klackring 恶意软件哈希](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="process-activity-security-content"></a>进程活动安全内容

ASIM 规范化支持以下内置进程活动内容。

### <a name="analytics-rules"></a>分析规则

 - [可能的 AdFind Recon 工具使用情况（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
 - [Base64 编码 Windows 进程命令行（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
 - [回收站中的恶意软件（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
 - [NOBELIUM - vbscript 的可疑 rundll32.exe 执行（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
 - [SUNBURST 可疑 SolarWinds 子进程（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

### <a name="hunting-queries"></a>搜寻查询

 - [Cscript 脚本每日摘要明细（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
 - [用户和组的枚举（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
 - [已添加 Exchange PowerShell 管理单元（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
 - [主机导出邮箱和删除导出（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
 - [Invoke-PowerShellTcpOneLine 使用情况（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
 - [Base64 的 Nishang 反向 TCP Shell（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
 - [使用非通用/未记录的命令行开关创建的用户摘要（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
 - [Powercat 下载（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
 - [PowerShell 下载（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
 - [给定主机的进程熵（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
 - [SolarWinds 清单（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
 - [使用 Adfind 工具进行可疑枚举（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
 - [Windows 系统关闭/重启（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
 - [Certutil（LOLBin 和 LOLScript，规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
 - [Rundll32（LOLBin 和 LOLScript，规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
 - [非通用进程 - 下 5%（规范化进程事件）](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
 - [命令行中的 Unicode 模糊处理](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)

## <a name="registry-activity-security-content"></a>注册表活动安全内容

ASIM 规范化支持以下内置注册表活动内容。

### <a name="hunting-queries"></a>搜寻查询

- [通过 IFEO 注册表项持久保存](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml)

## <a name="modify-your-content-to-use-normalized-data"></a><a name="modify"></a>修改内容以使用规范化数据

要使自定义内容能够使用规范化，请执行以下操作：

- 修改查询，以使用与查询相关的与源无关的分析器。
- 修改查询中的字段名称，以使用规范化架构字段名称。
- 在适用的情况下，更改条件以在查询中使用字段的规范化值。

例如，考虑使用“观测到反向 DNS 查找计数较高的罕见客户端”DNS 分析规则来处理 Infoblox DNS 服务器发送的 DNS 事件：

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

以下代码是与源无关的版本，它使用规范化来为提供 DNS 查询事件的任何源提供相同的检测：

```kusto
imDns(responsecodename='NXDOMAIN')
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns(responsecodename='NXDOMAIN')) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr```
```

与源无关的规范化版本具有以下差别：

- 使用 `imDns` 规范化分析器而不是 Infoblox 分析器。

- `imDns` 只提取 DNS 查询事件，因此无需检查事件类型，而 Infoblox 版本中的 `where ProcessName =~ "named" and Log_Type =~ "client"` 会执行这种检查。

- 使用 `SrcIpAddr` 字段而不是 `Client_IP`。
 
- 分析程序参数筛选用于 ResponseCodeName，从而无需显式 where 子句。


除支持任何规范化的 DNS 源以外，规范化版本更短且更易于理解。 

如果架构或分析程序不支持筛选参数，则更改类似，但最后一个除外。 相反，筛选条件将保留在原始查询中，如下所示：

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

## <a name="next-steps"></a>后续步骤

本文介绍 Azure Sentinel 信息模型 (ASIM) 内容。

有关详细信息，请参阅：

- [Azure Sentinel 信息模型概述](normalization.md)
- [Azure Sentinel 信息模型架构](normalization-about-schemas.md)
- [Azure Sentinel 信息模型分析器](normalization-about-parsers.md)