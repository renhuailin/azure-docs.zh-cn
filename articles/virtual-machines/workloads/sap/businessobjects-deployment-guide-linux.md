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
ms.openlocfilehash: bf1ca9de6d7ee8ac6c7e87bd444cbd1d98b56f51
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112463398"
---
# <a name="sap-businessobjects-bi-platform-deployment-guide-for-linux-on-azure"></a>Linux on Azure 的 SAP BusinessObjects BI 平台部署指南

本文将介绍在适用于 Linux 的 Azure 上部署 SAP BusinessObjects BI (BOBI) 平台的策略。 在此示例中，你将配置两台以高级固态硬盘 (SSD) 托管磁盘作为安装目录的虚拟机。 将 Azure Database for MySQL 用于 CMS 数据库，并将文件存储库服务器的 Azure NetApp 文件在两个服务器之间共享。 在这两台虚拟机上，你将同时安装默认 Tomcat Java Web 应用程序和 BI 平台应用程序。 为了对用户请求进行负载均衡，你将使用包含本机 TLS/SSL 卸载功能的 Azure 应用程序网关。

这种体系结构适用于小型部署或非生产环境。 对于大型部署或生产环境，你可以为 Web 应用程序提供单独的主机。 还可以提供多个 BOBI 应用程序主机，使服务器能够处理更多信息。

![适用于 Linux 的 Azure 上的 SAP BOBI 部署示意图](media/businessobjects-deployment-guide/businessobjects-deployment-linux.png)

下面是此示例使用的产品版本和文件系统布局：

- SAP BusinessObjects 平台 4.3
- SUSE Linux Enterprise Server 12 SP5
- Azure Database for MySQL（版本：8.0.15）
- MySQL C API Connector - libmysqlclient（版本：6.1.11）

| 文件系统        | 说明                                                                                                               | 大小(GB)             | “所有者”  | 组  | 存储                    |
|--------------------|---------------------------------------------------------------------------------------------------------------------------|-----------------------|--------|--------|----------------------------|
| /usr/sap           | 用于安装 SAP BOBI 实例、默认 Tomcat Web 应用程序和数据库驱动程序的文件系统（如有必要）。 | SAP 大小调整准则 | bl1adm | sapsys | 托管高级磁盘 - SSD |
| /usr/sap/frsinput  | 装载目录适用于将用作输入文件存储库目录的所有 BOBI 主机上的共享文件。  | 业务需求         | bl1adm | sapsys | Azure NetApp 文件         |
| /usr/sap/frsoutput | 装载目录适用于将用作输出文件存储库目录的所有 BOBI 主机上的共享文件 | 业务需求         | bl1adm | sapsys | Azure NetApp 文件         |

## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机

在本部分中，你将使用 Linux 操作系统映像为 SAP BOBI 平台创建两台虚拟机。 用于创建虚拟机的概括性步骤如下所示：

1. 创建[资源组](../../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups)。

