---
title: 将 Active Directory Alsid 连接到 Azure Sentinel |Microsoft Docs
description: 了解如何使用 Alsid for Active Directory 连接器将 Alsid 日志请求到 Azure Sentinel。 查看工作簿中的 Alsid 数据、创建警报并改善调查。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 654ecb65068e4321b85594d96e8ca7a7f73cde7e
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566682"
---
# <a name="connect-your-alsid-for-active-directory-ad-to-azure-sentinel"></a>将 Active Directory (AD) 的 Alsid 连接到 Azure Sentinel

> [!IMPORTANT]
> Active Directory 连接器的 Alsid 当前为 **预览版**。 请参阅 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) ，了解适用于 Azure 功能的其他法律条款，这些功能适用于 beta 版、预览版或其他情况下尚未公开上市。

本文介绍了如何将 Alsid for AD 解决方案连接到 Azure Sentinel。 Active Directory 数据连接器的 Alsid 可让你轻松地将 Alsid 用于 AD 日志，并将其用于查看工作簿中的数据、查询数据以创建自定义警报，并结合它来改善调查。 用于 AD 和 Azure Sentinel 的 Alsid 之间的集成利用安装了 Log Analytics 代理的 Syslog 服务器。 它还使用基于 Kusto 函数的自定义的日志分析器。

> [!NOTE]
> 数据将存储在运行 Azure Sentinel 的工作区的地理位置。

## <a name="prerequisites"></a>先决条件

- 您必须对 Azure Sentinel 工作区具有写入权限。

- 你的 Alsid for AD 解决方案必须配置为通过 Syslog 导出日志。

## <a name="send-alsid-for-ad-logs-to-azure-sentinel-via-the-syslog-agent"></a>通过 Syslog 代理将 AD 日志的 Alsid 发送到 Azure Sentinel

将 Alsid 配置为通过 Syslog 代理将 Syslog 消息转发到 Azure Sentinel 工作区。

1. 在 Azure Sentinel 导航菜单中，选择 " **数据连接器**"。

1. 从 " **数据连接器** " 库中，选择 " **Alsid For Active Directory (预览")** "连接器"，然后单击 " **连接器" 页面**。

