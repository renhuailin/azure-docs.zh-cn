---
title: 快速入门：创建私有云
titleSuffix: Azure VMware Solutions by CloudSimple
description: 了解如何使用 Azure VMware Solutions by CloudSimple 创建和配置私有云
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 487308aca3231650aee3fac5ae127006649e19b9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "87073904"
---
# <a name="quickstart---configure-a-private-cloud-environment"></a>快速入门 - 配置私有云环境

本文介绍如何创建 CloudSimple 私有云并设置私有云环境。

## <a name="before-you-begin"></a>开始之前

查看[网络先决条件](cloudsimple-network-checklist.md)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="create-a-private-cloud"></a>创建私有云

私有云是一个独立的 VMware 堆栈，支持 ESXi 主机、vCenter、vSAN 和 NSX。

私有云通过 CloudSimple 门户进行管理。 它们在各自的管理域中具有各自的 vCenter 服务器。 堆栈在专用节点和隔离的裸机硬件节点上运行。

1. 选择“所有服务”  。
2. 搜索“CloudSimple 服务”。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 从“概述”中，单击“创建私有云”以打开 CloudSimple 门户的新浏览器标签页。  如果系统提示，请使用 Azure 登录凭据进行登录。  

    ![从 Azure 创建私有云](media/create-private-cloud-from-azure.png)

5. 在 CloudSimple 门户中，提供私有云的名称。
6. 选择私有云的“位置”。
7. 选择与在 Azure 上预配的类型一致的“节点类型”。
8. 指定“节点计数”。  创建私有云需要至少三个节点。

    ![创建私有云 - 基本信息](media/create-private-cloud-basic-info.png)

9. 单击“下一步: 高级选项”。
10. 输入 vSphere/vSAN 子网的 CIDR 范围。 请确保 CIDR 范围不与任何本地或其他 Azure 子网（虚拟网络）或网关子网重叠。

    CIDR 范围选项：/24、/23、/22 或 /21。 /24 CIDR 范围支持最多 26 个节点，/23 CIDR 范围支持最多 58 个节点，而 /22 和 /21 CIDR 范围支持 64 个节点（私有云中的最大节点数）。  若要了解详细信息以及 VLAN 和子网，请参阅 [VLAN 和子网概述](cloudsimple-vlans-subnets.md)。

      > [!IMPORTANT]
      > vSphere/vSAN CIDR 范围中的 IP 地址保留给私有云基础结构使用。  请不要在任何虚拟机上使用此范围内的 IP 地址。

11. 单击“下一步: 查看并创建”。
12. 查看设置。 如果要更改任何设置，请单击“上一步”。
13. 单击“创建”。

私有云预配过程开始。  预配私有云可能需要长达两个小时。

## <a name="launch-cloudsimple-portal"></a>启动 CloudSimple 门户

