---
title: 将威胁情报导入 Azure Sentinel | Microsoft Docs
description: 使用 Azure Sentinel 中的威胁情报源来分析数据、检测威胁并生成警报和事件。 利用工作簿直观显示威胁情报信息。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/26/2020
ms.author: yelevin
ms.openlocfilehash: 4936cf0dd399f0409332dc2dea2efb8dc9cd8299
ms.sourcegitcommit: ce9178647b9668bd7e7a6b8d3aeffa827f854151
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2021
ms.locfileid: "109810221"
---
# <a name="import-threat-intelligence-into-azure-sentinel"></a>将威胁情报导入 Azure Sentinel

## <a name="introduction-to-threat-intelligence"></a>威胁情报简介

网络威胁情报 (CTI) 是描述系统和用户的已知现有或潜在威胁的信息。 这类信息采用多种形式，包括详细描述特定威胁参与者的动机、基础结构和技术的书面报告，以及与网络威胁关联的 IP 地址、域和文件哈希的特定观察结果。 组织使用 CTI 为异常活动提供必要的背景信息，以便安全负责人可以快速采取措施来保护其人员和资产。 CTI 的来源有很多，例如开源数据源、威胁情报共享社区、商业情报源，以及在组织内部安全调查中收集的本地情报。

在 Azure Sentinel 等安全信息和事件管理 (SIEM) 解决方案中，最广泛利用的 CTI 形式是威胁指标，通常称为泄露迹象 (IoC)。 威胁指标是将观察结果（如 URL、文件哈希或 IP 地址）与已知威胁活动（如钓鱼、僵尸网络或恶意软件）相关联的数据。 这种形式的威胁情报通常称为战术性威胁情报，因为它可以大规模地应用于安全产品和自动化服务，以防范和检测组织面临的潜在威胁。 在 Azure Sentinel 中，你可以使用威胁指标来帮助检测在环境中观测到的恶意活动，并向安全调查人员提供上下文以帮助其决定如何响应。

可以通过以下活动将威胁情报 (TI) 集成到 Azure Sentinel 中：

- 使用面向各种 TI 平台的数据连接器，[将威胁情报导入](./connect-threat-intelligence.md) Azure Sentinel。
- 在“日志”和 Azure Sentinel 的新“威胁情报”区域中查看和管理导入的威胁情报。
- 使用内置的“分析”规则模板，根据导入的威胁情报生成安全警报和事件。
- 利用“威胁情报”工作簿直观显示 Azure Sentinel 中威胁情报的关键信息。

