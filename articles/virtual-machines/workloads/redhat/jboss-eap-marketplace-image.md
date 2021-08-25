---
title: 适用于 Azure Red Hat Enterprise Linux 虚拟机 (VM) 和虚拟机规模集上的 Red Hat JBoss EAP 的 Azure 市场产品/服务
description: 如何使用 Azure Marketplace 产品/服务在 Azure Red Hat Enterprise Linux (RHEL) VM 和虚拟机规模集上部署 Red Hat JBoss EAP。
author: theresa-nguyen
ms.author: bicnguy
ms.topic: article
ms.service: virtual-machines
ms.subservice: redhat
ms.assetid: 9b37b2c4-5927-4271-85c7-19adf33d838b
ms.date: 05/25/2021
ms.openlocfilehash: 3c819367f35cb4a8174abaac1380eb439ace206a
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2021
ms.locfileid: "122769703"
---
# <a name="deploy-red-hat-jboss-enterprise-platform-eap-on-azure-vms-and-virtual-machine-scale-sets-using-the-azure-marketplace-offer"></a>使用 Azure 市场产品/服务在 Azure VM 和虚拟机规模集上部署 Red Hat JBoss Enterprise Platform (EAP)

适用于 Azure [Red Hat Enterprise Linux (RHEL)](https://www.redhat.com/en/technologies/linux-platforms/enterprise-linux) 上的 [Red Hat JBoss Enterprise Application Platform](https://www.redhat.com/en/technologies/jboss-middleware/application-platform) 的 Azure 市场产品/服务是 [Red Hat](https://www.redhat.com/) 和 Microsoft 的联合解决方案。 Red Hat 是领先的开源解决方案提供商和参与者，参与过的项目包括 [Java](https://www.java.com/) 标准、[OpenJDK](https://openjdk.java.net/)、[MicroProfile](https://microprofile.io/)、[Jakarta EE](https://jakarta.ee/) 和 [Quarkus](https://quarkus.io/)。 JBoss EAP 是一个领先的 Java 应用服务器平台，在 Web Profile 和 Full Platform 中均获得了 Java EE 认证并且兼容 Jakarta EE。 每个 JBoss EAP 版本都在各种市场领先的操作系统、Java 虚拟机 (JVM) 和数据库组合下进行了测试并且均被支持。  JBoss EAP 和 RHEL 包括在任何环境中生成、运行、部署和管理企业 Java 应用程序所需的一切内容。  包括本地环境、虚拟环境、私有云、公有云和混合云。 Red Hat 和 Microsoft 的联合解决方案包含集成的支持和软件许可灵活性。

## <a name="jboss-eap-on-azure-integrated-support"></a>Azure 上的 JBoss EAP 集成支持

Azure 市场上的 JBoss EAP 产品/服务是 Red Hat 和 Microsoft 联合推出的解决方案，包括集成支持和软件许可灵活性。 你可以访问 Microsoft 和 Red Hat 来提交支持票证。  我们将共同分享和解决问题，你无需向每个供应商提供多个票证。 集成支持涵盖了所有 Azure 基础结构和 Red Hat 应用程序服务器级别的支持问题。    

## <a name="prerequisites"></a>先决条件

* 具有活动订阅的 Azure 帐户 - 如果没有 Azure 订阅，可以激活 [Visual Studio 订阅订户权益](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details)（以前称为 MSDN）或[免费创建帐户](https://azure.microsoft.com/pricing/free-trial)。

* JBoss EAP 安装 - 需要有一个 Red Hat 帐户，该帐户具有针对 JBoss EAP 的 Red Hat 订阅管理 (RHSM) 权利。 这项权利允许你下载经过 Red Hat 测试和认证的 JBoss EAP 版本。  如果没有 EAP 权利，请通过[适用于个人的 Red Hat 开发人员订阅](https://developers.redhat.com/register)注册免费的开发人员订阅。 注册后，您可以在 [Red Hat 客户门户](https://access.redhat.com/management/)中找到必要的凭据（池 ID）。

* RHEL 选项 - 在即用即付 (PAYG) 或自带订阅 (BYOS) 之间进行选择。 若使用 BYOS，则在使用解决方案模板部署市场产品/服务之前激活 [Red Hat Cloud Access](https://access.redhat.com/) [RHEL 黄金映像](https://azure.microsoft.com/updates/red-hat-enterprise-linux-gold-images-now-available-on-azure/)。 按照[以下说明](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)启用 RHEL 黄金映像，以在 Microsoft Azure 中使用。

* Azure 命令行界面 (CLI)。

## <a name="azure-marketplace-offer-subscription-options"></a>Azure 市场产品/服务订阅选项

适用于 RHEL 上的 JBoss EAP 的 Azure 市场产品/服务将在不到 10 分钟的时间内安装和预配 Azure VM/虚拟机规模集部署。 可以从 [Azure 市场](https://azuremarketplace.microsoft.com/)访问这些产品/服务

市场产品/服务包含 EAP 和 RHEL 版本的各种组合，以满足你的要求。 JBoss EAP 始终为 BYOS，但对于 RHEL 操作系统 (OS)，你可以在 BYOS 或 PAYG 之间进行选择。 产品/服务包括作为独立虚拟机、群集虚拟机或群集虚拟机规模集的 RHEL 上的 JBoss EAP 的计划配置选项。 

六个可用计划为： 

- RHEL 8.3 独立虚拟机上的 Boss EAP 7.3 (PAYG)
- RHEL 8.3 独立虚拟机上的 JBoss EAP 7.3 (BYOS)
- RHEL 8.3 群集虚拟机上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 群集虚拟机上的 JBoss EAP 7.3 (BYOS)
- RHEL 8.3 群集虚拟机规模集上的 JBoss EAP 7.3 (PAYG)
- RHEL 8.3 群集虚拟机规模集上的 JBoss EAP 7.3 (BYOS)

## <a name="using-rhel-os-with-payg-model"></a>将 RHEL OS 与 PAYG 模式结合使用

Azure 市场产品/服务允许你作为按需 PAYG 虚拟机/虚拟机规模集来部署 RHEL。 除了正常的 Azure 基础设施计算、网络和存储成本之外，PAYG 计划还有额外的每小时 RHEL 订阅费用。

有关 PAYG 模式的详细信息，请查看 [Red Hat Enterprise Linux 定价](https://azure.microsoft.com/pricing/details/virtual-machines/red-hat/)。 若要在 PAYG 模式中使用 RHEL，你需要 Azure 订阅。 （[Azure 市场上的 RHEL](https://azuremarketplace.microsoft.com/marketplace/apps/redhat.rhel-20190605)产品服务要求在 Azure 订阅中指定付款方式）。

## <a name="using-rhel-os-with-byos-model"></a>将 RHEL OS 与 BYOS 模式结合使用

若要使用 RHEL 作为 BYOS 虚拟机/虚拟机规模集，你需要拥有有效的 Red Hat 订阅，并具备在 Azure 中使用 RHEL 的权利。 RHEL BYOS 计划中的 JBoss EAP 将作为 [Azure 专用产品/服务](../../../marketplace/private-offers.md)提供。 要从 Azure 市场中部署 RHEL BYOS 产品/服务计划，必须完成以下先决条件。 

1. 确保已将 RHEL OS 和 JBoss EAP 权利附加到 Red Hat 订阅。
2. 授权 Azure 订阅 ID 使用 RHEL BYOS 映像。 按照 [Red Hat 订阅管理 (RHSM) 文档](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/index)完成此进程，其中包括以下步骤：
    1. 在 Red Hat Cloud Access 仪表板中启用 Microsoft Azure 作为提供商。
    2. 添加 Azure 订阅 ID。
    3. 在 Microsoft Azure 上启用 Cloud Access 的新产品。
    4. 为 Azure 订阅激活 Red Hat 黄金映像。 请参阅有关[启用和维护 Cloud Access 的订阅](https://access.redhat.com/documentation/en-us/red_hat_subscription_management/1/html/red_hat_cloud_access_reference_guide/understanding-gold-images_cloud-access#using-gold-images-on-azure_cloud-access#using-gold-images-on-azure_cloud-access)的章节，了解更多详细信息。 
    5. 等待 Red Hat 黄金映像在 Azure 订阅中可用。 这些黄金映像通常在提交后 3 小时（或者小于 Azure 专用产品/服务的时间）内可用。

3. 接受 RHEL BYOS 映像的 Azure 市场条款和条件 (T&C)。 若要接受，请运行 [Azure 命令行界面 (CLI)](/cli/azure/install-azure-cli) 命令，如下所示。 请参阅 [Azure 中的 RHEL BYOS 黄金映像](./byos.md)文档，了解更多详细信息。 务必运行最新 Azure CLI 版本。
    1. 启动 Azure CLI 会话，并使用 Azure 帐户进行身份验证。 请参阅[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)以获得帮助。 在继续操作之前，请确保运行最新 Azure CLI 版本。
    2. 通过运行以下 CLI 命令，验证 RHEL BYOS 计划在订阅中是否可用。 如果此处未显示任何结果，请参阅步骤 2。 确保你的 Azure 订阅已激活，并且具备 RHEL 上的 JBoss EAP BYOS 计划的权利。

    ```cli
    az vm image list --offer rhel-byos --all #use this command to verify the availability of RHEL BYOS images
    ```

    3. 运行以下命令，根据 RHEL 上的 JBoss EAP BYOS 计划的需要，接受 Azure 市场条款和条件。

    ```cli
    az vm image terms accept --publisher redhat --offer jboss-eap-rhel --plan $PLANID
    ```

    4. 其中 `$PLANID` 下列其中一项（针对你要使用的每个市场产品/服务计划重复步骤 3）：

    ```cli
    jboss-eap-73-byos-rhel-80-byos
    jboss-eap-73-byos-rhel-8-byos-clusteredvm
    jboss-eap-73-byos-rhel-80-byos-vmss
    jboss-eap-73-byos-rhel-80-payg
    jboss-eap-73-byos-rhel-8-payg-clusteredvm
    jboss-eap-73-byos-rhel-80-payg-vmss
    ```

4. 你的订阅现已准备好部署 RHEL 上的 EAP BYOS 计划。 在部署过程中，将使用在 `subscription-manager` 以及在部署期间提供的凭据自动附加你的订阅。

## <a name="using-jboss-eap-with-byos-model"></a>将 JBoss EAP 与 BYOS 模式结合使用

JBoss EAP 在 Azure 上仅以 BYOS 模式提供。 部署 RHEL 上的 JBoss EAP 计划时，需要提供 RHSM 凭据以及具有有效 EAP 权利的 RHSM 池 ID。 如果你没有 EAP 权利，请获取[面向个人的 Red Hat 开发人员订阅](https://developers.redhat.com/register)。 注册后，可以在订阅导航菜单中找到必要的凭据（池 ID）。 在部署过程中，将使用在 `subscription-manager` 以及在部署期间提供的凭据自动附加你的订阅。

## <a name="template-solution-architectures"></a>模板解决方案体系结构

这些产品/服务计划创建所有 Azure 计算资源，以在 RHEL 上运行 JBoss EAP 安装程序。 模板解决方案将创建以下资源：

- RHEL 虚拟机
- 一个负载均衡器（用于群集配置）
- 包含单个子网的虚拟网络
- RHEL 虚拟机/虚拟机规模集（独立或群集）上的 JBoss EAP 安装程序
- Java 应用程序示例
- 存储帐户！

## <a name="after-a-successful-deployment"></a>成功部署到后

1. 在不同的虚拟网络中[创建一个具有 VNet 对等互连的跳板虚拟机](../../windows/quick-create-portal.md#create-virtual-machine)，并使用[虚拟网络对等互连](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks)访问服务器和公开应用程序。
2. [创建公共 IP](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) 以访问服务器和应用程序。
3. 在同一 VNet 中的不同子网（新子网）中，[在同一个虚拟网络 (VNet) 中创建一个跳板虚拟机](../../windows/quick-create-portal.md#create-virtual-machine)并通过跳板虚拟机访问服务器。 跳板虚拟机可用于公开应用程序。
4. 使用[应用程序网关](../../../application-gateway/quick-create-portal.md#create-an-application-gateway)公开应用程序。
5. 使用外部负载均衡器 (ELB) 公开应用程序。
6. [使用 Azure Bastion](../../../bastion/bastion-overview.md) 通过浏览器和 Azure 门户访问 RHEL 虚拟机。 

### <a name="1-create-a-jump-vm-in-a-different-virtual-network-and-access-the-rhel-vm-using-virtual-network-peering-recommended-method"></a>1. 在不同的虚拟网络中创建一个跳板虚拟机，并使用虚拟网络对等互连访问 RHEL 虚拟机（推荐方法）

1. [创建 Windows 虚拟机](../../windows/quick-create-portal.md#create-virtual-machine) - 在新的 Azure 资源组中，创建 Windows 虚拟机，该虚拟机必须与 RHEL 虚拟机位于同一区域。 提供所需的详细信息，并将其他配置保留为默认值，因为将在新的虚拟网络中创建跳板虚拟机。
2. [虚拟网络对等](../../../virtual-network/tutorial-connect-virtual-networks-portal.md#peer-virtual-networks) - 对等互连是将 RHEL 虚拟机与跳板虚拟机进行关联的方式。 虚拟网络对等互连成功后，两个虚拟机便可以相互通信。
3. 转到跳板虚拟机详细信息页面并复制公共 IP。 使用公共 IP 登录到跳板虚拟机。
4. 从输出页复制 RHEL 虚拟机的专用 IP，并使用这个专用 IP 从跳转虚拟机登录到 RHEL 虚拟机。
5. 将从输出页复制的应用 URL 粘贴到跳转虚拟机内的浏览器中。 在此浏览器中查看 Azure 上的 JBoss EAP 网页。
6. 访问 JBoss EAP 管理控制台 - 将从输出页复制的管理控制台 URL 粘贴到跳转虚拟机内的浏览器中，输入 JBoss EAP 用户名和密码以登录。

### <a name="2-create-a-public-ip-to-access-the-rhel-vm-and-jboss-eap-admin-console"></a>2.创建公共 IP 以访问 RHEL 虚拟机和 JBoss EAP 管理控制台。

1. 你创建的 RHEL 虚拟机没有与之关联的公共 IP。 可以[创建一个公共 IP](../../../virtual-network/virtual-network-public-ip-address.md#create-a-public-ip-address) 以用于访问虚拟机，并将[公共 IP 关联到虚拟机](../../../virtual-network/associate-public-ip-address-vm.md)。 要创建公共 IP，可以使用 Azure 门户，也可以使用 [Azure PowerShell](/powershell/) 命令或 [Azure CLI](/cli/azure/install-azure-cli) 命令。
2. 获取虚拟机的公共 IP - 转到 VM 详细信息页并复制公共 IP。 使用公共 IP 来访问虚拟机和 JBoss EAP 管理控制台。
3. 查看 Azure 上的 JBoss EAP 网页 - 打开 Web 浏览器并转到 *http://<PUBLIC_HOSTNAME>:8080/* ，应会看到默认的 EAP 欢迎页。
4. 登录到 JBoss EAP 管理控制台 - 打开 Web 浏览器并转到 *http://<PUBLIC_HOSTNAME>:9990*。 输入 JBoss EAP 用户名和密码以登录。

### <a name="3-create-a-jump-vm-in-a-different-subnet-new-subnet-in-the-same-vnet-and-access-the-rhel-vm-via-a-jump-vm"></a>3. 在同一个 VNet 中的不同子网（新子网）中创建一个跳板虚拟机，并通过跳板虚拟机访问 RHEL 虚拟机。

1. 在包含 RHEL 虚拟机的现有虚拟网络中[添加一个新子网](../../../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)。
2. 在与 RHEL 虚拟机相同的资源组 (RG) 中的 Azure 中[创建一个 Windows 虚拟机](../../windows/quick-create-portal.md#create-virtual-machine)。 提供所需的详细信息，将 VNet 和子网以外的其他配置保留为默认值。 请确保在 RG 中选择现有的 VNet，并选择在上一步中创建的子网，因为这将成为你的跳板虚拟机。
3. 访问跳板虚拟机公共 IP - 成功部署后，转到虚拟机详细信息页并复制公共 IP。 使用公共 IP 登录到跳板虚拟机。
4. 登录到 RHEL 虚拟机 - 从输出页复制 RHEL 虚拟机的专用 IP，并使用这个专用 IP 从跳转虚拟机登录到 RHEL 虚拟机。
5. 访问 JBoss EAP 欢迎页 - 在跳板虚拟机中，打开浏览器并粘贴从部署的输出页复制的应用 URL。
6. 访问 JBoss EAP 管理控制台 - 将从输出页复制的管理控制台 URL 粘贴到跳转虚拟机内的浏览器中，以访问 JBoss EAP 管理控制台，输入要登录的 JBoss EAP 用户名和密码以登录。

### <a name="4-expose-the-application-using-an-external-load-balancer"></a>4. 使用外部负载均衡器公开应用程序

1. [创建应用程序网关](../../../application-gateway/quick-create-portal.md#create-an-application-gateway) - 要访问 RHEL 虚拟机的端口，请在不同的子网中创建应用程序网关。 子网必须仅包含应用程序网关。
2. 设置“前端”参数 - 请确保选择“公共 IP”，并提供所需的详细信息。 在“后端”部分中，选择“添加后端池”选项，并将 RHEL 虚拟机添加到应用程序网关的后端池中。
3. 设置访问端口 - 在“配置”部分下添加路由规则，以访问 RHEL 虚拟机的端口 8080 和 9990。
4. 复制应用程序网关的公共 IP - 使用所需的配置创建了应用程序网关之后，转到概述页面并复制应用程序网关的公共 IP。
5. 查看 Azure 上的 JBoss EAP 网页 - 打开 Web 浏览器，然后转到 *http://<PUBLIC_IP_AppGateway>:8080*，应会看到默认的 EAP 欢迎页。
6. 登录到 JBoss EAP 管理控制台 - 打开 Web 浏览器，然后转到 *http://<PUBLIC_IP_AppGateway>:9990*。 输入 JBoss EAP 用户名和密码以登录。

### <a name="5-use-an-external-load-balancer-elb-to-access-your-rhel-vmvirtual-machine-scale-sets"></a>5. 使用外部负载均衡器 (ELB) 访问RHEL 虚拟机/虚拟机规模集

1. [创建负载均衡器](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources)以访问 RHEL 虚拟机的端口。 提供部署外部负载均衡器所需的详细信息，将其他配置保留为默认值。 对于 ELB 配置，将 SKU 保留为“基本”。
2. 添加负载均衡器规则 - 成功创建负载均衡器后，[创建负载均衡器资源](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md?tabs=option-1-create-load-balancer-standard#create-load-balancer-resources)，然后添加负载均衡器规则以访问 RHEL 虚拟机的端口 8080 和 9990。
3. 将 RHEL 虚拟机添加到负载均衡器后端池 - 单击设置部分下的“后端池”，然后选择在以上步骤中创建的后端池。 选择与“关联到”选项对应的虚拟机，然后添加 RHEL 虚拟机。
4. 要获取负载均衡器的公共 IP，请转到负载均衡器概述页并复制负载均衡器的公共 IP。
5. 查看 Azure 上的 JBoss EAP 网页 - 打开 Web 浏览器，然后转到 *http://<PUBLIC_IP_LoadBalancer>:8080/* ，应会看到默认的 EAP 欢迎页。
6. 登录到 JBoss EAP 管理控制台 - 打开 Web 浏览器，然后转到 *http://<PUBLIC_IP_LoadBalancer>:9990*。 输入 JBoss EAP 用户名和密码以登录。

### <a name="6-use-azure-bastion-to-connect"></a>6. 使用 Azure Bastion 进行连接 

1. 为 Azure Bastion RHEL 虚拟机所在的 VNet 创建一个 Azure Bastion 主机。 Bastion 服务将自动使用 SSH 连接到 RHEL VM。
2. 在 VM 上创建“读者”角色，使用虚拟机的专用 IP 创建 NIC 以及 Azure Bastion 资源。
3. RHEL入站的开放端口为 SSH(22)。 
4. 在 Azure 门户中使用用户名和密码进行连接。 单击“连接”，然后从下拉列表中选择“Bastion”。 然后选择“连接”以连接到 RHEL 虚拟机。 您可以使用私钥文件或存储在 Azure 密钥保管库中的私钥进行连接。

## <a name="azure-platform"></a>Azure 平台

- 验证失败 - 如果不满足参数条件（例如，不满足管理密码条件），或者如果参数部分没有提供任何必需参数，则部署将无法启动。 在单击“创建”之前，可以先检查参数的详细信息。
- 资源预配失败 - 部署开始后，部署页上会显示正在部署的资源。 如果在参数验证过程之后出现部署失败的情况，则会提供更详细的失败消息。 
- 虚拟机预配失败 - 如果部署在 **虚拟机自定义脚本扩展** 资源点失败，虚拟机日志文件中会显示更详细的失败消息。 有关进一步故障排除，请参阅下一部分。

## <a name="troubleshooting-eap-deployment-extension"></a>EAP 部署扩展故障排除

这些产品/服务使用虚拟机自定义脚本扩展来部署 JBoss EAP 并配置 JBoss EAP 管理用户。 你的部署可能在此时失败，原因多种多样，包括：

- RHSM 或 EAP 权利无效
- JBoss EAP 或 RHEL OS 权利池 ID 无效
- RHEL 虚拟机未接受 Azure 市场条款和条件

请按照以下步骤进一步排查问题：

1. 通过 SSH 登录到预配虚拟机。 可以从 Azure 门户的虚拟机概述页面检索虚拟机的公共 IP。
1. 切换到 root 用户

    ```cli
    sudo su -
    ```

1. 在出现提示时输入虚拟机管理员密码。
1. 将目录更改为日志记录目录

    ```cli
    cd /var/lib/waagent/custom-script/download/0 #for EAP on RHEL stand-alone VM
    ```

    ```cli
    cd /var/lib/waagent/custom-script/download/1 #for EAP on RHEL clustered VM
    ```

1. 查看 eap.log 日志文件中的日志。

    ```cli
    more eap.log
    ```

日志文件将包含详细信息，包括部署失败原因和可能的解决方案。 如果部署是由于 RHSM 帐户或权利而失败，请参阅“支持和订阅说明”部分，以完成先决条件。 然后重试。 在 RHEL 群集计划上部署 EAP 时，请确保部署不会达到配额限制。 在提供部署的实例计数之前，必须检查区域 vCPU 和虚拟机系列 vCPU 配额。 如果你的订阅或区域的配额限制不足，请通过 Azure 门户[请求配额](../../../azure-portal/supportability/regional-quota-requests.md)。

有关排查虚拟机自定义脚本扩展问题的详细信息，请参阅[将 Azure 自定义脚本扩展版本 2 与 Linux 虚拟机一起使用](../../extensions/custom-script-linux.md)。

## <a name="resource-links-and-support"></a>资源链接和支持

有关任何与支持相关的疑问、问题或自定义要求，请联系 [Red Hat 支持](https://access.redhat.com/support)或 [Microsoft Azure 支持](https://ms.portal.azure.com/?quickstart=true#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)。

* 详细了解 [JBoss EAP](https://access.redhat.com/documentation/en-us/red_hat_jboss_enterprise_application_platform/)
* [Azure Red Hat OpenShift 上的 JBoss EAP](https://azure.microsoft.com/services/openshift/)
* [Azure 应用服务中的 JBoss EAP](/azure/developer/java/ee/jboss-on-azure) 
* [Azure 混合权益](../../windows/hybrid-use-benefit-licensing.md)
* [Red Hat 订阅管理](https://access.redhat.com/products/red-hat-subscription-management)
* [关于 Azure 上的 Red Hat 的 Microsoft 文档](./overview.md)

## <a name="next-steps"></a>后续步骤

* 从[Azure 市场](https://aka.ms/AMP-JBoss-EAP)部署 RHEL 虚拟机/虚拟机规模集上的 JBoss EAP
* 从 [Azure 快速入门](https://aka.ms/Quickstart-JBoss-EAP)部署 RHEL 虚拟机/虚拟机规模集上的 JBoss EAP
* 为 [Azure 应用服务](../../../app-service/configure-language-java.md)配置 Java 应用
* [如何将 JBoss EAP 部署到 Azure 应用服务](https://github.com/JasonFreeberg/jboss-on-app-service)教程
* 使用 Azure [应用服务迁移协助](https://azure.microsoft.com/services/app-service/migration-assistant/)
* 使用 Red Hat [Migration Toolkit for Applications](https://developers.redhat.com/products/mta)
