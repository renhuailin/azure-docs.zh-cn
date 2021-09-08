---
title: 将威胁情报平台连接到 Azure Sentinel | Microsoft Docs
description: 了解如何将威胁情报平台 (TIP) 或自定义源连接到 Azure Sentinel 并发送威胁指标。
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
ms.openlocfilehash: e1f6a508e72277fcc596a5217b0c5a061758e131
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123253558"
---
# <a name="connect-your-threat-intelligence-platform-to-azure-sentinel"></a>将威胁情报平台连接到 Azure Sentinel

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

**另请参阅**：[将 Azure Sentinel 连接到 STIX/TAXII 威胁情报源](connect-threat-intelligence-taxii.md)

许多组织使用威胁情报平台 (TIP) 解决方案从各种来源汇总威胁指标源，在平台内策展数据，然后选择要应用于各种安全解决方案（如网络设备、EDR/XDR 解决方案或像 Azure Sentinel 这样的 SIEM）的威胁指标。 通过威胁情报平台数据连接器，可以使用这些解决方案将威胁指标导入到 Azure Sentinel。 

由于 TIP 数据连接器与 [Microsoft Graph 安全性 tiIndicators API](/graph/api/resources/tiindicator) 协作完成此操作，因此你可以使用该连接器，将指标从任何其他可以与该 API 通信的自定义威胁情报平台发送到 Azure Sentinel（以及 Microsoft 365 Defender 等其他 Microsoft 安全解决方案）。

:::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-import-path.png" alt-text="威胁情报导入路径":::

