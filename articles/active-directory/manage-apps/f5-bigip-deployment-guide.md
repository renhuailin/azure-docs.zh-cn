---
title: 使用 F5 的 Azure AD 安全混合访问部署指南 |Microsoft Docs
description: 在 Azure IaaS 中部署 F5 大 IP 虚拟版 (VE) VM 以实现安全混合访问的教程
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 10/12/2020
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: f962bf131b87f17712186145b8c8b8e6090f7002
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730650"
---
# <a name="tutorial-to-deploy-f5-big-ip-virtual-edition-vm-in-azure-iaas-for-secure-hybrid-access"></a>在 Azure IaaS 中部署 F5 大 IP 虚拟版 VM 以实现安全混合访问的教程

本教程将指导你完成在 Azure IaaS 中部署大 IP Vitural Edition (VE) 的端到端过程。 本教程结束时，你应该具有：

- 完全准备好的大 IP 虚拟机 (VM) 用于对安全混合访问进行建模 (SHA) 概念证明

- 用于测试新的大型 IP 系统更新和修补程序的临时实例

## <a name="prerequisites"></a>先决条件

但在此情况下，不需要 [大 ip 经验](https://www.f5.com/services/resources/glossary)或知识，我们建议你自行熟悉。 在 Azure 中部署适用于 SHA 的大 IP 需要：

- 付费 Azure 订阅或为期12个月的免费 [试用订阅](https://azure.microsoft.com/free/)。

- 以下任意 F5 大 IP 许可证 Sku

  - F5 大 IP®最佳捆绑包

  - F5 BIG-IP Access Policy Manager™ (APM) 独立许可证

  - F5 大 IP 访问策略管理器™ (APM) 现有大 IP F5 大 IP 上的附加许可证®本地流量管理器™ (LTM) 
  
  - 90天大 IP 完整功能 [试用版许可证](https://www.f5.com/trial/big-ip-trial.php)。

-  (SAN) 证书的通配符或使用者备用名称，用于通过安全套接字层 (SSL) 发布 web 应用程序。 [我们的加密](https://letsencrypt.org/) 提供免费的90天证书用于测试。

- 用于保护 BIG-IPs 管理界面的 SSL 证书。 如果证书使用者与大 IP 的完全限定域名相对应 (FQDN) ，则可以使用用于发布 web 应用的证书。 例如，使用 subject *. contoso.com 定义的通配符证书适用于 `https://big-ip-vm.contoso.com:8443`

VM 部署和基本系统配置需要大约30分钟，此时你的大 IP 平台将准备好实现 [此处](f5-aad-integration.md)列出的任何 SHA 方案。

对于测试方案，本教程假定大 IP 将部署到包含 Active Directory (AD) 环境的 Azure 资源组中。 环境应包含域控制器 (DC) 和 web 主机 (IIS) Vm。 如果将这些服务器置于大 IP VM 的其他位置，还可以提供对支持给定方案所需的每个角色的大 IP。 还支持使用大 IP VM 通过 VPN 连接连接到另一环境的情况。

如果在此处未提到用于测试的项目，则可以使用此 [脚本](https://github.com/Rainier-MSFT/Cloud_Identity_Lab)将整个 AD 域环境部署到 Azure 中。 还可以使用此 [脚本的自动化](https://github.com/jeevanbisht/DemoSuite)，以编程方式将示例测试应用程序的集合部署到 IIS web 主机。

>[!NOTE]
>[Azure 门户](https://portal.azure.com/#home)不断发展，因此本教程中的某些步骤可能与 Azure 门户中观测到的实际布局不同。

## <a name="azure-deployment"></a>Azure 部署

可以在不同的拓扑中部署大 IP。 本指南重点介绍单个网络接口 (NIC) 部署。 但是，如果大 IP 部署需要多个网络接口来实现高可用性、网络隔离或超过 1 GB 的吞吐量，请考虑使用 F5 bigip 预编译 [Azure 资源管理器 (ARM) 模板](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_multiNIC.html)。

完成以下任务，从 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps)部署大 IP VE。

1. 使用具有创建 Vm 权限的帐户登录 [Azure 门户](https://portal.azure.com/#home) 。 例如，参与者

2. 在顶部的功能区搜索框中键入 **marketplace**，然后 **输入**

3. 在 Marketplace 筛选器中键入 **F5** ，然后 **输入**

4. 在顶部功能区中选择 " **+ 添加** "，然后在 marketplace 筛选器中键入 **F5** ，然后 **输入**

5. 选择 " **F5 大 ip 虚拟版 (BYOL")**  >  **选择软件计划**  >  **F5 大 ip VE (BYOL，2个启动位置)**

6. 选择“创建”。

![此图显示了选择软件计划的步骤](./media/f5ve-deployment-plan/software-plan.png)

7. 单步执行 " **基本** " 菜单并使用以下设置

 |  项目详细信息     |  值     |
 |:-------|:--------|
 |订阅|大型 IP VM 部署的目标订阅|
 |资源组 | 将大 IP VM 部署到其中或创建一个现有 Azure 资源组。 应该是 DC 和 IIS Vm 的相同资源组|
 | **实例详细信息**|  |
 |VM 名称| 大 IP VM 示例 |
 |区域 | 大型 IP 的目标 Azure 地域-VM |
 |可用性选项| 仅在生产中使用 VM 时启用|
 |图像| F5 大 IP VE-ALL (BYOL，2 Boot 位置) |
 |Azure Spot 实例| 不能，但可以根据需要启用 |
 |大小| 最低规格应为2个 vcpu 和 8 Gb 内存|
 |**管理员帐户**|  |
 |身份验证类型|选择 "立即为密码"。 稍后可以切换到密钥对 |
 |用户名|将创建为访问其管理接口的大 IP 本地帐户的标识。 用户名区分大小写。|
 |密码|使用强密码保护管理员访问|
 |**入站端口规则**|  |
 |公共入站端口|无|

8. 选择 " **下一步"：** 保留所有默认值的磁盘，然后选择 **下一步：联网**。

9. 在 " **网络** " 菜单上，完成这些设置。

 |网络接口|      值 |
 |:--------------|:----------------|
 |虚拟网络|DC 和 IIS Vm 使用相同的 Azure VNet，或创建一个|
 |子网| 与 DC 和 IIS Vm 相同的 Azure 内部子网，或创建一个|
 |公共 IP |  无|
 |NIC 网络安全组| 如果你在前面步骤中选择的 Azure 子网已与网络安全组 (NSG) 相关联，请选择 "无"。否则选择 "基本"|
 |加速网络| 关 |
 |**负载均衡**|     |
 |负载平衡 VM| 否|

10. 选择 " **下一步：管理** " 并完成这些设置。

 |监视|    值 |
 |:---------|:-----|
 |详细监视| 关|
 |启动诊断|使用自定义存储帐户启用。 允许通过 Azure 门户中的串行控制台选项连接到大 IP 安全外壳 (SSH) 接口。 选择任何可用的 Azure 存储帐户|
 |**标识**|  |
 |系统分配托管标识|关|
 |Azure Active Directory|大 IP 目前不支持此选项|
 |**自动关闭**|    |
 |启用自动关闭| 如果要进行测试，请考虑将大 IP VM 设置为每天关机|

11. 选择 " **下一步"：高级** 保留所有默认值并选择 " **下一步：标记**"。

12. 选择 " **下一步"：** 在选择 " **创建** " 开始部署之前，查看 "+ 创建" 查看你的大 IP VM 配置。

13. 完全部署大 IP VM 的时间通常为5分钟。 完成后，请选择 " **转到资源**"，而不是展开 Azure 门户的左侧菜单，并选择 " **资源组** "，导航到新的大 IP VM。 如果 VM 创建失败，请选择 "上 **一步" 和 "下一步**"。

## <a name="network-configuration"></a>网络配置

当大 IP VM 首次启动时，将使用动态主机配置协议颁发的 **主** 专用 IP 来设置其 NIC， (其连接到的 Azure 子网的 DHCP) 服务。 此 IP 将由大 IP 的流量管理操作系统 (TMOS) 用于与通信：

- 与其他主机和服务通信

- 对公共 internet 的出站访问

- BIG-IPs web 配置和 SSH 管理接口的入站访问

将其中任一管理接口公开到 internet 会增加 BIG-IPs 攻击面，因此在部署过程中未使用公共 IP 设置 BIG-IPs 主 IP 的原因。 相反，将为发布服务预配辅助内部 IP 和关联的公共 IP。
VM 公共 IP 与专用 IP 之间的1到1个映射允许外部流量访问 VM。 但是，允许使用 Azure NSG 规则的方式与防火墙大致相同。

此关系图显示了 Azure 中的大 IP VE 的单个 NIC 部署，使用主 IP 进行常规操作和管理，以及用于发布服务的单独的虚拟服务器 Ip。
在这种情况下，NSG 规则允许将目标为的远程流量 `intranet.contoso.com` 路由到已发布服务的公共 IP，然后将其转发到大 IP 虚拟服务器。

![默认情况下，此图显示单个 nic 部署 ](./media/f5ve-deployment-plan/single-nic-deployment.png) ，颁发给 Azure vm 的专用和公共 ip 始终是动态的，因此在每次重启 VM 时可能会发生更改。 通过将 BIG-IPs 管理 IP 更改为静态，并对用于发布服务的辅助 Ip 执行相同操作，避免出现无法预料的连接问题。

1. 从大 IP VM 的菜单中转到 "**设置**" "  >  **网络**"

2. 在 "网络" 视图中，选择 **网络接口** 右侧的链接

![此图显示了网络配置](./media/f5ve-deployment-plan/network-config.png)

>[!NOTE]
>VM 名称是在部署期间随机生成的。

3. 在左侧窗格中，选择 " **IP 配置** "，然后选择 " **ipconfig1** 行"

4. 将 **IP 分配** 选项设置为静态，如有必要，请更改大 ip VM 主 IP 地址。 然后选择 " **保存** " 并关闭 "ipconfig1" 菜单

>[!NOTE]
>你将使用此主 IP 来连接和管理大 IP VM。

5. 在顶部功能区中选择 " **+ 添加** "，并为辅助专用 IP 提供名称，例如 vm1nic2-ipconfig2

6. 将 "专用 IP 地址" 设置的 " **分配** " 选项设置为 " **静态**"。 提供下一个较高或较低的连续 IP 有助于保持一切正常。

7. 设置要 **关联** 的公共 IP 地址，然后选择 "**创建**"

8. 为新的公共 IP 地址提供名称，例如，大 IP VM_ipconfig2_Public

9. 如果系统提示，请将 **SKU** 设置为 Standard

10. 如果出现提示，请将 **层** 设置为 **Global** ，并

11. 将 " **分配** " 选项设置为 " **静态**"，然后选择 **"确定"** 两次

你的大 IP VM 现在可以设置：

- **主要专用 IP**：专用于通过其 Web 配置实用工具和 SSH 管理大 IP VM。 它将由大 IP 系统用作其 **自 IP** 来连接到已发布的后端服务。 此外，它还连接到外部服务，例如 NTP、AD 和 LDAP。

- **辅助专用 IP**：创建大 ip APM 虚拟服务器以侦听对已发布服务 () 的入站请求时使用。

- **公共 ip**：与辅助专用 ip 相关联，允许来自公共 internet 的客户端流量到达已发布服务 (的大 IP 虚拟服务器) 

该示例演示了 VM 公共 Ip 与专用 Ip 之间的1到1关系。 Azure VM NIC 只能有一个主 IP，并且任何其他创建的 Ip 始终称为 "辅助"。

>[!NOTE]
>你需要辅助 IP 映射来发布大 IP 服务。

![此图像显示所有辅助 Ip](./media/f5ve-deployment-plan/secondary-ips.png)

若要使用大 IP **访问指导配置** 来实现 SHA，请重复步骤5-11，为你计划通过大 ip APM 发布的每个附加服务创建其他专用和公共 IP 对。 如果使用 BIG-IPs **高级配置** 发布服务，也可使用相同的方法。 但是，在这种情况下，你可以选择使用 [服务器名称指示器 (SNI) ](https://support.f5.com/csp/#/article/K13452) 配置来避免公共 IP 开销。 在此配置中，大 IP 虚拟服务器将接受接收的所有客户端流量，并将其路由到其目标。

## <a name="dns-configuration"></a>DNS 配置

必须为客户端配置 DNS，以便将已发布的 SHA 服务解析为大 IP VM 的公共 IP () 。

以下步骤假定在 Azure 中管理用于 SHA 服务的公共域的 DNS 区域。 但是，创建定位符记录的相同 DNS 原则仍适用，无论你的 DNS 区域是在哪里管理的。

1. 如果尚未打开，请展开门户的左侧菜单，并通过 " **资源组** " 选项导航到大 IP VM

2. 从大 IP VM 的菜单中转到 "**设置**" "  >  **网络**"

3. 在 "大 IP-Vm 网络" 视图中，从下拉 IP 配置列表中选择第一个辅助 IP，并为其 **NIC 公共 IP** 选择链接

![显示 NIC 公共 IP 的屏幕截图](./media/f5ve-deployment-plan/networking.png)

4. 在左侧窗格的 " **设置** " 部分下，选择 " **配置** " 以显示所选辅助 IP 的 "公共 IP 和 DNS 属性" 菜单

5. 选择并 **创建** 别名记录，并从下拉列表中选择你的 **DNS 区域** 。 如果 DNS 区域尚不存在，则它可能会在 Azure 外部进行管理，也可以为在 Azure AD 中验证的域后缀创建一个。

6. 使用以下详细信息创建第一个 DNS 别名记录：

 | 字段 | 值 |
 |:-------|:-----------|
 |订阅| 与大 IP VM 相同的订阅|
 |DNS 区域| 已发布网站将使用的已验证域后缀的权威 DNS 区域，例如，www.contoso.com |
 |名称 | 指定的主机名将解析为与所选辅助 IP 关联的公共 IP。 请确保将正确的 DNS 定义为 IP 映射。 请参阅网络配置部分中的最后一个图像，例如 intranet.contoso.com > 13.77.148.215|
 | TTL | 1 |
 |TTL 单位 | 小时 |

7. 选择 " **创建** Azure" 以将这些设置保存到公共 DNS。

8. 保留 **DNS 名称标签 (可选)** 并在关闭公共 IP 菜单之前选择 " **保存** "。

9. 重复步骤1到步骤6，为计划使用大 IP 引导式配置发布的每个服务创建其他 DNS 记录。

  使用 DNS 记录时，可以使用任何联机工具（例如 [dns 检查器](https://dnschecker.org/) ）来验证创建的记录是否已成功地传播到所有全局公共 DNS 服务器。

  如果使用外部提供程序（如 [GoDaddy](https://www.godaddy.com/)）管理 DNS 域命名空间，则需要使用其自己的 DNS 管理工具创建记录。

>[!NOTE]
>如果测试和频繁切换 DNS 记录，还可以使用电脑的本地主机文件。 可以通过按键盘上的 Win + R 并在 "运行" 框中提交 word **驱动程序** 来访问 Windows 电脑上的 localhosts 文件。 请注意，localhost 记录将仅为本地 PC 提供 DNS 解析，而不为其他客户端提供 DNS 解析。

## <a name="client-traffic"></a>客户端流量

默认情况下，Azure Vnet 和关联的子网是无法接收 Internet 流量的专用网络。 应将大 IP VM 的 NIC 附加到部署过程中指定的 NSG。 要使外部 web 流量到达大 IP VM，你必须定义一个入站 NSG 规则，以允许端口 443 (HTTPS) 和 80 (通过公共 internet) 的 HTTP。

1. 在大 IP VM 的主 **概述** 菜单中，选择 "**网络**"

2. 选择 " **添加** 入站规则" 以输入以下 NSG 规则属性：

 |     字段   |   值        |
 |:------------|:------------|
 |源| 任意|
 |源端口范围| *|
 |目标 IP 地址|所有大 IP VM 辅助专用 Ip 的逗号分隔列表|
 |目标端口| 80,443|
 |协议| TCP |
 |操作| Allow|
 |优先级|最小可用值介于 100-4096 之间|
 |名称 | 一个描述性名称，例如： `BIG-IP-VM_Web_Services_80_443`|

3. 选择 " **添加** " 以提交更改，并关闭 " **网络** " 菜单。

现在允许来自任何位置的 HTTP 和 HTTPS 流量访问所有大 IP Vm 辅助接口。 如果允许大 IP APM 自动将用户从 HTTP 重定向到 HTTPS，则允许端口80很有用。 此规则可以根据需要进行编辑，以便添加或删除目标 Ip。

## <a name="manage-big-ip"></a>管理大 IP

大型 IP 系统通过其 web 配置 UI 进行管理，可以使用以下建议的方法之一对其进行访问：

- 从大 IP 内部网络中的计算机

- 从连接到大 IP VM 内部网络的 VPN 客户端

- 通过[Azure AD 应用程序代理](./application-proxy-add-on-premises-application.md)发布

你需要确定最适合的方法，然后才能继续进行其他配置。 如有必要，你可以通过使用公共 IP 配置大 IP 的主 IP，从 internet 直接连接到 web 配置。 然后添加 NSG 规则，以允许将8443流量发送到该主 IP。 请确保将源限制为你自己的受信任 IP，否则任何人都可以连接。

准备就绪后，确认可以连接到大 IP VM 的 web 配置，并通过在 VM 部署过程中指定的凭据登录：

- 如果要从其内部网络上的 VM 或通过 VPN 进行连接，请直接连接到 BIG-IPs 主 IP 和 web 配置端口。 例如，`https://<BIG-IP-VM_Primary_IP:8443`。 你的浏览器将提示连接不安全，但你可以忽略此提示，直到配置了大 IP。 如果浏览器一定阻止访问，请清除其缓存，然后重试。

- 如果是通过应用程序代理发布的 web 配置，则使用定义的 URL 来访问 web 配置，而无需追加端口，例如 `https://big-ip-vm.contoso.com` 。 内部 URL 必须使用 web 配置端口进行定义，例如 `https://big-ip-vm.contoso.com:8443` 

还可以通过其底层 SSH 环境管理大 IP 系统，该环境通常用于命令行 (CLI) 任务和根级别访问。 有几个选项可用于连接到 CLI，其中包括：

- [Azure 堡垒服务](../../bastion/bastion-overview.md)：允许从任何位置快速安全地连接到 vNET 中的任何 VM

- 通过 JIT 方法直接通过 SSH 客户端（如 PuTTY）进行连接

- 串行控制台：在门户的 "Vm" 菜单的 "支持和故障排除" 部分的底部提供。 它不支持文件传输。

- 与 web 配置一样，你还可以通过使用公共 IP 配置大 IP IP 的主 IP 并添加 NSG 规则以允许 SSH 流量，直接从 internet 连接到 CLI。 同样，如果使用此方法，请务必将源限制为你自己的受信任 IP。  

## <a name="big-ip-license"></a>大 IP 许可证

必须先使用 APM 模块激活和设置大 IP 系统，然后才能为发布服务和 SHA 配置该系统。

1. 重新登录到 web 配置，在 "**常规属性**" 页上选择 "**激活**"

2. 在 " **基注册密钥** " 字段中，输入按 F5 提供的区分大小写的密钥

3. 将 **激活方法** 设置为 " **自动** "，然后选择 " **下一步**"，大 IP 将验证许可证并显示最终用户许可协议 (EULA) 。

4. 选择 " **接受** " 并等待激活完成，然后再选择 " **继续**"。

5. 重新登录，并在 "许可证摘要" 页的底部，选择 " **下一步**"。 大 IP 现在将显示提供 SHA 所需的各种功能的模块列表。 如果看不到此，请在主选项卡中，执行 "**系统**  >  **资源设置**"。

6. 在 APM) 中检查访问策略的预配列 (

![此图像显示访问权限设置](./media/f5ve-deployment-plan/access-provisioning.png)

7. 选择 " **提交** " 并接受警告

8. 请耐心等待，并等待大 IP 完成全新功能的反射。 它可能会循环几次，才能完全初始化。 

9. 准备好后，选择 " **继续** "，然后从 " **关于** " 选项卡中选择 " **运行安装程序**

>[!IMPORTANT]
>F5 许可证被限制为在任一时间由单个大 IP VE 实例使用。 可能有理由将许可证从一个实例迁移到另一个实例，如果这样做，请务必在解除活动实例[](https://support.f5.com/csp/article/K41458656)的试用版许可证后将其取消，否则许可证会永久丢失。

## <a name="provision-big-ip"></a>设置大 IP

保护进出 BIG-IPs web 配置的管理流量极其重要。 配置设备管理证书，以帮助防止 web 配置通道泄露。

1. 从左侧导航栏中转到 "**系统**  >  **证书管理**" "  >  **通讯证书管理**" "  >  **SSL 证书列表**  >  **导入**"

2. 从 " **导入类型** " 下拉列表中，选择 " **PKCS 12 (IIS)** 并 **选择" 文件**"。 在接下来的几个步骤中，查找具有使用者名称或 SAN 的 SSL web 证书，该证书将涵盖用于分配大 IP VM 的 FQDN。

3. 提供证书的密码，然后选择 "**导入**"

4. 从左侧导航栏中转到 "**系统**  >  **平台**"

5. 使用完全限定的主机名和其环境的时区配置大 IP VM，然后 **更新**

![图像显示常规属性](./media/f5ve-deployment-plan/general-properties.png)

6. 从左侧导航栏中转到 "**系统**  >  **配置**  >  **设备**  >  **NTP** "

7. 指定可靠的 NTP 源并选择 " **添加**"，然后选择 " **更新**"。 例如： `time.windows.com`

你现在需要一条 DNS 记录，以将之前步骤中指定的 BIG-IPs FQDN 解析到其主专用 IP。 应将一条记录添加到你的环境的内部 DNS，或添加到将用于连接到大 IP web 配置的 PC 的 localhost 文件中。无论采用哪种方式，在直接连接到 web 配置时，都不会再出现浏览器警告。 也就是说，不通过应用程序代理或其他反向代理。

## <a name="ssl-profile"></a>SSL 配置文件

作为反向代理，大 IP 系统可以充当简单转发服务（也称为透明代理），也可以充当完全参与客户端和服务器之间交换的完全代理。
完全代理将创建两个不同的连接：一个前端 TCP 客户端连接和一个单独的后端 TCP 服务器连接，然后再加上一个软空白。 客户端连接到一端（也称为 **虚拟服务器**）上的代理侦听器，而代理建立与后端服务器的独立连接。 这是双向的。
在此完全代理模式下，F5 大 IP 系统可以检查流量，因此也可以与请求和响应进行交互。 某些功能（例如负载平衡和 web 性能优化）以及更高级的流量管理服务（例如应用程序层安全性、web 加速、页面路由和安全远程访问）依赖于此功能。
在发布基于 SSL 的服务时，在客户端和后端服务之间对流量进行解密和加密的过程由大 IP SSL 配置文件处理。

存在两种类型的配置文件：

- **客户端 SSL**：设置大 IP 系统以便使用 SSL 发布内部服务的最常见方法是创建客户端 SSL 配置文件。 使用客户端 SSL 配置文件，大 IP 系统可以在将入站客户端请求发送到下游服务之前对其进行解密。 然后，在将出站后端响应发送到客户端之前对其进行加密。

- **服务器 ssl**：对于使用 HTTPS 配置的后端服务，可以将大 IP 配置为使用服务器 SSL 配置文件。 使用服务器 SSL 配置文件，大 IP 会在将客户端请求发送到目标后端服务之前对其进行重新加密。 当服务器返回加密响应时，大 IP 系统会在将响应发送到客户端之前，通过配置的客户端 SSL 配置文件对响应进行解密和重新加密。

同时，客户端和服务器 SSL 配置文件都预配置了大 IP，并为所有 SHA 方案做好了准备。

1. 从左侧导航栏中转到 "**系统**  >  **证书管理**" "  >  **通讯证书管理**" "  >  **SSL 证书列表**  >  **导入**"

2. 从 " **导入类型** " 下拉列表中，选择 " **PKCS 12 (IIS")**

3. 为导入的证书提供名称，例如  `ContosoWilcardCert`

4. 选择 " **选择文件** "，浏览到 "使用者名称" 与你计划用于已发布服务的域后缀对应的 SSL web 证书

5. 提供已导入证书的 **密码**，然后选择 "**导入**"

6. 从左侧导航栏中转到 "**本地流量**  >  **配置文件**" "  >  **SSL**  >  **客户端**"，然后选择 "**创建**"

7. 在 "**新建客户端 Ssl 配置文件**" 页中，为新的客户端 ssl 配置文件提供唯一的友好名称，并确保将 "父配置文件" 设置为 " **clientssl** "。

![此图像显示更新大 ip](./media/f5ve-deployment-plan/client-ssl.png)

8. 选中 "**证书密钥链**" 行中最右侧的复选框，然后选择 "**添加**"

9. 从三个下拉列表中，选择你导入的不带密码的通配符证书，然后选择 "**添加**  >  **已完成**"。

![此图像显示更新大 ip contoso 通配符](./media/f5ve-deployment-plan/contoso-wildcard.png)

10. 重复步骤6-9 以创建 **SSL 服务器证书配置文件**。 在顶部功能区中，选择 " **SSL**  >  **服务器**  >  **创建**"。

11. 在 "**新建服务器 Ssl 配置文件**" 页中，为新的服务器 ssl 配置文件提供唯一的友好名称，并确保将 "父配置文件" 设置为 " **serverssl** "。

12. 选中 "证书" 和 "密钥" 行的最右侧复选框，并从下拉列表中选择导入的证书，然后选择 " **已完成**"。

![此图像显示 "更新大 ip 服务器" 所有配置文件](./media/f5ve-deployment-plan/server-ssl-profile.png)

>[!NOTE]
>不要失望如果您无法购买 SSL 证书，则可以使用集成的自签名大 IP 服务器和客户端 SSL 证书。 浏览器中只会出现证书错误。

为 SHA 准备大 IP 的最后一个步骤是确保它能够找到其发布的资源，以及它依赖于 SSO 的目录服务。 大 IP 有两个名称解析源，从其本地/.../hosts 文件开始，或者如果找不到记录，则大 IP 系统将使用已配置的任何 DNS 服务。 Hosts 文件方法不适用于使用 FQDN 的 APM 节点和池。

1. 在 web 配置中，请参阅 **系统**  >  **配置**  >  **设备**  >  **DNS**

2. 在 " **Dns 查找服务器" 列表** 中，输入环境 DNS 服务器的 IP 地址

3. 选择 **添加**  >  **更新**

作为一个单独的可选步骤，您可以考虑使用 [LDAP 配置](https://somoit.net/f5-big-ip/authentication-using-active-directory) 对 AD 的大 IP 系统管理员进行身份验证，而不是管理本地大 ip 帐户。

## <a name="update-big-ip"></a>更新大 IP

应更新大 IP VM，以解锁 [基于方案的指南](f5-aad-integration.md)中涵盖的所有新功能。 您可以检查系统 TMOS 版本，将鼠标指针悬停在主页左上角的 BIG-IPs 主机名上。 建议运行 v15 和更高版本，可从 [F5 下载](https://downloads.f5.com/esd/productlines.jsp)获得。 使用 [指南](https://support.f5.com/csp/article/K34745165) 更新 (TMOS) 的主系统操作系统。

如果无法更新主 TMOS，请至少考虑使用这些步骤仅升级指导配置。

1. 从大 IP web 配置中的主选项卡转到 **访问**  >  **指导配置**

2. 在 "**引导式配置**" 页上，选择 "**升级指导配置**"

![此图显示了如何更新大 ip](./media/f5ve-deployment-plan/update-big-ip.png)

3. 在 "**升级指导配置**" 对话框中，**选择 "文件**" 以上载已下载的用例包，并选择 "**上传和安装**"

4. 升级完成后，选择 " **继续**"。

## <a name="backup-big-ip"></a>备份大 IP

现在已完全预配大 IP 系统，我们建议对其配置进行完整备份：

1. 中转到 **系统**  >  **存档**  >  **创建**

2. 提供一个唯一的 **文件名**，并使用密码启用 **加密**

3. 将 " **私钥** " 选项设置为 " **包括** "，同时备份设备和 SSL 证书

4. 选择 " **已完成** " 并等待进程完成

5. 将显示一个操作状态，提供 "备份结果" 的状态。 选择“确定”

6. 通过选择该备份的链接并选择 " **下载**"，将用户配置集 (UCS) 存档保存在本地。

作为可选步骤，你还可以使用 [Azure 快照](../../virtual-machines/windows/snapshot-copy-managed-disk.md)来执行整个系统磁盘的备份，这不同于 web 配置备份将在 TMOS 版本之间提供一些应急措施，或者回滚到新系统。

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

## <a name="restore-big-ip"></a>还原大 IP

还原大 IP 后，请遵循类似的过程进行备份，并且还可用于在大 IP Vm 之间迁移配置。 导入备份之前应遵循有关支持的升级路径的详细信息。

1. 中转到 **系统**  >  **存档**

2. 选择要还原的备份的链接，或选择 " **上传** " 按钮，浏览到列表中未列出的以前保存的 UCS 存档

3. 提供备份的密码，然后选择 "**还原**"

```PowerShell
# Authenticate to Azure
Connect-azAccount

# Set subscription by Id
Set-AzContext -SubscriptionId ‘<Azure_Subscription_ID>’

#Restore Snapshot
Get-AzVmSnapshot -ResourceGroupName '<E.g.contoso-RG>' -VmName '<E.g.BIG-IP-VM>' | Restore-AzVmSnapshot

```

>[!NOTE]
>撰写本文时，AzVmSnapshot cmdlet 限制为基于日期还原最新的快照。 快照存储在 VM 的资源组的根目录中。 请注意，还原快照将重新启动 Azure VM，因此请仔细考虑。

## <a name="additional-resources"></a>其他资源

-   [在 Azure 中重置大 IP VE 密码](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html)
    -   [在不使用门户的情况下重置密码](https://clouddocs.f5.com/cloud/public/v1/shared/azure_passwordreset.html#reset-the-password-without-using-the-portal)

-   [更改用于大 IP VE 管理的 NIC](https://clouddocs.f5.com/cloud/public/v1/shared/change_mgmt_nic.html)

-   [关于单个 NIC 配置中的路由](https://clouddocs.f5.com/cloud/public/v1/shared/routes.html)

-   [Microsoft Azure： Waagent](https://clouddocs.f5.com/cloud/public/v1/azure/Azure_waagent.html)

## <a name="next-steps"></a>后续步骤

选择 [部署方案](f5-aad-integration.md) ，然后启动实现。