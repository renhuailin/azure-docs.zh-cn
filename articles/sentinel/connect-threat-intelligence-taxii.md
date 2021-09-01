---
title: 将 Azure Sentinel 连接到 STIX/TAXII 威胁情报源 | Microsoft Docs
description: 了解如何将 Azure Sentinel 连接到行业标准威胁情报源，以导入威胁指标。
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
ms.date: 07/12/2021
ms.author: yelevin
ms.openlocfilehash: ce1d2fafd330848c19dc5386fad11d87bfe2670a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732529"
---
# <a name="connect-azure-sentinel-to-stixtaxii-threat-intelligence-feeds"></a>将 Azure Sentinel 连接到 STIX/TAXII 威胁情报源

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

另请参阅：[将威胁情报平台 (TIP) 连接到 Azure Sentinel](connect-threat-intelligence-tip.md)

威胁情报传输最广泛采用的行业标准是 [STIX 数据格式和 TAXII 协议的组合](https://oasis-open.github.io/cti-documentation/)。 如果组织从支持当前 STIX/TAXII 版本（2.0 或 2.1）的解决方案中获取威胁指标，则可以使用“威胁情报 - TAXII”数据连接器将威胁指标引入 Azure Sentinel。 此连接器使 Azure Sentinel 中的内置 TAXII 客户端可以从 TAXII 2.x 服务器导入威胁情报。

:::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-taxii-import-path.png" alt-text="TAXII 导入路径":::

详细了解 Azure Sentinel 中的[威胁情报](understand-threat-intelligence.md)，尤其是可与 Azure Sentinel 集成的 [TAXII 威胁情报源](threat-intelligence-integration.md#taxii-threat-intelligence-feeds)。

## <a name="prerequisites"></a>先决条件  

- 必须拥有 Azure Sentinel 工作区的读取和写入权限，才能存储威胁指标。
- 必须拥有 TAXII 2.0 或 TAXII 2.1“API 根 URI”和“集合 ID”。

## <a name="instructions"></a>Instructions

按照以下步骤将 STIX 格式的威胁指标从 TAXII 服务器导入到 Azure Sentinel：

1. 获取 TAXII 服务器 API 根和集合 ID

1. 在 Azure Sentinel 中启用“威胁情报 - TAXII”数据连接器

### <a name="get-the-taxii-server-api-root-and-collection-id"></a>获取 TAXII 服务器 API 根和集合 ID

TAXII 2.x 服务器播发 API 根，这是托管威胁情报集合的 URL。 通常可以在托管 TAXII 服务器的威胁情报提供程序的文档页中查找 API 根和集合 ID。 

> [!NOTE]
> 某些情况下，提供程序将仅播发名为“发现终结点”的 URL。 可以使用 cURL 实用工具浏览发现终结点并请求 API 根，具体情况[如下所示](#find-the-api-root)。

### <a name="enable-the-threat-intelligence---taxii-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中启用“威胁情报 - TAXII”数据连接器

若要将威胁指标从 TAXII 服务器导入到 Azure Sentinel，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com/)中，导航到“Azure Sentinel”服务。

1. 选择要从 TAXII 服务器将威胁指标导入到的工作区。

1. 从菜单中选择“数据连接器”，从连接器库中选择“威胁情报 - TAXII”，然后单击“打开连接器页面”按钮。

1. 为此 TAXII 服务器集合输入“易记名称”，以及“API 根 URL”、“集合 ID”、“用户名”（如有需要）和“密码”（如有需要），然后选择指示器组以及所需的轮询频率。 单击 **“添加”** 按钮。

    :::image type="content" source="media/connect-threat-intelligence-taxii/threat-intel-configure-taxii-servers.png" alt-text="配置 TAXII 服务器":::
 
应该会收到指出已成功连接到 TAXII 服务器的确认信息，可以根据需要多次重复上面的最后一步，以从一个或多个 TAXII 服务器连接到多个集合。

几分钟后，威胁指标应开始流入此 Azure Sentinel 工作区。 可以在“威胁情报”边栏选项卡中找到新指标，该边栏选项卡可从 Azure Sentinel 导航菜单访问。

### <a name="find-the-api-root"></a>查找 API 根

以下示例说明如何使用 [cURL](https://en.wikipedia.org/wiki/CURL) 命令行实用工具（在 Windows 和大多数 Linux 发行版中提供）发现 API 根，并浏览仅给定发现终结点的 TAXII 服务器集合。 借助异常 [Anomali Limo](https://www.anomali.com/community/limo) ThreatStream TAXII 2.0 服务器的发现终结点，可以先请求 API 根 URI，然后再请求集合。

1.  在浏览器中输入 https://limo.anomali.com/taxii ，然后导航到 ThreatStream TAXII 2.0 服务器发现终结点，以检索 API 根。 使用用户名和密码 `guest` 进行身份验证。

    你会收到以下响应：

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

2.  使用上一个响应中的 cURL 实用工具和 API 根 (https://limo.anomali.com/api/v1/taxii2/feeds/) )，然后将“`collections/`”追加到 API 根中，以浏览 API 根上托管的集合 ID 列表：

    ```json
    curl -u guest https://limo.anomali.com/api/v1/taxii2/feeds/collections/
    ```
    再次使用密码“guest”执行身份验证后，将收到以下响应：

    ```json
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

## <a name="next-steps"></a>后续步骤

本文档介绍了如何使用 TAXII 协议将 Azure Sentinel 连接到威胁情报源。 若要详细了解 Azure Sentinel，请参阅以下文章。

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。
