---
title: 私密且安全地连接到你的 Purview 帐户
description: 本文介绍如何设置专用终结点，以便从受限网络连接到 Purview 帐户。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/18/2021
ms.openlocfilehash: 26b98ad9c4c042c1e6bf60889625a4d23090f6f5
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515770"
---
# <a name="connect-privately-and-securely-to-your-purview-account"></a>私密且安全地连接到你的 Purview 帐户
通过本指南，你将了解如何为 Purview 帐户部署专用终结点，以便仅从 VNet 和专用网络连接到 Azure Purview 帐户。 若要实现此目标，需要为 Azure Purview 帐户部署帐户、门户并引入专用终结点。

借助 Azure Purview 帐户专用终结点，你可以仅允许来自虚拟网络内部的客户端调用访问 Azure Purview 帐户，从而额外增加一层安全层。 此专用终结点也是门户专用终结点的一个必备组件。

若要使用专用网络连接到 Azure Purview Studio，需要使用 Azure Purview 门户专用终结点。

   :::image type="content" source="media/catalog-private-link/purview-private-link-account-portal.png" alt-text="显示 Azure Purview 和专用链接体系结构的关系图。":::

有关 Azure 专用链接服务的详细信息，请参阅[专用链接和专用终结点](../private-link/private-endpoint-overview.md)。

## <a name="deployment-checklist"></a>部署清单
通过使用本指南提供的部署方案之一，可以使用帐户和门户专用终结点部署新的 Azure Purview 帐户，也可选择为现有的 Azure Purview 帐户部署上述专用终结点：

1. 选择适当的 Azure 虚拟网络和子网来部署 Azure Purview 专用终结点。 选择以下选项之一：
   - 在 Azure 订阅中部署[新的虚拟网络](../virtual-network/quick-create-portal.md)。
   - 找到 Azure 订阅中现有的 Azure 虚拟网络和子网。
  
