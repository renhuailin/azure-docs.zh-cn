---
title: Azure VMware Solution by CloudSimple - 为私有云配置 vSAN 加密
description: 介绍如何配置 vSAN 软件加密功能，以便 CloudSimple 私有云可以使用 Azure 虚拟网络中运行的密钥管理服务器。
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 021868f32f26e0ec2739c91a1830fd3671230e81
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129613883"
---
# <a name="configure-vsan-encryption-for-cloudsimple-private-cloud"></a>为 CloudSimple 私有云配置 vSAN 加密

可以配置 vSAN 软件加密功能，以便 CloudSimple 私有云可以使用 Azure 虚拟网络中运行的密钥管理服务器。

使用 vSAN 加密时，VMware 需要使用符合外部 KMIP 1.1 的第三方密钥管理服务器 (KMS) 工具。 可以利用经 VMware 认证并可用于 Azure 的任何受支持的 KMS。

此指南介绍如何使用 Azure 虚拟网络中运行的 HyTrust KeyControl KMS。 对于 vSAN 其他经认证的第三方 KMS 解决方案，可使用相似的方法。

此 KMS 解决方案的要求如下：

* 在 Azure 虚拟网络中安装、配置和管理经 VMware 认证的第三方 KMS 工具。
* 为 KMS 工具提供自己的许可证。
* 使用 Azure 虚拟网络中运行的第三方 KMS 工具在私有云中配置和管理 vSAN 加密。

## <a name="kms-deployment-scenario"></a>KMS 部署方案

KMS 服务器群集在 Azure 虚拟网络中运行，是可通过配置的 Azure ExpressRoute 连接从私有云 vCenter 访问的 IP。

![Azure 虚拟网络中的 ../media/KMS 群集](media/vsan-kms-cluster.png)

## <a name="how-to-deploy-the-solution"></a>如何部署解决方案

部署过程包含以下步骤：

