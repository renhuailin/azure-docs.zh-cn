---
title: 为虚拟机装载 Azure NetApp 文件卷
description: 了解如何为 Azure 中的 Windows 虚拟机或 Linux 虚拟机装载或卸载卷。
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 83d6e051f520737e750e6c46c192eb698e7bf0e3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "94842251"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>为 Windows 或 Linux 虚拟机装载或卸载卷 

可以根据需要为 Windows 或 Linux 虚拟机装载或卸载卷。  Azure NetApp 文件上提供了 Linux 虚拟机的装载说明。  

## <a name="requirements"></a>要求 

* 必须至少有一个导出策略才能访问 NFS 卷。
* 要成功装载 NFS 卷，请确保客户端和 NFS 卷之间的以下 NFS 端口打开：
    * 111 TCP/UDP = `RPCBIND/Portmapper`
    * 635 TCP/UDP = `mountd`
    * 2049 TCP/UDP = `nfs`
    * 4045 TCP/UDP = `nlockmgr`（仅限 NFSv3）
    * 4046 TCP/UDP = `status`（仅限 NFSv3）

## <a name="steps"></a>步骤

1. 单击“卷”边栏选项卡，然后选择要装载的卷。 
2. 单击所选卷中的“装载说明”，然后按照说明装载卷。 

    ![装载说明 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![装载说明 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)  
    * 如果要装载 NFS 卷，请确保使用 `mount` 命令中的 `vers` 选项来指定与要装载的卷相对应的 NFS 协议版本。 
    * 如果使用的是 NFSv4.1，请使用以下命令来装载文件系统：`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  
        > [!NOTE]
        > 如果使用 NFSv4.1，请确保装载导出的所有 VM 都使用唯一的主机名。

3. 如果希望在启动或重启 Azure VM 时自动装载 NFS 卷，请将条目添加到主机上的 `/etc/fstab` 文件中。 

    例如：`$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP` 是在“卷属性”边栏选项卡中找到的 Azure NetApp 文件卷的 IP 地址。
    * `$FILEPATH` 是 Azure NetApp 文件卷的导出路径。
    * `$MOUNTPOINT` 是在用于装载 NFS 导出的 Linux 主机上创建的目录。

4. 如果要使用 NFS 将卷装载到 Windows，请执行以下操作：

    a. 首先将卷装载到 Unix 或 Linux VM。  
    b. 对该卷运行 `chmod 777` 或 `chmod 775` 命令。  
    c. 在 Windows 上通过 NFS 客户端装载卷。
    
5. 如果要装载 NFS Kerberos 卷，请参阅[配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)以了解更多详细信息。 

## <a name="next-steps"></a>后续步骤

* [为 Azure NetApp 文件配置 NFSv4.1 默认域](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 常见问题解答](./azure-netapp-files-faqs.md#nfs-faqs)
* [网络文件系统概述](/windows-server/storage/nfs/nfs-overview)
* [装载 NFS Kerberos 卷](configure-kerberos-encryption.md#kerberos_mount)