详细了解 Azure Sentinel 中的[威胁情报](understand-threat-intelligence.md)，尤其是可与 Azure Sentinel 集成的[威胁情报平台产品](threat-intelligence-integration.md#integrated-threat-intelligence-platform-products)。

## <a name="prerequisites"></a>先决条件  

- 必须具有全局管理员或安全管理员 Azure AD 角色，用于向 TIP 产品或任何其他自定义应用程序授予所需权限，使其能够利用与 Microsoft Graph 安全性 tiIndicators API 的直接集成 。

- 必须对 Azure Sentinel 工作区具有读取和写入权限，用于存储威胁指标。

## <a name="instructions"></a>Instructions

按照以下步骤，从集成式 TIP 或自定义威胁情报解决方案向 Azure Sentinel 导入威胁指标：
1.  从 Azure Active Directory 获取应用程序 ID 和客户端密码
2.  将此信息输入到 TIP 解决方案或自定义应用程序中
3.  在 Azure Sentinel 中启用“威胁情报平台”数据连接器

### <a name="sign-up-for-an-application-id-and-client-secret-from-your-azure-active-directory"></a>从 Azure Active Directory 注册应用程序 ID 和客户端密码

无论你是使用 TIP 还是使用自定义解决方案，tiIndicators API 都需要一些基本信息，以便你能够将源连接到它并向它发送威胁指标。 你需要的三项信息为：

- 应用程序（客户端）ID
- 目录（租户）ID
- 客户端机密

可以通过“应用注册”过程从 Azure Active Directory 获取这些信息，该过程包括以下三个步骤：

- 向 Azure Active Directory 注册应用
- 指定应用连接到 Microsoft Graph tiIndicators API 并发送威胁指标所需的权限
- 获得组织同意，向此应用程序授予这些权限。

#### <a name="register-an-application-with-azure-active-directory"></a>将应用程序注册到 Azure Active Directory

1. 在 Azure 门户中，导航到 Azure Active Directory 服务。
1. 从菜单中选择“应用注册”，然后选择“新建注册”。
1. 为应用程序注册选择一个名称，选择“单租户”单选按钮，然后选择“注册” 。 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-register-application.png" alt-text="注册应用程序":::

1. 从随后出现的屏幕中复制“应用程序(客户端) ID”和“目录(租户) ID”值。 这是你以后配置 TIP 或自定义解决方案以向 Azure Sentinel 发送威胁指标时需要的前两项信息。 第三项是客户端密码，稍后显示。

#### <a name="specify-the-permissions-required-by-the-application"></a>指定应用程序所需的权限

1. 返回 Azure Active Directory 服务主页。

1. 从菜单中选择“应用注册”，然后选择新注册的应用。

1. 从菜单中选择“API 权限”，然后选择“添加权限”按钮 。

1. 在“选择 API”页上，选择“Microsoft Graph”API，然后从 Microsoft Graph 权限列表中进行选择 。

1. 显示提示“应用程序需要哪种类型的权限?”时 选择“应用程序权限”。 这是通过应用 ID 和应用机密（API 密钥）进行身份验证的应用程序所使用的权限类型。

1. 选择“ThreatIndicators.ReadWrite.OwnedBy”，然后选择“添加权限”，以将此权限添加到应用的权限列表中。

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-1.png" alt-text="指定权限":::

#### <a name="get-consent-from-your-organization-to-grant-these-permissions"></a>获得组织同意以授予这些权限

1. 若要获得同意，你需要以 Azure Active Directory 全局管理员的身份在应用的“API 权限”页上选择“为租户授予管理员同意”按钮 。 如果你的帐户上没有全局管理员角色，此按钮将不可用，你需要请组织的全局管理员执行此步骤。 

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-api-permissions-2.png" alt-text="授予许可":::

1. 向应用授予同意后，“状态”下应该会出现一个绿色复选标记。

现在，应用已注册并授予权限，你可以在列表中找到最后一项信息 - 应用的客户端密码。

1. 返回 Azure Active Directory 服务主页。

1. 从菜单中选择“应用注册”，然后选择新注册的应用。

1. 从菜单中选择“证书和机密”，然后选择“新建客户端密码”按钮以接收应用密码（API 密钥） 。

    :::image type="content" source="media/connect-threat-intelligence-tip/threat-intel-client-secret.png" alt-text="获取客户端密码":::

1. 选择“添加”按钮并复制客户端密码 。

    > [!IMPORTANT]
    > 在离开此屏幕之前，必须复制客户端密码。 如果离开此页，则无法再次检索此密码。 配置 TIP 或自定义解决方案时需要此值。

### <a name="input-this-information-into-your-tip-solution-or-custom-application"></a>将此信息输入到 TIP 解决方案或自定义应用程序中

现在，你已获得配置 TIP 或自定义解决方案以向 Azure Sentinel 发送威胁指标所需的所有信息。

- 应用程序（客户端）ID
- 目录（租户）ID
- 客户端机密

1. 根据需要在集成式 TIP 或自定义解决方案的配置中输入这些值。

1. 对于目标产品，请指定 Azure Sentinel。

1. 对于操作，请指定警报。

完成此配置后，将通过 Microsoft Graph tiIndicators API 将威胁指标从 TIP 或自定义解决方案发送到 Azure Sentinel。

### <a name="enable-the-threat-intelligence-platforms-data-connector-in-azure-sentinel"></a>在 Azure Sentinel 中启用“威胁情报平台”数据连接器

集成过程的最后一步是在 Azure Sentinel 中启用威胁情报平台数据连接器。 启用连接器可让 Azure Sentinel 接收从 TIP 或自定义解决方案发送的威胁指标。 这些指标可供组织的所有 Azure Sentinel 工作区使用。 请按照以下步骤为每个工作区启用“威胁情报平台”数据连接器：

1. 在 Azure 门户中，导航到 Azure Sentinel 服务。

1. 选择要将从 TIP 或自定义解决方案发送的威胁指标导入到的工作区。

1. 从菜单中选择“数据连接器”，从连接器库中选择“威胁情报平台”，然后选择“打开连接器页面”按钮  。

1. 完成应用注册并将 TIP 或自定义解决方案配置为发送威胁指标后，剩下的唯一步骤就是选择“连接”按钮。

几分钟后，威胁指标应开始流入此 Azure Sentinel 工作区。 可以在“威胁情报”边栏选项卡中找到新指标，该边栏选项卡可从 Azure Sentinel 导航菜单访问。

## <a name="next-steps"></a>后续步骤

在本文档中，你了解了如何将威胁情报平台连接到 Azure Sentinel。 若要详细了解 Azure Sentinel，请参阅以下文章。

- 了解如何[洞悉数据和潜在威胁](get-visibility.md)。
- 开始[使用 Azure Sentinel 检测威胁](./detect-threats-built-in.md)。