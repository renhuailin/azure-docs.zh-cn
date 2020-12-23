---
title: 为 Azure NetApp 文件配置 NFS 客户端 |Microsoft Docs
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
ms.date: 11/09/2020
ms.author: b-juche
ms.openlocfilehash: c1cdeaa41dda11f2ab520cf8d31ddb2116587082
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94409563"
---
# <a name="configure-an-nfs-client-for-azure-netapp-files"></a>为 Azure NetApp 文件配置 NFS 客户端

本文中所述的 NFS 客户端配置是在 [配置 nfsv 4.1 Kerberos 加密](configure-kerberos-encryption.md) 或 [创建双协议卷](create-volumes-dual-protocol.md)时安装的一部分。 可在 Azure NetApp 文件中使用多种 Linux 分发版。 本文介绍两种更常用的环境配置： RHEL 8 和 Ubuntu 18.04。 

无论使用何种 Linux 风格，都需要以下配置：
* 配置 NTP 客户端以避免出现时间偏差问题。
* 配置 Linux 客户端的 DNS 条目以进行名称解析。  
    此配置必须包含 "A" (前进) 记录和 PTR (反向) 记录。 
* 对于 "域加入"，请在目标 Active Directory 中创建 Linux 客户端的计算机帐户， (该帐户是在领域加入命令) 创建的。 
    > [!NOTE] 
    > `$SERVICEACCOUNT`下面的命令中使用的变量应该是具有在目标组织单位中创建计算机帐户的权限或委派的用户帐户。

## <a name="rhel-8-configuration"></a>RHEL 8 配置 

本部分介绍 NFSv 4.1 Kerberos 加密和双重协议所需的 RHEL 配置。  

本部分中的示例使用以下域名和 IP 地址：  

* 域名： `contoso.com`
* 专用 IP： `10.6.1.4`

### <a name="rhel-8-configuration-if-you-are-using-nfsv41-kerberos-encryption"></a><a name="rhel8_nfsv41_kerberos"></a>RHEL 8 配置（如果使用的是 NFSv 4.1 Kerberos 加密）

1. 配置 `/etc/resolv.conf` 适当的 DNS 服务器。  

    例如：  

    `[root@reddoc cbs]# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver 10.6.1.4(private IP)`   

2. 在 dns 服务器中为 DNS 正向和反向查找区域添加 NFS 客户端记录。