威胁情报还在其他 Azure Sentinel 体验（如“搜寻”和“Notebooks”）中提供有用的背景信息，虽然本文并未涉及，但 [Ian Hellen 关于 Azure Sentinel 中的 Jupyter Notebook 的精彩博文](https://techcommunity.microsoft.com/t5/azure-sentinel/using-threat-intelligence-in-your-jupyter-notebooks/ba-p/860239)中介绍了这些体验，其中包括 Notebooks 中 CTI 的使用。

## <a name="azure-sentinel-data-connectors-for-threat-intelligence"></a>用于威胁情报的 Azure Sentinel 数据连接器

就像 Azure Sentinel 中的所有其他事件数据一样，威胁指标使用数据连接器导入。 Azure Sentinel 有两个专门为威胁指标提供的数据连接器：威胁情报 - TAXII 和威胁情报平台。 你可以单独使用其中一个数据连接器，也可以两个连接器一起使用，这取决于你的组织从哪里获得威胁指标。 

请参阅 Azure Sentinel 提供的 [威胁情报集成](threat-intelligence-integration.md) 目录。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence-platforms-data-connector"></a>使用“威胁情报平台”数据连接器向 Azure Sentinel 添加威胁指标

许多组织使用威胁情报平台 (TIP) 解决方案从各种来源汇总威胁指标源，在平台内策展数据，然后选择要应用于各种安全解决方案（如网络设备、高级威胁防护解决方案或像 Azure Sentinel 这样的 SIEM）的威胁指标。 如果组织使用集成式 TIP 解决方案，如 MISP、Anomali ThreatStream、ThreatConnect、EclecticIQ Platform、ThreatQ 威胁情报平台或 Palo Alto Networks 的 MineMeld，“威胁情报平台”数据连接器允许你使用 TIP 将威胁指标导入 Azure Sentinel。 由于该连接器与 [Microsoft Graph Security tiIndicators API](/graph/api/resources/tiindicator) 合作完成此操作，因此，任何自定义威胁情报平台也可以使用该连接器，利用 tiIndicators API 将指标发送到 Azure Sentinel（以及 Defender ATP 等其他 Microsoft 安全解决方案）。

:::image type="content" source="media/import-threat-intelligence/threat-intel-import-path.png" alt-text="威胁情报导入路径":::

按照以下步骤，从集成式 TIP 或自定义威胁情报解决方案向 Azure Sentinel 导入威胁指标：

1. 从 Azure Active Directory 获取应用程序 ID 和客户端密码

1. 将此信息输入到 TIP 解决方案或自定义应用程序中

1. 在 Azure Sentinel 中启用“威胁情报平台”数据连接器

下面详细介绍了每个步骤。

#### <a name="obtain-an-application-id-and-client-secret-from-your-azure-active-directory"></a>从 Azure Active Directory 获取应用程序 ID 和客户端密码

无论你是使用 TIP 还是使用自定义解决方案，tiIndicators API 都需要一些基本信息来连接和发送威胁指标。 你需要获取的三项信息为：
- 应用程序（客户端）ID
- 目录（租户）ID
- 客户端机密

这些信息始终来自 Azure Active Directory，由一个称为应用注册的过程产生，该过程包括以下三个步骤：
- 向 Azure Active Directory 注册应用
- 指定应用连接到 Microsoft Graph tiIndicators API 并发送威胁指标所需的权限
- 获得组织同意，向此应用程序授予这些权限。  

**向 Azure Active Directory 注册应用程序**

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Active Directory 服务。

1. 从菜单中选择“应用注册”，然后选择“新建注册”。

1. 为应用程序注册选择一个名称，选择“单租户”单选按钮，然后选择“注册”。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-register-application.png" alt-text="注册应用程序":::

1. 从随后出现的屏幕中复制“应用程序(客户端) ID”和“目录(租户) ID”值。 这是你以后配置 TIP 或自定义解决方案以向 Azure Sentinel 发送威胁指标时需要的前两项信息。

**指定应用程序所需的权限**

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Active Directory 服务。

1. 从菜单中选择“应用注册”，然后选择新注册的应用。

1. 从菜单中选择“API 权限”，然后单击“添加权限”按钮。

1. 在“选择 API”页面上选择“Microsoft Graph”，以从 Microsoft Graph 权限列表中进行选择。

1. 当系统询问“应用程序需要哪种类型的权限?”时，选择“应用程序权限”。 这是通过应用 ID 和应用机密（API 密钥）进行身份验证的应用程序所使用的权限类型。

1. 选择“ThreatIndicators.ReadWrite.OwnedBy”，然后选择“添加权限”，以将此权限添加到应用的权限列表中。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-1.png" alt-text="指定权限":::

**获得组织同意以授予这些权限**

1. 若要授予同意，你需要以 Azure Active Directory 全局管理员的身份在应用的 API 权限页面上选择“代表租户授予管理员同意”按钮。 如果你的帐户上没有全局管理员角色，此按钮将不可用，你需要请组织的全局管理员执行此步骤。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-api-permissions-2.png" alt-text="授予许可":::

1. 向应用授予同意后，“状态”下应该会出现一个绿色复选标记。
 
现在，应用已注册并授予权限，你可以在列表中找到最后一项信息 - 应用的客户端密码。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Active Directory 服务。

1. 从菜单中选择“应用注册”，然后选择新注册的应用。

1. 从菜单中选择“证书和机密”，然后单击“新建客户端密码”按钮以获取应用机密（API 密钥）。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-client-secret.png" alt-text="获取客户端密码":::

1. 单击“添加”按钮，并确保复制客户端密码，因为如果离开此页面，你将无法再次检索此密码。 配置 TIP 或自定义解决方案时需要此值。

#### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>将此信息输入到 TIP 解决方案或自定义应用程序中

现在，你已获得配置 TIP 或自定义解决方案以向 Azure Sentinel 发送威胁指标所需的所有信息。 
- 应用程序（客户端）ID
- 目录（租户）ID
- 客户端机密

在需要的情况下，在集成式 TIP 或自定义解决方案的配置中输入这些值，威胁指标将通过 Microsoft Graph tiIndicators API 发送到 Azure Sentinel。

#### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中启用“威胁情报平台”数据连接器

集成过程的最后一步是在 Azure Sentinel 中启用“威胁情报平台”数据连接器。 此步骤通过 Microsoft Graph tiIndicators API 将威胁指标从 TIP 或自定义解决方案发送到 Azure Sentinel。 这些指标可供组织的所有 Azure Sentinel 工作区使用。 请按照以下步骤为每个工作区启用“威胁情报平台”数据连接器：

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择要将从 TIP 或自定义解决方案发送的威胁指标导入到的工作区。

1. 从菜单中选择“数据连接器”，从连接器库中选择“威胁情报平台”，然后单击“打开连接器页面”按钮。

1. 完成应用注册并将 TIP 或自定义解决方案配置为发送威胁指标后，剩下的唯一步骤就是单击“连接”按钮。

几分钟后，威胁指标应开始流入此 Azure Sentinel 工作区。

### <a name="adding-threat-indicators-to-azure-sentinel-with-the-threat-intelligence---taxii-data-connector"></a>使用“威胁情报 - TAXII”数据连接器向 Azure Sentinel 添加威胁指标

威胁情报传输最广泛采用的行业标准是 [STIX 数据格式和 TAXII 协议的组合](https://oasis-open.github.io/cti-documentation/)。 如果组织从支持当前 STIX/TAXII 版本（2.0 或 2.1）的解决方案中获取威胁指标，则可以使用“威胁情报 - TAXII”数据连接器将威胁指标引入 Azure Sentinel。 “威胁情报 - TAXII”数据连接器使 Azure Sentinel 中的内置 TAXII 客户端可以从 TAXII 2.x 服务器导入威胁情报。

:::image type="content" source="media/import-threat-intelligence/threat-intel-taxii-import-path.png" alt-text="TAXII 导入路径":::
 
按照以下步骤将 STIX 格式的威胁指标从 TAXII 服务器导入到 Azure Sentinel：

1. 获取 TAXII 服务器 API 根和集合 ID

1. 在 Azure Sentinel 中启用“威胁情报 - TAXII”数据连接器

现在，让我们详细了解每个步骤。

#### <a name="obtain-the-taxii-server-api-root-and-collection-id"></a>获取 TAXII 服务器 API 根和集合 ID

TAXII 2.x 服务器播发 API 根，这是托管威胁情报集合的 URL。 大多数情况下，API 根可以通过托管 TAXII 服务器的威胁情报提供程序的文档页面获取。 然而，有时播发的唯一信息是一个称为发现终结点的 URL。 如果是这样，可以使用发现终结点轻松找到 API 根。 如果已经知道 TAXII 服务器 API 根和要使用的集合 ID，可随时跳到下一部分“在 Azure Sentinel 中启用‘威胁情报 - TAXII’数据连接器”。

让我们看一个实际的示例，该示例说明如何使用一个称为 [cURL](https://en.wikipedia.org/wiki/CURL) 的简单命令行实用工具（在 Windows 和大多数 Linux 发行版中提供）发现 API 根并浏览仅从发现终结点开始的 TAXII 服务器集合。 在此示例中，我们将使用 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 服务器的发现终结点。

1.  从浏览器导航到 [ThreatStream TAXII 2.0 服务器发现终结点](https://limo.anomali.com/taxii)，以检索 API 根。 使用用户和密码 `guest` 进行身份验证。

    ```json
    {
        "api_roots":
        [
            "https://limo.anomali.com/api/v1/taxii2/feeds/",
            "https://limo.anomali.com/api/v1/taxii2/trusted_circles/",
            "https://limo.anomali.com/api/v1/taxii2/search_filters/"
        ],
        "contact": "info@anomali.com",
        "default": "https://limo.anomali.com/api/v1/taxii2/feeds/",
        "description": "TAXII 2.0 Server (guest)",
        "title": "ThreatStream Taxii 2.0 Server"
    }
    ```

2.  使用上一步中的 cURL 实用工具和 API 根 (https://limo.anomali.com/api/v1/taxii2/feeds/) 浏览 API 根上托管的集合 ID 列表

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    {
        "collections":
        [
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "107",
                "title": "Phish Tank"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "135",
                "title": "Abuse.ch Ransomware IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "136",
                "title": "Abuse.ch Ransomware Domains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "150",
                "title": "DShield Scanning IPs"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "200",
                "title": "Malware Domain List - Hotlist"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "209",
                "title": "Blutmagie TOR Nodes"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "31",
                "title": "Emerging Threats C&C Server"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "33",
                "title": "Lehigh Malwaredomains"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "41",
                "title": "CyberCrime"
            },
            {
                "can_read": true,
                "can_write": false,
                "description": "",
                "id": "68",
                "title": "Emerging Threats - Compromised"
            }
        ]
    }
    ```

现在，你已获得将 Azure Sentinel 连接到 Anomali Limo 提供的一个或多个 TAXII 服务器集合所需的所有信息。

| **API 根** (https://limo.anomali.com/api/v1/taxii2/feeds/) | 集合 ID |
| ------------------------------------------------------------ | ------------: |
| **Phish Tank**                                               | 107           |
| **Abuse.ch 勒索软件 IP**                                  | 135           |
| **Abuse.ch 勒索软件域**                              | 136           |
| **DShield 扫描 IP**                                     | 150           |
| **恶意软件域列表 - Hotlist**                            | 200           |
| **Blutmagie TOR 节点**                                      | 209           |
| **新兴威胁 C&C 服务器**                              |  31           |
| **Lehigh Malwaredomains**                                    |  33           |
| **网络犯罪**                                               |  41           |
| **新兴威胁 - 已入侵**                           |  68           |
|

#### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中启用“威胁情报 - TAXII”数据连接器

若要将威胁指标从 TAXII 服务器导入到 Azure Sentinel，请执行以下步骤：

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择要从 TAXII 服务器将威胁指标导入到的工作区。

1. 从菜单中选择“数据连接器”，从连接器库中选择“威胁情报 - TAXII”，然后单击“打开连接器页面”按钮。

1. 键入此 TAXII 服务器集合的名称、“API 根 URL”、“集合 ID”、“用户名”（如果需要）和“密码”（如果需要），然后单击“添加”按钮。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-configure-taxii-servers.png" alt-text="配置 TAXII 服务器":::
 
你应该会收到指出已成功连接到 TAXII 服务器的确认信息，你可以根据需要多次重复上面的步骤 (4)，以从相同或不同的 TAXII 服务器连接到多个集合。

## <a name="view-your-threat-indicators-in-azure-sentinel"></a>查看 Azure Sentinel 中的威胁指标

你已经使用“威胁情报平台”和/或“威胁情报 - TAXII”数据连接器将威胁指标成功导入到 Azure Sentinel，现在可以在“日志”（其中存储了你的所有 Azure Sentinel 事件数据）的 ThreatIntelligenceIndicator 表中查看它们。 此表是其他 Azure Sentinel 功能（如分析和工作簿）执行查询的基础。 下面介绍了如何在 ThreatIntelligenceIndicator 表中查找和查看威胁指标。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择使用任一威胁情报数据连接器将威胁指标导入到的工作区。

1. 从 Azure Sentinel 菜单的“常规”部分中选择“日志”。

1. ThreatIntelligenceIndicator 表位于 Azure Sentinel 组下方。

1. 选择表名称旁边的“预览数据”图标（眼睛），然后选择“在查询编辑器中查看”按钮以执行查询，该查询将显示此表中的记录。

结果应类似于下面所示的示例威胁指标：

:::image type="content" source="media/import-threat-intelligence/threat-intel-sample-query.png" alt-text="示例查询数据":::
 
## <a name="manage-your-threat-indicators-in-the-new-threat-intelligence-area-of-azure-sentinel"></a>在 Azure Sentinel 的新威胁情报区域中管理威胁指标

在新的威胁情报区域（可从 Azure Sentinel 菜单访问）中，你还可以查看、排序、筛选和搜索导入的威胁指标，甚至无需编写日志查询。 利用这个新区域，你还可以直接在 Azure Sentinel 界面中创建威胁指标，以及执行常见的威胁情报管理任务，例如指标标记和创建与安全调查相关的新指标。
让我们看看两个最常见的任务，即创建新的威胁指标和标记指标以便轻松分组和引用。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择使用任一威胁情报数据连接器将威胁指标导入到的工作区。

1. 从 Azure Sentinel 菜单的“威胁管理”部分选择“威胁情报”。

1. 从页面的顶部菜单中选择“新增”按钮。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-add-new-indicator.png" alt-text="添加新的威胁指标" lightbox="media/import-threat-intelligence/threat-intel-add-new-indicator.png":::

1. 选择指标类型，然后在“新建指标”面板上填写标有红色星号 (*) 的必填字段。

1. 选择“应用”。 系统会将该指标添加到“指标”网格，同时发送到“日志”的 ThreatIntelligenceIndicator 表中。

通过标记威胁指标，可以轻松地对它们进行分组，使其更易于查找。 通常，你可以将标记应用于与特定事件相关的指标，或应用于表示来自某个已知参与者或已知攻击活动的威胁的指标。 你可以单独标记威胁指标，也可以选择多个指标，并同时对它们进行标记。 下面显示了使用事件 ID 标记多个指标的示例。 由于标记采用自由格式，建议的做法是为威胁指标标记创建标准命名约定。 可以将多个标记应用于每个指示器。

:::image type="content" source="media/import-threat-intelligence/threat-intel-tagging-indicators.png" alt-text="向威胁指标应用标记" lightbox="media/import-threat-intelligence/threat-intel-tagging-indicators.png":::

## <a name="analytics-puts-your-threat-indicators-to-work-detecting-potential-threats"></a>分析功能可将威胁指标用于检测潜在威胁

你已将威胁指标馈送到 Azure Sentinel 中；你已了解如何查看和管理它们；现在看看它们能为你做什么。 在诸如 Azure Sentinel 之类的 SIEM 解决方案中，威胁指标最重要的用例是为分析规则提供支持。  这些基于指标的规则将数据源中的原始事件与威胁指标进行比较，以检测组织中的安全威胁。 在 Azure Sentinel 的“分析”中，你可以创建按计划运行的分析规则并生成安全警报。 规则由查询以及一些配置驱动，这些配置用于确定规则的运行频率、应生成安全警报的查询结果类型，以及要在生成警报时触发的任何自动响应。

你始终可以从头开始创建新的分析规则，不过，Azure Sentinel 提供了一组由 Microsoft 安全工程师创建的内置规则模板，你可以按原样使用这些模板，也可以根据需要进行修改。 你可以轻松识别使用威胁指标的规则模板，因为它们的标题都以“TI 将…”开头。 所有这些规则模板的运行方式都相似，唯一的区别在于所用的威胁指标类型（域、电子邮件、文件哈希、IP 地址或 URL）以及要匹配的事件类型。 每个模板都列出了规则起作用所需的数据源，因此你可以一目了然地看到是否已在 Azure Sentinel 中导入了必要的事件。

让我们来看看其中一个规则模板，并逐步介绍如何启用和配置规则，以使用导入到 Azure Sentinel 的威胁指标生成安全警报。 在此示例中，我们将使用名为“TI 将 IP 实体映射到 AzureActivity”的规则模板。 此规则会将所有 IP 地址类型的威胁指标与所有 Azure 活动事件进行匹配。 找到匹配项后，系统会生成一个警报，以及一个相应的事件，供安全操作团队进行调查。 只有启用了“威胁情报”数据连接器（用于导入威胁指标）和/或“Azure 活动”数据连接器（用于导入 Azure 订阅级事件），此分析规则才能成功运行。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择将威胁指标（使用“威胁情报”数据连接器）和 Azure 活动数据（使用“Azure 活动”数据连接器）导入到的工作区。

1. 从 Azure Sentinel 菜单的“配置”部分中选择“分析”。

1. 选择“规则模板”选项卡以查看可用分析规则模板的列表。

1. 导航到标题为“TI 将 IP 实体映射到 AzureActivity”的规则，确保已连接所有必需的数据源，如下所示。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-required-data-sources.png" alt-text="必需的数据源":::

1. 选择此规则，然后选择“创建规则”按钮。 此操作将打开一个向导来配置规则。 完成此处的设置，然后选择“下一步: 设置规则逻辑 >”按钮。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-create-analytics-rule.png" alt-text="创建分析规则":::

1. 向导的规则逻辑部分包含：
    - 将在规则中使用的查询。

    - 实体映射，用于指示 Azure Sentinel 如何识别帐户、IP 地址和 URL 等实体，以便事件和调查了解如何在此规则生成的任何安全警报中处理这些数据。

    - 此规则的运行计划。

    - 生成安全警报之前所需的查询结果数。

    模板中的默认设置为：
    - 每小时运行一次。

    - 将 ThreatIntelligenceIndicator 表中的所有 IP 地址威胁指标与在 AzureActivity 表中的最后一小时事件中找到的所有 IP 地址进行匹配。

    - 如果查询结果大于零（即找到任何匹配项），则生成安全警报。

你可以保留默认设置，也可以根据需要更改其中任何一项。 完成后，选择“下一步: 自动响应 >”按钮

1. 此向导步骤允许你配置根据此分析规则生成安全警报时要触发的任何自动化操作。 Azure Sentinel 中的自动化操作使用 Playbook（由 Azure 逻辑应用提供支持）完成。 若要了解详细信息，请参阅此[教程：在 Azure Sentinel 中设置自动威胁响应](./tutorial-respond-threats-playbook.md)。 在此示例中，我们将直接选择“下一步: 查看 >”按钮继续操作。

1. 这最后一步将验证规则中的设置。 当你准备好启用规则时，选择“创建”按钮，你的操作就完成了。

你已经启用了分析规则，现在可以在 Azure Sentinel 的“分析”部分的“活动规则”选项卡中找到已启用的规则。 你可以在其中编辑、启用、禁用、复制或删除活动规则。 新规则在激活后立即运行，并从那时起按定义的计划运行。

根据默认设置，规则每次按计划运行时，发现的任何结果都会生成安全警报。 可以在 Azure Sentinel 的“日志”部分，Azure Sentinel 组下的 SecurityAlert表中查看 Azure Sentinel 中的安全警报。

在 Azure Sentinel 中，根据分析规则生成的警报还会生成安全事件，这些事件可以在 Azure Sentinel 菜单的“威胁管理”下的“事件”中找到。 安全操作团队将对事件进行会审和调查，以确定适当的响应操作。 你可以在此[教程：使用 Azure Sentinel 调查事件](./tutorial-investigate-cases.md)中找到详细信息。

## <a name="workbooks-provide-insights-about-your-threat-intelligence"></a>工作簿提供有关威胁情报的见解

最后，你可以使用 Azure Sentinel 工作簿直观显示 Azure Sentinel 中威胁情报的关键信息，并且可以根据业务需求轻松自定义工作簿。
让我们逐步介绍如何查找 Azure Sentinel 中提供的威胁情报工作簿，我们还将演示如何编辑工作簿，以便对其进行自定义。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到 Azure Sentinel 服务。

1. 选择使用任一威胁情报数据连接器将威胁指标导入到的工作区。

1. 从 Azure Sentinel 菜单的“威胁管理”部分中选择“工作簿”。

1. 导航到标题为“威胁情报”的工作簿，并验证 ThreatIntelligenceIndicator 表中是否包含数据，如下所示。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-verify-data.png" alt-text="验证数据":::
 
1. 选择“保存”按钮，然后选择用于存储工作簿的 Azure 位置。 如果要以任何方式修改工作簿并保存更改，就必须执行此步骤。

1. 现在，选择“查看保存的工作簿”按钮以打开工作簿进行查看和编辑。

1. 现在应该可以看到模板提供的默认图表。 现在，让我们对其中一个图表进行一些更改。 选择页面顶部的“编辑”按钮，进入工作簿的编辑模式。

1. 让我们按威胁类型添加一个新的威胁指标图表。 滚动到页面底部，选择“添加查询”。

1. 将以下文本添加到“Log Analytics 工作区日志查询”文本框中：
    ```kusto
    ThreatIntelligenceIndicator
    | summarize count() by ThreatType
    ```

1. 在“可视化效果”下拉列表中，选择“条形图”。

1. 选择“完成编辑”按钮。 你已经为工作簿创建了一个新图表。

    :::image type="content" source="media/import-threat-intelligence/threat-intel-bar-chart.png" alt-text="条形图":::

工作簿提供功能强大的交互式仪表板，让你能够深入了解 Azure Sentinel 的各个方面。 你可以用工作簿做很多事情；虽然系统提供的模板是一个很好的起点，但你可能想要深入了解并自定义这些模板，或者结合许多不同的数据源创建新的仪表板，以便通过独特的方式直观显示数据。 Azure Sentinel 工作簿基于 Azure Monitor 工作簿，因此，系统已经提供大量文档以及更多模板。 介绍如何[使用 Azure Monitor 工作簿创建交互式报表](../azure-monitor/visualize/workbooks-overview.md)的这篇文章是一个很好的起点。 

GitHub 上还有一个内容丰富的 [Azure Monitor 工作簿社区](https://github.com/microsoft/Application-Insights-Workbooks)，你可以下载更多模板并贡献自己的模板。

## <a name="next-steps"></a>后续步骤
本文档介绍了 Azure Sentinel 的威胁情报功能以及新的“威胁情报”边栏选项卡。 有关如何使用 Azure Sentinel 的威胁情报功能的实用指导，请参阅以下文章：

- [将威胁情报数据连接](./connect-threat-intelligence.md)到 Azure Sentinel。
- [集成 TIP 平台、TAXII 源和扩充](threat-intelligence-integration.md) 与 Azure Sentinel。
- 在 Azure Sentinel 中创建[内置](./tutorial-detect-threats-built-in.md)或[自定义](./tutorial-detect-threats-custom.md)警报，并[调查](./tutorial-investigate-cases.md)事件。