可以从 Azure 门户访问 CloudSimple 门户。  CloudSimple 门户会使用单一登录 (SSO)，通过 Azure 登录凭据进行启动。  访问 CloudSimple 门户需要授权“CloudSimple Service Authorization”应用程序。  有关授予权限的详细信息，请参阅 [同意使用“CloudSimple Service Authorization”应用程序](access-cloudsimple-portal.md#consent-to-cloudsimple-service-authorization-application)。

1. 选择“所有服务”  。
2. 搜索“CloudSimple 服务”。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 在概述中单击“转到 CloudSimple 门户”，为 CloudSimple 门户打开一个新的浏览器选项卡。  如果系统提示，请使用 Azure 登录凭据进行登录。  

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

## <a name="create-point-to-site-vpn"></a>创建点到站点 VPN

点到站点 VPN 连接是从计算机连接到私有云的最简单方法。 如果要远程连接到私有云，请使用点到站点 VPN 连接。  若要快速访问私有云，请执行以下步骤。  可以使用[站点到站点 VPN](vpn-gateway.md) 或 [Azure ExpressRoute](on-premises-connection.md)，从本地网络访问 CloudSimple 区域。

### <a name="create-gateway"></a>创建网关

1. 启动 CloudSimple 门户，然后选择“网络”。
2. 选择“VPN 网关”。
3. 单击“新建 VPN 网关”。

    ![创建 VPN 网关](media/create-vpn-gateway.png)

4. 对于“网关配置”，请指定以下设置，然后单击“下一步”。

    * 选择“点到站点 VPN”作为网关类型。
    * 输入名称以标识网关。
    * 选择要在其中部署 CloudSimple 服务的 Azure 位置。
    * 为点到站点网关指定客户端子网。  连接时，将从此子网中指定 DHCP 地址。

5. 对于“连接/用户”，指定以下设置，然后单击“下一步”。

    * 如果要自动允许当前和未来的所有用户通过此点到站点网关访问私有云，请选择“自动添加所有用户”。 选择此选项时，将自动选择用户列表中的所有用户。 用户可以通过取消选择列表中的单个用户来替代自动选项。
    * 若要仅选择单个用户，请单击用户列表中的复选框。

6. 通过 VLAN/子网部分，可以为网关和连接指定管理和用户 VLAN/子网。

    * “自动添加”选项设置此网关的全局策略。 这些设置将应用于当前的网关。 这些设置可在“选择”区域中被替代。
    * 选择“添加私有云的管理 VLAN/子网”。
    * 如果要添加所有用户定义的 VLAN/子网，请单击“添加用户定义的 VLAN/子网”。
    * “选择”设置将替代“自动添加”下的全局设置。

7. 单击“下一步”查看设置。 单击编辑图标进行任何更改。
8. 单击“创建”，创建 VPN 网关。

### <a name="connect-to-cloudsimple-using-point-to-site-vpn"></a>使用点到站点 VPN 连接到 CloudSimple

从计算机连接到 CloudSimple 时需要 VPN 客户端。  下载适用于 Windows 的 [OpenVPN 客户端](https://openvpn.net/community-downloads/)或用于 macOS 和 OS X 的 [Viscosity](https://www.sparklabs.com/viscosity/download/)。

1. 启动 CloudSimple 门户，然后选择“网络”。
2. 选择“VPN 网关”。
3. 从 VPN 网关列表中，单击“点到站点 VPN 网关”。
4. 选择“用户”。
5. 单击“下载我的 VPN 配置”。

    ![下载 VPN 配置](media/download-p2s-vpn-configuration.png)

6. 在 VPN 客户端上导入配置。

    * 有关[在 Windows 客户端上导入配置](https://openvpn.net/vpn-server-resources/connecting-to-access-server-with-windows/#openvpn-open-source-openvpn-gui-program)的说明
    * 有关[在 macOS 或 OS X 上导入配置](https://www.sparklabs.com/support/kb/article/getting-started-with-viscosity-mac/#creating-your-first-connection)的说明

7. 连接到 CloudSimple。

## <a name="create-a-vlan-for-your-workload-vms"></a>为工作负载 VM 创建 VLAN

创建私有云之后，创建将在其中部署工作负载/应用程序 VM 的 VLAN。

1. 在 CloudSimple 门户中，选择“网络”。
2. 单击“VLAN/子网”。
3. 单击“创建 VLAN/子网”。

    ![创建 VLAN/子网](media/create-new-vlan-subnet.png)

4. 选择新 VLAN/子网的“私有云”。
5. 从列表中选择 VLAN ID。  
6. 输入子网名称以标识子网。
7. 指定子网 CIDR 范围和掩码。  此范围不得与任何现有子网重叠。
8. 单击“提交”  。

    ![创建 VLAN/子网详细信息](media/create-new-vlan-subnet-details.png)

将创建 VLAN/子网。  现在可以使用此 VLAN ID 在私有云 vCenter 上创建分布式端口组。

## <a name="connect-your-environment-to-an-azure-virtual-network"></a>将环境连接到 Azure 虚拟网络

CloudSimple 会为你提供私有云的 ExpressRoute 线路。 可以将 Azure 上的虚拟网络连接到 ExpressRoute 线路。 有关设置连接的完整详细信息，请遵循[使用 ExpressRoute 进行的 Azure 虚拟网络连接](./cloudsimple-azure-network-connection.md)中的步骤。

## <a name="sign-in-to-vcenter"></a>登录 vCenter

现在可以登录 vCenter 来设置虚拟机和策略。

1. 若要访问 vCenter，请从 CloudSimple 门户开始操作。 在主页上的“常见任务”下，单击“启动 vSphere 客户端” 。  选择私有云，然后在私有云上单击“启动 vSphere 客户端”。

    ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

2. 选择首选 vSphere 客户端以访问 vCenter，并使用用户名和密码进行登录。  默认值为：
    * 用户名：`CloudOwner@cloudsimple.local`
    * 密码：`CloudSimple123!`

后续过程中的 vCenter 屏幕来自 vSphere (HTML5) 客户端。

## <a name="change-your-vcenter-password"></a>更改 vCenter 密码

CloudSimple 建议你在首次登录 vCenter 时更改密码。  
设置的密码必须满足以下要求：

* 最长生存期：密码必须每 365 天更改一次
* 限制重复使用：用户不能重复使用之前五个密码
* 长度：8-20 个字符
* 特殊字符：至少一个特殊字符
* 字母字符：至少一个大写字符 A-Z，以及至少一个小写字符 a-z
* 数字：至少一个数字字符 0-9
* 最大相同相邻字符数：三个

    示例：可接受 CC 或 CCC 作为密码的一部分，但不可接受 CCCC。

如果设置的密码不符合要求：

* 如果使用 vSphere Flash 客户端，它会报告错误
* 如果使用 HTML5 客户端，它不会报告错误。 客户端不接受更改，旧密码会继续发挥作用。

## <a name="access-nsx-manager"></a>访问 NSX 管理器

NSX 管理器使用默认密码进行部署。 

* 用户名：admin
* 密码：CloudSimple123!

可以在 CloudSimple 门户上查看 NSX 管理器的完全限定的域名 (FQDN) 和 IP 地址。

1. 启动 CloudSimple 门户，然后选择“资源”。
2. 单击要使用的私有云。
3. 选择“vSphere 管理网络”
4. 使用 NSX 管理器的 FQDN 或 IP 地址，并使用 Web 浏览器进行连接。

    ![查找 NSX 管理器 FQDN](media/private-cloud-nsx-manager-fqdn.png)

## <a name="create-a-port-group"></a>创建端口组

若要在 vSphere 中创建分布式端口组，请执行以下操作：

1. 按照 [vSphere 网络指南](https://docs.vmware.com/en/VMware-vSphere/6.5/vsphere-esxi-vcenter-server-65-networking-guide.pdf)的“添加分布式端口组”中的说明进行操作。
2. 设置分布式端口组时，请提供在 [为工作负载 VM 创建 VLAN](#create-a-vlan-for-your-workload-vms) 中创建的 VLAN ID。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure ExpressRoute 连接到本地网络](on-premises-connection.md)
* [从本地设置站点到站点 VPN](vpn-gateway.md)
