---
title: 为 Azure NetApp 文件配置 NFS 客户端 | Microsoft Docs
description: 介绍如何配置 NFS 客户端以与 Azure NetApp 文件一起使用。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/22/2021
ms.author: b-juche
ms.openlocfilehash: 2d0e323271cbc465f2f46c4904f01d5c1654426d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576755"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFS 客户端

本文中所述的 NFS 客户端配置属于[配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)或[创建双协议卷](create-volumes-dual-protocol.md)的一部分设置。 有多种 Linux 分发版可与 Azure NetApp 文件一起使用。 本文介绍两种更常用的环境配置：RHEL 8 和 Ubuntu 18.04。 

## <a name="requirements-and-considerations"></a>要求和注意事项  

无论使用何种 Linux 风格，都需要以下配置：

* 配置 NTP 客户端以避免出现时间偏差问题。
* 配置 Linux 客户端的 DNS 条目以进行名称解析。  
    此配置必须包含“A”（正向）记录和 PTR（反向）记录。 
* 对于域加入，请在目标 Active Directory（在领域加入命令过程中创建）中创建 Linux 客户端的计算机帐户。 
    > [!NOTE] 
    > 下面命令中使用的 `$SERVICEACCOUNT` 变量应该是具有在目标组织单位中创建计算机帐户的权限或受委派创建该帐户的用户帐户。

## <a name="rhel-8-configuration"></a>RHEL 8 配置 

本部分介绍 NFSv4.1 Kerberos 加密和双重协议所需的 RHEL 配置。  

本部分中的示例使用以下域名和 IP 地址：  