2. 定义适当的 [DNS 名称解析方法](./catalog-private-link-name-resolution.md#deployment-options)，以便可以通过专用 IP 地址访问 Azure Purview 帐户和 Web 门户。 可以使用以下任一选项：
   - 使用本指南中进一步说明的步骤来部署新的 Azure DNS 区域。
   - 使用本指南中进一步说明的步骤来将所需的 DNS 记录添加到现有 Azure DNS 区域。
   - 完成本指南所述步骤后，在现有 DNS 服务器中手动添加所需的 DNS A 记录。
3. 使用帐户和门户专用终结点部署[新 Purview 帐户](#option-1---deploy-a-new-azure-purview-account-with-account-and-portal-private-endpoints)，或者为[现有 Purview 帐户](#option-2---enable-account-and-portal-private-endpoint-on-existing-azure-purview-accounts)部署帐户和门户专用终结点。
4. 如果专用网络将网络安全组规则设置为拒绝所有公共 Internet 流量，请[启用对 Azure Active Directory 的访问](#enable-access-to-azure-active-directory)。
5. 完成本指南后，根据需要调整 DNS 配置。
6. 验证网络和名称解析（从管理计算机到 Azure Purview）。 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-and-_portal_-private-endpoints"></a>方案 1：使用帐户和门户专用终结点部署新的 Azure Purview 帐户 

1. 转到 [Azure 门户](https://portal.azure.com)，然后前往“Azure Purview 帐户”页。 选择“+ 创建”，以创建新的 Azure Purview 帐户。

2. 填写基本信息，然后在“网络”选项卡中，将“连接方法”设置为“专用终结点”。 将“启用专用终结点”设置为“仅有帐户和门户”。

3. 在“帐户和门户”下，选择“+ 添加”为 Azure Purview 帐户添加专用终结点 。

   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-1.png" alt-text="显示“为帐户和门户创建专用终结点”页面选项的屏幕截图。":::

4. 在“创建专用终结点”页上，为“Purview 子资源”选择创建位置，提供帐户专用终结点的名称，然后选择“帐户” 。 在“网络”下，选择虚拟网络和子网，然后选择“与专用 DNS 区域集成”（可选）以创建新的 Azure 专用 DNS 区域 。 
   
   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-2.png" alt-text="显示“创建帐户专用终结点”页面的屏幕截图。":::


   > [!NOTE]
   > 你也可以使用现有的 Azure 专用 DNS 区域，或稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

5. 选择“确定”。
   
6. 在“创建 Purview 帐户”向导下，再次选择“+ 添加”，以添加门户专用终结点 。
     
7. 在“创建专用终结点”页上，为“Purview 子资源”选择创建位置，提供门户专用终结点的名称，然后选择“门户” 。 在“网络”下，选择虚拟网络和子网，然后选择“与专用 DNS 区域集成”（可选）以创建新的 Azure 专用 DNS 区域 。 
   
   > [!NOTE]
   > 你也可以使用现有的 Azure 专用 DNS 区域，或稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

8.  选择“确定”。
   
   :::image type="content" source="media/catalog-private-link/purview-pe-scenario-1-3.png" alt-text="显示“创建专用终结点”查看页面的屏幕截图。":::

9.  选择“查看 + 创建”  。 在“查看 + 创建”页面上，Azure 会验证你的配置。

10. 看到“验证通过”的消息后，选择“创建”。

## <a name="option-2---enable-_account_-and-_portal_-private-endpoint-on-existing-azure-purview-accounts"></a>方案 2：在现有 Azure Purview 帐户上启用帐户和门户专用终结点 

你可以通过两种方式来为现有 Azure Purview 帐户添加 Azure Purview 帐户和门户专用终结点：

- 使用 Azure 门户（Azure Purview 帐户）。
- 使用专用链接中心。

### <a name="use-the-azure-portal-azure-purview-account"></a>使用 Azure 门户（Azure Purview 帐户）

1. 转到 [Azure 门户](https://portal.azure.com)，然后单击进入 Azure Purview 帐户，在“设置”下选择“网络”，然后选择“专用终结点连接”  。

    :::image type="content" source="media/catalog-private-link/pe-portal.png" alt-text="显示创建帐户专用终结点的屏幕截图。":::

2. 选择“+ 专用终结点”创建新的专用终结点。

3. 填写基本信息。

4. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/accounts”。

5. 对于“资源”，选择“Azure Purview 帐户”；对于“目标子资源”，选择“帐户”。

6. 在“配置”选项卡上，选择虚拟网络并根据需要选择 Azure 专用 DNS 区域，以创建新的 Azure DNS 区域。
   
   > [!NOTE]
   > 对于 DNS 配置，还可以使用下拉列表中现有的 Azure 专用 DNS 区域，或稍后手动将所需 DNS 记录添加到 DNS 服务器。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

7. 转到“摘要”页面，选择“创建”以创建门户专用终结点。
   
8. 遵循同样的步骤，为“目标子资源”选择“门户” 。

### <a name="use-the-private-link-center"></a>使用专用链接中心

1. 转到 [Azure 门户](https://portal.azure.com)。

1. 在页面顶部的搜索栏中，搜索“专用链接”，然后选择第一个选项转到“专用链接”窗格。

1. 选择“+ 添加”，并填写基本详细信息。

   :::image type="content" source="media/catalog-private-link/private-link-center.png" alt-text="显示从专用链接中心创建专用终结点的屏幕截图。":::

1. 对于“资源”，选择已创建的 Azure Purview 帐户。 对于“目标子资源”，选择“帐户”。

1. 在“配置”选项卡中，选择“虚拟网络”和“专用 DNS 区域”。 转到“摘要”页面，选择“创建”以创建帐户专用终结点。

> [!NOTE]
> 遵循同样的步骤，为“目标子资源”选择“门户” 。

## <a name="enable-access-to-azure-active-directory"></a>启用对 Azure Active Directory 的访问

> [!NOTE]
> 如果 VM、VPN 网关或 VNet 对等互连网关具有公共 Internet 访问权限，则它可以访问 Azure Purview 门户以及启用了专用终结点的 Azure Purview 帐户。 出于此原因，无需遵循其他说明。 如果专用网络的网络安全组规则设置为拒绝所有公共 Internet 流量，则需要添加一些规则来启用 Azure Active Directory (Azure AD) 访问。 请按照这些说明进行操作。

这些说明用于指导从 Azure VM 安全地访问 Azure Purview。 如果使用 VPN 或其他 VNet 对等互连网关，必须遵循类似的步骤。

1. 在 Azure 门户中转到 VM，并在“设置”下选择“网络”。 然后选择“出站端口规则”、“添加出站端口规则”。

   :::image type="content" source="media/catalog-private-link/outbound-rule-add.png" alt-text="显示添加出站规则的屏幕截图。":::

1. 在“添加出站安全规则”窗格中：

   1. 在“目标”下，选择“服务标记”。
   1. 在“目标服务标记”下，选择“AzureActiveDirectory”。
   1. 在“目标端口范围”下，选择 *。
   1. 在“操作”下，选择“允许”。 
   1. 在“优先级”下，该值应高于拒绝所有 Internet 流量的规则。
   
   创建规则。

   :::image type="content" source="media/catalog-private-link/outbound-rule-details.png" alt-text="显示添加出站规则详细信息的屏幕截图。":::

1. 按照相同的步骤创建另一个规则以允许“AzureResourceManager”服务标记。 如果需要访问 Azure 门户，还可以为“AzurePortal”服务标记添加规则。

1. 连接到 VM 并打开浏览器。 选择 Ctrl+Shift+J 转到浏览器控制台，并切换到“网络”选项卡以监视网络请求。 在“URL”框中输入“web.purview.azure.com”，并尝试使用 Azure AD 凭据登录。 登录可能会失败，并且在控制台上的“网络”选项卡中，可以看到 Azure AD 尝试访问 aadcdn.msauth.net 但被阻止。

   :::image type="content" source="media/catalog-private-link/login-fail.png" alt-text="显示登录失败详细信息的屏幕截图。":::

1. 在这种情况下，请在 VM 上打开命令提示符并运行 ping aadcdn.msauth.net 命令，获取其 IP，然后为 VM 的网络安全规则中的 IP 添加出站端口规则。 将“目标”设置为“IP 地址”，并将“目标 IP 地址”设置为 aadcdn IP。 由于 Azure 负载均衡器和 Azure 流量管理器，Azure AD 内容分发网络 IP 可能是动态的。 获取其 IP 后，最好将它添加到 VM 的主机文件，强制浏览器访问该 IP 以获取 Azure AD 内容分发网络。

   :::image type="content" source="media/catalog-private-link/ping.png" alt-text="显示测试 ping 的屏幕截图。":::

   :::image type="content" source="media/catalog-private-link/aadcdn-rule.png" alt-text="显示 Azure AD 内容分发网络规则的屏幕截图。":::

1. 创建新规则后，返回到 VM，再次尝试使用 Azure AD 凭据登录。 如果登录成功，则 Azure Purview 门户可供使用。 但在某些情况下，Azure AD 会根据客户的帐户类型重定向到其他域进行登录。 例如，对于 live.com 帐户，Azure AD 会重定向到 live.com 进行登录，然后这些请求会再次被阻止。 对于 Microsoft 员工帐户，Azure AD 将访问 msft.sts.microsoft.com 以获取登录信息。

   在浏览器“网络”选项卡中检查网络请求以查看哪些域的请求被阻止，重新执行上一步以获取其 IP，并在网络安全组中添加出站端口规则以允许对该 IP 的请求。 如果可能，将 URL 和 IP 添加到 VM 的主机文件以修复 DNS 解析。 如果知道确切的登录域 IP 范围，还可以直接将它们添加到网络规则。

1. 现在，你的 Azure AD 登录应该会成功。 Azure Purview 门户将成功加载，但列出所有 Azure Purview 帐户将不起作用，因为它只能访问特定的 Azure Purview 帐户。 输入 `web.purview.azure.com/resource/{PurviewAccountName}` 以直接访问已成功设置专用终结点的 Azure Purview 帐户。

## <a name="next-steps"></a>后续步骤

-  [验证专用终结点的解析](./catalog-private-link-name-resolution.md)
-  [在 Azure Purview 中管理数据源](./manage-data-sources.md)
-  [对 Azure Purview 帐户的专用终结点配置进行故障排除](./catalog-private-link-troubleshoot.md)