1. 按照 " **Alsid for Active Directory** 连接器" 页上的说明进行操作：

    1. 配置 Syslog 服务器

        1. 如果还没有，请创建 Linux Syslog 服务器，使 Alsid for AD 向其发送日志。 Azure Sentinel 支持 **rsyslog** 和 **syslog-ng** 的守护程序。 

        1. 在单独的文件中，将 Syslog 服务器配置为输出 Alsid AD 日志。

    1. 将 Alsid 配置为将日志发送到 Syslog 服务器

        1. 在 **Alsid FOR AD** 门户上，依次参阅 *"系统*"、" *配置* " 和 " *Syslog*"。 从这里，你可以针对 Syslog 服务器创建新的 Syslog 警报。 对于远程服务器，请使用安装 Linux 代理的 Linux 计算机的 IP 地址。

        1. 检查是否在服务器上正确收集了日志 (为此，可以使用 Alsid for AD) *中的 "* **测试配置"** 按钮。

    1. 安装并载入适用于 Linux 的 Log Analytics 代理

        -  (物理或虚拟) 选择 Azure Linux VM 或非 Azure Linux 计算机。 按照屏幕上的链接和说明进行操作。 有关更多详细信息，请参阅 [配置 Linux 计算机或设备](connect-syslog.md#configure-your-linux-machine-or-appliance) 。

    1. 配置 Log Analytics 代理收集的日志

        - 选择工作区高级设置配置中的功能和严重性。

            1. 单击 "连接器" 页上的 " **打开工作区高级设置配置 >** " 链接。

            1. 在 " **高级设置** " 屏幕中，选择 " **数据** "，然后选择 " **自定义日志**"。

            1. 将 **以下配置标记为 "我的 linux 计算机** " 复选框，然后单击 " **添加**"。

            1. 单击 " **选择文件** "，上传运行 Syslog 服务器的 Linux 计算机上的 ALSID For AD Syslog 文件示例，然后单击 " **下一步**"。

            1. 选中 " **设置记录分隔符** " 设置为 " **新行** "，然后单击 " **下一步**"。

            1. 选择 " **Linux** " 并输入 Syslog 文件的文件路径，单击，然后单击 " **+** **下一步**"。

            1. 在 "名称" 字段中键入 " *AlsidForADLog* "，然后单击 " **完成** _CL"。
    
可能需要长达20分钟的时间，日志才会开始出现在 Log Analytics 中。

## <a name="find-your-data"></a>查找数据

成功建立连接后，数据将显示在 "**日志**" 中的 " *AlsidForADLog_CL* " 表中的 "**自定义日志**" 下。

此数据连接器依赖于基于 Kusto 函数的分析器来按预期方式工作。 使用以下步骤设置要在查询和工作簿中使用的 **afad_parser** Kusto 函数。

1. 在 Azure Sentinel 导航菜单中，选择 " **日志**"。

1. 复制以下查询并将其粘贴到查询窗口中。
    ```kusto
    let CodenameTable=datatable(Codename: string, Explanation: string) [
    "test-checker-codename", "This is a test checker",
    "", "Not an alert",
    "C-ADM-ACC-USAGE", "Recent use of the default administrator account",
    "C-UNCONST-DELEG", "Dangerous delegation",
    "C-PASSWORD-DONT-EXPIRE", "Accounts with never expiring passwords",
    "C-USERS-CAN-JOIN-COMPUTERS", "Users allowed to join computers to the domain",
    "C-CLEARTEXT-PASSWORD", "Potential clear-text password",
    "C-PROTECTED-USERS-GROUP-UNUSED", "Protected Users group not used",
    "C-PASSWORD-POLICY", "Weak password policies are applied on users",
    "C-GPO-HARDENING", "Domain without computer-hardening GPOs",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-AAD-CONNECT", "Verify permissions related to AAD Connect accounts",
    "C-AAD-SSO-PASSWORD", "Verify AAD SSO account password last change",
    "C-GPO-SD-CONSISTENCY", "Verify sensitive GPO objects and files permissions",
    "C-DSHEURISTICS", "Domain using a dangerous backward-compatibility configuration",
    "C-DOMAIN-FUNCTIONAL-LEVEL", "Domains have an outdated functional level",
    "C-DISABLED-ACCOUNTS-PRIV-GROUPS", "Disabled accounts in privileged groups",
    "C-DCSHADOW", "Rogue domain controllers",
    "C-DC-ACCESS-CONSISTENCY", "Domain controllers managed by illegitimate users",
    "C-DANGEROUS-TRUST-RELATIONSHIP", "Dangerous trust relationship",
    "C-DANGEROUS-SENSITIVE-PRIVILEGES", "Dangerous sensitive privileges",
    "C-DANG-PRIMGROUPID", "User Primary Group ID",
    "C-BAD-PASSWORD-COUNT", "Brute-force attack detection",
    "C-ADMINCOUNT-ACCOUNT-PROPS", "AdminCount attribute set on standard users",
    "C-ACCOUNTS-DANG-SID-HISTORY", "Accounts having a dangerous SID History attribute",
    "C-ABNORMAL-ENTRIES-IN-SCHEMA", "Dangerous rights in AD's schema",
    "C-GPOLICY-DISABLED-UNLINKED", "Unlinked, disabled or orphan GPO",
    "C-KERBEROS-CONFIG-ACCOUNT", "Kerberos configuration on user account",
    "C-KRBTGT-PASSWORD", "KDC password last change",
    "C-LAPS-UNSECURE-CONFIG", "Local administrative account management",
    "C-NATIVE-ADM-GROUP-MEMBERS", "Native administrative group members",
    "C-NETLOGON-SECURITY", "Unsecured configuration of Netlogon protocol",
    "C-OBSOLETE-SYSTEMS", "Computers running an obsolete OS",
    "C-PASSWORD-NOT-REQUIRED", "Account that might have an empty password",
    "C-PKI-WEAK-CRYPTO", "Use of weak cryptography algorithms into Active Directory PKI",
    "C-PRE-WIN2000-ACCESS-MEMBERS", "Accounts using a pre-Windows 2000 compatible access control",
    "C-PRIV-ACCOUNTS-SPN", "Privileged accounts running Kerberos services",
    "C-REVER-PWD-GPO", "Reversible passwords in GPO",
    "C-ROOTOBJECTS-SD-CONSISTENCY", "Root objects permissions allowing DCSync-like attacks",
    "C-SDPROP-CONSISTENCY", "Ensure SDProp consistency",
    "C-SENSITIVE-CERTIFICATES-ON-USER", "Ensure SDProp consistency",
    "C-SLEEPING-ACCOUNTS", "Sleeping accounts",
    "C-USER-PASSWORD", "User account using old password",
    "C-USERS-REVER-PWDS", "Reversible passwords"
    ];
    let Common = AlsidForADLog_CL
    | parse RawData with
                         Time:datetime  " "
                         Host:string  " "
                         Product:string "["
                         PID:int "]: \""
                         MessageType:int "\" \""
                         AlertID:int "\" \""
                         Forest:string "\" \""
                         Domain:string "\" "
                         DistinctPart:string;
    let Deviances = Common
    | where MessageType == 0 | parse DistinctPart with "\""
                         Codename:string "\" \""
                         Severity:string "\" \""
                         ADObject:string "\" \""
                         DevianceID:string "\" \""
                         ProfileID:string "\" \""
                         ReasonCodename:string "\" \""
                         EventID:string "\""
                         Attributes:string "\r\n";
    let Changes = Common
    | where MessageType == 1
    | parse kind=regex DistinctPart with "\""
                         ADObject:string "\" \""
                         EventID:string "\" \""
                         EventType:string "\" "
                         Attributes:string "\r?\n";
    union Changes, Deviances
    | project-away DistinctPart, Product, _ResourceId, _SubscriptionId
    | lookup kind=leftouter CodenameTable on Codename;
    ```

1. 单击 " **保存** " 下拉箭头，然后单击 " **保存**"。 在 " **保存** " 面板中，

    1. 在 " **名称**" 下，输入 **afad_parser**。

    1. 在 " **另存为**" 下选择 " **函数**"。

    1. 在 " **函数别名**" 下，输入 **afad_parser**。

    1. 在 " **类别**" 下输入 **函数**。

    1. 单击“保存”。

    函数应用通常需要10到15分钟的时间才能激活。

现在，可以通过 `afad_parser` 在 "查询" 窗口的顶部行中输入来查询 Alsid FOR AD data。

有关更多查询示例，请参阅连接器页中的 " **后续步骤** " 选项卡。

## <a name="next-steps"></a>后续步骤

本文档介绍了如何将 Alsid for AD 连接到 Azure Sentinel。 要详细了解 Azure Sentinel，请参阅以下文章：

- 了解如何了解 [你的数据和潜在威胁](quickstart-get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](tutorial-detect-threats-built-in.md)。
- [使用工作簿](tutorial-monitor-your-data.md)监视数据。