* 域名：`contoso.com`
* 专用 IP：`10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>RHEL 8 配置（如果使用的是 NFSv4.1 Kerberos 加密）

1. 使用适当的 DNS 服务器配置 `/etc/resolv.conf`。  

    例如：  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. 在 DNS 服务器中为 DNS 正向和反向查找区域添加 NFS 客户端记录。

3. 若要验证 DNS，请从 NFS 客户端运行以下命令：   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. 安装包：   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  配置 NTP 客户端。  

    默认情况下，RHEL 8 使用 chrony。 遵循[使用 `Chrony` 套件配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)中的配置指南。

6.  加入 Active Directory 域：  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    例如： 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    确保将 `default_realm` 设置为 `/etc/krb5.conf` 中提供的领域。  否则，请将其添加到文件中的 `[libdefaults]` 部分下，如下例所示：
    
    ```
    [libdefaults]
        default_realm = CONTOSO.COM
        default_tkt_enctypes = aes256-cts-hmac-sha1-96
        default_tgs_enctypes = aes256-cts-hmac-sha1-96
        permitted_enctypes = aes256-cts-hmac-sha1-96
    [realms]
        CONTOSO.COM = {
            kdc = dc01.contoso.com
            admin_server = dc01.contoso.com
            master_kdc = dc01.contoso.com
            default_domain = contoso.com
        }
    [domain_realm]
        .contoso.com = CONTOSO.COM
        contoso.com = CONTOSO.COM
    [logging]
        kdc = SYSLOG:INFO
        admin_server = FILE=/var/kadm5.log
    ```

7. 重启所有 NFS 服务：  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    重启会阻止在 Kerberos 装载期间出现错误条件 `“mount.nfs: an incorrect mount option was specified”`。

8. 使用用户帐户运行 `kinit` 命令以获取票证： 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    例如：   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>RHEL 8 配置（如果使用的是双重协议）

可选择执行以下步骤。 仅当在 NFS 客户端上使用用户映射时，才需要执行这些步骤： 

1. 完成 [RHEL 8 配置（如果使用的是 NFSv4.1 Kerberos 加密）](#rhel8_nfsv41_kerberos)部分中所述的所有步骤。   

2. 在 /etc/hosts 文件中添加静态 DNS 记录，以对 AD 使用完全限定的域名 (FQDN)，而不是使用 SSSD 配置文件中的 IP 地址：  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. 为域添加额外部分来解析 AD LDAP 服务器中的标识符：    

    `[root@reddoc cbs]# cat /etc/sssd/sssd.conf`   
    `[sssd]`   
    `domains = contoso.com, contoso-ldap (new entry added for LDAP as id_provider)`   
    `config_file_version = 2`   
    `services = nss, pam, ssh, sudo (ensure nss is present in this list)`   
 
    `[domain/contoso-ldap] (Copy the following lines. Modify as per your domain name.)`   
    `auth_provider = krb5`   
    `chpass_provider = krb5`   
    `id_provider = ldap`   
    `ldap_search_base = dc=contoso,dc=com(your domain)`   
    `ldap_schema = rfc2307bis`   
    `ldap_sasl_mech = GSSAPI`   
    `ldap_user_object_class = user`   
    `ldap_group_object_class = group`   
    `ldap_user_home_directory = unixHomeDirectory`   
    `ldap_user_principal = userPrincipalName`   
    `ldap_account_expire_policy = ad`   
    `ldap_force_upper_case_realm = true`   
    `ldap_user_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_group_search_base = cn=Users,dc=contoso,dc=com (based on your domain)`   
    `ldap_sasl_authid = REDDOC$ (ensure $ at the end you can get this from “klist -kte” command)`   
    `krb5_server = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `krb5_realm = CONTOSO.COM (domain name in caps)`   
    `krb5_kpasswd = winad2016.contoso.com (same as AD address which is added in /etc/hosts)`   
    `use_fully_qualified_names = false`   
    
    在上面的 `[domain/contoso-ldap]` 配置中：
    * `id_provider` 设置为 `ldap`，而非 `ad`。
    * 该配置已指定用于搜索的搜索库及用户类和组类。
    * `ldap_sasl_authid` 是 `klist -kte` 中的计算机帐户名称。
    * `use_fully_qualified_names` 设置为 `false`。  此设置表示在使用短名称时使用此配置。
    * `ldap_id_mapping` 未指定，默认为 `false`。

    该 `realm join` 配置由客户端生成，如下所示：
 
    `[domain/contoso.com]  (Do not edit or remove any of the following information. This information is automatically generated during the realm join process.)`   
    `ad_domain = contoso.com`   
    `krb5_realm = CONTOSO.COM`   
    `realmd_tags = manages-system joined-with-adcli`   
    `cache_credentials = True`   
    `id_provider = ad`   
    `krb5_store_password_if_offline = True`   
    `default_shell = /bin/bash`   
    `ldap_id_mapping = True`   
    `use_fully_qualified_names = True`   
    `fallback_homedir = /home/%u@%d`   
    `access_provider = ad`   
    
    在上面的 `[domain/contoso.com]` 配置中：
    * 将 `id_provider` 设置为 `ad`。
    * 将 `ldap_id_mapping` 设置为 `true`。 它使用 SSSD 生成的 ID。 或者，如果要对所有用户名样式使用 POSIX UID，可将此值设置为 `false`。 可根据客户端配置来确定该值。 
    * `use_fully_qualified_names` 为 `true`。 此设置表示 `user@CONTOSO.COM` 将使用此配置。

4. 确保 `/etc/nsswitch.conf` 具有 `sss` 条目：   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. 重启 `sssd` 服务并清除缓存：   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. 测试以确保客户端与 LDAP 服务器集成：   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu 配置   

本部分介绍 NFSv4.1 Kerberos 加密和双重协议所需的 Ubuntu 配置。 

本部分中的示例使用以下域名和 IP 地址：  

* 域名：`contoso.com`
* 专用 IP：`10.6.1.4`

1. 使用适当的 DNS 服务器配置 `/etc/resolv.conf`：

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. 在 DNS 服务器中为 DNS 正向和反向查找区域添加 NFS 客户端记录。
 
    若要验证 DNS，请从 NFS 客户端运行以下命令：

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. 安装包：
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    根据提示，输入 `$DOMAIN.NAME`（使用大写，例如 `CONTOSO.COM`）作为默认的 Kerberos 领域。

4. 重启服务 `rpc-gssd.service`： 

    `sudo systemctl start rpc-gssd.service`

5. 默认情况下，Ubuntu 18.04 使用 chrony。 按照 [Ubuntu Bionic：使用 chrony 配置 NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp) 中的配置指南操作。

6. 加入 Active Directory 域：   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    例如：    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. 使用用户帐户执行 `kinit` 以获取票证： 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    例如：    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>Ubuntu 配置（如果使用的是双重协议）  

可选择执行以下步骤。  仅当在 NFS 客户端上使用用户映射时，才需要执行这些步骤：  

1. 运行以下命令以升级已安装的包：   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    下面的示例使用示例值。 当命令提示你输入时，应根据环境提供输入。 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. 确保 `/etc/nsswitch.conf` 文件包含以下 `ldap` 条目：   
    `passwd:    compat systemd ldap`   
    `group:     compat systemd ldap`

3. 运行以下命令以重启并启用该服务：

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

以下示例从 Ubuntu LDAP 客户端向 LDAP 用户 `‘hari1’` 查询 AD LDAP 服务器： 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   

## <a name="configure-two-vms-with-the-same-hostname-to-access-nfsv41-volumes"></a>配置两个具有相同主机名的 VM 以访问 NFSv4.1 卷 

本部分介绍如何配置两个主机名相同的 VM，以访问 Azure NetApp 文件 NFSv4.1 卷。 执行灾难恢复 (DR) 测试并且需要一个其主机名与主 DR 系统相同的测试系统时，可以使用此过程。 只有访问相同 Azure NetApp 文件卷的两个 VM 上的主机名相同时，才需要使用此过程。  

NFSv4.x 要求每个客户端使用唯一字符串向服务器表明自身身份。 在一个客户端和一个服务器之间共享的文件打开和锁定状态与此身份相关联。 为了支持实现可靠的 NFSv4.x 状态恢复和透明状态迁移，此身份字符串不得在客户端重启时更改。

1. 使用以下命令在 VM 客户端上显示 `nfs4_unique_id` 字符串：
    
    `# systool -v -m nfs | grep -i nfs4_unique`     
    `    nfs4_unique_id      = ""`

    若要在具有相同主机名的其他 VM 上装载同一卷（例如 DR 系统），请创建一个 `nfs4_unique_id`，这样它就能够以独一无二的方式向 Azure NetApp 文件 NFS 服务表明自身身份。  此步骤可以让服务能够区分两个主机名相同的 VM 并允许在两个 VM 上装载 NFSv4.1 卷。  

    只需在测试 DR 系统上执行此步骤。 为了保持一致性，可以考虑在每个涉及的虚拟机上应用唯一设置。

2. 在测试 DR 系统中，将以下行添加到 `nfsclient.conf` 文件（通常位于 `/etc/modprobe.d/` 中）：

    `options nfs nfs4_unique_id=uniquenfs4-1`  

    字符串 `uniquenfs4-1` 可以是任何字母数字字符串，只要它在要连接到服务的 VM 中是唯一的即可。

    查看发行版的文档，了解如何配置 NFS 客户端设置。

    重启 VM，使更改生效。

3. 在测试 DR 系统中，验证 VM 重启后是否设置了 `nfs4_unique_id`：       

    `# systool -v -m nfs | grep -i nfs4_unique`   
    `   nfs4_unique_id      = "uniquenfs4-1"`   

4. 在两个 VM 上正常[装载 NFSv4.1 卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)。

    现在，两个主机名相同的 VM 都可以装载和访问 NFSv4.1 卷。  

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [为 Azure NetApp 文件创建双重协议卷](create-volumes-dual-protocol.md)
* [为 Windows 或 Linux 虚拟机装载或卸载卷](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) 
