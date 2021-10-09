---
title: 部署 Bitnami 虚拟设备
description: 了解由 Bitnami 打包的、可部署在 Azure VMware 解决方案私有云中的虚拟设备。
ms.topic: how-to
ms.date: 09/15/2021
ms.openlocfilehash: 41201eddaa2ba14110f36c18350860efd4d3a93e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128698983"
---
# <a name="bitnami-appliance-deployment"></a>Bitnami 设备部署

VMware 的 Bitnami 提供丰富的统包式虚拟设备目录。 你可以部署 Bitnami 在 [VMware Marketplace](https://marketplace.cloud.vmware.com/) 中提供的任何与 vSphere 兼容的设备，包括许多最常见的开源软件项目。

本文介绍如何在 Azure VMware 解决方案私有云上安装和配置 Bitnami 打包的以下虚拟设备：

- LAMP

- Jenkins

- PostgreSQL

- NGINX

- RabbitMQ



## <a name="prerequisites"></a>先决条件

- [部署了至少三个节点](deploy-azure-vmware-solution.md)的 Azure VMware 解决方案私有云。

- 根据[网络规划清单](tutorial-network-checklist.md)中所述配置的网络。



## <a name="step-1-download-the-bitnami-virtual-appliance-ovaovf-file"></a>步骤 1。 下载 Bitnami 虚拟设备 OVA/OVF 文件


1. 转到 [VMware Marketplace](https://marketplace.cloud.vmware.com/)，下载要安装在 Azure VMware 解决方案私有云上的虚拟设备：

   - [Bitnami 打包的 LAMP 虚拟设备](https://marketplace.cloud.vmware.com/services/details/lampstack?slug=true)

   - [Jenkins](https://marketplace.cloud.vmware.com/services/details/jenkins?slug=true)

   - [PostgreSQL](https://marketplace.cloud.vmware.com/services/details/postgresql?slug=true)

   - [NGINX](https://marketplace.cloud.vmware.com/services/details/nginxstack?slug=true)

   - [RabbitMQ](https://marketplace.cloud.vmware.com/services/details/rabbitmq?slug=true)

1. 选择版本，选择“下载”，然后接受 EULA 许可协议。 

   >[!NOTE]
   >确保可从虚拟机访问该文件。



## <a name="step-2-access-the-local-vcenter-of-your-private-cloud"></a>步骤 2。 访问私有云的本地 vCenter

1. 登录到 [Azure 门户](https://portal.azure.com)，选择你的私有云，然后选择“管理” > “标识” 。

1. 复制 vCenter URL、用户名和密码。 你将使用这些信息来访问你的虚拟机 (VM)。 

1. 选择“概述”，选择该 VM，然后通过 RDP 连接到该 VM。 连接时如需帮助，请参阅[连接到虚拟机](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine)了解详细信息。

1. 在 VM 中，打开浏览器并导航到 vCenter URL。 

1. 使用前面复制的 `cloudadmin@vsphere.local` 用户凭据登录。

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="显示 VMware vSphere 登录页的屏幕截图。" border="true":::




## <a name="step-3-install-the-bitnami-ovaovf-file-in-vcenter"></a>步骤 3。 在 vCenter 中安装 Bitnami OVA/OVF 文件

1. 右键单击要安装 LAMP 虚拟设备的群集，然后选择“部署 OVF 模板”。

1. 选择“本地文件”并导航到前面下载的 OVF 文件。 然后，选择“下一步”。

1. 选择你的数据中心，并为虚拟设备 VM 提供一个名称，例如 bitnami-lampstack。 然后，选择“下一步”。

1. 选择 ESXi 主机作为计算资源来运行 VM，然后选择“下一步”。

1. 查看详细信息并选择“下一步”。

1. 接受许可协议，然后选择 **下一步**。

1. 为 VM 选择存储，然后选择“下一步”。

1. 为 VM 选择目标网络，然后选择“下一步”。

1. 提供用于自定义模板的信息，例如 VM 和网络属性。 然后，选择“下一步”。  

1. 检查配置设置，然后选择“完成”。

1. 在“任务控制台”中，验证 OVF 模板部署状态是否为成功完成。

1. 安装完成后，在“操作”下，选择“开机”打开设备 。 

1. 在 vCenter 控制台中，选择“启动 Web 控制台”并登录到 Bitnami 虚拟设备。 查看 [Bitnami 虚拟设备支持文档](https://docs.bitnami.com/vmware-marketplace/faq/get-started/find-credentials/)获取默认用户名和密码。

   >[!NOTE]
   >可将默认密码更改为更安全的密码。 有关详细信息，请参阅...



## <a name="step-4-assign-a-static-ip-to-the-virtual-appliance"></a>步骤 4。 为虚拟设备分配静态 IP

在此步骤中，你将修改 bootproto 和 onboot 参数，并为 Bitnami 虚拟设备分配一个静态 IP 地址 。 

1. 搜索网络配置文件。 

   ```bash
   sudo find /etc -name \*ens160\*
   ```

1. 编辑 \/etc\/sysconfig\/network-scripts\/ifcfg-ens160 文件并修改启动参数。 然后添加静态 IP、网络掩码和网关地址。

   - bootproto=static

   - onboot=yes


1. 查看并确认对 ifcfg-ens160 文件所做的更改。

   ```bash
   cat ifcfg-ens160  
   ```

1. 重启网络服务。 这会先停止网络服务，然后应用 IP 配置。 

   ```bash
   sudo systemctl restart network
   ```

1. Ping 网关 IP 地址，以验证配置以及 VM 的网络连接。

1. 确认列出了默认路由 0.0.0.0。

   ```bash
   sudo route -n
   ```



## <a name="step-5-enable-ssh-access-to-the-virtual-appliance"></a>步骤 5。 启用对虚拟设备的 SSH 访问

在此步骤中，你将在虚拟设备上启用 SSH，以便能够进行远程访问控制。 SSH 服务默认已禁用。 你还将使用 PuTTy 连接到主机控制台。

1. 启用并启动 SSH 服务。

   ```bash
   sudo rm /etc/ssh/sshd_not_to_be_run
   sudo systemctl enable sshd
   sudo systemctl start sshd
   ```

1. 编辑 \/etc\/ssh\/sshd_config 文件以更改密码身份验证。

   ```bash
   PasswordAuthentication yes
   ```

1. 查看并确认对 sshd_config 文件所做的更改。

   ```bash
   sudo cat sshd_config
   ```

1. 重新加载对该文件所做的更改。 

   ```bash
   sudo /etc/init.d/ssh force-reload
   ```

1. 打开 PuTTY，选择“SSH”选项，提供主机名并输入 *22 作为端口。 然后选择“打开”。 

1. 在虚拟设备控制台提示符下，输入 Bitnami 用户名和密码以连接到主机。 



