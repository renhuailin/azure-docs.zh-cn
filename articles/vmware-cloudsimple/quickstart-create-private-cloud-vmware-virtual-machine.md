---
title: 快速入门 - 在私有云上创建 Azure VMware VM - Azure VMware Solution by CloudSimple
description: 了解如何在 CloudSimple 私有云上创建 VMware 虚拟机。 从 Azure 门户访问 CloudSimple 门户。
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4eec39455834d6ebaa2fcb1ed2799b0e023d48bf
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616163"
---
# <a name="create-vmware-virtual-machines-on-your-private-cloud"></a>在私有云上创建 VMware 虚拟机

若要在私有云上创建虚拟机，请首先从 Azure 门户访问 CloudSimple 门户。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="access-the-cloudsimple-portal"></a>访问 CloudSimple 门户

1. 选择“所有服务”  。
2. 搜索“CloudSimple 服务”。
3. 选择要在其上创建私有云的 CloudSimple 服务。
4. 在“概述”页上，单击“转到CloudSimple 门户”，为 CloudSimple 门户打开一个新的浏览器选项卡 。  如果系统提示，请使用 Azure 登录凭据进行登录。  

    ![启动 CloudSimple 门户](media/launch-cloudsimple-portal.png)

## <a name="launch-vcenter-web-ui"></a>启动 vCenter web-ui

现在，你可以启动 vCenter 来设置虚拟机和策略。

若要访问 vCenter，请从 CloudSimple 门户开始操作。 在主页上的“常见任务”下，单击“启动 vSphere 客户端” 。  选择私有云，然后在私有云上单击“启动 vSphere 客户端”。

   ![启动 vSphere 客户端](media/launch-vcenter-from-cloudsimple-portal.png)

## <a name="upload-an-iso-or-vsphere-template"></a>上传 ISO 或 vSphere 模板

  > [!WARNING]
  > 对于 ISO 上传，请使用 vSphere HTML5 客户端。  使用 Flash 客户端可能会导致错误。

1. 获取要上传到 vCenter 用于创建 VM 的 ISO 或 vSphere 模板，并使其在本地系统上可用。
2. 在 vCenter 中，单击“磁盘”图标并选择“vsanDatastore” 。 单击“文件”，然后单击“新建文件夹” 。
    ![vCenter ISO](media/vciso00.png)

3. 创建名为“ISO 和模板”的文件夹。

4. 导航到 ISO 和模板中的 ISO 文件夹，然后单击“上传文件”。 按照屏幕上的说明上传 ISO。

## <a name="create-a-virtual-machine-in-vcenter"></a>在 vCenter 中创建虚拟机

1. 在 vCenter 中，单击“主机和群集”图标。

2. 右键单击“工作负载”，然后选择“新建虚拟机” 。
    ![突出显示“新建虚拟机”菜单选项的屏幕截图。](media/vcvm01.png)

3. 选择“新建虚拟机”，然后单击“下一步” 。
    ![突出显示“新建虚拟机”选项的屏幕截图。](media/vcvm02.png)

4. 为虚拟机命名，选择“工作负载 VM”的位置，然后单击“下一步” 。
    ![突出显示“工作负载 VM”选项的屏幕截图。](media/vcvm03.png)

5. 选择“工作负载”计算资源，然后单击“下一步” 。
    ![突出显示“工作负载”计算资源的屏幕截图。](media/vcvm04.png)

6. 选择“vsanDatastore”，然后单击“下一步” 。
    ![突出显示 vsanDatastore 选项的屏幕截图。](media/vcvm05.png)

7. 保留默认的 ESXi 6.5 兼容性选择，并单击“下一步”。
    ![显示选定的 ESXi 6.5 兼容性选项的屏幕截图。](media/vcvm06.png)

8. 选择要创建的 VM 的 ISO 来宾操作系统，然后单击“下一步”。
    ![显示如何为 VM 选择 ISO 的来宾操作系统的屏幕截图。](media/vcvm07.png)

9. 选择“硬盘和网络”选项。 对于新的 CD/DVD 驱动器，请选择“数据存储 ISO 文件”。  如果要允许来自公共 IP 地址的流量流向此 VM，请选择网络作为“vm-1”。
    ![突出显示选择数据存储 ISO 文件位置的屏幕截图。](media/vcvm08.png)

10. “选择”窗口随即打开。 选择之前上传到“ISO 和模板”文件夹的文件，然后单击“确定”。
    ![新建 VM](media/vcvm10.png)

11. 检查设置，然后单击“确定”以创建 VM。
    ![显示查看设置的位置的屏幕截图。](media/vcvm11.png)

VM 现在已添加到工作负载计算资源并且可供使用。 
![显示已添加到工作负载计算资源的 VM 的屏幕截图。](media/vcvm12.png)

基础设置现已完成。 你可以像使用本地虚拟机基础架构那样开始使用私有云。

以下部分包含有关为私有云工作负载设置 DNS 和 DHCP 服务器以及修改默认网络配置的可选信息。

## <a name="add-users-and-identity-sources-to-vcenter-optional"></a>将用户和标识源添加到 vCenter（可选）

CloudSimple 将使用用户名 `cloudowner@cloudsimple.local` 分配默认 vCenter 用户帐户。 不需要额外的帐户设置即可开始使用。  CloudSimple 通常为管理员分配执行正常操作所需的特权。  将本地 active directory 或 Azure AD 设置为私有云上的[其他标识源](set-vcenter-identity.md)。

## <a name="create-a-dns-and-dhcp-server-optional"></a>创建 DNS 和 DHCP 服务器（可选）