2. 创建[虚拟网络](../../../virtual-network/quick-create-portal.md#create-a-virtual-network)。

   - 请不要在 SAP BI 平台部署中为所有 Azure 服务使用一个子网。 基于 SAP BI 平台体系结构，需要创建多个子网。 在此部署中，你将创建三个子网：分别用于应用程序、文件存储库存储和应用程序网关。
   - 在 Azure 中，应用程序网关和 Azure NetApp 文件必须始终位于单独的子网中。 有关详细信息，请参阅 [Azure 应用程序网关](../../../application-gateway/configuration-overview.md)和 [Azure NetApp 文件网络规划准则](../../../azure-netapp-files/azure-netapp-files-network-topologies.md)。

3. 创建可用性集。 若要为多实例部署中的每个层实现冗余，请将每个层的虚拟机置于可用性集中。 请确保根据你的体系结构分离每个层的可用性集。

4. 创建虚拟机 1，名称为 (azusbosl1)。

   - 可以使用自定义映像，也可以从 Azure 市场中选择一个映像。 有关详细信息，请参阅[为 SAP 部署来自 Azure 市场的 VM](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-1-deploying-a-vm-from-the-azure-marketplace-for-sap) 或[使用自定义映像为 SAP 部署 VM](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/deployment-guide.md#scenario-2-deploying-a-vm-with-a-custom-image-for-sap)。

5. 创建虚拟机 2，名称为 (azusbosl2)。
6. 添加一个高级 SSD 盘。 你将把它用作 SAP BOBI 安装目录。

## <a name="provision-azure-netapp-files"></a>预配 Azure NetApp 文件

在继续设置 Azure NetApp 文件之前，请先熟悉 [Azure NetApp 文件文档](../../../azure-netapp-files/azure-netapp-files-introduction.md)。

Azure NetApp 文件在多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中可用。 查看所选 Azure 区域是否提供 Azure NetApp 文件。

使用[各 Azure 区域的 Azure NetApp 文件可用性](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)查看各区域的 Azure NetApp 文件可用性。

在部署 Azure NetApp 文件之前，请参阅[注册 Azure NetApp 文件说明](../../../azure-netapp-files/azure-netapp-files-register.md)。

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和将装载 Azure NetApp 文件资源的 VM 必须部署在同一 Azure 虚拟网络或对等 Azure 虚拟网络中。

1. 如果尚未部署资源，请[注册 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-register.md)。

2. 在所选的 Azure 区域[创建 Azure NetApp 文件帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)。

3. [设置 Azure NetApp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)。 本文中介绍的 SAP BI 平台体系结构使用“高级”服务级别的单个 Azure NetApp 文件容量池。 对于 Azure 上的 SAP BI 文件存储库服务器，建议使用 Azure NetApp 文件“高级”或“超高性能”[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。

4. [将子网委托给 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)。

5. 按照[为 Azure NetApp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明部署 Azure NetApp 文件卷。

   可以将卷部署为 NFSv3 和 NFSv4.1，因为 SAP BOBI 平台支持这两种协议。 将卷部署在其各自的 Azure NetApp 文件子网中。 将自动分配 Azure NetApp 文件卷的 IP 地址。

请记住，Azure NetApp 文件资源和 Azure VM 必须位于同一 Azure 虚拟网络或对等 Azure 虚拟网络中。 例如，azusbobi-frsinput 和 azusbobi-frsoutput 是卷名，nfs://10.31.2.4/azusbobi-frsinput 和 nfs://10.31.2.4/azusbobi-frsoutput 是 Azure NetApp 文件卷的文件路径。   

- 卷 azusbobi-frsinput (nfs://10.31.2.4/azusbobi-frsinput)
- 卷 azusbobi-frsoutput (nfs://10.31.2.4/azusbobi-frsoutput)

### <a name="important-considerations"></a>重要注意事项

在为 SAP BOBI 平台文件存储库服务器创建 Azure NetApp 文件时，请注意以下事项：

- 最小容量池为 4 TiB。
- 最小卷大小为 100 GiB。
- Azure NetApp 文件和所有虚拟机（将装载 Azure NetApp 文件卷）必须位于同一 Azure 虚拟网络中或同一区域中的[对等虚拟网络](../../../virtual-network/virtual-network-peering-overview.md)中。 支持通过同一区域中的虚拟网络对等互连进行 Azure NetApp 文件访问。 目前不支持通过全球对等互连进行 Azure NetApp 文件访问。
- 所选的虚拟网络必须具有一个委派给 Azure NetApp 文件的子网。
- 通过 Azure NetApp 文件[导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以对允许的客户端、访问类型（例如读写或只读）进行控制。
- Azure NetApp 文件功能尚没有区域感知性。 目前，该功能未在 Azure 区域中的所有可用性区域中部署。 请注意某些 Azure 区域的潜在延迟影响。
- Azure NetApp 文件卷可以部署为 NFSv3 或 NFSv4.1 卷。 SAP BI 平台应用程序支持这两种协议。

## <a name="configure-file-systems-on-linux-servers"></a>在 Linux 服务器上配置文件系统

本部分中的步骤使用以下前缀：

**[A]** ：该步骤适用于所有主机。

### <a name="format-and-mount-the-sap-file-system"></a>格式化和装载 SAP 文件系统

1. **[A]** 列出所有附加的磁盘。

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
    # Premium SSD of 128 GB is attached to virtual machine, whose device name is sdc
    ```

2. **[A]** 对 /usr/sap 的块设备进行格式设置。

    ```bash
    sudo mkfs.xfs /dev/sdc
    ```

3. **[A]** 创建装载目录。

    ```bash
    sudo mkdir -p /usr/sap
    ```

4. **[A]** 获取块设备的 UUID。

    ```bash
    sudo blkid

    # It will display information about block device. Copy UUID of the formatted block device

    /dev/sdc: UUID="0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b" TYPE="xfs"
    ```

5. **[A]** 维护 /etc/fstab 中的文件系统装载条目。

    ```bash
    sudo echo "UUID=0eb5f6f8-fa77-42a6-b22d-7a9472b4dd1b /usr/sap xfs defaults,nofail 0 2" >> /etc/fstab
    ```

6. **[A]** 装载文件系统。

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

### <a name="mount-the-azure-netapp-files-volume"></a>装载 Azure NetApp 文件卷

1. **[A]** 创建装载目录。

   ```bash
   sudo mkdir -p /usr/sap/frsinput
   sudo mkdir -p /usr/sap/frsoutput
   ```

2. **[A]** 将客户端操作系统配置为支持 NFSv4.1 装载（仅当使用 NFSv4.1 时适用）。

   如果要将 Azure NetApp 文件卷和 NFSv4.1 协议一起使用，请在需要装入 Azure NetApp 文件 NFSv4.1 卷的所有 VM 上运行以下配置。

   在此步骤中，需要验证 NFS 域设置。 请确保域配置为默认的 Azure NetApp 文件域 (`defaultv4iddomain.com`)，并且映射设置为 `nobody`。

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
   > 确保在 VM 上的 /etc/idmapd.conf 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置 (`defaultv4iddomain.com`)。 如果不匹配，则装载在 VM 上的 Azure NetApp 文件卷的文件权限将显示为 `nobody`。

   验证 `nfs4_disable_idmapping`。 它应设置为 `Y`。 若要创建 `nfs4_disable_idmapping` 所在的目录结构，请运行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。

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

3. **[A]** 添加装载条目。

   如果使用 NFSv3：

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=3" >> /etc/fstab
   ```

   如果使用 NFSv4.1：

   ```bash
   sudo echo "10.31.2.4:/azusbobi-frsinput /usr/sap/frsinput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   sudo echo "10.31.2.4:/azusbobi-frsoutput /usr/sap/frsoutput  nfs   rw,hard,rsize=65536,wsize=65536,vers=4.1,sec=sys" >> /etc/fstab
   ```

4. **[A]** 装载 NFS 卷。

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

## <a name="configure-azure-database-for-mysql"></a>配置 Azure Database for MySQL

本部分提供有关如何使用 Azure 门户预配 Azure Database for MySQL 的详细信息。 它还提供了有关如何为 SAP BOBI 平台创建 CMS 和审核数据库的说明，以及如何创建用于访问数据库的用户帐户。

仅当使用 Azure Database for MySQL 时，这些准则才适用。 关于其他数据库，请参阅 SAP 或数据库特定的文档以了解相关说明。

### <a name="create-a-database"></a>创建数据库

登录到 Azure 门户，并按照[快速入门：使用 Azure 门户创建 Azure Database for MySQL 服务器](../../../mysql/quickstart-create-mysql-server-database-using-azure-portal.md)中的步骤执行操作。 下面是预配 Azure Database for MySQL 时的几点注意事项：

- 为 Azure Database for MySQL 选择 SAP BI 平台应用程序服务器运行于的区域。

- 根据 [SAP BI 产品可用性矩阵 (PAM)](https://support.sap.com/pam) 选择特定于你的 SAP BOBI 版本的受支持的数据库版本。

- 在“计算 + 存储”中，选择“配置服务器”，并根据调整大小的输出选择适当的定价层。 

- 默认启用存储自动增长。 请记住，[存储](../../../mysql/concepts-pricing-tiers.md#storage)只能扩展，而不能缩减。

- 默认情况下，“备份保留期”为七天。 可以[选择配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)为长达 35 天。

- 默认情况下，在本地冗余备份 Azure Database for MySQL。 如果要在异地冗余存储中进行服务器备份，请从“备份冗余选项”中选择“异地冗余”。

>[!Important]
>不支持在创建服务器后更改[备份冗余选项](../../../mysql/concepts-backup.md#backup-redundancy-options)。

>[!Note]
>专用链接功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保数据库服务器位于其中一个定价层中。

### <a name="configure-azure-private-link"></a>配置 Azure 专用链接

在本节中，你将创建一个专用链接，该链接允许 SAP BOBI 虚拟机通过专用终结点连接到 Azure Database for MySQL。 Azure 专用链接可将 Azure 服务引入到你的专用虚拟网络中。

1. 选择在上一部分创建的数据库。
2. 转到“安全” > “专用终结点连接”。
3. 在“专用终结点连接”中，选择“专用终结点”。
4. 选择“订阅” > “资源组” > “位置”  。
5. 输入专用终结点的名称。
6. 在“资源”部分中，指定以下内容：
   - 资源类型：Microsoft.DBforMySQL/服务器
   - 资源：在上一部分创建的 MySQL 数据库
   - 目标子资源：mysqlServer
7. 在“网络”部分中，选择将部署 SAP BOBI 应用程序的虚拟网络和子网。
   >[!NOTE]
   >如果已为子网启用网络安全组 (NSG)，则系统将只对该子网上的专用终结点禁用 NSG。 子网中的其他资源仍将强制使用 NSG。
8. 对于“与专用 DNS 区域集成”，接受“默认值(是)” 。
9.  从下拉列表中选择“专用 DNS 区域”。
10. 选择“查看 + 创建”并创建专用终结点。

有关详细信息，请参阅 [Azure Database for MySQL 的专用链接](../../../mysql/concepts-data-access-security-private-link.md)。

### <a name="create-the-cms-and-audit-databases"></a>创建 CMS 和审核数据库

1. 从 [MySQL 网站](https://dev.mysql.com/downloads/workbench/)下载并安装 MySQL Workbench。 请确保在可访问 Azure Database for MySQL 的服务器上安装 MySQL Workbench。

2. 使用 MySQL Workbench 连接服务器。 按照[获取连接信息](../../../mysql/connect-workbench.md#get-connection-information)中的说明进行操作。 如果连接测试成功，你会收到以下消息：

   ![MySQL Workbench 消息的屏幕截图。](media/businessobjects-deployment-guide/businessobjects-sql-workbench.png)

3. 在“SQL 查询”选项卡中运行以下查询，为 CMS 和审核数据库创建架构。

   ```sql
   # Here cmsbl1 is the database name of CMS database. You can provide the name you want for CMS database.
   CREATE SCHEMA `cmsbl1` DEFAULT CHARACTER SET utf8;

   # auditbl1 is the database name of Audit database. You can provide the name you want for CMS database.
   CREATE SCHEMA `auditbl1` DEFAULT CHARACTER SET utf8;
   ```

4. 创建用于连接到架构的用户帐户。

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

5. 若要查看 MySQL 用户帐户的特权和角色，请执行以下操作：

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

### <a name="install-mysql-c-api-connector-on-a-linux-server"></a>在 Linux 服务器上安装 MySQL C API 连接器

要使 SAP BOBI 应用程序服务器能够访问数据库，需要数据库客户端驱动程序。 若要访问 CMS 和审核数据库，必须使用适用于 Linux 的 MySQL C API 连接器。 不支持与 CMS 数据库的 ODBC 连接。 本部分提供有关如何在 Linux 上设置 MySQL C API 连接器的说明。

1. 请参阅[与 Azure Database for MySQL 兼容的 MySQL 驱动程序和管理工具](../../../mysql/concepts-compatibility.md)。 请参阅文章中的“MySQL 连接器/C (libmysqlclient)”驱动程序。

2. 若要下载驱动程序，请参阅 [MySQL 产品存档](https://downloads.mysql.com/archives/c-c/)。

3. 选择操作系统并下载 MySQL 连接器的共享组件 rpm 包。 在此示例中，使用了 mysql-connector-c-shared-6.1.11 连接器版本。

4. 在所有 SAP BOBI 应用程序实例中安装连接器。

   ```bash
   # Install rpm package
   SLES: sudo zypper install <package>.rpm
   RHEL: sudo yum install <package>.rpm
   ```

5. 检查 libmysqlclient.so 的路径。

   ```bash
   # Find the location of libmysqlclient.so file
   whereis libmysqlclient

   # sample output
   libmysqlclient: /usr/lib64/libmysqlclient.so
   ```

6. 将 `LD_LIBRARY_PATH` 设置为指向将用于安装的用户帐户的 `/usr/lib64` 目录。

   ```bash
   # This configuration is for bash shell. If you are using any other shell for sidadm, kindly set environment variable accordingly.
   vi /home/bl1adm/.bashrc

   export LD_LIBRARY_PATH=/usr/lib64
   ```

## <a name="server-preparation"></a>服务器准备

本部分中的步骤使用以下前缀：

**[A]** ：该步骤适用于所有主机。

1. **[A]** 根据 LINUX 的风格 (SLES 或 RHEL)，需要设置内核参数并安装所需的库。 请参阅[适用于 Unix 的商业智能平台安装指南](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3)中的“系统要求”部分。

2. **[A]** 确保计算机上的时区设置正确。 请参阅安装指南中的[其他 Unix 和 Linux 要求](https://help.sap.com/viewer/65018c09dbe04052b082e6fc4ab60030/4.3/46b143336e041014910aba7db0e91070.html)。

3. **[A]**  创建软件的后台进程在其下运行的用户帐户 (bl1adm) 和组 (sapsys)。 使用此帐户运行安装及软件。 此帐户不需要 root 权限。

4. **[A]** 设置用户帐户 (bl1adm) 环境以使用支持的 UTF-8 区域设置，并确保控制台软件支持 UTF-8 字符集。 若要确保操作系统使用正确的区域设置，请在 (bl1adm) 用户环境中将 `LC_ALL` 和 `LANG` 环境变量设置为你的首选区域设置。

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

检查服务器上用户帐户 bl1adm 的区域设置：

```bash
bl1adm@azusbosl1:~> locale
LANG=en_US.utf8
LC_ALL=en_US.utf8
```

转到 SAP BOBI 平台的媒体并使用 bl1adm 用户运行以下命令：

```bash
./setup.sh -InstallDir /usr/sap/BL1
```

遵循特定于你的版本的适用于 Unix 的 [SAP BOBI 平台](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM)安装指南。 安装 SAP BOBI 平台时，请注意以下几点：

- 在“配置产品注册”上，你可以使用 SAP 说明 [1288121](https://launchpad.support.sap.com/#/notes/1288121) 中的 SAP BusinessObjects 解决方案临时许可密钥，也可以在 SAP 服务市场中生成许可密钥。

- 在“选择安装类型”上，在第一台服务器 (`azusbosl1`) 上选择“完全”安装 。 对于其他服务器 (`azusbosl2`)，选择“自定义/扩展”，这将扩展现有 BOBI 设置。

- 在“选择默认或现有数据库”上，选择“配置现有数据库”，这将提示你选择 CMS 和审核数据库。  为这些数据库类型选择“MySQL”。

  如果不想在安装期间配置审核，还可以选择“无审核数据库”。

- 在“选择 Java Web 应用程序服务器”屏幕上，根据你的 SAP BOBI 体系结构选择适当的选项。 在此示例中，我们选择了选项 1，它在同一 SAP BOBI 平台上安装 tomcat 服务器。

- 在“配置 CMS 存储库数据库 - MySQL”中输入 CMS 数据库信息。 以下示例显示了用于 Linux 安装的 CMS 数据库信息的输入。 Azure Database for MySQL 用于默认端口 3306。
  
  ![显示 Linux 上的 SAP BOBI 部署 - CMS 数据库的屏幕截图。](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cms.png)

- （可选）在“配置审核存储库数据库 - MySQL”中输入审核数据库信息。 以下示例显示了用于 Linux 安装的审核数据库信息的输入。

  ![显示 Linux 上的 SAP BOBI 部署 - 审核数据库的屏幕截图。](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-audit.png)

- 按照说明，提供所需的输入来完成安装。

对于多实例部署，请在第二个主机 (`azusbosl2`) 上运行安装程序。 对于“选择安装类型”，选择“自定义/扩展”，这将扩展现有 BOBI 设置。 

在 Azure Database for MySQL 中，网关可重定向与服务器实例的连接。 建立连接后，MySQL 客户端显示网关中设置的 MySQL 版本，而不是 MySQL 服务器实例上运行的实际版本。 若要确定 MySQL 服务器实例的版本，可在 MySQL 提示符处使用 `SELECT VERSION();` 命令。 有关详细信息，请参阅[支持的 Azure Database for MySQL 服务器版本](../../../mysql/concepts-supported-versions.md)。

![显示 Linux 上的 SAP BOBI 部署 - CMC 设置的屏幕截图。](media/businessobjects-deployment-guide/businessobjects-deployment-linux-sql-cmc.png)

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

SAP BOBI 平台的多实例安装完成后，需要执行额外的后配置步骤来支持应用程序的高可用性。

### <a name="configure-the-cluster-name"></a>配置群集名称

在 SAP BOBI 平台的多实例部署中，你想要在群集中同时运行多个 CMS 服务器。 一个群集由两个或更多 CMS 服务器组成，这些服务器在一个通用 CMS 系统数据库上协同工作。 如果在 CMS 上运行的某个节点出现故障，则另一个 CMS 上的节点将继续为 BI 平台请求提供服务。 默认情况下，在 SAP BOBI 平台中，群集名称反映的是你安装的第一个 CMS 的主机名。

若要在 Linux 上配置群集名称，请按照 [SAP 商业智能平台管理员指南](https://help.sap.com/viewer/2e167338c1b24da9b2a94e68efd79c42/4.3)中的说明进行操作。 配置群集名称后，请按照 SAP 说明 [1660440](https://launchpad.support.sap.com/#/notes/1660440) 在 CMC 或 BI 启动台登录页上设置默认的系统入口。

### <a name="configure-input-and-output-filestore-location-to-azure-netapp-files"></a>将输入和输出文件存储位置配置到 Azure NetApp 文件

文件存储是指实际的 SAP BusinessObjects 文件所在的磁盘目录。 SAP BOBI 平台的文件存储库服务器的默认位置位于本地安装目录中。 在多实例部署中，必须在共享存储（例如 Azure NetApp 文件）上设置文件存储。 这样便可以从所有存储层服务器访问文件存储。

1. 如果尚未创建 NFS 卷，请在 Azure NetApp 文件中创建它们。 （请按照前面“预配 Azure NetApp 文件”一节中的说明进行操作。）

2. 装载 NFS 卷。 （请按照前面“装载 Azure NetApp 文件卷”一节中的说明进行操作。）

3. 按照 SAP 说明 [2512660](https://launchpad.support.sap.com/#/notes/0002512660) 更改文件存储库的路径（输入和输出）。

### <a name="session-replication-in-tomcat-clustering"></a>Tomcat 群集中的会话复制

Tomcat 支持将两个或更多应用程序服务器组成群集，用于会话复制和故障转移。 SAP BOBI 平台会话将进行序列化，这样即使应用程序服务器发生故障，用户会话也可以无缝地故障转移到另一个 Tomcat 实例。

例如，假设用户连接到的 web 服务器在用户在 SAP BI 应用程序中导航文件夹层次结构时失败。 通过正确配置的群集，用户可以继续导航文件夹层次结构，而无需重定向到登录页。

请参阅 SAP 说明 [2808640](https://launchpad.support.sap.com/#/notes/2808640)，使用多播了解配置 Tomcat 群集的步骤。 但请注意，Azure 不支持多播。 因此，若要使 Tomcat 群集在 Azure 中可用，必须使用 [StaticMembershipInterceptor](https://tomcat.apache.org/tomcat-8.0-doc/config/cluster-interceptor.html#Static_Membership)（SAP 说明 [2764907](https://launchpad.support.sap.com/#/notes/2764907)）。 有关详细信息，请参阅博客文章[使用 SAP BUSINESSOBJECTS BI 平台的静态成员身份进行 Tomcat 群集操作](https://blogs.sap.com/2020/09/04/sap-on-azure-tomcat-clustering-using-static-membership-for-sap-businessobjects-bi-platform/)。

### <a name="load-balancing-web-tier-of-sap-bi-platform"></a>SAP BI 平台的负载均衡 web 层

在 SAP BOBI 多实例部署中，Java Web 应用程序服务器（web 层）在两个或更多主机上运行。 若要在 Web 服务器之间均匀分配用户负载，可以在最终用户与 Web 服务器之间使用负载均衡器。 在 Azure 中，可以使用 Azure 负载均衡器或 Azure 应用程序网关来管理 web 应用程序服务器的流量。 下一节将介绍有关每个产品的详细信息。

#### <a name="azure-load-balancer"></a>Azure 负载均衡器

[Azure 负载均衡器](../../../load-balancer/load-balancer-overview.md)是高性能、低延迟的第 4 层（TCP、UDP）负载均衡器。 它可在正常运行的虚拟机 (VM) 之间分配流量。 负载均衡器的运行状况探测监视每个 VM 上的指定端口，并仅将流量分配给可操作的 VM。 可以根据是否允许从 Internet 访问 SAP BI 平台，选择公共负载均衡器或内部负载均衡器。 它是区域冗余的，确保跨可用性区域的高可用性。

在以下示意图中，请参阅“内部负载均衡器”部分。 Web 应用程序服务器在端口 8080（默认 Tomcat HTTP 端口）上运行，该端口将由运行状况探测进行监视。 来自最终用户的任何传入请求都将重定向到 Web 应用程序服务器（`azusbosl1` 或 `azusbosl2`）。 负载均衡器不支持 TLS/SSL 终止（也称为 TLS/SSL 卸载）。 如果使用负载均衡器将流量分配到 Web 服务器，请使用标准负载均衡器。

> [!NOTE]
> 如果没有公共 IP 地址的 VM 放在内部（无公共 IP 地址）标准负载均衡器的池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](high-availability-guide-standard-load-balancer-outbound-connections.md)。

![显示 Azure 负载均衡器均衡 Web 服务器流量的示意图。](media/businessobjects-deployment-guide/businessobjects-deployment-load-balancer.png)

#### <a name="azure-application-gateway"></a>Azure 应用程序网关

[Azure 应用程序网关](../../../application-gateway/overview.md)以服务形式提供应用程序传送控制器 (ADC)。 此服务用于帮助应用程序将用户流量重定向到一个或多个 Web 应用程序服务器。 它提供各种第 7 层负载均衡功能，例如 TLS/SSL 卸载、Web 应用程序防火墙 (WAF) 和基于 Cookie 的会话相关性。

在 SAP BI 平台中，应用程序网关将应用程序 Web 流量定向到指定资源 `azusbosl1` 或 `azusbos2`。 你将向端口分配侦听器，创建规则，并向池中添加资源。 在以下示意图中，应用程序网关提供一个专用 IP 地址 (10.31.3.20)，它充当用户的入口点。 它还处理传入 TLS/SSL (HTTPS - TCP/443) 连接，解密 TLS/SSL，并将未加密的请求 (HTTP-TCP/8080) 传递给服务器。 它简化了操作，只需在应用程序网关上维护一个 TLS/SSL 证书。

![显示应用程序网关均衡 Web 服务器流量的示意图。](media/businessobjects-deployment-guide/businessobjects-deployment-application-gateway.png)

若要为 SAP BOBI Web 服务器配置应用程序网关，请参阅博客文章[使用 Azure 应用程序网关实现 SAP BOBI Web 服务器的负载均衡](https://blogs.sap.com/2020/09/17/sap-on-azure-load-balancing-web-application-servers-for-sap-bobi-using-azure-application-gateway/)。

> [!NOTE]
> Azure 应用程序网关更适合对 Web 服务器的流量进行负载均衡。 它提供各种有用功能，例如 SSL 卸载、用于减少服务器加密和解密开销的集中式 SSL 管理、分配流量的循环算法、WAF 功能和高可用性。

## <a name="sap-bobi-platform-reliability-on-azure"></a>Azure 上的 SAP BOBI 平台可靠性

SAP BOBI 平台包含不同的层，这些层针对特定任务和操作进行了优化。 当任何一层中的组件变为不可用时，SAP BOBI 应用程序将变得不可访问，或者某些功能将受限。 请确保每个层的可靠性，使应用程序可以正常运行而不会出现任何业务中断。

本指南将探讨应如何将 Azure 的原生功能与 SAP BOBI 平台配置相结合，以改进 SAP 部署的可用性。 本部分重点介绍以下选项：

- **备份和还原：** 将数据和应用程序的副本定期创建到单独位置的过程。 如果原始数据或应用程序丢失或损坏，可将其还原或恢复到以前的状态。

- **高可用性：** 高可用平台的所有组件在 Azure 区域中都至少有两个实例，这样，即使其中一个服务器不可用，也能使应用程序保持正常运行。
- **灾难恢复：** 这是在出现灾难性损失（例如由于某些自然灾害而导致整个 Azure 区域不可用）时还原应用程序功能的过程。

此解决方案的实现因 Azure 中系统设置的性质而异。 根据业务要求，为你定制备份/还原、高可用性和灾难恢复解决方案。

## <a name="backup-and-restore"></a>备份和还原

备份和还原是任何业务灾难恢复策略的基本组成部分。 若要为 SAP BOBI 平台开发全面的策略，请确定导致应用程序系统停机或中断的组件。 在 SAP BOBI 平台中，以下组件的备份对保护应用程序至关重要：

- SAP BOBI 安装目录（托管高级磁盘）
- 文件存储库服务器（Azure NetApp 文件或 Azure 高级文件）
- CMS 数据库（Azure database for MySQL 或 Azure 虚拟机上的数据库）

以下部分介绍如何为每个组件实现备份和还原策略。

### <a name="backup-and-restore-for-sap-bobi-installation-directory"></a>SAP BOBI 安装目录的备份和还原

在 Azure 中，备份应用程序服务器和所有附加磁盘的最简单方法是使用 [Azure 备份](../../../backup/backup-overview.md)。 它提供独立且隔离的备份来防止 VM 上的数据被意外破坏。 备份存储在提供恢复点内置管理的恢复服务保管库中。 配置和缩放很简单，备份经过优化，可以轻松地根据需要还原。

在备份过程中，将创建快照，并将数据传输到保管库，而不影响生产工作负载。 有关详细信息，请参阅[快照一致性](../../../backup/backup-azure-vms-introduction.md#snapshot-consistency)。 还可使用选择性磁盘备份和还原功能，选择备份 VM 中的一部分数据磁盘。 有关详细信息，请参阅 [Azure VM 备份](../../../backup/backup-azure-vms-introduction.md)和[常见问题解答 - 备份 Azure VM](../../../backup/backup-azure-vm-backup-faq.yml)。

### <a name="backup-and-restore-for-file-repository-server"></a>文件存储库服务器的备份和还原

根据 Linux 上的 SAP BOBI 部署，可以将 Azure NetApp 文件用作 SAP BOBI 平台的文件存储。 根据用于文件存储的存储点，从以下选项中选择备份和还原的方式。

- **Azure NetApp 文件：** 可以创建按需快照，并使用快照策略安排自动拍摄快照。 快照副本提供卷的时间点副本。 有关详细信息，请参阅[使用 Azure NetApp 文件管理快照](../../../azure-netapp-files/azure-netapp-files-manage-snapshots.md)。

- 如果已创建单独的 NFS 服务器，请确保为该服务器实现备份和还原策略。

### <a name="backup-and-restore-for-cms-and-audit-databases"></a>适用于 CMS 和审核数据库的备份和还原

在 Linux VM 上，CMS 和审核数据库可以在任何受支持的数据库上运行。 有关详细信息，请参阅[支持矩阵](businessobjects-deployment-guide.md#support-matrix)。 请务必根据用于 CMS 和审核数据存储的数据库来采用备份和还原策略。

- Azure Database for MySQL 可自动创建服务器备份并将其存储在用户配置的本地冗余或异地冗余存储中。 Azure Database for MySQL 会创建数据文件和事务日志的备份。 根据支持的最大存储大小，将进行完整备份和差异备份（最大 4 TB 的存储服务器）或快照备份（最大 16 TB 的存储服务器）。 可以通过这些备份将服务器还原到所配置的备份保留期中的任意时间点。 默认备份保持期为 7 天，可以[选择配置](../../../mysql/howto-restore-server-portal.md#set-backup-configuration)为 3 天。 所有备份都使用 AES 256 位加密进行加密。 这些备份文件不公开给用户，因此无法导出。 这些备份只能用于 Azure Database for MySQL 中的还原操作。 可以使用 [mysqldump](../../../mysql/concepts-migrate-dump-restore.md) 复制数据库。 有关详细信息，请参阅[在 Azure Database for MySQL 中进行备份和还原](../../../mysql/concepts-backup.md)。

- 对于在 Azure 虚拟机上安装的数据库，可以为支持的数据库使用标准备份工具或 [Azure 备份](../../../backup/sap-hana-db-about.md)。 此外，还可以使用支持的第三方备份工具，为所有 SAP BOBI 平台组件的备份和还原提供代理。

## <a name="high-availability"></a>高可用性

*高可用性* 是一组技术，这些技术通过提供应用程序和服务的业务连续性来最大程度减少 IT 中断。 它通过同一数据中心内的冗余、容错或故障转移保护组件来实现。 在本例中，数据中心位于一个 Azure 区域内。 有关详细信息，请参阅[适用于 SAP 的高可用性体系结构和方案](sap-high-availability-architecture-scenarios.md)。

根据 SAP BOBI 平台的大小调整结果，你需要设计格局，并确定跨 Azure 虚拟机和子网的 BI 组件分布。 分布式体系结构中的冗余级别取决于业务所需的恢复时间目标 (RTO) 和恢复点目标 (RPO)。 SAP BOBI 平台包含不同的层，应对每个层上的组件进行设计以实现冗余。 例如：

- 冗余应用程序服务器，如 BI 应用程序服务器和 Web 服务器。
- CMS 数据库、文件存储库服务器、负载均衡器等独特组件。

下一部分介绍如何在 SAP BOBI 平台的每个组件上实现高可用性。

### <a name="high-availability-for-application-servers"></a>应用程序服务器的高可用性

可以通过冗余来实现应用程序服务器的高可用性。 为此，请在各种 Azure VM 中配置多个 BI 和 Web 服务器实例。

若要降低一个或多个事件导致停机的影响，建议：

- 使用可用性区域防范数据中心故障。
- 在可用性集中配置多个 VM 以确保冗余。
- 为可用性集中的 VM 使用托管磁盘。
- 将每个应用程序层配置到不同的可用性集中。

有关详细信息，请参阅[管理 Linux 虚拟机的可用性](../../availability.md)。

>[!Important]
>Azure 可用性区域和 Azure 可用性集的概念是互斥的。 可以将一对或多个 VM 部署到特定的可用性区域或可用性集中，但是不可以同时部署到两者中。

### <a name="high-availability-for-a-cms-database"></a>CMS 数据库的高可用性

如果将 Azure Database for MySQL 用于 CMS 和审核数据库，则本地默认拥有冗余的高可用性框架。 只需选择区域和服务固有的高可用性、冗余和复原能力，无需配置任何其他组件。 如果 SAP BOBI 平台的部署策略跨可用性区域，则需要确保为 CMS 和审核数据库实现区域冗余。 有关详细信息，请参阅 [Azure Database for MySQL 中的高可用性](../../../mysql/concepts-high-availability.md)和 [Azure SQL 数据库的高可用性](../../../azure-sql/database/high-availability-sla.md)。

有关 CMS 数据库的其他部署，请参阅 [SAP 工作负载的 DBMS 部署指南](dbms_guide_general.md)中的高可用性信息。

### <a name="high-availability-for-filestore"></a>文件存储的高可用性

文件存储指存储报表、universe 和连接等内容的磁盘目录。 它在该系统的所有应用程序服务器之间共享。 因此，必须确保它与其他 SAP BOBI 平台组件都具有高可用性。

对于在 Linux 上运行的 SAP BOBI 平台，可以选择适用于文件分享的[ Azure 高级文件](../../../storage/files/storage-files-introduction.md)或 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)，这些经专门设计的文件本质具有高可用性和高耐用性。 有关详细信息，请参阅 Azure 文件存储的[冗余](../../../storage/files/storage-files-planning.md#redundancy)。

> [!Important]
> Azure 文件存储的 SMB 协议已公开发布，但针对 Azure 文件存储的 NFS 协议支持目前为预览版。 有关详细信息，请参阅[对 Azure 文件存储的 NFS 4.1 支持现在为预览版](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)。

请注意，此文件共享服务并非在所有区域都可用。 请参阅[各区域的产品可用性](https://azure.microsoft.com/global-infrastructure/services/)查找最新信息。 如果该服务在你的区域中不可用，可以创建一个 NFS 服务器，并从中将文件系统共享给 SAP BOBI 应用程序。 但你还需要考虑其高可用性。

### <a name="high-availability-for-load-balancer"></a>负载均衡器的高可用性

若要在 Web 服务器之间分配流量，可以使用 Azure 负载均衡器或 Azure 应用程序网关。 可根据你为部署选择的 SKU 来实现任一这些服务的冗余。

- 对于 Azure 负载均衡器，可以通过将标准负载均衡器配置为区域冗余来实现冗余。 有关详细信息，请参阅[标准负载均衡器和可用性区域](../../../load-balancer/load-balancer-standard-availability-zones.md)。

- 对于应用程序网关，可以根据部署过程中选择的层类型来实现高可用性。
   -  如果部署了两个或更多实例，v1 SKU 支持高可用性方案。 Azure 跨更新域和容错域分配这些实例，确保实例不会全部同时发生故障。 实现区域内冗余。
   -  v2 SKU 可以自动确保新实例分布到各个容错域和更新域中。 如果选择“区域冗余”，则最新实例还将分布到各个可用性区域中以提供区域性故障复原能力。 有关详细信息，请参阅[自动缩放和区域冗余应用程序网关 v2](../../../application-gateway/application-gateway-autoscaling-zone-redundant.md)。

### <a name="reference-high-availability-architecture-for-sap-bobi-platform"></a>SAP BOBI BI 平台的参考高可用性体系结构

以下示意图显示了在使用 Linux 服务器上运行的可用性集时 SAP BOBI 平台的设置。 该体系结构显示了所使用的各种不同服务，例如 Azure 应用程序网关、Azure NetApp 文件以及 Azure Database for MySQL。 这些服务提供内置冗余，可降低管理不同高可用性解决方案的复杂性。

请注意，使用 Azure 应用程序网关 v1 SKU 对传入流量 (HTTPS - TCP/443) 进行了负载均衡，此 SKU 在两个或更多实例上部署时高度可用。 Web 服务器、管理服务器和处理服务器的多个实例部署在单独的 VM 中以实现冗余，并且每个层部署在不同的可用性集中。 Azure NetApp 文件在数据中心内有内置冗余，因此文件存储库服务器的 Azure NetApp 文件卷将高度可用。 CMS 数据库预配于 Azure Database for MySQL 上，该服务具有固有的高可用性。 有关详细信息，请参阅 [Azure Database for MySQL 中的高可用性](../../../mysql/concepts-high-availability.md)。

![显示使用可用性集的 SAP BusinessObjects BI 平台冗余的示意图。](media/businessobjects-deployment-guide/businessobjects-deployment-high-availability.png)

上述体系结构提供了有关如何在 Azure 上部署 SAP BOBI 的见解。 但它并未涵盖所有可能的配置选项。 可以根据业务要求定制部署。

可以在多个 Azure 区域中使用可用性区域。 这意味着可以利用独立的电源、冷却和网络供应。 这样便可以跨两个或三个可用性区域部署应用程序。 如果希望跨可用性区域实现高可用性，可以跨这些区域部署 SAP BOBI 平台，从而确保应用程序中的每个组件都是区域冗余的。

## <a name="disaster-recovery"></a>灾难恢复

本节将介绍为在 Linux 上运行的 SAP BOBI 平台提供灾难恢复保护的策略。 它补充了 [SAP 的灾难恢复](../../../site-recovery/site-recovery-sap.md)文档，该文档代表总体 SAP 灾难恢复方法的主要资源。 有关 SAP BOBI，请参阅 SAP 说明 [2056228](https://launchpad.support.sap.com/#/notes/2056228)，其中描述了以下方法来安全地实现灾难恢复环境。

- 完整或选择性使用生命周期管理或联合，以提升或分配来自主系统的内容。
- 定期复制 CMS 和文件存储库服务器内容。

本指南重点介绍第二个选项。 其中不包含所有可能的灾难恢复配置选项，但涵盖与 SAP BOBI 平台配置结合使用原生 Azure 服务的解决方案。

>[!Important]
>在 SAP BOBI 平台中，每个组件的可用性都应在次要区域中纳入考虑范畴，且必须全面测试整个灾难恢复策略。

### <a name="reference-disaster-recovery-architecture-for-sap-bobi-platform"></a>SAP BOBI 平台的参考灾难恢复体系结构

此参考体系结构通过冗余的应用程序服务器运行 SAP BOBI 平台的多实例部署。 对于灾难恢复，应将 SAP BOBI 平台的所有组件故障转移到次要区域。 在以下示意图中，Azure NetApp 文件用作文件存储，Azure Database for MySQL 用作 CMS 和审核存储库，Azure 应用程序网关用作负载均衡器。 为每个组件实现灾难恢复保护的策略各不相同，下面各节将介绍这些不同之处。

![显示 SAP BusinessObjects BI 平台灾难恢复的示意图。](media/businessobjects-deployment-guide/businessobjects-deployment-disaster-recovery.png)

### <a name="load-balancer"></a>负载均衡器

负载均衡器用于在 SAP BOBI 平台的 Web 应用程序服务器之间分配流量。 在 Azure 中，可以使用 Azure 负载均衡器或 Azure 应用程序网关来实现这一目的。 若要为负载均衡器服务实现灾难恢复，需要在次要区域中实现另一个 Azure 负载均衡器或 Azure 应用程序网关。 若要在完成灾难恢复故障转移后保留相同的 URL，需要更改 DNS 中的条目，并指向在次要区域中运行的负载均衡服务。

### <a name="vms-running-web-and-bi-application-servers"></a>运行 Web 和 BI 应用程序服务器的 VM

使用 [Azure Site Recovery](../../../site-recovery/site-recovery-overview.md) 在次要区域中复制运行 Web 和 BI 应用程序服务器的 VM。 它会将服务器及其中附加的所有托管磁盘复制到次要区域，以便在发生灾难和停机时，你可以轻松地将故障转移到复制的环境中，并继续工作。 若要开始将所有 SAP 应用程序 VM 复制到 Azure 灾难恢复数据中心，请按照[将虚拟机复制到 Azure](../../../site-recovery/azure-to-azure-tutorial-enable-replication.md) 中的指导进行操作。

### <a name="file-repository-servers"></a>文件存储库服务器

文件存储指磁盘目录，其中存储了报表和 BI 文档等实际文件。 文件存储中的所有文件都必须同步到灾难恢复区域，这一点很重要。 根据在 Linux 上运行的 SAP BOBI 平台所使用的文件共享服务类型，需要采用相应的灾难恢复策略来同步内容。

- **Azure NetApp 文件** 提供 NFS 和 SMB 卷，因此可使用任何基于文件的复制工具在 Azure 区域之间复制数据。 有关如何在另一个区域中复制卷的详细信息，请参阅[关于 Azure NetApp 文件的常见问题解答](../../../azure-netapp-files/azure-netapp-files-faqs.md#how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region)。

  可以使用 Azure NetApp 文件跨区域复制，该功能当前提供[预览版](https://azure.microsoft.com/blog/azure-netapp-files-cross-region-replication-and-new-enhancements-in-preview/)。 只会以经压缩的高效格式通过网络发送更改后的块。 这最大程度地减少了跨区域复制所需的数据量，从而节省了数据传输成本。 它还可以缩短复制时间，便于实现较小的 RPO。 有关详细信息，请参阅[使用跨区域复制的要求和注意事项](../../../azure-netapp-files/cross-region-replication-requirements-considerations.md)。

- **Azure 高级文件** 仅支持本地冗余 (LRS) 和区域冗余存储 (ZRS)。 对于灾难恢复策略，可以使用 [AzCopy](../../../storage/common/storage-use-azcopy-v10.md) 或 [Azure PowerShell](/powershell/module/az.storage/) 将文件复制到不同区域中的其他存储帐户。 有关详细信息，请参阅[灾难恢复和存储帐户故障转移](../../../storage/common/storage-disaster-recovery-guidance.md)。

   > [!Important]
   > Azure 文件存储的 SMB 协议已公开发布，但针对 Azure 文件存储的 NFS 协议支持目前为预览版。 有关详细信息，请参阅[对 Azure 文件存储的 NFS 4.1 支持现在为预览版](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/)。

### <a name="cms-database"></a>CMS 数据库

灾难恢复区域中的 CMS 和审核数据库必须是在主要区域中运行的数据库的副本。 根据数据库类型，请务必按照业务要求的 RTO 和 RPO 将数据库复制到灾难恢复区域。

#### <a name="azure-database-for-mysql"></a>Azure Database for MySQL

Azure Database for MySQL 提供了多个可在发生灾难时恢复数据库的选项。 选择适用于你的业务的适当选项。

- 可以使用跨区域只读副本来增强业务连续性和灾难恢复计划。 可将源服务器中的数据复制到最多 5 个副本。 只读副本使用 MySQL 的二进制日志复制技术进行异步更新。 副本是新的服务器，可以像管理 Azure Database for MySQL 中的普通服务器一样对其进行管理。 有关详细信息，请参阅 [Azure Database for MySQL 中的只读副本](../../../mysql/concepts-read-replicas.md)。

- 使用异地还原功能，通过异地冗余备份来还原服务器。 即使你的服务器所在的区域离线，也可访问这些备份。 可以使用这些备份还原到任何其他区域并使服务器恢复联机。

  > [!NOTE]
  > 只有当为服务器预配了异地冗余备份存储时，异地还原才是可行的。 不支持在创建服务器后更改“备份冗余选项”。 有关详细信息，请参阅[备份冗余](../../../mysql/concepts-backup.md#backup-redundancy-options)。

下表显示了此示例中使用的每个层的灾难恢复建议。

| SAP BOBI 平台层   | 建议                                                                                           |
|---------------------------|----------------------------------------------------------------------------------------------------------|
| Azure 应用程序网关 | 次要区域上应用程序网关的并行安装。                                             |
| Web 应用程序服务器   | 使用 Azure Site Recovery 进行复制。                                                                  |
| BI 应用程序服务器    | 使用 Site Recovery 进行复制。                                                                        |
| Azure NetApp 文件        | 基于文件的复制工具将数据复制到次要区域或使用跨区域复制。      |
| Azure Database for MySQL  | 跨区域只读副本或从异地冗余备份还原备份。                                |

## <a name="next-steps"></a>后续步骤

- [为多层 SAP 应用部署设置灾难恢复](../../../site-recovery/site-recovery-sap.md)
- [适用于 SAP 的 Azure 虚拟机规划和实施](planning-guide.md)
- [适用于 SAP 的 Azure 虚拟机部署](deployment-guide.md)
- [适用于 SAP 的 Azure 虚拟机 DBMS 部署](./dbms_guide_general.md)
