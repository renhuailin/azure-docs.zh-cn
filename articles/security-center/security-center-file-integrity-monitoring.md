---
title: Azure 安全中心内的文件完整性监视
description: 使用本演练了解如何在 Azure 安全中心配置文件完整性监视 (FIM)。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/01/2021
ms.author: memildin
ms.openlocfilehash: 0632389800c4c524623314bcc50bb21a375e3855
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734874"
---
# <a name="file-integrity-monitoring-in-azure-security-center"></a>Azure 安全中心内的文件完整性监视
使用本演练了解如何在 Azure 安全中心配置文件完整性监视 (FIM)。


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|正式发布版 (GA)|
|定价：|需要[适用于服务器的 Azure Defender](defender-for-servers-introduction.md)。<br>FIM 将数据上传到 Log Analytics 工作区。 需要根据上传的数据量支付数据费用。 请参阅 [Log Analytics 定价](https://azure.microsoft.com/pricing/details/log-analytics/)了解详细信息。|
|所需角色和权限：|工作区所有者可以启用/禁用 FIM（有关详细信息，请参阅 [Log Analytics 的 Azure 角色](/services-hub/health/azure-roles#azure-roles)）。<br>读取者可以查看结果。|
|云：|:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 国家/地区/主权（Azure 政府、Azure 中国世纪互联）<br>仅在可以使用 Azure 自动化更改跟踪解决方案的区域中受支持。<br>:::image type="icon" source="./media/icons/yes-icon.png"::: [ 已启用 Azure Arc](../azure-arc/servers/overview.md) 的设备。<br>请参阅[链接的 Log Analytics 工作区支持的区域](../automation/how-to/region-mappings.md)。<br>[详细了解更改跟踪](../automation/change-tracking/overview.md)。|
|||

## <a name="what-is-fim-in-security-center"></a>安全中心内的 FIM 是什么？
文件完整性监视 (FIM)（也称为“更改监视”）检查操作系统文件、Windows 注册表、应用程序软件、Linux 系统文件等是否发生了可能表示受到攻击的变化。 

安全中心建议使用 FIM 监视实体，你也可以定义自己的 FIM 策略或要监视的实体。 发生如下所述的可疑活动时，FIM 将通知你：

- 创建或删除文件和注册表项
- 文件修改（文件大小、访问控制列表和内容哈希的更改）
- 注册表修改（大小、访问控制列表、类型和内容的更改）

在本教程中，将了解如何：

> [!div class="checklist"]
> * 查看建议使用 FIM 监视的实体列表
> * 定义自己的自定义 FIM 规则
> * 审核对受监视实体所做的更改
> * 使用通配符简化跨目录的跟踪


## <a name="how-does-fim-work"></a>FIM 的工作原理是什么？

FIM 将这些项的当前状态与上次扫描过程中的状态进行比较，如果发生了可疑的修改，FIM 将通知你。

FIM 使用 Azure 更改跟踪解决方案来跟踪和识别环境中发生的更改。 启用文件完整性监视后，会获得一个“解决方案”类型的“更改跟踪”资源 。 有关数据收集频率的详细信息，请参阅[更改跟踪数据收集详细信息](../automation/change-tracking/overview.md#change-tracking-and-inventory-data-collection)。

> [!NOTE]
> 如果删除该“更改跟踪”资源，则还将禁用安全中心内的文件完整性监视功能。

## <a name="which-files-should-i-monitor"></a>应监视哪些文件？

选择要监视的文件时，请考虑对系统和应用程序至关重要的文件。 监视预期不会在计划外发生更改的文件。 如果选择应用程序或操作系统经常更改的文件（例如日志文件和文本文件），则会造成很多的干扰，使攻击识别变得困难。

安全中心提供了以下建议项列表，用于根据已知攻击模式进行监视。

|Linux 文件|Windows 文件|Windows 注册表项 (HKLM = HKEY_LOCAL_MACHINE)|
|:----|:----|:----|
|/bin/login|C:\autoexec.bat|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/bin/passwd|C:\boot.ini|HKLM\SOFTWARE\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/*.conf|C:\config.sys|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\IniFileMapping\SYSTEM.ini\boot|
|/usr/bin|C:\Windows\system.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Windows|
|/usr/sbin|C:\Windows\win.ini|HKLM\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/bin|C:\Windows\regedit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|/sbin|C:\Windows\System32\userinit.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell 文件夹|
|/boot|C:\Windows\explorer.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\Run|
|/usr/local/bin|C:\Program Files\Microsoft Security Client\msseces.exe|HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnce|
|/usr/local/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunOnceEx|
|/opt/bin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServices|
|/opt/sbin||HKLM\SOFTWARE\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|/etc/crontab||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{C689AAB8-8E78-11D0-8C47-00C04FC295EE}|
|/etc/init.d||HKLM\SOFTWARE\WOW6432Node\Microsoft\Cryptography\OID\EncodingType 0\CryptSIPDllRemoveSignedDataMsg\{603BCC1F-4B59-4E08-B724-D2C6297EF351}|
|/etc/cron.hourly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\IniFileMapping\system.ini\boot|
|/etc/cron.daily||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Windows|
|/etc/cron.weekly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows NT\CurrentVersion\Winlogon|
|/etc/cron.monthly||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\Run|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnce|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunOnceEx|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServices|
|||HKLM\SOFTWARE\WOW6432Node\Microsoft\Windows\CurrentVersion\RunServicesOnce|
|||HKLM\SYSTEM\CurrentControlSet\Control\hivelist|
|||HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\KnownDLLs|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\DomainProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\PublicProfile|
|||HKLM\SYSTEM\CurrentControlSet\Services\SharedAccess\Parameters\FirewallPolicy\StandardProfile|


## <a name="enable-file-integrity-monitoring"></a>启用文件完整性监视 

只能通过 Azure 门户中的安全中心页面使用 FIM。 目前无法通过任何 REST API 使用 FIM。

1. 在“Azure Defender”仪表板的“高级保护”区域中，选择“文件完整性监视”  。

   :::image type="content" source="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png" alt-text="启动 FIM。" lightbox="./media/security-center-file-integrity-monitoring/open-file-integrity-monitoring.png":::

    此时会打开“文件完整性监视”配置页。

    为每个工作区提供了以下信息：

    - 过去一周发生的更改总数（如果未对该工作区启用 FIM，可能会看到短划线“-”）
    - 向工作区报告的计算机和 VM 总数
    - 工作区的地理位置
    - 工作区所在的 Azure 订阅

1. 使用此页可以：

    - 访问和查看每个工作区的状态与设置

    - ![“升级计划”图标。][4] 升级工作区以使用 Azure Defender。 此图标表示相应的工作区或订阅不受 Azure Defender 保护。 若要使用 FIM 功能，你的订阅必须受 Azure Defender 保护。 有关详细信息，请参阅 [Azure 安全中心免费版与已启用 Azure Defender 的版本的比较](security-center-pricing.md)。

    - ![启用图标][3] 对工作区下的所有计算机启用 FIM，并配置 FIM 选项。 此图标表示未对工作区启用 FIM。

        :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-list-fim.png" alt-text="对特定的工作区启用 FIM。":::


    > [!TIP]
    > 如果未显示启用或升级按钮，并且行尾空间是空白的，则表示已对工作区启用 FIM。


1. 选择“启用”。 此时将显示工作区的详细信息，包括工作区下的 Windows 和 Linux 计算机数目。

    :::image type="content" source="./media/security-center-file-integrity-monitoring/workspace-fim-status.png" alt-text="FIM 工作区详细信息页。":::

   此外，还会列出适用于 Windows 和 Linux 的建议设置。  展开“Windows 文件”、“注册表”和“Linux 文件”查看建议项的完整列表。  

1. 清除你不想要通过 FIM 监视的任何推荐实体对应的复选框。

1. 选择“应用文件完整性监视”以启用 FIM。

> [!NOTE]
> 随时可以更改设置。 请参阅下面的[编辑受监视的实体](#edit-monitored-entities)来了解详细信息。



## <a name="audit-monitored-workspaces"></a>审核受监视的工作区 

“文件完整性监视”仪表板显示已启用 FIM 的工作区。 对工作区启用 FIM 后或者在“文件完整性监视”窗口中选择已启用 FIM 的工作区时，FIM 仪表板将会打开。

:::image type="content" source="./media/security-center-file-integrity-monitoring/fim-dashboard.png" alt-text="FIM 仪表板及其各种信息面板。":::

工作区的 FIM 仪表板显示以下详细信息：

- 连接到该工作区的计算机总数
- 所选时间段内发生的更改总数
- 更改类型（文件、注册表）的细分
- 更改类别的（修改、添加、删除）细分

选择仪表板顶部的“筛选器”可以更改所显示的更改的时间段。

:::image type="content" source="./media/security-center-file-integrity-monitoring/dashboard-filter.png" alt-text="FIM 仪表板的时间段筛选器。":::

“服务器”选项卡列出向此工作区报告的计算机。 对于每台计算机，仪表板将会列出：

- 所选时间段内发生的更改总数
- 更改总数的细分，以文件更改数或注册表更改数的形式列出

选择计算机时，该查询将与结果一起显示，这些结果标识所选时间段内对计算机所进行的更改。 可以展开某项更改以查看其详细信息。

:::image type="content" source="./media/security-center-file-integrity-monitoring/query-machine-changes.png" alt-text="日志分析查询，其中显示了由 Azure 安全中心的文件完整性监视所标识的更改" lightbox="./media/security-center-file-integrity-monitoring/query-machine-changes.png":::

“更改”选项卡（如下所示）列出所选时间段内对该工作区所做的所有更改。 对于更改的每个实体，仪表板将会列出：

- 发生更改的计算机
- 更改类型（注册表或文件）
- 更改类别（修改、添加、删除）。
- 更改日期和时间

:::image type="content" source="./media/security-center-file-integrity-monitoring/changes-tab.png" alt-text="Azure 安全中心的文件完整性监视更改选项卡" lightbox="./media/security-center-file-integrity-monitoring/changes-tab.png":::

在搜索字段中输入某项更改，或选择“更改”选项卡下列出的某个实体时，“更改详细信息”将会打开。 

:::image type="content" source="./media/security-center-file-integrity-monitoring/change-details.png" alt-text="Azure 安全中心的文件完整性监视，其中显示了更改的详细信息窗格" lightbox="./media/security-center-file-integrity-monitoring/change-details.png":::

## <a name="edit-monitored-entities"></a>编辑受监视的实体

1. 从工作区的文件完整性监视仪表板的工具栏中，选择“设置” 。 

    :::image type="content" source="./media/security-center-file-integrity-monitoring/file-integrity-monitoring-dashboard-settings.png" alt-text="访问工作区的文件完整性监视设置。" lightbox="./media/security-center-file-integrity-monitoring/file-integrity-monitoring-dashboard-settings.png":::

   “工作区配置”打开，并显示可监视的各类元素的选项卡：

      - Windows 注册表
      - Windows 文件
      - Linux 文件
      - 文件内容
      - Windows 服务

      每个选项卡列出可在该类别中编辑的实体。 对于列出的每个实体，安全中心会指出是已启用 (true) 还是未启用 (false) FIM。  编辑实体可以启用或禁用 FIM。

    :::image type="content" source="./media/security-center-file-integrity-monitoring/file-integrity-monitoring-workspace-configuration.png" alt-text="Azure 安全中心中文件完整性监视的工作区配置。":::

1. 从其中一个选项卡选择一个条目，然后在“编辑更改跟踪”窗格中编辑任意可用字段。 选项包括：

    - 启用 (True) 或禁用 (False) 文件完整性监视
    - 提供或更改实体名称
    - 提供或更改值或路径
    - 删除实体

1. 放弃或保存更改。


## <a name="add-a-new-entity-to-monitor"></a>添加要监视的新实体

1. 在工作区的文件完整性监视仪表板中，从工具栏中选择“设置”。 

    “工作区配置”随即打开。

1. 在“工作区配置”上：

    1. 选择要添加的实体类型的选项卡：Windows 注册表、Windows 文件、Linux 文件、文件内容或 Windows 服务。 
    1. 选择 **添加** 。 

        在此示例中，我们选择了“Linux 文件”。

        :::image type="content" source="./media/security-center-file-integrity-monitoring/file-integrity-monitoring-add-element.png" alt-text="在 Azure 安全中心的文件完整性监视中添加要监视的元素" lightbox="./media/security-center-file-integrity-monitoring/file-integrity-monitoring-add-element.png":::

1. 选择“添加”  。 此时会打开“更改跟踪的添加”。

1. 输入所需信息，然后选择“保存”。

## <a name="folder-and-path-monitoring-using-wildcards"></a>使用通配符监视文件夹和路径

可使用通配符简化跨目录的跟踪。 使用通配符配置文件夹监视时，以下规则适用：
-   跟踪多个文件需要使用通配符。
-   只能在路径的最后一段中使用通配符，例如 C:\folder\file 或 /etc/*.conf
-   如果环境变量包含无效的路径，验证将成功，但运行清单时，该路径将失败。
-   设置路径时，请避免使用 c:\*.* 等常规路径，因为这会导致遍历过多的文件夹。

## <a name="disable-fim"></a>禁用 FIM
可以禁用 FIM。 FIM 使用 Azure 更改跟踪解决方案来跟踪和识别环境中发生的更改。 禁用 FIM 会从所选工作区中删除“更改跟踪”解决方案。

若要禁用 FIM：

1. 从工作区的文件完整性监视仪表板中，选择“禁用” 。

    :::image type="content" source="./media/security-center-file-integrity-monitoring/disable-file-integrity-monitoring.png" alt-text="从设置页面禁用文件完整性监视。":::

1. 选择“删除” 。

## <a name="next-steps"></a>后续步骤
在本文中，你已了解如何在安全中心使用文件完整性监视 (FIM)。 若要了解有关安全中心的详细信息，请参阅以下页面：

* [设置安全策略](tutorial-security-policy.md) -- 了解如何为 Azure 订阅和资源组配置安全策略。
* [管理安全建议](security-center-recommendations.md) -- 了解建议如何帮助你保护 Azure 资源。
* [Azure 安全博客](/archive/blogs/azuresecurity/) - 获取最新的 Azure 安全新闻和信息。

<!--Image references-->
[3]: ./media/security-center-file-integrity-monitoring/enable.png
[4]: ./media/security-center-file-integrity-monitoring/upgrade-plan.png