在私有云环境中运行的应用程序和工作负载需要使用名称解析和 DHCP 服务来进行查找和 IP 地址分配。 需有适当的 DHCP 和 DNS 基础结构才能提供这些服务。 在私有云环境中，可以在 vCenter 中配置一个虚拟机来提供这些服务。

先决条件

* 配置了 VLAN 的分布式端口组

* 将设置路由到本地或基于 Internet 的 DNS 服务器

* 用于创建虚拟机的虚拟机模板或 ISO

以下链接提供有关在 Linux 和 Windows 上设置 DHCP 和 DNS 服务器的指导。

#### <a name="linux-based-dns-server-setup"></a>基于 Linux 的 DNS 服务器设置

Linux 提供了用于设置 DNS 服务器的各种包。  下面是有关设置开放源代码 BIND DNS 服务器的说明的链接。

[示例设置](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-centos-7)

#### <a name="windows-based-setup"></a>基于 Windows 的设置

这些 Microsoft 主题介绍如何将 Windows 服务器设置为 DNS 服务器和 DHCP 服务器。

[将 Windows Server 作为 DNS 服务器](/windows-server/networking/dns/dns-top)

[将 Windows Server 作为 DHCP 服务器](/windows-server/networking/technologies/dhcp/dhcp-top)

## <a name="customize-networking-configuration-optional"></a>自定义网络配置（可选）

CloudSimple 门户中的“网络”页使你能够为 VM 指定防火墙表和公共 IP 地址的配置。

### <a name="allocate-public-ips"></a>分配公共 IP

1. 在 CloudSimple 门户中，导航到“网络”>“公共 IP”。
2. 单击“分配公共 IP”。
3. 输入一个名称以标识 IP 地址条目。
4. 保留默认位置。
5. 如果需要，使用滑块来更改空闲超时值。
6. 输入要为其分配公共 IP 地址的本地 IP 地址。
7. 如果需要，输入关联的 DNS 名称。
8. 单击“完成”  。

    ![公共 IP](media/quick-create-pc-public-ip.png)

分配公共 IP 地址的任务正式开始。 可以在“活动”>“任务”页上检查任务的状态。 分配完成后，公共 IP 页上会显示新条目。

此 IP 地址必须映射到的 VM 需要配置上文中指定的本地地址。 配置 IP 地址的过程特定于 VM 操作系统。 有关正确的过程，请参阅 VM 操作系统的文档。

#### <a name="example"></a>示例

例如，下面是 Ubuntu 16.04 的详细信息。

将静态方法添加到文件 /etc/network/interfaces 中的 inet 地址系列配置。 更改地址、子网和网关值。 在此示例中，我们使用的是 eth0 接口，内部 IP 地址为 192.168.24.10，网关地址为 192.168.24.1，子网掩码为 255.255.255.0。 对于你的环境，欢迎电子邮件中提供了可用的子网信息。

```
sudo vi /etc/network/interfaces
```

```
auto eth0
Iface eth0 inet static
iface eth0 inet static
address 192.168.24.10
netmask 255.255.255.0
gateway 192.168.24.1
dns-nameservers 8.8.8.8
dns-domain acme.com
dns-search acme.com
```
手动禁用接口。

```
sudo ifdown eth0
```
再次手动启用接口。

```
sudo ifup eth0
```

默认情况下，系统会拒绝来自 Internet 的所有传入流量。 如果要打开任何其他端口，请创建[防火墙表](firewall.md)。

将内部 IP 地址配置为静态 IP 地址后，验证是否可以从 VM 内部访问 Internet。

```
ping 8.8.8.8
```
同时，验证是否可以使用公共 IP 地址通过 Internet 访问 VM。

确保 VM 上的任何 iptable 规则未阻止入站端口 80。
        
```
netstat -an | grep 80
```

启动侦听端口 80 的 http 服务器。
       
```
python2.7 -m SimpleHTTPServer 80
```

或

```
python3 -m http.server 80
```
在桌面上启动浏览器，并将其指向公共 IP 地址的端口 80，以便浏览 VM 上的文件。

### <a name="default-cloudsimple-firewall-rules-for-public-ip"></a>公共 IP 的默认 CloudSimple 防火墙规则

* VPN 流量：允许 VPN（流出/流入）与所有工作负载网络和管理网络之间的所有流量。
* 私有云内部流量：允许（流出/流入）工作负载网络和管理网络（如上图所示）之间进行所有东西向流量。
* Internet 流量：
  * 拒绝从 Internet 传入的所有流量流向工作负载网络和管理网络。
  * 允许从工作负载网络或管理网络流向 Internet 的所有流量。

你还可以使用防火墙规则功能修改保护流量的方式。 有关详细信息，请参阅[设置防火墙表和规则](firewall.md)。

## <a name="install-solutions-optional"></a>安装解决方案（可选）

可以在 CloudSimple 私有云上安装解决方案，充分利用私有云 vCenter 环境。 可以设置备份、灾难恢复、复制和其他功能来保护虚拟机。 示例包括 VMware Site Recovery Manager (VMware SRM) 和 Veeam 备份和复制。

若要安装解决方案，必须在有限的时间段内请求其他特权。 请参阅[提升特权](escalate-private-cloud-privileges.md)。

## <a name="next-steps"></a>后续步骤

* [使用 Azure 上的 VMware VM](quickstart-create-vmware-virtual-machine.md)
* [使用 Azure ExpressRoute 连接到本地网络](on-premises-connection.md)
* [在 CloudSimple 网络上设置 VPN 网关](vpn-gateway.md)
