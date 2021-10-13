---
title: Azure 安全中心的自动部署代理 |Microsoft Docs
description: 本文介绍如何设置 Azure 安全中心使用的 Log Analytics 代理和其他代理及扩展的自动预配
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: quickstart
ms.date: 10/08/2021
ms.author: memildin
ms.openlocfilehash: 58f4ce3a7b7faeec729f0b56cad6adb23a0cd2f2
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129713726"
---
# <a name="configure-auto-provisioning-for-agents-and-extensions-from-azure-security-center"></a>从 Azure 安全中心为代理和扩展配置自动预配

Azure 安全中心使用资源的相关代理或扩展以及已启用的数据收集类型从资源中收集数据。 使用以下过程来确保资源具有安全中心所需的代理和扩展。

## <a name="prerequisites"></a>先决条件
若要开始使用安全中心，必须具有 Microsoft Azure 订阅。 如果没有订阅，可以注册[免费帐户](https://azure.microsoft.com/pricing/free-trial/)。

## <a name="availability"></a>可用性

| 方面                  | 详细信息                                                                                                                                                                                                                      |
|-------------------------|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 发布状态：          | **功能**：自动预配功能已正式发布 (GA)<br>代理和扩展：适用于 Azure VM 的 Log Analytics 代理已正式发布，Microsoft Dependency Agent 为预览版，适用于 Kubernetes 的策略加载项已正式发布，来宾配置代理为预览版  |
| 定价：                | 免费                                                                                                                                                                                                                         |
| 支持的目标： | :::image type="icon" source="./media/icons/yes-icon.png":::Azure 计算机<br>:::image type="icon" source="./media/icons/no-icon.png":::Azure Arc 计算机<br>:::image type="icon" source="./media/icons/no-icon.png":::Kubernetes 节点<br>:::image type="icon" source="./media/icons/no-icon.png":::虚拟机规模集 |
| 云：                 | **功能**：<br>:::image type="icon" source="./media/icons/yes-icon.png"::: 商用云<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure 政府、Azure 中国世纪互联<br>**代理和扩展**：<br>适用于 Azure VM 的 Log Analytics 代理在所有云上可用，适用于 Kubernetes 的策略加载项在所有云上可用，来宾配置代理仅在商业云上可用  |
|                         |                                                                                                                                                                                                                              |

## <a name="how-does-security-center-collect-data"></a>安全中心如何收集数据？

安全中心从 Azure 虚拟机 (VM)、虚拟机规模集、IaaS 容器和非 Azure 计算机（包括本地计算机）收集数据，以监视安全漏洞和威胁。 

必须收集数据才能深入了解缺少的更新、配置不当的 OS 安全设置、终结点保护状态，以及运行状况和威胁防护结果。 只需对计算资源（例如 VM、虚拟机规模集、IaaS 容器和非 Azure 计算机）启用数据收集。 

即使未预配代理，也可以从 Azure 安全中心受益。 但是，你的安全性有限，并且不支持上面列出的功能。  

使用以下工具收集数据：

- Log Analytics 代理，该代理从计算机中读取各种与安全相关的配置和事件日志，然后将数据复制到工作区进行分析。 此类数据的示例包括：操作系统类型和版本、操作系统日志（Windows 事件日志）、正在运行的进程、计算机名称、IP 地址和已登录的用户。
- 安全扩展插件，如[用于 Kubernetes 的 Azure Policy 的加载项](../governance/policy/concepts/policy-for-kubernetes.md)，它还可以向安全中心提供有关专用资源类型的数据。

> [!TIP]
> 随着安全中心的发展，可以监视的资源类型也在增加。 扩展的数量也有所增加。 自动预配工具已扩展，可通过利用 Azure Policy 的功能来支持更多资源类型。

## <a name="why-use-auto-provisioning"></a>为什么要使用自动预配？
此页所述的任何代理和扩展都可以手动安装（请参阅[手动安装 Log Analytics 代理](#manual-agent)）。 但是，自动预配通过在现有和新计算机上安装所有必需的代理和扩展来确保所有受支持的资源能够更快地获得安全保障，从而减少管理开销。 

建议启用自动预配，但默认情况下它是禁用的。

## <a name="how-does-auto-provisioning-work"></a>自动预配的工作原理
对于每种支持的扩展类型，安全中心的自动预配设置都有相应的切换开关。 启用扩展的自动配置后，可以分配适当的“如果不存在则部署”策略。 此策略类型可确保在该类型的所有现有和将来的资源上预配扩展。

> [!TIP]
> 可参阅[了解 Azure Policy 效果](../governance/policy/concepts/effects.md)，了解有关 Azure Policy 效果的详细信息，包括“如果不存在则部署”。


## <a name="enable-auto-provisioning-of-the-log-analytics-agent-and-extensions"></a>启用 Log Analytics 代理和扩展 <a name="auto-provision-mma"></a> 的自动预配

为 Log Analytics 代理启用自动预配后，安全中心可在所有受支持的 Azure VM 以及创建的所有新 Azure VM 上部署代理。 有关支持的平台列表，请参阅 [Azure 安全中心支持的平台](security-center-os-coverage.md)。

若要启用 Log Analytics 代理的自动预配：

1. 从安全中心的菜单中，选择“定价和设置”。
1. 选择相关订阅。
1. 在“自动预配”页中，将 Log Analytics 代理的自动预配状态设置为“打开” 。

    :::image type="content" source="./media/security-center-enable-data-collection/enable-automatic-provisioning.png" alt-text="启用 Log Analytics 代理的自动预配。" lightbox="./media/security-center-enable-data-collection/enable-automatic-provisioning.png":::

1. 在配置选项窗格中，定义要使用的工作区。

    :::image type="content" source="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png" alt-text="用于将 Log Analytics 代理自动预配到 VM 的配置选项。" lightbox="./media/security-center-enable-data-collection/log-analytics-agent-deploy-options.png":::

    - **将 Azure VM 连接到安全中心创建的默认工作区** - 安全中心在同一地理位置创建新的资源组和默认工作区，并将代理连接到该工作区。 如果订阅包含多个地理位置中的 VM，安全中心会创建多个工作区，以确保符合数据隐私要求。

        工作区和资源组的命名约定是： 
        - 工作区：DefaultWorkspace-[subscription-ID]-[geo] 
        - 资源组：DefaultResourceGroup-[geo] 

        安全中心会根据针对订阅设置的定价层，在工作区中自动启用安全中心解决方案。 

        > [!TIP]
        > 有关默认工作区的问题，请参阅：
        >
        > - [安全中心创建的工作区中的 Azure Monitor 日志是否会产生费用？](./faq-data-collection-agents.yml#am-i-billed-for-azure-monitor-logs-on-the-workspaces-created-by-security-center-)
        > - [创建的默认 Log Analytics 工作区的位置是哪里？](./faq-data-collection-agents.yml#where-is-the-default-log-analytics-workspace-created-)
        > - [是否可以删除安全中心创建的默认工作区？](./faq-data-collection-agents.yml#can-i-delete-the-default-workspaces-created-by-security-center-)

    - **将 Azure VM 连接到不同的工作区** - 从下拉列表中，选择用于存储收集的数据的工作区。 下拉列表包含所有订阅中的所有工作区。 你可以使用此选项从在不同订阅中运行的虚拟机收集数据，并将其全部存储在所选工作区中。  

        如果已有一个 Log Analytics 工作区，可以使用该工作区（需要工作区上的读取和写入权限）。 如果在组织中使用集中式工作区，并想要使用该工作区来收集安全数据，则此选项非常有用。 从[在 Azure Monitor 中管理对日志数据和工作区的访问](../azure-monitor/logs/manage-access.md)中了解详细信息。

        如果所选的工作区中已启用“Security”或“SecurityCenterFree”解决方案，则会自动设置定价。 如果没有，请在工作区中安装安全中心解决方案：

        1. 从安全中心的菜单中，打开“定价和设置”。
        1. 选择要将代理连接到的工作区。
        1. 选择“启用 Azure Defender”或“关闭 Azure Defender”。

1. 从“Windows 安全事件”配置中，选择要存储的原始事件数据量：
    - **无** - 禁用安全事件存储。 这是默认设置。
    - **最小** - 一个小事件集，适合在希望最大程度地减小事件量时使用。
    - **通用** - 一个事件集，可满足大多数客户的需求，并提供完整的审核线索。
    - 所有事件 - 适用于想要确保存储所有事件的客户。

    > [!TIP]
    > 若要在工作区级别设置这些选项，请参阅[在工作区级别设置安全事件选项](#setting-the-security-event-option-at-the-workspace-level)。
    > 
    > 有关这些选项的详细信息，请参阅 [Log Analytics 代理的 Windows 安全事件选项](#data-collection-tier)。

1. 选择“配置”窗格中的“应用”。

1. 若要启用除 Log Analytics 代理之外的扩展的自动预配，请执行以下操作： 

    1. 如果要为 Microsoft Dependency Agent 启用自动预配，请确保将 Log Analytics 代理设置为自动部署。
    1. 将相关扩展的状态切换为“打开”。

        :::image type="content" source="./media/security-center-enable-data-collection/toggle-kubernetes-add-on.png" alt-text="切换以启用 K8s 策略加载项的自动预配。":::

    1. 选择“保存”。 分配 Azure Policy 定义并创建修正任务。

        |分机  |策略  |
        |---------|---------|
        |适用于 Kubernetes 的策略加载项                      |[将 Azure Policy 加载项部署到 Azure Kubernetes 服务群集](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fa8eff44f-8c92-45c3-a3fb-9880802d67a7)|
        |Microsoft Dependency Agent（预览）(Windows VM)|[为 Windows 虚拟机部署 Dependency Agent](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f1c210e94-a481-4beb-95fa-1571b434fb04)         |
        |Microsoft Dependency Agent（预览）(Linux VM)  |[为 Linux 虚拟机部署 Dependency Agent](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da21710-ce6f-4e06-8cdb-5cc4c93ffbee)|
        |来宾配置代理（预览版）               |[部署先决条件以在虚拟机上启用来宾配置策略](https://github.com/Azure/azure-policy/blob/64dcfa3033a3ff231ec4e73d2c1dad4db4e3b5dd/built-in-policies/policySetDefinitions/Guest%20Configuration/GuestConfiguration_Prerequisites.json)|
        |||

1. 选择“保存”。 如果需要设置工作区，那么代理安装过程可能需要最多 25 分钟的时间。

1. 系统会询问你是否要重新配置之前已连接到默认工作区的受监视 VM：

    :::image type="content" source="./media/security-center-enable-data-collection/reconfigure-monitored-vm.png" alt-text="查看用于重新配置受监视的 VM 的选项。":::

    - **否** - 新的工作区设置将仅应用于未安装 Log Analytics 代理的新发现的 VM。
    - **是** - 新的工作区设置将应用于所有 VM，当前连接到安全中心创建的工作区的每个 VM 都将重新连接到新的目标工作区。

   > [!NOTE]
   > 如果选择“是”，请不要删除安全中心创建的工作区，除非所有 VM 都已重新连接到新的目标工作区。 如果过早删除工作区，此操作将会失败。


## <a name="windows-security-event-options-for-the-log-analytics-agent"></a>Log Analytics 代理的 Windows 安全事件选项 <a name="data-collection-tier"></a> 

在 Azure 安全中心选择数据收集层只会影响 Log Analytics 工作区中安全事件的存储。 无论你选择在工作区中存储哪一级别的安全事件，Log Analytics 代理仍将收集和分析安全中心威胁防护所需的安全事件。 选择存储安全事件可以在工作区中调查、搜索和审核这些事件。

### <a name="requirements"></a>要求 
需要 Azure Defender 才能存储 Windows 安全事件数据。 [详细了解 Azure Defender](azure-defender.md)。

在 Log Analytics 中存储数据可能会产生额外的数据存储费用。 有关详细信息，请参阅[定价页](https://azure.microsoft.com/pricing/details/security-center/)。

### <a name="information-for-azure-sentinel-users"></a>Azure Sentinel 用户的信息 
Azure Sentinel 的用户：请注意，可以从 Azure 安全中心或 Azure Sentinel 配置单个工作区上下文中的安全事件集合，但不能同时从这两者进行配置。 如果你计划将 Azure Sentinel 添加到已从 Azure 安全中心获得警报并设置为收集安全事件的工作区，你有两种选择：
- 保留 Azure 安全中心的安全事件集合不变。 你将能够在 Azure Sentinel 以及 Azure Defender 中查询和分析这些事件。 但是，你将不能监视连接器的连接状态或在 Azure Sentinel 中更改其配置。 如果这对你很重要，请考虑第二种选择。
- 禁用 Azure 安全中心中的安全事件集合（在 Log Analytics 代理的配置中将“Windows 安全事件”设置为“无”） 。 然后在 Azure Sentinel 中添加安全事件连接器。 与第一种选择一样，你将能够在 Azure Sentinel 和 Azure Defender/ASC 中查询和分析事件，但现在你将能够监视连接器的连接状态或在且仅在 Azure Sentinel 中更改其配置。

### <a name="what-event-types-are-stored-for-common-and-minimal"></a>哪些类型的事件存储为“通用”和“最小”？
这些集合专门用于典型应用场景。 请务必先评估哪个事件集适合你的需求，再进行实现。

为了确定“通用”和“最小”选项的事件，我们与客户进行协作，参照行业标准，了解了每个事件及其使用情况的未筛选频率 。 我们在此过程中使用了以下准则：

- 最小 - 确保此集只涵盖可能指示成功违反的事件以及数量很少的重要事件。 例如，此集包含用户成功和失败的登录（事件 ID 4624 和 4625），但不包含对审核很重要但对检测毫无意义且数量相对较多的注销。 此集的大多数数据量是登录事件和进程创建事件（事件 ID 4688）。
- 通用 - 提供此集中的完整用户审核跟踪。 例如，此集包含用户登录和用户注销（事件 ID 4634）。 我们加入审核操作，如安全组更改、关键域控制器 Kerberos 操作以及行业组织建议的其他事件。

数量非常少的事件包含在通用集中，因为在所有事件中选择该集的主要动机是为了减少数量，而不是筛选出特定事件。

下面是对每个集的安全和 App Locker 事件 ID 的完整分类：

| 数据层 | 收集的事件指示器 |
| --- | --- |
| 最少 | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| 通用 | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - 如果使用组策略对象 (GPO)，建议启用审核策略过程创建事件 4688 以及事件 4688 内的 CommandLine 字段。 有关过程创建事件 4688 的详细信息，请参阅安全中心的[常见问题解答](./faq-data-collection-agents.yml#what-happens-when-data-collection-is-enabled-)。 有关这些审核策略的详细信息，请参阅[审核策略建议](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)。
> -  若要为[自适应应用程序控件](security-center-adaptive-application.md)启用数据收集，安全中心会在审核模式下配置本地 AppLocker 策略以允许所有应用程序。 这将导致 AppLocker 生成事件，然后由安全中心收集和利用这些事件。 请务必注意，不会在已配置 AppLocker 策略的任何计算机上配置此策略。 
> - 若要收集 Windows 筛选平台[事件 ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156)，需要启用[审核筛选平台连接](/windows/security/threat-protection/auditing/audit-filtering-platform-connection) (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable)
>

### <a name="setting-the-security-event-option-at-the-workspace-level"></a>在工作区级别设置安全事件选项

可以将安全事件数据的存储级别定义为工作区级别。

1. 在 Azure 门户的“安全中心”菜单中，选择“定价和设置”。
1. 选择相关工作区。 工作区的唯一数据收集事件是此页上描述的 Windows 安全事件。

    :::image type="content" source="media/security-center-enable-data-collection/event-collection-workspace.png" alt-text="设置要存储在工作区中的安全事件数据。":::

1. 选择要存储的原始事件数据量，然后选择“保存”。

## <a name="manual-agent-provisioning"></a>手动代理预配 <a name="manual-agent"></a>
 
若要手动安装 Log Analytics 代理：

1. 禁用自动预配。

1. （可选）创建工作区。

1. 在要安装 Log Analytics 代理的工作区上启用 Azure Defender：

    1. 在安全中心的菜单中，选择“定价和设置”。

    1. 设置要安装代理的工作区。 确保该工作区位于安全中心内所用的同一个订阅中，并且你具有该工作区的读/写权限。

    1. 选择“启用 Azure Defender”，然后“保存” 。

       >[!NOTE]
       >如果工作区中已启用 **Security** 或 **SecurityCenterFree** 解决方案，则会自动设置定价层。 

1. 若要使用资源管理器模板在新的 VM 上部署代理，请安装 Log Analytics 代理：

   - [安装适用于 Windows 的 Log Analytics 代理](../virtual-machines/extensions/oms-windows.md)
   - [安装适用于 Linux 的 Log Analytics 代理](../virtual-machines/extensions/oms-linux.md)

1. 若要在现有 VM 上部署代理，请按照[收集有关 Azure 虚拟机的数据](../azure-monitor/vm/monitor-virtual-machine.md)（“收集事件和性能数据”部分为可选）中的说明进行操作。

1. 若要使用 PowerShell 部署代理，请按照虚拟机文档中的说明进行操作：

    - [对于 Windows 计算机](../virtual-machines/extensions/oms-windows.md?toc=%2fazure%2fazure-monitor%2ftoc.json#powershell-deployment)
    - [对于 Linux 计算机](../virtual-machines/extensions/oms-linux.md?toc=%2fazure%2fazure-monitor%2ftoc.json#azure-cli-deployment)

> [!TIP]
> 有关如何使用 PowerShell 加入安全中心的说明，请参阅[使用PowerShell 自动加入 Azure 安全中心](security-center-powershell-onboarding.md)。


## <a name="automatic-provisioning-in-cases-of-a-pre-existing-agent-installation"></a>在预先安装了代理的情况下进行自动预配 <a name="preexisting"></a> 

以下用例指定在已安装代理或扩展的情况下如何进行自动预配。 

- **Log Analytics 代理已安装在计算机上，但不是作为扩展（直接代理）安装的** - 如果 Log Analytics 代理直接安装在 VM 上（而不是作为 Azure 扩展安装），安全中心将安装 Log Analytics 代理扩展，可能还会将 Log Analytics 代理升级到最新版本。
安装的代理将继续向其已配置的工作区报告，此外，它还会向安全中心上配置的工作区报告（Windows 计算机支持多主页）。
如果配置的工作区是用户工作区（而不是安全中心的默认工作区），则需要在该工作区上安装“Security”或“SecurityCenterFree”解决方案，以便安全中心开始处理向该工作区报告的 VM 和计算机中的事件。

    对于 Linux 计算机，尚不支持代理多主页，因此，如果检测到现有的代理安装，则不会进行自动预配，并且不会更改计算机的配置。

    对于在 2019 年 3 月 17 日之前载入到安全中心的订阅上的现有计算机，检测到现有代理时，将不会安装 Log Analytics 代理扩展，并且计算机将不会受到影响。 对于这些计算机，请参阅“解决计算机上的监视代理运行状况问题”建议，以解决这些计算机上的代理安装问题。
  
- **已在计算机上安装 System Center Operations Manager 代理** - 安全中心会将 Log Analytics 代理扩展并行安装到现有 Operations Manager。 现有 Operations Manager 代理将继续正常向 Operations Manager 服务器报告。 Operations Manager 代理和 Log Analytics 代理共享公共运行时库，在此过程中这些库将更新为最新版本。 如果已安装 Operations Manager 代理版本 2012，则 **请勿** 启用自动预配。

- **存在预先存在的 VM 扩展**：
    - 当将监视代理作为扩展安装时，扩展配置仅允许向单个工作区进行报告。 安全中心不会覆盖用户工作区的现有连接。 如果已连接的工作区中安装了“Security”或“SecurityCenterFree”解决方案，安全中心会将来自 VM 的安全性数据存储在该工作区中。 在此过程中，安全中心可以将扩展版本升级到最新版本。
    - 若要查看现有扩展将数据发送到哪个工作区，请运行测试来[验证与 Azure 安全中心的连接](/archive/blogs/yuridiogenes/validating-connectivity-with-azure-security-center)。 或者，可以打开 Log Analytics 工作区，选择一个工作区，选择 VM，然后查看 Log Analytics 代理连接。
    - 如果环境中的 Log Analytics 代理安装在客户端工作站上并向现有的 Log Analytics 工作区报告，请查看 [Azure 安全中心支持的操作系统](security-center-os-coverage.md)列表以确保操作系统受支持。 有关详细信息，请参阅[现有 Log Analytics 客户](./faq-azure-monitor-logs.yml)。
 

## <a name="disable-auto-provisioning"></a>禁用自动预配 <a name="offprovisioning"></a>

禁用自动预配后，将不会在新 VM 上预配代理。

若要关闭代理的自动预配：

1. 在门户的“安全中心”菜单中，选择“定价和设置”。
1. 选择相关订阅。
1. 选择“自动预配”。
1. 将相关代理的状态切换为“关闭”。

    :::image type="content" source="./media/security-center-enable-data-collection/agent-toggles.png" alt-text="切换以禁用按代理类型的自动预配。":::

1. 选择“保存”。 自动预配处于禁用状态时，不会显示默认的工作区配置部分：

    :::image type="content" source="./media/security-center-enable-data-collection/empty-configuration-column.png" alt-text="禁用自动预配后，配置单元为空":::


> [!NOTE]
>  禁用自动预配不会从曾预配了 Log Analytics 代理的 Azure VM 中删除该代理。 有关删除 OMS 扩展的信息，请参阅[如何删除安全中心安装的 OMS 扩展](./faq-data-collection-agents.yml#how-do-i-remove-oms-extensions-installed-by-security-center-)。
>


## <a name="troubleshooting"></a>疑难解答

-   若要识别自动预配安装问题，请参阅[监视代理运行状况问题](security-center-troubleshooting-guide.md#mon-agent)。
-  若要确定监视代理网络要求，请参阅[监视代理网络要求故障排除](security-center-troubleshooting-guide.md#mon-network-req)。
-   若要识别手动加入问题，请参阅[如何排查 Operations Management Suite 加入问题](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues)。



## <a name="next-steps"></a>后续步骤
此页说明了如何为 Log Analytics 代理和其他安全中心扩展启用自动预配。 还介绍了如何定义用于存储所收集数据的 Log Analytics 工作区。 这两项操作都需要启用数据收集。 如果将数据存储在 Log Analytics 中，无论是使用新工作区还是现有工作区，都可能会产生更多的数据存储费用。 有关所选货币以及你所在区域的定价详细信息，请参阅[安全中心定价](https://azure.microsoft.com/pricing/details/security-center/)。