3. 若要验证 DNS，请从 NFS 客户端使用以下命令：   

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`

4. 安装包：   

    `yum update`   
    `sudo yum -y install realmd sssd adcli samba-common krb5-workstation chrony nfs-utils`

5.  配置 NTP 客户端。  

    RHEL 8 默认情况下使用 chrony。 遵循 [使用 `Chrony` 套件配置 NTP](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/8/html/configuring_basic_system_settings/using-chrony-to-configure-ntp)中的配置准则。

6.  加入 Active Directory 域：  

    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`

    例如： 

    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`
    
    确保将 `default_realm` 设置为中提供的领域 `/etc/krb5.conf` 。  否则，请将其添加到 `[libdefaults]` 文件中的节下，如以下示例所示：
    
    `default_realm = CONTOSO.COM`

7. 重新启动所有 NFS 服务：  
 
    `systemctl start nfs-*`   
    `systemctl restart rpc-gssd.service`

    Restart 会阻止在 `“mount.nfs: an incorrect mount option was specified”` Kerberos 装入期间出现错误。

8. `kinit`用用户帐户运行命令以获取票证： 
 
    `sudo kinit $SERVICEACCOUNT@DOMAIN`  

    例如：   

    `sudo kinit ad_admin@CONTOSO.COM`


### <a name="rhel-8-configuration-if-you-are-using-dual-protocol"></a>RHEL 8 配置（如果使用的是双重协议）

以下步骤是可选的。 仅当在 NFS 客户端上使用用户映射时，才需要执行这些步骤： 

1. [如果使用的是 nfsv 4.1 Kerberos 加密](#rhel8_nfsv41_kerberos)部分，请完成 RHEL 8 配置中所述的所有步骤。   

2. 在/etc/hosts 文件中添加静态 DNS 记录，以对 AD 使用完全限定的域名 (FQDN) ，而不是使用 SSSD 配置文件中的 IP 地址：  

    `cat /etc/hosts`   
    `10.6.1.4 winad2016.contoso.com`

3. 添加域的额外部分来解析 AD LDAP 服务器中的标识符：    

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

4. 确保 `/etc/nsswitch.conf` 具有 `sss` 以下条目：   

    `cat /etc/nsswitch.conf`   
    `passwd: sss files systemd`   
    `group: sss files systemd`   
    `netgroup: sss files`   

5. 重新启动 `sssd` 服务并清除缓存：   

    `service sssd stop`   
    `rm -f /var/lib/sss/db/*`   
    `service sssd start`   
 
6. 测试以确保客户端与 LDAP 服务器集成：   

    `[root@red81 cbs]# id ldapuser1`   
    `uid=1234(ldapuser1) gid=1111(ldapgroup1) groups=1111(ldapgroup1)`   

## <a name="ubuntu-configuration"></a>Ubuntu 配置   

本部分介绍 NFSv 4.1 Kerberos 加密和双重协议所需的 Ubuntu 配置。 

本部分中的示例使用以下域名和 IP 地址：  

* 域名： `contoso.com`
* 专用 IP： `10.6.1.4`

1. 配置 `/etc/resolv.conf` 适当的 DNS 服务器：

    `root@ubuntu-rak:/home/cbs# cat /etc/resolv.conf`   
    `search contoso.com`   
    `nameserver <private IP address of DNS server>`   

2. 在 DNS 服务器中为 DNS 正向和反向查找区域添加 NFS 客户端记录。
 
    若要验证 DNS，请从 NFS 客户端使用以下命令：

    `# nslookup [hostname/FQDN of NFS client(s)]`   
    `# nslookup [IP address of NFS client(s)]`   

3. 安装包：
 
    `apt-get update`   
    `apt-get install -y realmd packagekit sssd adcli samba-common chrony krb5-user nfs-common`
    
    系统提示时， `$DOMAIN.NAME` 使用大写 (输入，例如 `CONTOSO.COM`) 默认的 Kerberos 领域。

4. 重新启动该服务 `rpc-gssd.service` ： 

    `sudo systemctl start rpc-gssd.service`

5. Ubuntu 18.04 默认情况下使用 chrony。 按照 Ubuntu Bionic 中的配置准则 [进行操作：使用 chrony 配置 NTP](https://ubuntu.com/blog/ubuntu-bionic-using-chrony-to-configure-ntp)。

6. 加入 Active Directory 域：   
 
    `sudo realm join $DOMAIN.NAME -U $SERVICEACCOUNT --computer-ou="OU=$YOUROU"`
 
    例如：    
    `sudo realm join CONTOSO.COM -U ad_admin --computer-ou="CN=Computers"`

7. 为 `kinit` 用户执行获取票证的操作： 
 
    `sudo kinit $SERVICEACCOUNT`   
 
    例如：    
    `sudo kinit ad_admin`  

### <a name="ubuntu-configuration-if-you-are-using-dual-protocol"></a>如果使用的是双重协议，则使用 Ubuntu 配置  

以下步骤是可选的。  仅当要在 NFS 客户端上使用用户映射时，才需要执行这些步骤：  

1. 运行以下命令以升级已安装的包：   
    `sudo apt update && sudo apt install libnss-ldap libpam-ldap ldap-utils nscd`

    下面的示例使用示例值。 当命令提示输入时，应根据环境提供输入。 

    `base dc=contoso,dc=com uri ldap://10.20.0.4:389/ ldap_version 3 rootbinddn cn=admin,cn=Users,dc=contoso,dc=com pam_password ad`   

2. 运行以下命令以重新启动并启用该服务：

    `sudo systemctl restart nscd && sudo systemctl enable nscd`   

以下示例从 Ubuntu LDAP 客户端向 LDAP 用户查询 AD LDAP 服务器 `‘hari1’` ： 

`root@cbs-k8s-varun4-04:/home/cbs# getent passwd hari1`   
`hari1:*:1237:1237:hari1:/home/hari1:/bin/bash`   


## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [为 Azure NetApp 文件创建双协议卷](create-volumes-dual-protocol.md)

