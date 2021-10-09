---
title: 私密且安全地连接到你的 Azure Purview 并扫描数据源
description: 本文介绍如何设置专用终结点以连接到 Purview 帐户，并扫描来自受限网络的数据源以实现端到端隔离
author: zeinam
ms.author: zeinam
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 4f15d39da8f97897498f07892eb90d37a1bd8574
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209251"
---
# <a name="connect-to-your-azure-purview-and-scan-data-sources-privately-and-securely"></a>私密且安全地连接到你的 Azure Purview 并扫描数据源

在本指南中，你将了解如何为 Azure Purview 帐户部署帐户、门户和专用终结点，以使用自承载集成运行时安全且私密地访问 Purview 帐户和扫描数据源，从而实现端到端网络隔离  。

Azure Purview 帐户专用终结点用于增加额外的安全层，它可以实现仅允许源自虚拟网络内部的客户端调用访问 Azure Purview 帐户的场景。 此专用终结点也是门户专用终结点的一个必备组件。

若要使用专用网络连接到 [Azure Purview Studio](https://web.purview.azure.com/resource/)，需要使用 Azure Purview 门户专用终结点。

Azure Purview 可以使用引入专用终结点扫描 Azure 或本地环境中的数据源。 部署引入专用终结点时，需要部署三个专用终结点资源并将其链接到 Azure Purview 托管资源：

 - Blob 专用终结点链接到 Azure Purview 托管存储帐户。
 - 队列专用终结点链接到 Azure Purview 托管存储帐户。
 - 命名空间专用终结点链接到 Azure Purview 托管事件中心命名空间。

  :::image type="content" source="media/catalog-private-link/purview-private-link-architecture.png" alt-text="显示 Azure Purview 和专用链接体系结构的关系图。":::

## <a name="deployment-checklist"></a>部署清单
使用本指南中进一步介绍的部署选项之一，可以使用帐户、门户和引入专用终结点部署新的 Azure Purview 帐户，也可选择为现有的 Azure Purview 帐户部署这些专用终结点  ：

1. 选择适当的 Azure 虚拟网络和子网来部署 Azure Purview 专用终结点。 选择以下选项之一：
   - 在 Azure 订阅中部署[新的虚拟网络](../virtual-network/quick-create-portal.md)。
   - 在 Azure 订阅中查找现有的 Azure 虚拟网络和子网。
  
2. 定义适当的 [DNS 名称解析方法](./catalog-private-link-name-resolution.md#deployment-options)，以便可以使用专用网络访问 Azure Purview 帐户和扫描数据源。 可以使用以下任一选项：
   - 使用本指南中进一步说明的步骤来部署新的 Azure DNS 区域。
   - 使用本指南中进一步说明的步骤来将所需的 DNS 记录添加到现有 Azure DNS 区域。
   - 完成本指南中的步骤后，在现有 DNS 服务器中手动添加所需的 DNS A 记录。
3. 使用帐户、门户和引入专用终结点部署[新的 Purview 帐户](#option-1---deploy-a-new-azure-purview-account-with-account-portal-and-ingestion-private-endpoints)，或为[现有 Purview 帐户](#option-2---enable-account-portal-and-ingestion-private-endpoint-on-existing-azure-purview-accounts)部署专用终结点。
4. 如果专用网络将网络安全组规则设置为拒绝所有公共 Internet 流量，请[启用对 Azure Active Directory 的访问](#enable-access-to-azure-active-directory)。
5. 在部署 Azure Purview 引入专用终结点的同一 VNet 内部署和注册[自承载集成运行时](#deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources)。
6. 完成本指南后，根据需要调整 DNS 配置。
7. 验证 Azure Purview 管理计算机、自承载 IR VM 和数据源之间的网络和名称解析。 

## <a name="option-1---deploy-a-new-azure-purview-account-with-_account_-_portal_-and-_ingestion_-private-endpoints"></a>选项 1 - 使用帐户、门户和引入专用终结点部署新的 Azure Purview 帐户  

1. 转到 [Azure 门户](https://portal.azure.com)，然后转到“Purview 帐户”页面。 选择“+ 创建”，以创建新的 Azure Purview 帐户。

2. 填写基本信息，然后在“网络”选项卡中，将“连接方法”设置为“专用终结点”。 将“启用专用终结点”设置为“帐户、门户和引入”。

3. 在“帐户和门户”下，选择“+ 添加”为 Azure Purview 帐户添加专用终结点 。

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-end-to-end.png" alt-text="显示创建专用终结点端到端页面选择的屏幕截图。":::

4. 在“创建专用终结点”页面上，对于“Purview 子资源”，选择你的位置，提供帐户专用终结点的名称，然后选择“帐户” 。 在“网络”下，选择虚拟网络和子网，然后选择“与专用 DNS 区域集成”（可选）以创建新的 Azure 专用 DNS 区域 。 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-account.png" alt-text="显示“创建帐户专用终结点”页面的屏幕截图。":::

   > [!NOTE]
   > 你也可以使用现有的 Azure 专用 DNS 区域，或稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

5. 选择“确定”。
   
6. 在“帐户和门户”向导下，再次选择“+ 添加”，以添加门户专用终结点 。 
  
7. 在“创建专用终结点”页面上，对于“Purview 子资源”，选择你的位置，提供门户专用终结点的名称，然后选择“门户” 。 在“网络”下，选择虚拟网络和子网，然后选择“与专用 DNS 区域集成”（可选）以创建新的 Azure 专用 DNS 区域 。 

   :::image type="content" source="media/catalog-private-link/purview-pe-deploy-portal.png" alt-text="显示“创建门户专用终结点”页的屏幕截图。":::
   
   > [!NOTE]
   > 你也可以使用现有的 Azure 专用 DNS 区域，或稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

8. 选择“确定”。

9.  在“引入”下，提供要与专用终结点配对的“订阅”、“虚拟网络”和“子网”的详细信息，以设置引入专用终结点   。

10. 或者选择“专用 DNS 集成”，以使用 Azure 专用 DNS 区域。
   
      :::image type="content" source="media/catalog-private-link/purview-pe-deploy-ingestion.png" alt-text="显示创建专用终结点概述页面的屏幕截图。":::

      > [!IMPORTANT]
      > 必须选择正确的 Azure 专用 DNS 区域，才能在 Azure Purview 和数据源之间进行正确的名称解析。 也可使用现有 Azure 专用 DNS 区域，或者稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)。

11. 选择“查看 + 创建”  。 在“查看 + 创建”页面上，Azure 会验证你的配置。

12. 看到“验证通过”的消息后，选择“创建”。
   

## <a name="option-2---enable-_account_-_portal_-and-_ingestion_-private-endpoint-on-existing-azure-purview-accounts"></a>选项 2 - 在现有 Azure Purview 帐户上启用帐户、门户和引入专用终结点  

1. 转到 [Azure 门户](https://portal.azure.com)，选择 Azure Purview 帐户，在“设置”下选择“网络”，然后选择“专用终结点连接”  。

    :::image type="content" source="media/catalog-private-link/purview-pe-add-to-existing.png" alt-text="显示创建帐户专用终结点的屏幕截图。":::

2. 选择“+ 专用终结点”创建新的专用终结点。

3. 填写基本信息。

4. 在“资源”选项卡中，对于“资源类型”，选择“Microsoft.Purview/accounts”。

5. 对于“资源”，选择“Azure Purview 帐户”；对于“目标子资源”，选择“帐户”。

6. 在“配置”选项卡上，选择虚拟网络并根据需要选择 Azure 专用 DNS 区域，以创建新的 Azure DNS 区域。
   
   > [!NOTE]
   > 对于 DNS 配置，还可以使用下拉列表中现有的 Azure 专用 DNS 区域，或稍后手动将所需 DNS 记录添加到 DNS 服务器。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

7. 转到“摘要”页面，选择“创建”以创建门户专用终结点。
   
8. 对于“目标子资源”选择“门户” 时，请执行相同的步骤。
   
9. 从 Azure Purview 帐户中，在“设置”下选择“网络”，然后选择“引入专用终结点连接”  。

10. 在引入专用终结点连接下，选择“+ 新建”，以创建新的引入专用终结点。

      :::image type="content" source="media/catalog-private-link/purview-pe-add-ingestion-to-existing.png" alt-text="显示将专用终结点添加到现有帐户的屏幕截图。":::

11. 填写基本信息，选择现有虚拟网络和子网详细信息。 或者选择“专用 DNS 集成”，以使用 Azure 专用 DNS 区域。 从每个列表中选择正确的 Azure 专用 DNS 区域。

      > [!NOTE]
      > 也可使用现有 Azure 专用 DNS 区域，或者稍后在 DNS 服务器中手动创建 DNS 记录。 有关详细信息，请参阅[为专用终结点配置 DNS 名称解析](./catalog-private-link-name-resolution.md)

12. 选择“创建”以完成设置。

## <a name="enable-access-to-azure-active-directory"></a>启用对 Azure Active Directory 的访问

> [!NOTE]
> 如果 VM、VPN 网关或 VNet 对等互连网关具有公共 Internet 访问权限，则它可以访问 Azure Purview 门户以及启用了专用终结点的 Azure Purview 帐户。 出于此原因，无需遵循其他说明。 如果专用网络的网络安全组规则设置为拒绝所有公共 Internet 流量，则需要添加一些规则来启用 Azure Active Directory (Azure AD) 访问。 请按照这些说明进行操作。

这些说明用于指导从 Azure VM 安全地访问 Azure Purview。 如果使用 VPN 或其他 VNet 对等互连网关，必须遵循类似的步骤。

1. 在 Azure 门户中转到你的 VM，并在“设置”下选择“网络”。 然后选择“出站端口规则” > “添加出站端口规则”。

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

## <a name="deploy-self-hosted-integration-runtime-ir-and-scan-your-data-sources"></a>部署自承载集成运行时 (IR) 并扫描数据源。
为 Azure Purview 部署引入专用终结点后，需要设置并注册至少一个自承载集成运行时 (IR)：

- 当前仅基于自承载 IR 的扫描支持所有本地源类型（如 Microsoft SQL Server、Oracle、SAP 等）。 自承载 IR 必须在专用网络中运行，然后与你在 Azure 中的虚拟网络对等互连。 
   
- 对于 Azure Blob 存储和 Azure SQL 数据库等所有 Azure 源类型，必须显式选择使用与 Azure Purview 引入专用终结点部署在同一 VNet 中的自承载集成运行时来运行扫描。 

按照[创建和管理自承载集成运行时](manage-integration-runtimes.md)中的步骤设置自承载 IR。 然后，在“通过集成运行时进行连接”下拉框中选择该自承载 IR 以确保网络隔离，以在 Azure 源上设置扫描。
    
   :::image type="content" source="media/catalog-private-link/shir-for-azure.png" alt-text="显示使用自承载 IR 运行 Azure 扫描的屏幕截图。":::

> [!IMPORTANT]
> 如果在 2021 年 8 月 18 日之后创建了 Azure Purview 帐户，请确保从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并安装最新版本的自承载集成运行时。

## <a name="firewalls-to-restrict-public-access"></a>用于限制公共访问的防火墙

要完全切断从公共 Internet 对 Azure Purview 帐户的访问，请执行这些步骤。 此设置适用于专用终结点连接和引入专用终结点连接。

1. 从 Azure 门户转到 Azure Purview 帐户，在“设置” > “网络”下，选择“专用终结点连接”。

1. 转到“防火墙”选项卡，确保将切换设置为“拒绝”。

   :::image type="content" source="media/catalog-private-link/private-endpoint-firewall.png" alt-text="显示专用终结点防火墙设置的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

-  [验证专用终结点的解析](./catalog-private-link-name-resolution.md)
-  [在 Azure Purview 中管理数据源](./manage-data-sources.md)
-  [对 Azure Purview 帐户的专用终结点配置进行故障排除](./catalog-private-link-troubleshoot.md)
