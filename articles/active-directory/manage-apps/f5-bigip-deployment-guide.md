---
title: 配合 F5 的 Azure AD 安全混合访问部署指南 | Microsoft Docs
description: 在 Azure IaaS 中部署 F5 BIG-IP 虚拟版本 (VE) VM 以实现安全混合访问的教程
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.custom: devx-track-azurepowershell
ms.reviewer: miccohen
ms.openlocfilehash: f33e9a8207e3b8e6986999b7ea19aedbcb19b4da
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749210"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>关于在 Azure IaaS 中部署 F5 BIG-IP 虚拟版 VM 以实现安全混合访问的教程

本教程将指导你完成在 Azure IaaS 中部署 BIG-IP 虚拟版本 (VE) 的端到端过程。 在完成本教程后，你应该会获得：

- 完全准备好的 BIG-IP 虚拟机 (VM)，用于对安全混合访问 (SHA) 概念证明建模

- 临时过程实例，用于测试新 BIG-IP 系统更新和修补程序

## <a name="prerequisites"></a>先决条件

先前的 F5 BIG-IP 的经验或知识并不是必要的，但是，还是建议你让自己熟悉 [F5 BIG-IP 术语](https://www.f5.com/services/resources/glossary)。 为 SHA 在 Azure 中部署 BIG-IP 需要：

- 已付费的 Azure 订阅，或为期 12 个月的免费[试用版订阅](https://azure.microsoft.com/free/)。

- 以下任意 F5 BIG-IP 许可证 SKU

  - F5 BIG-IP® Best 捆绑包

  - F5 BIG-IP Access Policy Manager™ (APM) 独立许可证

  - 现有 BIG-IP F5 BIG-IP® Local Traffic Manager™ (LTM) 上的 F5 BIG-IP Access Policy Manager™ (APM) 加载项许可证
  
  - 90 天的 BIG-IP 完整功能[试用版许可证](https://www.f5.com/trial/big-ip-trial.php)。

- 通配符或使用者可选名称 (SAN) 证书，用于通过安全套接字层 (SSL) 发布 Web 应用程序。 [Let’s encrypt](https://letsencrypt.org/) 提供免费的 90 天证书，以用于测试。

- 用于保护 BIG-IP 管理界面的 SSL 证书。 如果证书使用者对应 BIG-IP 的完全限定域名 (FQDN)，则可使用用于发布 Web 应用的证书。 例如，定义了使用者 *. contoso.com 的通配符证书会适用于 `https://big-ip-vm.contoso.com:8443`

VM 部署和基本系统配置会需要大约 30 分钟，然后，你的 BIG-IP 平台将会准备就绪，以便实现[此处](f5-aad-integration.md)列出的任何 SHA 方案。

为了测试这些方案，本教程假定 BIG-IP 将会部署到包含 Active Directory (AD) 环境的 Azure 资源组中。 环境应该包含域控制器 (DC) 和 Web 主机 (IIS) VM。 将这些服务器置于 BIG-IP VM 的其他位置也是可以的，只要 BIG-IP 能够访问支持某个给定方案所需的每一个角色。 还支持通过 VPN 连接将 BIG-IP VM 连接到另一环境的方案。

如果你没有此处提到的项来进行测试，可使用此[脚本](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)将整个 AD 域环境部署到 Azure 中。 还可以使用此[脚本式自动化](https://github.com/jeevanbisht/DemoSuite)以编程方式将一批示例测试应用程序部署到 IIS Web 主机。

>[!NOTE]
>[Azure 门户](https://portal.azure.com/#home)是在不断发展的，因此，本教程中有些步骤可能会与 Azure 门户中观测到的实际布局不同。

## <a name="azure-deployment"></a>Azure 部署

可以在不同的拓扑中部署 BIG-IP。 本指南重点介绍单个网络接口 (NIC) 部署。 但是，如果 BIG-IP 部署需要多个网络接口来实现高可用性、网络隔离或超过 1 GB 的吞吐量，请考虑使用 F5 的预编译 [Azure 资源管理器 (ARM) 模板](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)。

请完成以下任务，以便从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps)部署 BIG-IP VE。

1. 使用具有创建 VM 的权限的帐户登录到 [Azure 门户](https://portal.azure.com/#home)。 例如，参与者

2. 在顶部功能区搜索框中键入“市场”，然后按 Enter 键 

3. 在“市场”筛选器中键入“F5”，然后按 Enter 键 

4. 从顶部功能区选择“+ 添加”，然后在市场筛选器中键入“F5”，然后按 Enter 键  

5. 选择“F5 BIG-IP 虚拟版本 (BYOL)” > “选择软件计划” > “F5 BIG-IP VE - 全部 (BYOL，2 个启动位置)”  

6. 选择“创建”  。

![图中显示了选择软件计划的步骤](./media/f5ve-deployment-plan/software-plan.png)

7. 逐步遍历“基本信息”菜单并使用以下设置

 |  项目详细信息     |  值     |
 |:-------|:--------|
 |订阅|BIG-IP VM 部署的目标订阅|
 |资源组 | BIG-IP VM 将部署到的现有 Azure 资源组，或者创建一个。 应该是 DC 和 IIS VM 所在的同一资源组|
 | **实例详细信息**|  |
 |VM 名称| 示例 BIG-IP-VM |
 |区域 | BIG-IP-VM 的目标 Azure 地区 |
 |可用性选项| 只有在生产中使用 VM 的情况下启用|
 |映像| F5 BIG-IP VE - 全部 (BYOL，2 个启动位置)|
 |Azure Spot 实例| 否，不过可以根据需要随时启用 |
 |大小| 最低规格应为 2 个 vCPU 和 8 Gb 内存|
 |**管理员帐户**|  |
 |身份验证类型|目前先选择密码。 以后可以切换到密钥对 |
 |用户名|该标识将会被创建为 BIG-IP 本地帐户，以用于访问其管理界面。 用户名区分大小写。|
 |密码|请使用复杂密码来保护管理员访问权限|
 |**入站端口规则**|  |
 |公共入站端口|无|

8. 选择“下一步：磁盘”，保留所有默认值，然后选择“下一步：网络” 。

9. 在“网络”菜单上，完成以下设置。

 |Linux|      值 |
 |:--------------|:----------------|
 |虚拟网络|DC 和 IIS VM 所使用的同一 Azure VNet，或者创建一个|
 |子网| DC 和 IIS VM 所在的同一 Azure 内部子网，或者创建一个|
 |公共 IP |  无|
 |NIC 网络安全组| 如果你在前面步骤中选择的 Azure 子网已与某个网络安全组 (NSG) 关联，请选择“无”；否则请选择“基本”|
 |加速网络| 关 |
 |**负载均衡**|     |
 |对 VM 进行负载均衡| 否|

10. 选择“下一步：管理”并完成以下设置。

 |监视|    值 |
 |:---------|:-----|
 |详细监视| 关|
 |启动诊断|使用自定义存储帐户启用。 允许通过 Azure 门户中的串行控制台选项连接到 BIG-IP 安全外壳 (SSH) 接口。 请选择任何可用的 Azure 存储帐户|
 |**标识**|  |
 |系统分配托管标识|关|
 |Azure Active Directory|BIG-IP 目前不支持此选项|
 |自动关闭|    |
 |启用自动关闭| 如果要进行测试，请考虑将 BIG-IP-VM 设置为每天关机|

11. 选择“下一步：高级”，保留所有默认值，并选择“下一步：标记” 。

12. 选择“下一步：查看 + 创建”来查看 BIG-IP-VM 配置，然后再选择“创建”来启动部署 。

13. 完全部署 BIG-IP VM 的时间通常为 5 分钟。 在完成后，请不要选择“转到资源”，而是要展开 Azure 门户的左侧菜单并选择“资源组”，以导航到新的 BIG-IP-VM 。 如果 VM 创建失败，请选择“上一步”和“下一步” 。

## <a name="network-configuration"></a>网络配置

在 BIG-IP VM 首次启动时，将使用它连接到的 Azure 子网的动态主机配置协议 (DHCP) 服务颁发的主专用 IP 来预配它的 NIC。 此 IP 将会由 BIG-IP 的流量管理操作系统 (TMOS) 用于与以下目标通信：

- 与其他主机和服务通信

- 到公共 Internet 的出站访问

- 到 BIG-IP Web 配置接口和 SSH 管理接口的入站访问

将这两个管理接口中的任意一个公开到 Internet 会增加 BIG-IP 攻击面，所以没有在部署过程中使用公共 IP 来预配 BIG-IP 主 IP。 而是将会为发布服务预配辅助内部 IP 和关联的公共 IP。
这个 VM 公共 IP 与专用 IP 之间的 1 对 1 映射允许外部流量访问 VM。 但是，允许流量也需要 Azure NSG 规则，方式与防火墙大致相同。

图中显示了 Azure 中 BIG-IP VE 的单个 NIC 部署，配置了主 IP 以便进行常规操作和管理，并且配置了单独的虚拟服务器 IP 以便发布服务。
根据这种安排，NSG 规则允许目标为 `intranet.contoso.com` 的远程流量在被转发到 BIG-IP 虚拟服务器之前先路由到已发布服务的公共 IP。

![图中显示了单个 NIC 部署](./media/f5ve-deployment-plan/single-nic-deployment.png)。在默认情况下，颁发到 Azure VM 的专用和公共 IP 始终是动态的，因此，在每次重启 VM 时都可能会发生更改。 请通过将 BIG-IP 管理 IP 更改为静态来避免出现无法预料的连接问题，并对用于发布服务的辅助 IP 执行相同的操作。

1. 从 BIG-IP VM 的菜单中，转到“设置” > “网络” 

2. 在网络视图中，选择“网络接口”右侧的链接

![图中显示了网络配置](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM 名称是在部署期间随机生成的。

3. 在左侧窗格中，选择“IP 配置”，然后选择“ipconfig1”行 

4. 将“IP 分配”选项设置为静态，并更改 BIG-IP VM 主 IP 地址（如有必要）。 然后选择“保存”并关闭 ipconfig1 菜单

>[!NOTE]
>你将使用此主 IP 来连接和管理 BIG-IP-VM。

5. 在顶部功能区中选择“+ 添加”，并提供辅助专用 IP 的名称，例如“ipconfig2”

6. 将专用 IP 地址设置的“分配”选项设置为“静态” 。 提供下一个较高或较低的连续 IP 会有助于保持井然有序。

7. 设置要关联的公共 IP 地址并选择“创建” 

8. 为新的公共 IP 地址提供名称，例如“BIG-IP-VM_ipconfig2_Public”

9. 如果系统提示，请将“SKU”设置为“标准”

10. 如果系统提示，请将“层级”设置为“全局”并且 

11. 将“分配”选项设置为“静态”，然后选择“确定”两次  

现在，你的 BIG-IP-VM 已准备就绪，可以进行以下设置了：

- 主要专用 IP：专用于通过 BIG-IP-VM 的 Web 配置实用工具和 SSH 来管理它。 此 IP 将由 BIG-IP 系统用作其“自我 IP”，以连接到发布的后端服务。 此外，它还会连接到外部服务，例如 NTP、AD 和 LDAP。

- 辅助专用 IP：在创建 BIG-IP APM 虚拟服务器时使用，以便侦听对已发布的服务的入站请求。

- 公共 IP：与辅助专用 IP 关联，允许来自公共 internet 的客户端流量访问已发布的服务的 BIG-IP 虚拟服务器

该示例说明了 VM 公共 IP 与专用 IP 之间的 1 对 1 关系。 Azure VM NIC 只能有一个主 IP，并且任何另外创建的 IP 都始终会被视为辅助 IP。

>[!NOTE]
>你将会需要辅助 IP 映射以便发布 BIG-IP 服务。

![图中显示了所有的辅助 IP](./media/f5ve-deployment-plan/secondary-ips.png)

若要使用 BIG-IP 访问引导式配置来实现 SHA，请重复步骤 5-11，以便为计划通过 BIG-IP APM 发布的每个额外服务都创建额外的专用 IP 和公共 IP 对。 如果使用 BIG-IP 高级配置发布服务，也可使用相同的方法。 但是，在这种情况下，你可以选择通过使用[服务器名称指示 (SNI)](https://support.f5.com/csp/#/article/K13452) 配置来避免公共 IP 开销。 在此配置中，BIG-IP 虚拟服务器将接受它接收到的所有客户端流量，并将这些流量路由到它的目标。

## <a name="dns-configuration"></a>DNS 配置

必须为客户端配置 DNS，以便将发布的 SHA 服务解析为 BIG-IP-VM 的公共 IP。

以下步骤假定在 Azure 中管理用于 SHA 服务的公共域的 DNS 区域。 但是，创建定位符记录的相同 DNS 原则仍然适用，无论在哪里管理 DNS 区域。

1. 如果门户的左侧菜单尚未打开，请将它展开，并通过“资源组”选项导航到 BIG-IP-VM

2. 从 BIG-IP VM 的菜单中，转到“设置” > “网络” 

3. 在 BIG-IP-VM 网络视图中，从下拉 IP 配置列表选择第一个辅助 IP，并选择它的“NIC 公共 IP”的链接

![显示 NIC 公共 IP 的屏幕截图](./media/f5ve-deployment-plan/networking.png)

4. 在左侧窗格中的“设置”部分下，选择“配置”，以显示所选辅助 IP 的公共 IP 和 DNS 属性菜单 

5. 选择并创建别名记录，并从下拉列表中选择 DNS 区域 。 如果某个 DNS 区域尚不存在，那么它有可能是在 Azure 外部管理的，或者你可以为要在 Azure AD 中验证的域后缀创建一个 DNS 区域。

6. 请使用以下详细信息来创建第一个 DNS 别名记录：

 | 字段 | 值 |
 |:-------|:-----------|
 |订阅| 与 BIG-IP-VM 相同的订阅|
 |DNS 区域| 对已发布网站将使用的已验证域后缀而言具有权威性的 DNS 区域，例如， www.contoso.com |
 |名称 | 指定的主机名将解析为与所选辅助 IP 关联的公共 IP。 请确保将正确的 DNS 定义到 IP 映射。 请参阅网络配置部分中的最后一幅图，例如，intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL 单位 | 小时 |

7. 为 Azure 选择“创建”，以将那些设置保存到公共 DNS。

8. 保留“DNS 名称标签 (可选)”，并选择“保存”，然后再关闭公共 IP 菜单 。

9. 重复步骤 1 至步骤 6，以便为计划使用 BIG-IP 引导式配置发布的每个服务都创建额外的 DNS 记录。

  在有了 DNS 记录后，可以使用任何联机工具（例如 [DNS 检查器](https://dnschecker.org/)）来验证创建的记录是否已成功传播到所有全局公共 DNS 服务器中。

  如果使用外部提供程序（如 [GoDaddy](https://www.godaddy.com/)）管理 DNS 域命名空间，则需要使用这些程序自己的 DNS 管理工具来创建记录。

>[!NOTE]
>如果要测试和频繁切换 DNS 记录，还可以使用电脑的本地主机文件。 在 Windows 电脑上，可通过按键盘上的 Win + R 并在运行框中提交单词“drivers”来访问 localhosts 文件。 请注意，localhost 记录将只为本地 PC（不为其他客户端）提供 DNS 解析。

## <a name="client-traffic"></a>客户端流量

在默认情况下，Azure VNet 和关联的子网是无法接收 Internet 流量的专用网络。 BIG-IP-VM 的 NIC 应该附加到部署过程中指定的 NSG。 如果要让外部 Web 流量访问 BIG-IP-VM，必须定义入站 NSG 规则，以允许从公共 Internet 通过端口 443 (HTTPS) 和 80 (HTTP)。

1. 从 BIG-IP VM 的主“概述”菜单中，选择“网络” 

2. 选择“添加入站规则”，以输入以下 NSG 规则属性：

 |     字段   |   值        |
 |:------------|:------------|
 |源| 任意|
 |源端口范围| *|
 |目标 IP 地址|逗号分隔的所有 BIG-IP-VM 辅助专用 IP 的列表|
 |目标端口| 80,443|
 |协议| TCP |
 |操作| Allow|
 |优先级|介于 100 - 4096 之间的最低可用值|
 |名称 | 描述性名称，例如：`BIG-IP-VM_Web_Services_80_443`|

3. 选择“添加”，以提交更改，并关闭“网络”菜单 。

现在，将允许来自任何位置的 HTTP 和 HTTPS 流量访问所有 BIG-IP-VM 辅助接口。 如果要允许 BIG-IP APM 自动将用户从 HTTP 重定向到 HTTPS，则允许端口 80 很有用。 可在必要时编辑此规则，以添加或删除目标 IP。

## <a name="manage-big-ip"></a>管理 BIG-IP

BIG-IP 系统通过其 Web 配置 UI 进行管理，可使用以下建议的任一方法访问该 UI：

- 从 BIG-IP 的内部网络中的计算机

- 从连接到 BIG-IP-VM 的内部网络的 VPN 客户端

- 通过 [Azure AD 应用程序代理](../app-proxy/application-proxy-add-on-premises-application.md)发布

需要确定最适合的方法，然后才能继续进行其他配置。 如有必要，可从 Internet 直接连接到 Web 配置。方法如下：使用公共 IP 配置 BIG-IP 的主 IP。 然后添加 NSG 规则，以允许将 8443 流量发送到该主 IP。 请确保将源限制为你自己的受信任 IP，否则任何人都可以连接。

在准备就绪后，请确认可以连接到 BIG-IP VM 的 Web 配置，并使用在 VM 部署过程中指定的凭据来登录：

- 如果要从其内部网络上的 VM 或通过 VPN 进行连接，请直接连接到 BIG-IP 主 IP 和 Web 配置端口。 例如 `https://<BIG-IP-VM_Primary_IP:8443`。 浏览器将会提示该连接不安全，不过可以忽略该提示，直至 BIG-IP 配置完成。 如果浏览器坚持阻止访问，请清空浏览器缓存，然后重试。

- 如果是通过应用程序代理发布的 Web 配置，那么，请使用为从外部访问 Web 配置而定义的 URL，不追加端口，例如 `https://big-ip-vm.contoso.com`。 必须使用 Web 配置端口定义内部 URL，例如 `https://big-ip-vm.contoso.com:8443`

还可以通过 BIG-IP 系统的底层 SSH 环境来管理 BIG-IP 系统，该环境通常用于命令行 (CLI) 任务和根级别访问。 若要连接到 CLI，有多种可选方法，包括：

- [Azure Bastion 服务](../../bastion/bastion-overview.md)：允许从任何位置快速安全地连接到 vNET 中的任何 VM

- 利用 JIT 方法直接通过 SSH 客户端（如 PuTTY）进行连接

- 串行控制台：在门户的“VM”菜单的“支持和故障排除”部分的底部提供。 此方法不支持文件传输。

- 与 Web 配置一样，还可以通过使用公共 IP 来配置 BIG-IP 的主 IP 并添加允许 SSH 流量的 NSG 规则，以直接从 Internet 连接到 CLI。 同样，如果使用此方法，请确保将源限制为你自己的受信任 IP。  

## <a name="big-ip-license"></a>BIG-IP 许可证

BIG-IP 系统必须先激活并预配 APM 模块，然后才能配置该系统来发布服务和进行 SHA。

1. 重新登录到 Web 配置，并在“常规属性”页上选择“激活” 

2. 在“基础注册密钥”字段中，输入 F5 提供的区分大小写的密钥

3. 将“激活方法”设置为“自动”，然后选择“下一步”，BIG-IP 将无法验证许可证，并显示最终用户许可协议 (EULA)  。

4. 选择“接受”，并等待激活完成，然后选择“继续” 。

5. 重新登录，并在许可证摘要页的底部，选择“下一步”。 BIG-IP 现在将显示提供 SHA 所需各种功能的模块的列表。 如果该列表未出现，请在主选项卡中转到“系统” > “资源预配” 。

6. 检查预配列中是否有访问策略 (APM)

![图中显示了访问预配](./media/f5ve-deployment-plan/access-provisioning.png)

7. 选择“提交”并接受警告信息

8. 请耐心等待 BIG-IP 完成激活新功能。 此过程可能会循环几次才能完全初始化。 

9. 在准备就绪后，选择“继续”，然后从“关于”选项卡中选择“运行安装程序”  

>[!IMPORTANT]
>F5 许可证会被限制为在任何时间由一个 BIG-IP VE 实例使用。 有时可能需要将许可证从一个实例迁移到另一个实例，如果要这样操作，请确保在解除活动实例上的试用版许可证授权之前先将其[撤销](https://support.f5.com/csp/article/K41458656)，否则许可证将会永久丢失。

## <a name="provision-big-ip"></a>预配 BIG-IP

保护进出 BIG-IP Web 配置的管理流量极其重要。 请配置设备管理证书，以帮助防止 Web 配置通道发生泄漏。

1. 从左侧导航栏中转到“系统” > “证书管理” > “通讯证书管理” > “SSL 证书列表” > “导入”    

2. 从“导入类型”下拉列表中，选择“PKCS 12(IIS)”，并选择“选择文件”  。 找到具有使用者名称或 SAN 的 SSL Web 证书，该证书将涉及你在后面几个步骤中要分配 BIG-IP-VM 的 FQDN

3. 提供证书的密码，然后选择“导入”

4. 从左侧导航栏中转到“系统” > “平台” 

5. 使用完全限定的主机名和 BIG-IP-VM 环境的时区来配置 BIG-IP-VM，然后选择“更新”

![图中显示了常规属性](./media/f5ve-deployment-plan/general-properties.png)

6. 从左侧导航栏中转到“系统” > “配置” > “设备” > “NTP”   

7. 指定可靠的 NTP 源，并选择“添加”，然后选择“更新” 。 例如，`time.windows.com`

你现在需要 DNS 记录，以将前面步骤中指定的 BIG-IP FQDN 解析到其主专用 IP。 应将一条记录添加到你的环境的内部 DNS，或添加到将用于连接到 BIG-IP 的 Web 配置的 PC 的 localhost 文件中。无论采用哪种方式，在直接连接到 Web 配置时，都不会再出现浏览器警告。 也就是说，不通过应用程序代理或任何其他反向代理。

## <a name="ssl-profile"></a>SSL 配置文件

作为反向代理，BIG-IP 系统可以充当简单转发服务（也称为“透明代理”）或主动参与客户端和服务器之间交换的完全代理。
完全代理会创建两个非重复连接：一个前端 TCP 客户端连接和一个单独的后端 TCP 服务器连接，二者由中间的一个软间隙连接。 客户端在一端连接到代理侦听器（也称为“虚拟服务器”），并且代理会建立到后端服务器的单独无依赖项连接。 这在两边都是双向的。
在此完全代理模式下，F5 BIG-IP 系统可以检查流量，因此也可以与请求和响应进行交互。 有些功能（例如负载平衡和 Web 性能优化）以及更高级的流量管理服务（例如应用程序层安全性、Web 加速、页面路由和安全远程访问）依赖于此功能。
在发布基于 SSL 的服务时，在客户端和后端服务之间对流量进行解密和加密的过程由 BIG-IP SSL 配置文件处理。

存在两种类型的配置文件：

- 客户端 SSL：设置 BIG-IP 系统以使用 SSL 发布内部服务的最常见方法是创建客户端 SSL 配置文件。 使用客户端 SSL 配置文件，BIG-IP 系统可以在将入站客户端请求发送到下游服务之前先对这些请求进行解密。 然后，在将出站后端响应发送到客户端之前对这些响应进行加密。

- 服务器 SSL：对于为 HTTPS 配置的后端服务，可以将 BIG-IP 配置为使用服务器 SSL 配置文件。 使用服务器 SSL 配置文件，BIG-IP 会在将客户端请求发送到目标后端服务之前先对该请求重新加密。 在服务器返回加密的响应时，BIG-IP 系统会在将该响应发送到客户端之前，先通过配置的客户端 SSL 配置文件来对该响应进行解密和重新加密。

对客户端和服务器 SSL 配置文件都进行预配，将会预先配置好 BIG-IP 并让它为所有 SHA 方案准备就绪。

1. 从左侧导航栏中转到“系统” > “证书管理” > “通讯证书管理” > “SSL 证书列表” > “导入”    

2. 从“导入类型”下拉列表中，选择“PKCS 12(IIS)” 

3. 为导入的证书提供名称，例如 `ContosoWilcardCert`

4. 选择“选择文件”，浏览到其使用者名称对应于计划用于已发布服务的域后缀的 SSL Web 证书

5. 为导入的证书提供密码，然后选择“导入” 

6. 从左侧导航栏中转到“本地流量” > “配置文件” > “SSL” > “客户端”，然后选择“创建”    

7. 在“新建客户端 SSL 配置文件”页中，为新客户端 SSL 配置文件提供独一无二的易记名称，并确保将父配置文件设置为“clientssl” 

![图中显示了更新 big-ip](./media/f5ve-deployment-plan/client-ssl.png)

8. 选中“证书密钥链”行中最右侧的复选框，然后选择“添加” 

9. 从三个下拉列表中，选择你导入的不带密码的通配符证书，然后选择“添加” > “已完成” 。

![图中显示了更新 big-ip contoso 通配符](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. 重复步骤 6-9，以创建 SSL 服务器证书配置文件。 从顶部功能区中选择“SSL” > “服务器” > “创建”  。

11. 在“新建服务器 SSL 配置文件”页中，为新服务器 SSL 配置文件提供独一无二的易记名称，并确保将父配置文件设置为“serverssl” 

12. 选中“证书”和“密钥”行最右侧的复选框，并从下拉列表中选择导入的证书，然后选择“已完成”。

![图中显示了更新 big-ip 服务器所有配置文件](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>如果无法购买 SSL 证书，请不必担心，可以使用集成的自签名 BIG-IP 服务器和客户端 SSL 证书。 只是会在浏览器中看到证书错误。

为 SHA 准备 BIG-IP 的最后一步，是确保它能够找到它要发布的资源以及它进行 SSO 所依赖的目录服务。 BIG-IP 有两个名称解析的源，从其 local/.../hosts 文件开始，或者，如果找不到记录，则 BIG-IP 系统会使用任何它已配置的 DNS 服务。 hosts 文件方法不适用于使用 FQDN 的 APM 节点和池。

1. 在 Web 配置中，转到“系统” > “配置” > “设备” > “DNS”   

2. 在“DNS 查找服务器列表”中，输入环境 DNS 服务器的 IP 地址

3. 选择“添加” > “更新” 

作为一个单独的可选步骤，可以考虑使用 [LDAP 配置](https://somoit.net/f5-big-ip/authentication-using-active-directory)来针对 AD 对 BIG-IP 系统管理员进行身份验证，而不是管理本地 BIG-IP 帐户。

## <a name="update-big-ip"></a>更新 BIG-IP

应更新 BIG-IP VM，以解锁[基于方案的指南](f5-aad-integration.md)中介绍的所有新功能。 可以通过将鼠标指针悬停在主页左上角的 BIG-IP 主机名上来检查系统 TMOS 版本。 建议运行 v15.x 和更高版本（可从 [F5 下载](https://downloads.f5.com/esd/productlines.jsp)获得）。 请使用[指南](https://support.f5.com/csp/article/K34745165)来更新主系统 OS (TMOS)。

如果无法更新主 TMOS，请至少考虑使用以下步骤来单独升级引导式配置。

1. 从 BIG-IP Web 配置中的主选项卡，转到“访问” > “指导配置” 

2. 在“引导式配置”页上，选择“升级引导式配置” 

![图中显示了如何更新 big-ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. 在“升级引导式配置”对话框中，选择“选择文件”，以上传下载的用例包，然后选择“上传并安装”  

4. 在完成升级后，选择“继续”。

## <a name="backup-big-ip"></a>备份 BIG-IP

现在，已完全预配了 BIG-IP 系统，建议对其配置执行完整备份：

1. 转到“系统” > “存档” > “创建”  

2. 提供独一无二的“文件名”，并使用密码启用“加密” 

3. 将“私钥”选项设置为“包括”，以便也备份设备和 SSL 证书 

4. 选择“已完成”并等待该进程完成

5. 将会显示操作状态，提供备份结果的状态。 选择“确定”

6. 通过选择该备份的链接并选择“下载”，将用户配置集 (UCS) 存档保存在本地。

作为可选步骤，还可以使用 [Azure 快照](../../virtual-machines/windows/snapshot-copy-managed-disk.md)来执行整个系统磁盘的备份，此方法与 Web 配置备份不同，它会为在 TMOS 版本之间测试或回滚到全新系统提供一些应急措施。

```PowerShell
# Install modules
Install-module Az
Install-module AzureVMSnapshots

# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Create Snapshot
New-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>'

#List Snapshots
#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>'

#Get-AzVmSnapshot -ResourceGroupName ‘<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot -RemoveOriginalDisk 

```

## <a name="restore-big-ip"></a>还原 BIG-IP

还原 BIG-IP 会按照与备份类似的过程进行，并且还可用于在 BIG-IP VM 之间迁移配置。 在导入备份之前，应关注有关受支持的升级路径的详细信息。

1. 转到“系统” > “存档” 

2. 选择要还原的备份的链接，或者，选择“上传”按钮以浏览到列表中未列出的某个以前保存的 UCS 存档

3. 提供该备份的密码并选择“还原”

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>在撰写本文时，AzVmSnapshot cmdlet 限制为基于日期还原最新的快照。 快照存储在 VM 的资源组的根位置中。 请注意，还原快照会重启 Azure VM，因此，请慎重安排操作时间。

## <a name="additional-resources"></a>其他资源

- [在 Azure 中重置 BIG-IP VE 密码](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
- [在不使用门户的情况下重置密码](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

- [更改用于 BIG-IP VE 管理的 NIC](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

- [关于一个 NIC 配置中的路由](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

- [Microsoft Azure：Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>后续步骤

选择[部署方案](f5-aad-integration.md)并开始实现。
