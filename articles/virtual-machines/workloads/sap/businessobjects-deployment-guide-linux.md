---
title: 适用于 Linux 的 Azure 上的 SAP BusinessObjects BI 平台部署 | Microsoft Docs
description: 在适用于 Linux 的 Azure 上部署和配置 SAP BusinessObjects BI 平台
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files,mysql
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: b94e1f82409da3329eb6d978fa2ae0222928cd97
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102505930"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Azure 上的 SAP BusinessObjects BI 平台部署指南

本文介绍在适用于 Linux 的 Azure 上部署 SAP BOBI 平台的策略。 在此示例中，配置了两个以高级 SSD 托管磁盘作为安装目录的虚拟机。 Azure Database for MySQL 用于 CMS 数据库，文件存储库服务器的 Azure NetApp 文件在两个服务器之间共享。 默认 Tomcat Java Web 应用和 BI 平台应用程序一起安装在这两个虚拟机上。 为了对用户请求进行负载均衡，使用应用程序网关，该网关具有本机 TLS/SSL 卸载功能。

这种体系结构适用于小型部署或非生产环境。 对于生产或大规模部署，可以为 Web 应用提供单独的主机，还可以有多个 BOBI 应用程序主机，使服务器能够处理更多信息。

![适用于 Linux 的 Azure 上的 SAP BOBI 部署](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

在此示例中，使用了以下产品版本和文件系统布局

- SAP BusinessObjects Platform 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL（版本：8.0.15）
- MySQL C API Connector - libmysqlclient（版本：6.1.11）

| 文件系统        | 描述                                                                                                               | 大小(GB)             | “所有者”  | 组  | 存储                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | 用于安装 SAP BOBI 实例、默认 Tomcat Web 应用和数据库驱动程序的文件系统（如有必要） | SAP 大小调整准则 | bl1adm | sapsys | 托管高级磁盘 - SSD |
| /usr/sap/frsinput  | 装载目录适用于将用作输入文件存储库目录的所有 BOBI 主机上的共享文件  | 业务需求         | bl1adm | sapsys | Azure NetApp 文件         |
| /usr/sap/frsoutput | 装载目录适用于将用作输出文件存储库目录的所有 BOBI 主机上的共享文件 | 业务需求         | bl1adm | sapsys | Azure NetApp 文件         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>通过 Azure 门户部署 linux 虚拟机

在本部分中，我们将使用 Linux 操作系统 (OS) 映像为 SAP BOBI 平台创建两个虚拟机 (VM)。 用于创建虚拟机的概括性步骤如下所示：

1. 创建[资源组](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)

2. 创建[虚拟网络](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)。

   - 请勿在 SAP BI 平台部署中为所有 Azure 服务使用单个子网。 基于 SAP BI 平台体系结构，需要创建多个子网。 在此部署中，我们将创建三个子网：应用程序子网、文件存储库存储子网和应用程序网关子网。
   - 在 Azure 中，应用程序网关和 Azure NetApp 文件始终需要位于单独的子网中。 请查看 [Azure 应用程序网关](../../../application-gateway/configuration-overview.md)和 [Azure NetApp 文件网络规划准则](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)文章，了解更多详细信息。

3. 创建可用性集。

   - 若要为多实例部署中的每个层实现冗余，请将每个层的虚拟机置于可用性集中。 请确保根据你的体系结构分离每个层的可用性集。

4. 创建虚拟机 1 (azusbosl1)。

   - 可以使用自定义映像，也可以从 Azure 市场中选择一个映像。 请根据你的需求，参阅[为 SAP 部署来自 Azure 市场的 VM](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) 或[使用自定义映像为 SAP 部署 VM](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap)。

5. 创建虚拟机 2 (azusbosl2)。
6. 添加一个高级 SSD 盘。 它将用作 SAP BOBI 安装目录。

## <a name="provision-azure-netapp-files"></a>预配 Azure NetApp 文件

在继续设置 Azure NetApp 文件之前，请先熟悉 Azure [NetApp 文件文档](../../../azure-netapp-files/azure-netapp-files-introduction.md)。

Azure NetApp 文件在多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 查看所选 Azure 区域是否提供 Azure NetApp 文件。

使用[各 Azure 区域的 Azure NetApp 文件可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)页面，查看各区域的 Azure NetApp 文件可用性。

在部署 Azure NetApp 文件之前，请按照[注册 Azure NetApp 文件说明](../../../azure-netapp-files/azure-netapp-files-register.md)，请求载入 Azure NetApp 文件。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和 VM（将装载 Azure NetApp 文件资源）必须部署在同一 Azure 虚拟网络或对等 Azure 虚拟网络中。

1. 如果尚未部署资源，请请求[加入 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-register.md)。

2. 按照[创建 NetApp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的说明，在所选的 Azure 区域中创建一个 NetApp 帐户。

3. 按照[设置 Azure NetApp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明，设置 Azure NetApp 文件容量池。

   - 本文中介绍的 SAP BI 平台体系结构使用“高级”服务级别的单个 Azure NetApp 文件容量池。 对于 Azure 上的 SAP BI 文件存储库服务器，建议使用 Azure NetApp 文件“高级”或“超高性能” [服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

4. 按照[将子网委派给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明所述，将子网委派给 Azure NetApp 文件。

5. 按照[为 Azure NetApp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明，部署 Azure NetApp 文件卷。

   可以将 ANF 卷部署为 NFSv3 和 NFSv4.1，因为 SAP BOBI 平台支持这两种协议。 将卷部署在各自的 Azure NetApp 文件子网中。 将自动分配 Azure NetApp 卷的 IP 地址。

请记住，Azure NetApp 文件资源和 Azure VM 必须位于同一 Azure 虚拟网络或对等 Azure 虚拟网络中。 例如，azusbobi-frsinput、azusbobi-frsoutput 是卷名，nfs://10.31.2.4/azusbobi-frsinput、nfs://10.31.2.4/azusbobi-frsoutput 是 Azure NetApp 文件卷的文件路径。

- 卷 azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- 卷 azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>重要注意事项

在为 SAP BOBI 平台文件存储库服务器创建 Azure NetApp 文件时，请注意以下事项：

1. 最小容量池为 4 TiB。
2. 最小卷大小为 100 GiB。
3. Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)中。 现在支持通过同一区域中的 VNET 对等互连进行 Azure NetApp 文件访问。 尚不支持通过全球对等互连进行 Azure NetApp 访问。
4. 所选的虚拟网络必须具有一个委派给 Azure NetApp 文件的子网。
5. 通过 Azure NetApp 文件[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以对允许的客户端、访问类型（读写、只读等）进行控制。
6. Azure NetApp 文件功能尚没有区域感知性。 当前，该功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。
7. Azure NetApp 文件卷可以部署为 NFSv3 或 NFSv4.1 卷。 SAP BI 平台应用程序支持这两种协议。

## <a name="configure-file-systems-on-linux-servers"></a>在 linux 服务器上配置文件系统

本部分中的步骤使用以下前缀：

**[A]** ：该步骤适用于所有主机

### <a name="format-and-mount-sap-file-system"></a>格式化和装载 SAP 文件系统

1. **[A]** 列出所有附加的磁盘

    ```bash
    sudo lsblk
    NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
    sda      8:0    0   30G  0 disk
    ├─sda1   8:1    0    2M  0 part
    ├─sda2   8:2    0  512M  0 part /boot/efi
    ├─sda3   8:3    0    1G  0 part /boot
    └─sda4   8:4    0 28.5G  0 part /
    sdb      8:16   0   32G  0 disk
    └─sdb1   8:17   0   32G  0 part /mnt
    sdc      8:32   0  128G  0 disk
    sr0     11:0    1  628K  0 rom  
    # Premium SSD of 128 GB is attached to Virtual Machine, whose device name is sdc
    ```

2. **[A]** 对 /usr/sap 的块设备进行格式设置

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** 创建装载目录

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** 获取块设备的 UUID

    ```bash
    sudo blkid

    #It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** /etc/fstab 中维护文件系统装载条目

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** 装载文件系统

    ```bash
    sudo mount -a

    sudo df -h

    Filesystem                     Size  Used Avail Use% Mounted on
    devtmpfs                       7.9G  8.0K  7.9G   1% /dev
    tmpfs                          7.9G   82M  7.8G   2% /run
    tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
    /dev/sda4                       29G  1.8G   27G   6% /
    tmpfs                          1.6G     0  1.6G   0% /run/user/1000
    /dev/sda3                     1014M   87M  928M   9% /boot
    /dev/sda2                      512M  1.1M  511M   1% /boot/efi
    /dev/sdb1                       32G   49M   30G   1% /mnt
    /dev/sdc                       128G   29G  100G  23% /usr/sap
    ```

### <a name="mount-azure-netapp-files-volume"></a>装载 Azure NetApp 文件卷

1. **[A]** 创建装载目录

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** 将客户端 OS 配置为支持 NFSv4.1 装载（仅当使用 NFSv4.1 时适用）

   如果要将 Azure NetApp 文件卷和 NFSv4.1 协议一起使用，请在需要装入 Azure NetApp 文件 NFSv4.1 卷的所有 VM 上执行以下配置。

   **验证 NFS 域设置**

   请确保域配置为默认的 Azure NetApp 文件域（即 defaultv4iddomain.com），并且映射设置为 nobody。 

   ```bash
   sudo cat /etc/idmapd.conf
   # Example
   [General]
   Domain = defaultv4iddomain.com
   [Mapping]
   Nobody-User = nobody
   Nobody-Group = nobody
   ```

   > [!Important]
   >
   > 确保在 VM 上的 /etc/idmapd.conf 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置 defaultv4iddomain.com。 如果 NFS 客户端（即 VM）上的域配置和 NFS 服务器（即 Azure NetApp 配置）之间不匹配，则 VM 上已装载的 Azure NetApp 卷上文件的权限将显示为 nobody。

   验证 `nfs4_disable_idmapping`。 它应设置为“Y”。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。

   ```bash
   # Check nfs4_disable_idmapping
   cat /sys/module/nfs/parameters/nfs4_disable_idmapping

   # If you need to set nfs4_disable_idmapping to Y
   mkdir /mnt/tmp
   mount -t nfs -o sec=sys,vers=4.1 10.31.2.4:/azusbobi-frsinput /mnt/tmp
   umount /mnt/tmp

   echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping

   # Make the configuration permanent
   echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
   ```

3. **[A]** 添加装载条目

   如果使用 NFSv3

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   如果使用 FSv4.1

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** 装载 NFS 卷

   ```bash
   sudo mount -a

   sudo df -h

   Filesystem                     Size  Used Avail Use% Mounted on
   devtmpfs                       7.9G  8.0K  7.9G   1% /dev
   tmpfs                          7.9G   82M  7.8G   2% /run
   tmpfs                          7.9G     0  7.9G   0% /sys/fs/cgroup
   /dev/sda4                       29G  1.8G   27G   6% /
   tmpfs                          1.6G     0  1.6G   0% /run/user/1000
   /dev/sda3                     1014M   87M  928M   9% /boot
   /dev/sda2                      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1                       32G   49M   30G   1% /mnt
   /dev/sdc                       128G   29G  100G  23% /usr/sap
   10.31.2.4:/azusbobi-frsinput   101T   18G  100T   1% /usr/sap/frsinput
   10.31.2.4:/azusbobi-frsoutput  100T  512K  100T   1% /usr/sap/frsoutput
   ```

## <a name="configure-cms-database---azure-database-for-mysql"></a>配置 CMS 数据库 - Azure Database for MySQL

本部分提供有关如何使用 Azure 门户预配 Azure Database for MySQL 的详细信息。 它还提供了有关如何为 SAP BOBI 平台创建 CMS 和审核数据库的说明，以及如何创建用于访问数据库的用户帐户。

仅当使用 Azure DB for MySQL 时，这些准则才适用。 关于其他数据库，请参阅 SAP 或数据库特定的文档以了解相关说明。

### <a name="create-an-azure-database-for-mysql"></a>创建 Azure Database for MySQL

登录到 Azure 门户并按照此 [Azure Database for MySQL 快速入门指南](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)中所述的步骤进行操作。 预配 Azure Database for MySQL 时的几点注意事项：

1. 为 Azure Database for MySQL 选择 SAP BI 平台应用程序服务器运行于的区域。

2. 根据 [ SAP BI 产品可用性矩阵 (PAM)](https://support.sap.com/pam) 选择特定于你的 SAP BOBI 版本的受支持的 DB 版本。 遵循针对 SAP PAM 中的 MySQL AB 的相同兼容性准则

3. 在“计算 + 存储”中，选择“配置服务器”，并根据调整大小的输出选择适当的定价层。

4. 默认启用存储自动增长。 请记住，[存储](../../../mysql/concepts-pricing-tiers.md#storage)只能扩展，而不能缩减。

5. 默认情况下，备份保留期为七天，但你可以根据需要将其[选择配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)为最多 35 天。

6. 默认情况下，Azure Database for MySQL 的备份是本地冗余的，因此，如果要在异地冗余存储中备份服务器，请从“备份冗余选项”中选择“异地冗余”。 

> [!NOTE]
> 不支持在创建服务器后更改[备份冗余选项](../../../mysql/concepts-backup.md#backup-redundancy-options)。

### <a name="configure-connection-security"></a>配置连接安全性

默认情况下，创建的服务器使用防火墙进行保护，无法公开访问。 若要提供对 SAP BI 平台应用程序服务器运行云的虚拟网络的访问权限，请遵循以下步骤：  

1. 转到 Azure 门户中的服务器资源，然后从左侧菜单中为服务器资源选择“连接安全性”。
2. 对于“允许访问 Azure 服务”，选择“是”。 
3. 在 VNET 规则下，选择“添加现有虚拟网络”。 选择 SAP BI 平台应用程序服务器的虚拟网络和子网。 此外，还需要提供对跳转框或其他服务器的访问权限，通过这些服务器可将 [MySQL 工作台](../../../mysql/connect-workbench.md) 连接到 Azure Database for MySQL。 将使用 MySQL Workbench 创建 CMS 和审核数据库
4. 添加虚拟网络后，选择“保存”。

### <a name="create-cms-and-audit-database"></a>创建 CMS 和审核数据库

1. 从 [MySQL 网站](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。 请确保在可访问 Azure Database for MySQL 的服务器上安装 MySQL Workbench。

2. 使用 MySQL Workbench 连接到服务器。 遵循[本文](../../../mysql/connect-workbench.md#get-connection-information)中提及的说明来执行此操作。 如果连接测试成功，你会收到以下消息：

   ![SQL Workbench 连接](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. 在 SQL 查询选项卡中，运行以下查询以便为 CMS 和审核数据库创建架构。

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```
   
4. 创建用于连接到架构的用户帐户

   ```sql
   # Create a user that can connect from any host, use the '%' wildcard as a host part
   CREATE USER 'cmsadmin'@'%' IDENTIFIED BY 'password';
   CREATE USER 'auditadmin'@'%' IDENTIFIED BY 'password';

   # Grant all privileges to a user account over a specific database:
   GRANT ALL PRIVILEGES ON cmsbl1.* TO 'cmsadmin'@'%' WITH GRANT OPTION;
   GRANT ALL PRIVILEGES ON auditbl1.* TO 'auditadmin'@'%' WITH GRANT OPTION;

   # Following any updates to the user privileges, be sure to save the changes by issuing the FLUSH PRIVILEGES
   FLUSH PRIVILEGES;
   ```

5. 检查 MySQL 用户帐户的权限和角色

   ```sql
   USE sys;
   SHOW GRANTS for 'cmsadmin'@'%';
   +------------------------------------------------------------------------+
   | Grants for cmsadmin@%                                                  |
   +------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `cmsadmin`@`%`                                   |
   | GRANT ALL PRIVILEGES ON `cmsbl1`.* TO `cmsadmin`@`%` WITH GRANT OPTION |
   +------------------------------------------------------------------------+

   USE sys;
   SHOW GRANTS FOR 'auditadmin'@'%';
   +----------------------------------------------------------------------------+
   | Grants for auditadmin@%                                                    |
   +----------------------------------------------------------------------------+
   | GRANT USAGE ON *.* TO `auditadmin`@`%`                                     |
   | GRANT ALL PRIVILEGES ON `auditbl1`.* TO `auditadmin`@`%` WITH GRANT OPTION |
   +----------------------------------------------------------------------------+
   ```

### <a name="install-mysql-c-api-connector-libmysqlclient-on-linux-server"></a>在 linux 服务器上安装 MySQL C API 连接器 (libmysqlclient)

要使 SAP BOBI 应用程序服务器能够访问数据库，需要数据库客户端/驱动程序。 必须使用适用于 Linux 的 MySQL C API 连接器访问 CMS 和审核数据库。 不支持与 CMS 数据库的 ODBC 连接。 本部分提供有关如何在 Linux 上设置 MySQL C API 连接器的说明。

1. 请参阅 [与 Azure Database for MySQL 兼容的 MySQL 驱动程序和管理工具](../../../mysql/concepts-compatibility.md)一文，其中描述了与 Azure Database for MySQL 兼容的驱动程序。 请参阅文章中的“MySQL 连接器/C (libmysqlclient)”驱动程序。

2. 请参阅此[链接](https://downloads.mysql.com/archives/c-c/)以下载驱动程序。

3. 选择操作系统并下载 MySQL 连接器的共享组件 rpm 包。 在此示例中，使用了 mysql-connector-c-shared-6.1.11 连接器版本。

4. 在所有 SAP BOBI 应用程序实例中安装连接器。

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. 检查 libmysqlclient.so 的路径

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. 将 LD_LIBRARY_PATH 设置为指向将用于安装的用户帐户的 `/usr/lib64` 目录。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>服务器准备

本部分中的步骤使用以下前缀：

**[A]** ：该步骤适用于所有主机。

1. **[A]** 根据 LINUX 的风格 (SLES 或 RHEL)，需要设置内核参数并安装所需的库。 请参阅[适用于 Unix 的商业智能平台安装指南](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US)中的“系统要求”部分。

2. **[A]** 确保计算机上的时区设置正确。 请参阅安装指南中的[“其他 Unix 和 Linux 要求”部分](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/en-US/46b143336e041014910aba7db0e91070.html)。

3. **[A]**  创建软件的后台进程在其下运行的用户帐户 (bl1adm) 和组 (sapsys)。 使用此帐户执行安装并运行软件。 此帐户不需要 root 权限。

4. **[A]**  设置用户帐户 (bl1adm) 环境以使用支持的 UTF-8 区域设置，并确保控制台软件支持 UTF-8 字符集。 若要确保操作系统使用正确的区域设置，请在 (bl1adm) 用户环境中将 LC_ALL 和 LANG 环境变量设置为你的首选区域设置。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LANG=en_US.utf8
   export LC_ALL=en_US.utf8
   ```

5. **[A]**  配置用户帐户 (bl1adm)。

   ```bash
   # Set ulimit for bl1adm to unlimited
   root@azusbosl1:~> ulimit -f unlimited bl1adm
   root@azusbosl1:~> ulimit -u unlimited bl1adm

   root@azusbosl1:~> su - bl1adm
   bl1adm@azusbosl1:~> ulimit -a

   core file size          (blocks, -c) unlimited
   data seg size           (kbytes, -d) unlimited
   scheduling priority             (-e) 0
   file size               (blocks, -f) unlimited
   pending signals                 (-i) 63936
   max locked memory       (kbytes, -l) 64
   max memory size         (kbytes, -m) unlimited
   open files                      (-n) 1024
   pipe size            (512 bytes, -p) 8
   POSIX message queues     (bytes, -q) 819200
   real-time priority              (-r) 0
   stack size              (kbytes, -s) 8192
   cpu time               (seconds, -t) unlimited
   max user processes              (-u) unlimited
   virtual memory          (kbytes, -v) unlimited
   file locks                      (-x) unlimited
   ```

6. 从 SAP 服务市场下载并提取 SAP BusinessObjects BI 平台的媒体。

## <a name="installation"></a>安装

检查服务器上用户帐户 bl1adm 的区域设置

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

导航到 SAP BusinessObjects BI 平台的媒体，并通过 bl1adm 用户运行以下命令：

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

遵循特定于你的版本的适用于 Unix 的 [SAP BOBI 平台](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM)安装指南。 安装 SAP BOBI 平台时要注意的几个要点。

- 在“配置产品注册”屏幕上，你可以使用 SAP 说明 [1288121](https://launchpad.support.sap.com/#/notes/1288121) 中的 SAP BusinessObjects 解决方案临时许可密钥，也可以在 SAP 服务市场中生成许可密钥

- 在“选择安装类型”屏幕上，为第一台服务器 (azusbosl1) 选择“完整”安装；为另一台服务器 (azusbosl2) 选择“自定义/扩展”，这将扩展现有 BOBI 设置。  

- 在“选择默认或现有数据库”屏幕上，选择“配置现有数据库”，这将提示你选择 CMS 和审核数据库。  选择“MySQL”作为 CMS 数据库类型和审核数据库类型。

  如果不想在安装期间配置审核，还可以选择“无审核数据库”。

- 根据你的 SAP BOBI 体系结构，在“选择 Java Web 应用程序服务器”屏幕上选择适当的选项。 在此示例中，我们选择了选项 1，它在同一 SAP BOBI 平台上安装 tomcat 服务器。

- 在“配置 CMS 存储库数据库 - MySQL”中输入 CMS 数据库信息。 用于 Linux 安装的 CMS 数据库信息的示例输入。 Azure Database for MySQL 用于默认端口 3306
  
  ![Linux 上的 SAP BOBI 部署 - CMS 数据库](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- （可选）在“配置审核存储库数据库 - MySQL”中输入审核数据库信息。 用于 Linux 安装的审核数据库信息的示例输入。

  ![Linux 上的 SAP BOBI 部署 - 审核数据库](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 按照说明，提供所需的输入来完成安装。

对于多实例部署，请在第二个主机 (azusbosl2) 上运行安装安装程序。 在“选择安装类型”屏幕中，选择“自定义/扩展”，这将扩展现有 BOBI 设置。 

在 Azure Database for MySQL 产品中，使用网关将连接重定向到服务器实例。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。 因此，在中央管理控制台 (CMC) 中，你会发现不同的数据库版本，它基本上是在网关上设置的版本。 请查看[支持的 Azure Database for MySQL 服务器版本](../../../mysql/concepts-supported-versions.md)了解更多详细信息。

![Linux 上的 SAP BOBI 部署 - CMS 设置](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

```sql
# Run direct query to the database using MySQL Workbench

select version();

+-----------+
| version() |
+-----------+
| 8.0.15    |
+-----------+
```

## <a name="post-installation"></a>安装后

### <a name="tomcat-clustering---session-replication"></a>Tomcat 群集 - 会话复制

Tomcat 支持将两个或更多应用程序服务器组成群集，用于会话复制和故障转移。 SAP BOBI 平台会话将进行序列化，即使应用程序服务器发生故障，用户会话也可以无缝地故障转移到另一个 tomcat 实例。

例如，如果用户连接到的 web 服务器在用户在 SAP BI 应用程序中导航文件夹层次结构时失败。 使用正确配置的群集，用户可以继续导航文件夹层次结构，而无需重定向到登录页。

在 SAP 说明 [2808640](https://launchpad.support.sap.com/#/notes/2808640) 中，使用多播提供了配置 tomcat 群集的步骤。 但在 Azure 中，不支持多播。 因此，若要使 Tomcat 群集在 Azure 中可用，必须使用 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership)（SAP 说明 [2764907](https://launchpad.support.sap.com/#/notes/2764907)）。 请参阅 SAP 博客上的[使用 SAP BusinessObjects BI 平台静态成员身份的 Tomcat 群集](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)，在 Azure 中设置 tomcat 群集。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 平台的负载均衡 web 层

在 SAP BOBI 多实例部署中，Java Web 应用程序服务器（web 层）在两个或更多主机上运行。 若要在 Web 服务器之间均匀分配用户负载，可以在最终用户与 Web 服务器之间使用负载均衡器。 在 Azure 中，可以使用 Azure 负载均衡器或 Azure 应用程序网关来管理 web 应用程序服务器的流量。 下一节将介绍有关每个产品的详细信息。

#### <a name="azure-load-balancer-network-based-load-balancer"></a>Azure 负载均衡器（基于网络的负载均衡器）

[Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)是高性能、低延迟的第 4 层（TCP、UDP）负载均衡器，可在正常运行的虚拟机之间分配流量。 负载均衡器的运行状况探测监视每个 VM 上的给定端口，并仅将流量分发给正常运行的虚拟机。 可以根据是否允许从 Internet 访问 SAP BI 平台，选择公共负载均衡器或内部负载均衡器。 它是区域冗余的，确保跨可用性区域的高可用性。

请参阅下图中的“内部负载均衡器”部分，其中 web 应用程序服务器在端口 8080 上运行，它是默认 Tomcat HTTP 端口，将由运行状况探测进行监视。 因此，来自最终用户的任何传入请求都将被重定向到后端池中的 web 应用程序服务器 (azusbosl1 或 azusbosl2)。 负载均衡器不支持 TLS/SSL 终止（也称为 TLS/SSL 卸载）。 如果使用 Azure 负载均衡器将流量分发到 web 服务器，建议使用标准负载均衡器。

> [!NOTE]
> 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](high-availability-guide-standard-load-balancer-outbound-connections.md)。

![用于跨 Web 服务器均衡流量的 Azure 负载均衡器](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway-web-application-load-balancer"></a>Azure 应用程序网关（web 应用程序负载均衡器）

[Azure 应用程序网关 (AGW)](../../../application-gateway/overview.md) 以服务的形式提供应用程序传送控制器 (ADC)，用于帮助应用程序将用户流量定向到一个或多个 web 应用程序服务器。 它为应用程序提供各种第 7 层负载均衡功能，例如 TLS/SSL 卸载、Web 应用程序防火墙 (WAF)、基于 Cookie 的会话相关性和其他功能。

在 SAP BI 平台中，应用程序网关将应用程序 web 流量定向到后端池（azusbosl1 或 azusbos2）中的指定资源。 你将向端口分配侦听器，创建规则，并向后端池中添加资源。 在下图中，具有专用前端 IP 地址 (10.31.3.20) 的应用程序网关充当用户的入口点，处理传入的 TLS/SSL (HTTPS - TCP/443) 连接，对 TLS/SSL 进行解密，并将未加密的请求 (HTTP - TCP/8080) 传递到后端池中的服务器。 通过内置的 TLS/SSL 终止功能，只需在应用程序网关上维护一个 TLS/SSL 证书，这可以简化操作。

![用于跨 Web 服务器均衡流量的应用程序网关](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

若要为 SAP BOBI Web 服务器配置应用程序网关，可以参阅 SAP 博客上的[使用 Azure 应用程序网关实现 SAP BOBI 的负载均衡](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)。

> [!NOTE]
> 建议使用 Azure 应用程序网关对流量进行负载均衡，因为它提供 SSL 卸载、集中 SSL 管理（用于降低服务器上的加密和解密开销）、轮询算法（用于分发流量）、Web 应用程序防火墙 (WAF) 功能、高可用性等功能。

### <a name="sap-businessobjects-bi-platform---back-up-and-restore"></a>SAP BusinessObjects BI 平台 - 备份和还原

备份和还原是将数据和应用程序定期复制到单独位置的过程。 这样，如果原始数据或应用程序丢失或损坏，可将其还原或恢复到以前的状态。 它也是任何业务灾难恢复策略的基本组成部分。

若要为 SAP BOBI 平台开发全面的备份和还原策略，请确定导致应用程序系统停机或中断的组件。 在 SAP BOBI 平台中，以下组件的备份对保护应用程序至关重要。

- SAP BOBI 安装目录（托管高级磁盘）
- 文件存储库服务器（Azure NetApp 文件或 Azure 高级文件）
- CMS 数据库（Azure Database for MySQL 或 Azure VM 上的数据库）

以下部分介绍如何为 SAP BOBI 平台上的每个组件实现备份和还原策略。

#### <a name="backup--restore-for-sap-bobi-installation-directory"></a>SAP BOBI 安装目录的备份和还原

在 Azure 中，备份应用程序服务器和所有附加磁盘的最简单方法是使用 [Azure 备份](../../../backup/backup-overview.md)服务。 它提供独立且隔离的备份来防止 VM 上的数据被意外破坏。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和缩放很简单，备份经过优化，可以在需要时轻松还原。

在备份过程中，将创建快照，并将数据传输到恢复服务保管库，而不影响生产工作负载。 快照提供不同的一致性级别，如[快照一致性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)一文中所述。 还可使用选择性磁盘备份和还原功能，选择备份 VM 中的一部分数据磁盘。 有关详细信息，请参阅 [Azure VM 备份](../../../backup/backup-azure-vms-introduction.md)文档和[常见问题解答 - 备份 Azure VM](../../../backup/backup-azure-vm-backup-faq.yml)。

#### <a name="backup--restore-for-file-repository-server"></a>文件存储库服务器的备份和还原

对于 Azure NetApp 文件，可以创建按需快照，并使用快照策略计划自动拍摄快照。 快照副本提供 ANF 卷的时间点副本。 有关详细信息，请参阅[使用 Azure NetApp 文件管理快照](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)。

Azure 文件备份与本机 [Azure 备份](../../../backup/backup-overview.md)服务集成，该服务将备份和还原功能与 VM 备份集中在一起，从而简化操作。 有关详细信息，请参阅 [Azure 文件共享备份](../../../backup/azure-file-share-backup-overview.md)和[常见问题解答 - 备份 Azure 文件](../../../backup/backup-azure-files-faq.md)。

#### <a name="backup--restore-for-cms-database"></a>CMS 数据库的备份和还原

Azure Database for MySQL 是 Azure 中的 DBaaS 产品，可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 Azure Database for MySQL 会创建数据文件和事务日志的备份。 根据支持的最大存储大小，将进行完整备份和差异备份（最大 4 TB 的存储服务器）或快照备份（最大 16 TB 的存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认备份保持期为七天，可以[选择将其配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)为三天。 所有备份都使用 AES 256 位加密进行加密。

这些备份文件不公开给用户，因此无法导出。 这些备份只能用于 Azure Database for MySQL 中的还原操作。 可以使用 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) 复制数据库。 有关详细信息，请参阅[在 Azure Database for MySQL 中进行备份和还原](../../../mysql/concepts-backup.md)。

对于在虚拟机上安装的数据库，可以对 HANA 数据库使用标准的备份工具或 [Azure 备份](../../../backup/sap-hana-db-about.md)。 此外，如果 Azure 服务和工具无法满足你的要求，可以使用其他备份工具或脚本来创建磁盘备份。

## <a name="sap-businessobjects-bi-platform-reliability"></a>SAP BusinessObjects BI 平台可靠性

SAP BusinessObjects BI 平台包含不同的层，这些层针对特定任务和操作进行了优化。 当任何一层中的组件变为不可用时，SAP BOBI 应用程序将变得不可访问，或者应用程序的某些功能将无法正常工作。 因此，需要确保每个层的可靠性，使应用程序可以正常运行而不会出现任何业务中断。

本部分重点介绍 SAP BOBI 平台的以下选项：

- **高可用性：** 高可用平台的所有组件在 Azure 区域中都至少有两个实例，这样，即使其中一个服务器不可用，也能使应用程序保持正常运行。
- **灾难恢复：** 这是在出现灾难性损失（例如由于某些自然灾害而导致整个 Azure 区域不可用）时还原应用程序功能的过程。

此解决方案的实现因 Azure 中系统设置的性质而异。 因此，客户需要根据其业务需求定制高可用性和灾难恢复解决方案。

### <a name="high-availability"></a>高可用性

高可用性指的是一系列技术，这些技术可通过相同数据中心内受冗余、容错或故障转移保护的组件，为应用程序/服务提供业务连续性，以最大程度减少 IT 中断的情况。 在本例中，数据中心位于一个 Azure 区域内。 [适用于 SAP 的高可用性体系结构和方案](sap-high-availability-architecture-scenarios.md)一文为 Azure 上针对 SAP 应用程序提供的各种高可用性技术和建议提供了最初的见解，这将补充本部分中的说明。

根据 SAP BOBI 平台的大小调整结果，你需要设计格局，并确定跨 Azure 虚拟机和子网的 BI 组件分布。 分布式体系结构中的冗余级别取决于业务所需的恢复时间目标 (RTO) 和恢复点目标 (RPO)。 SAP BOBI 平台包含不同的层，应对每个层上的组件进行设计以实现冗余。 这样，如果一个组件发生故障，几乎或完全不会对 SAP BOBI 应用程序造成任何干扰。 例如，

- 冗余应用程序服务器，如 BI 应用程序服务器和 Web 服务器
- CMS 数据库、文件存储库服务器、负载均衡器等独特组件

下一部分介绍如何在 SAP BOBI 平台的每个组件上实现高可用性。

#### <a name="high-availability-for-application-servers"></a>应用程序服务器的高可用性

对于 BI 和 Web 应用程序服务器，无论它们是单独安装还是一起安装的，都不需要特定的高可用性解决方案。 可以通过冗余实现高可用性，即在不同的 Azure 虚拟机中配置 BI 和 Web 服务器的多个实例。

为降低因一个或多个事件导致停机的影响，建议对在多个虚拟机上运行的应用程序服务器遵循高可用性做法。

- 使用可用性区域防范数据中心故障。
- 在可用性集中配置多个虚拟机以确保冗余。
- 为可用性集中的 VM 使用托管磁盘。
- 将每个应用程序层配置到不同的可用性集。

有关详细信息，请参阅[管理 Linux 虚拟机的可用性](../../availability.md)

#### <a name="high-availability-for-cms-database"></a>CMS 数据库的高可用性

如果将 Azure 数据库即服务 (DBaaS) 用于 CMS 数据库，则默认提供高可用性框架。 只需选择区域和服务固有的高可用性、冗余和复原能力，无需配置任何其他组件。 有关 Azure 上受支持的 DBaaS 服务的 SLA 的更多详细信息，请参阅 [Azure Database for MySQL 中的高可用性](../../../azure-sql/database/high-availability-sla.md)和 [Azure SQL 数据库的高可用性](../../../mysql/concepts-high-availability.md)

对于 CMS 数据库的其他 DBMS 部署，请参阅 [适用于 SAP 工作负载的 DBMS 部署指南](dbms_guide_general.md)，其中提供了有关不同 DBMS 部署及其实现高可用性的方法的见解。

#### <a name="high-availability-for-file-repository-server"></a>文件存储库服务器的高可用性

文件存储库服务器 (FRS) 指存储报表、universe 和连接等内容的磁盘目录。 它在该系统的所有应用程序服务器之间共享。 因此必须确保其高度可用。

在 Azure 上，可以为设计为高度可用且高度持久的文件共享选择 [Azure 高级文件](../../../storage/files/storage-files-introduction.md)或 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)。 有关详细信息，请参阅 Azure 文件存储的[冗余](../../../storage/files/storage-files-planning.md#redundancy)部分。

> [!NOTE]
> Azure 文件存储的 SMB 协议已公开发布，但针对 Azure 文件存储的 NFS 协议支持目前为预览版。 有关详细信息，请参阅[对 Azure 文件存储的 NFS 4.1 支持现在为预览版](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/)

由于此文件共享服务仅在部分区域可用，因此请务必参考[各区域的可用产品](https://azure.microsoft.com/en-us/global-infrastructure/services/)站点以了解最新信息。 如果该服务在你的区域中不可用，可以创建 NFS 服务器，并从中将文件系统共享给 SAP BOBI 应用程序。 但你还需要考虑其高可用性。

#### <a name="high-availability-for-load-balancer"></a>负载均衡器的高可用性

若要在 web 服务器之间分配流量，可以使用 Azure 负载均衡器或 Azure 应用程序网关。 可根据你为部署选择的 SKU 来实现任一负载均衡器的冗余。

- 对于 Azure 负载均衡器，可以通过将标准负载均衡器前端配置为区域冗余来实现冗余。 有关详细信息，请参阅[标准负载均衡器和可用性区域](../../../load-balancer/load-balancer-standard-availability-zones.md)
- 对于应用程序网关，可以根据部署过程中选择的层类型来实现高可用性。
  - 如果部署了两个或更多实例，v1 SKU 支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，确保实例不会全部同时发生故障。 因此使用此 SKU 可在区域内实现冗余
  - v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果选择“区域冗余”，则最新实例还将分布到各个可用性区域中以提供区域性故障复原能力。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关 v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)

#### <a name="reference-high-availability-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台的参考高可用性体系结构

以下参考体系结构介绍了如何使用可用性集设置 SAP BOBI 平台，可用性集提供了区域中的 VM 冗余和可用性。 该体系结构展示了如何使用不同的 Azure 服务，例如 Azure 应用程序网关、Azure NetApp 文件以及提供内置冗余的适用于 SAP BOBI 平台的 Azure Database for MySQL，这降低了管理不同高可用性解决方案的复杂性。

在下图中，使用 Azure 应用程序网关 v1 SKU 对传入流量 (HTTPS - TCP/443) 进行了负载均衡，此 SKU 在两个或更多实例上部署时高度可用。 Web 服务器、管理服务器和处理服务器的多个实例部署在单独的虚拟机中以实现冗余，并且每个层部署在不同的可用性集中。 Azure NetApp 文件在数据中心内有内置冗余，因此文件存储库服务器的 ANF 卷将高度可用。 CMS 数据库预配于 Azure Database for MySQL (DBaaS) 上，该服务具有固有的高可用性。 有关详细信息，请参阅 [Azure Database for MySQL 中的高可用性](../../../mysql/concepts-high-availability.md)指南。

![使用可用性集的 SAP BusinessObjects BI 平台冗余](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

上述体系结构提供了有关如何在 Azure 上部署 SAP BOBI 的见解。 但它并未涵盖 Azure 上的 SAP BOBI 平台的所有可能的配置选项。 客户可根据其业务需求定制其部署，为负载均衡器、文件存储库服务器和 DBMS 等不同组件选择不同的产品/服务。

在一些 Azure 区域提供可用性区域，这意味着它具有独立的电源、冷却系统和网络。 它使客户能够跨两个或三个可用性区域部署应用程序。 对于希望跨 AZ 实现高可用性的客户，可以跨可用性区域部署 SAP BOBI 平台，从而确保应用程序中的每个组件都是区域冗余的。

### <a name="disaster-recovery"></a>灾难恢复

本部分中的说明介绍为 SAP BOBI 平台提供灾难恢复保护的策略。 它补充了 [SAP 的灾难恢复](../../../site-recovery/site-recovery-sap.md)文档，该文档代表总体 SAP 灾难恢复方法的主要资源。

#### <a name="reference-disaster-recovery-architecture-for-sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI 平台的参考灾难恢复体系结构

此参考体系结构通过冗余的应用程序服务器运行 SAP BOBI 平台的多实例部署。 对于灾难恢复，应将所有层故障转移到次要区域。 每个层使用不同的策略提供灾难恢复保护。

![SAP BusinessObjects BI 平台灾难恢复](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

#### <a name="load-balancer"></a>负载均衡器

负载均衡器用于在 SAP BOBI 平台的 Web 应用程序服务器之间分配流量。 若要为 Azure 应用程序网关实现 DR，请在次要区域中实现应用程序网关的并行安装。

#### <a name="virtual-machines-running-web-and-bi-application-servers"></a>运行 web 和 BI 应用程序服务器的虚拟机

可使用 Azure Site Recovery 服务在次要区域中复制运行 Web 和 BI 应用程序服务器的虚拟机。 它会在次要区域中复制服务器，以便在发生灾难和中断时，可以轻松地故障转移到复制的环境并继续工作

#### <a name="file-repository-servers"></a>文件存储库服务器

- **Azure NetApp 文件** 提供 NFS 和 SMB 卷，因此可使用任何基于文件的复制工具在 Azure 区域之间复制数据。 有关如何在另一个区域中复制 ANF 卷的详细信息，请参阅[关于 Azure NetApp 文件的常见问题解答](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)

  可以使用 Azure NetApp 文件跨区域复制，该功能当前提供[预览版](https://azure.microsoft.com/en-us/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)，它使用 NetApp SnapMirror® 技术。 因此只会以经压缩的高效格式通过网络发送更改后的块。 这项专有技术最大程度地减少了跨区域复制所需的数据量，可以节省数据传输成本。 它还可以缩短复制时间，让你可以实现较小的还原点目标 (RPO)。 有关详细信息，请参阅[使用跨区域复制的要求和注意事项](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)。

- **Azure 高级文件** 仅支持本地冗余 (LRS) 和区域冗余存储 (ZRS)。 对于 Azure 高级文件 DR 策略，可以使用 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 或 [Azure PowerShell](/powershell/module/az.storage/) 将文件复制到不同区域中的其他存储帐户。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../../../storage/common/storage-disaster-recovery-guidance.md)

#### <a name="cms-database"></a>CMS 数据库

Azure Database for MySQL 提供了多个选项，可在发生任何灾难时恢复数据库。 选择适用于你的业务的适当选项。

- 可以使用跨区域只读副本来增强业务连续性和灾难恢复计划。 可将源服务器中的数据复制到最多 5 个副本。 只读副本使用 MySQL 的二进制日志复制技术进行异步更新。 副本是新的服务器，可以像管理普通的 Azure Database for MySQL 服务器一样对其进行管理。 从[只读副本概念文章](../../../mysql/concepts-read-replicas.md)详细了解有关只读副本、可用区域、限制以及如何进行故障转移的信息。

- 使用 Azure Database for MySQL 的异地还原功能，该功能使用异地冗余备份来还原服务器。 即使你的服务器所在的区域离线，也可访问这些备份。 可以使用这些备份还原到任何其他区域并使服务器恢复联机。

  > [!NOTE]
  > 只有当为服务器预配了异地冗余备份存储时，异地还原才是可行的。 不支持在创建服务器后更改备份冗余选项。 有关详细信息，请参阅[备份冗余](../../../mysql/concepts-backup.md#backup-redundancy-options)一文。

下面是此示例中使用的每个层的灾难恢复建议。

| SAP BOBI 平台层   | 建议                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure 应用程序网关 | 次要区域上应用程序网关的并行安装                                                |
| Web 应用服务器   | 使用 Site Recovery 进行复制                                                                         |
| BI 应用程序服务器    | 使用 Site Recovery 进行复制                                                                         |
| Azure NetApp 文件        | 基于文件的复制工具将数据复制到次要区域或 ANF 跨区域复制（预览） |
| Azure Database for MySQL  | 跨区域只读副本或从异地冗余备份还原备份。                             |

## <a name="next-steps"></a>后续步骤

- [为多层 SAP 应用部署设置灾难恢复](../../../site-recovery/site-recovery-sap.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
- [适用于 SAP 的 Azure 虚拟机 DBMS 部署](./dbms_guide_general.md)
