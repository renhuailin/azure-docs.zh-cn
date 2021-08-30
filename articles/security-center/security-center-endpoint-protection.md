---
title: Azure 安全中心中的 Endpoint Protection 建议
description: 如何发现 Endpoint Protection 解决方案并将其标识为正常。
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 07/21/2021
ms.author: memildin
ms.openlocfilehash: 841d52b53fd492f1b9f6760deba438f68a47d004
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114470705"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Azure 安全中心中的 Endpoint Protection 评估和建议

Azure 安全中心提供[支持的](security-center-services.md#endpoint-supported) Endpoint Protection 解决方案版本的运行状况评估。 本文说明了使安全中心生成以下两个建议的场景：

- [应在计算机上安装终结点保护](https://portal.azure.com/#blade/Microsoft_Azure_Security/RecommendationsBlade/assessmentKey/4fb67663-9ab9-475d-b026-8c544cced439)
- 应在计算机上解决 Endpoint Protection 运行状况问题

## <a name="windows-defender"></a>Windows Defender

- 安全中心建议应在 [MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) 运行且结果为 AMServiceEnabled: False 时，在计算机上安装终结点保护 

- 安全中心建议应在 [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) 运行且出现以下任意情况时，解决在计算机上的终结点保护运行状况问题：

  * 以下任一属性为 false：

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * 如果下列一个或两个属性大于或等于 7：

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* 安全中心建议，导入 SCEPMpModule ("$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1") 并运行 Get-MProtComputerStatus 得到 AMServiceEnabled = false 时，应在计算机上安装终结点保护   。

* 安全中心建议，MprotComputerStatus 运行并出现以下任意情况时，应在计算机上解决终结点保护运行状况问题 ：

  * 以下属性至少一个为 false：

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * 如果以下一个或两个签名更新大于或等于 7：

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* 安全中心建议在不符合以下任意检查时，在计算机上安装终结点保护：
    - 存在 HKLM:\SOFTWARE\TrendMicro\Deep Security Agent
    - 存在 HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder
    - 在安装文件夹中找到 dsa_query.cmd 文件
    - 运行 dsa_query.cmd 结果且 Component.AM.mode 为“启用”- 检测到 Trend Micro Deep Security Agent 

## <a name="symantec-endpoint-protection"></a>Symantec 终结点保护
安全中心建议在不符合以下任意检查时，在计算机上安装终结点保护：

- HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"
- HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1

或

- HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"
- HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1

安全中心建议在不符合以下任意检查时，解决计算机上的终结点保护运行状况问题：

- 检查 Symantec 版本  >= 12：注册表位置：HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"
- 检查实时保护状态：HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1
- 检查签名更新状态：HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 天
- 检查完全扫描状态：HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 天
- 查找 Symantec 12 的签名版本的签名版本号路径：Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP" 
- Symantec 14 的签名版本的路径：Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"

注册表路径：
- "HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;
- "HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path

## <a name="mcafee-endpoint-protection-for-windows"></a>适用于 Windows 的 McAfee Endpoint Protection

安全中心建议在不符合以下任意检查时，在计算机上安装终结点保护：

- 存在 HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion
- HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1

安全中心建议在不符合以下任意检查时，解决计算机上的终结点保护运行状况问题：

- McAfee 版本：HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10
- 查找签名版本：HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"
- 查找签名日期：HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 天
- 查找扫描日期：HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 天

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>适用于 Linux 威胁防护的 McAfee 终结点安全性 

安全中心建议在不符合以下任意检查时，在计算机上安装终结点保护：

- 存在文件 /opt/isec/ens/threatprevention/bin/isecav
- /opt/isec/ens/threatprevention/bin/isecav --version 输出为：McAfee 名称 = 适用于 Linux 威胁防护的 McAfee 终结点安全性，并且 McAfee 版本 >= 10

安全中心建议在不符合以下任意检查时，解决计算机上的终结点保护运行状况问题：

- /opt/isec/ens/threatprevention/bin/isecav --listtask 返回“快速扫描”和“完全扫描”，并且两者 <= 7 天 
- /opt/isec/ens/threatprevention/bin/isecav--listtask 返回 DAT 和引擎更新时间，并且两者 < = 7 天 
- /opt/isec/ens/threatprevention/bin/isecav --getoasconfig --summary 返回“访问时扫描”状态 

## <a name="sophos-antivirus-for-linux"></a>适用于 Linux 的 Sophos 防病毒 

安全中心建议在不符合以下任意检查时，在计算机上安装终结点保护：
- 存在文件 /opt/sophos-av/bin/savdstatus 或搜索自定义位置 readlink $(which savscan) 
- /opt/sophos-av/bin/savdstatus --version 返回 Sophos 名称 = Sophos 防病毒，并且 Sophos 版本 >= 9 

安全中心建议在不符合以下任意检查时，解决计算机上的终结点保护运行状况问题：
- opt/sophos-av/bin/savlog --maxage=7 | grep -i "Scheduled scan .\* completed" | tail -1 返回一个值
- /opt/sophos-av/bin/savlog --maxage=7 | grep "scan finished" | tail -1 返回一个值
- opt/sophos-av/bin/savdstatus --lastupdate 返回 lastUpdate，它应 <= 7 天 
- opt/sophos-av/bin/savdstatus -v 即为“正在运行访问时扫描”  
- /opt/sophos-av/bin/savconfig get LiveProtection 返回“已启用”

## <a name="troubleshoot-and-support"></a>故障排除和支持

### <a name="troubleshoot"></a>故障排除

%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log 中提供了 Microsoft Antimalware 扩展日志

### <a name="support"></a>支持

如果需要更多帮助，请联系 [MSDN Azure 和 Stack Overflow 论坛](https://azure.microsoft.com/support/forums/)上的 Azure 专家。 或者提交 Azure 支持事件。 请转到 [Azure 支持站点](https://azure.microsoft.com/support/options/)并选择“获取支持”。 有关使用 Azure 支持的信息，请阅读 [Microsoft Azure 支持常见问题解答](https://azure.microsoft.com/support/faq/)。