1. [验证是否满足先决条件](#verify-prerequisites-are-met)
2. [CloudSimple 门户：获取 ExpressRoute 对等互连信息](#cloudsimple-portal-obtain-expressroute-peering-information)
3. [Azure 门户：将虚拟网络连接到私有云](#azure-portal-connect-your-virtual-network-to-your-private-cloud)
4. [Azure 门户：在虚拟网络中部署 HyTrust KeyControl 群集](#azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network)
5. [HyTrust WebUI：配置 KMIP 服务器](#hytrust-webui-configure-the-kmip-server)
6. [vCenter UI：配置 vSAN 加密以使用 Azure 虚拟网络中的 KMS 群集](#vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network)

### <a name="verify-prerequisites-are-met"></a>验证是否满足先决条件

在部署之前验证以下各项：

* 选择的 KMS 供应商、工具和版本位于 vSAN 兼容性列表中。
* 选择的供应商支持要在 Azure 中运行的工具版本。
* KMS 工具的 Azure 版本符合 KMIP 1.1。
* 已创建 Azure 资源管理器和虚拟网络。
* 已创建 CloudSimple 私有云。

### <a name="cloudsimple-portal-obtain-expressroute-peering-information"></a>CloudSimple 门户：获取 ExpressRoute 对等互连信息

要继续设置，需要 ExpressRoute 的授权密钥和对等线路 URI 以及对 Azure 订阅的访问权限。 此信息可在 CloudSimple 门户中的“虚拟网络连接”页上找到。 有关说明，请参阅[设置与私有云的虚拟网络连接](virtual-network-connection.md)。 如果在获取信息时遇到任何问题，请提交一个[支持请求](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)。

### <a name="azure-portal-connect-your-virtual-network-to-your-private-cloud"></a>Azure 门户：将虚拟网络连接到私有云

1. 按照[使用 Azure 门户为 ExpressRoute 配置虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)中的说明操作，为虚拟网络创建虚拟网络网关。
2. 按照[使用门户将虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)中的说明操作，将虚拟网络链接到 CloudSimple ExpressRoute 线路。
3. 使用在 CloudSimple 发送的欢迎电子邮件中收到的 CloudSimple ExpressRoute 线路信息，将虚拟网络链接到 Azure 中的 CloudSimple ExpressRoute 线路。
4. 输入授权密钥和对等线路 URI，并为连接命名，然后单击“确定”。

![创建虚拟网络时需提供 CS 对等线路 URI](media/vsan-azureportal01.png) 

### <a name="azure-portal-deploy-a-hytrust-keycontrol-cluster-in-the-azure-resource-manager-in-your-virtual-network"></a>Azure 门户：在虚拟网络的 Azure 资源管理器中部署 HyTrust KeyControl 群集

若要在虚拟网络的 Azure 资源管理器中部署 HyTrust KeyControl 群集，请执行以下任务。 有关详细信息，请参阅 [HyTrust 文档](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)。

1. 请按照 HyTrust 文档中的说明操作，使用指定的入站规则创建 Azure 网络安全组 (nsg-hytrust)。
2. 在 Azure 中生成 SSH 密钥对。
3. 从 Azure 市场中的映像部署初始 KeyControl 节点。  使用生成的密钥对的公钥，并选择 nsg-hytrust 作为 KeyControl 节点的网络安全组。
4. 将 KeyControl 的专用 IP 地址转换为静态 IP 地址。
5. 使用 KeyControl VM 的公共 IP 地址和前面提到的密钥对的私钥，通过 SSH 连接到 KeyControl VM。
6. SSH 安全外壳中出现提示时，选择 `No`，以将节点设置为初始 KeyControl 节点。
7. 重复此过程的步骤 3-5，并在系统提示是否添加到现有群集时选择 `Yes`，以便添加其他 KeyControl 节点。

### <a name="hytrust-webui-configure-the-kmip-server"></a>HyTrust WebUI：配置 KMIP 服务器

转到 https://public-ip，其中 public-ip 是 KeyControl 节点 VM 的公共 IP 地址。  请按照 [HyTrust 文档](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)中的步骤操作。

1. [配置 KMIP 服务器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)
2. [为 VMware 加密创建证书绑定](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

### <a name="vcenter-ui-configure-vsan-encryption-to-use-kms-cluster-in-your-azure-virtual-network"></a>vCenter UI：配置 vSAN 加密以使用 Azure 虚拟网络中的 KMS 群集

请按照 HyTrust 说明[在 vCenter 中创建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)。

![在 vCenter 中添加 KMS 群集详细信息](media/vsan-config01.png)

在 vCenter 中，转到“群集 > 配置”，并选择 vSAN 的“常规”选项。  启用加密并选择前面添加到 vCenter 的 KMS 群集。

![在 vCenter 中启用 vSAN 加密并配置 KMS 群集](media/vsan-config02.png)

## <a name="references"></a>参考

### <a name="azure"></a>Azure

[使用 Azure 门户配置 ExpressRoute 的虚拟网络网关](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md)

[使用门户将虚拟网络连接到 ExpressRoute 线路](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)

### <a name="hytrust"></a>HyTrust

[HyTrust DataControl 和 Microsoft Azure](https://docs.hytrust.com/DataControl/Admin_Guide-4.0/Default.htm#OLH-Files/Azure.htm%3FTocPath%3DHyTrust%2520DataControl%2520and%2520Microsoft%2520Azure%7C_____0)

[配置 KMPI 服务器](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/configuring-kmip-server.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____2)

[为 VMware 加密创建证书绑定](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-user-for-vmcrypt.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____3)

[在 vSphere 中创建 KMS 群集](https://docs.hytrust.com/DataControl/4.2/Admin_Guide-4.2/index.htm#Books/VMware-vSphere-VSAN-Encryption/creating-KMS-Cluster.htm%3FTocPath%3DHyTrust%2520KeyControl%2520with%2520VSAN%25C2%25A0and%2520VMware%2520vSphere%2520VM%2520Encryption%7C_____4)
