---
title: 对 Azure NetApp 文件的 SMB 或双协议卷进行故障排除 | Microsoft Docs
description: 介绍了有助于对 Azure NetApp 文件的 SMB 或双协议卷进行故障排除的错误消息和解决方法。
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
ms.topic: troubleshooting
ms.date: 02/19/2021
ms.author: b-juche
ms.openlocfilehash: 1ec2b7c3c9f4aaccd168031b718bbb5b50394506
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735766"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB 或双重协议卷故障排除

本文介绍了在创建或管理双协议卷时可能会遇到的错误情况的解决方法。

## <a name="errors-for-dual-protocol-volumes"></a>双协议卷的错误

|     错误条件    |     解决方法    |
|-|-|
| 已启用 LDAP over TLS，双协议卷创建失败，错误为 `This Active Directory has no Server root CA Certificate`。    |     如果在创建双协议卷时发生此错误，请确保在 NetApp 帐户中上传了根 CA 证书。    |
| 双协议卷创建失败，错误为 `Failed to validate LDAP configuration, try again after correcting LDAP configuration`。    |  DNS 服务器上可能缺少 AD 主机的指针 (PTR) 记录。 你需要在 DNS 服务器上创建反向查找区域，然后在此反向查找区域中添加 AD 主机的 PTR 记录。 <br> 例如，假设 AD 虚拟机的 IP 地址为 `10.x.x.x`，AD 虚拟机的主机名（通过 `hostname` 命令找到）为 `AD1`，域名为 `contoso.com`。  添加到反向查找区域的 PTR 记录应是 `10.x.x.x` -> `contoso.com`。   |
| 双协议卷创建失败，错误为 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.x.x.x, port 88 using TCP\\n**[ 950] FAILURE`。 |     此错误指明在 NetApp 帐户建立 Active Directory 联接时 AD 密码不正确。 请使用正确的密码更新 AD 连接，然后重试。 |
| 双协议卷创建失败，错误为 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available`。 |   此错误指明 DNS 不可访问。 原因可能是 DNS IP 不正确，或者存在网络问题。 请检查在 AD 连接中输入的 DNS IP，并确保此 IP 是正确无误的。 <br> 另外，还请确保 AD 和卷位于相同的区域和 VNet 中。 如果位于不同的 VNet 中，请确保在两个 VNet 之间建立 VNet 对等互连。 <br> 有关详细信息，请参阅 [Azure NetApp 文件网络规划指南](azure-netapp-files-network-topologies.md#azure-native-environments)。 |
| 在装载双协议卷时，发生“权限被拒绝”错误。 | 双协议卷同时支持 NFS 协议和 SMB 协议。  当你尝试在 UNIX 系统上访问装载的卷时，系统会尝试将你使用的 UNIX 用户映射到 Windows 用户。 如果找不到映射，则会发生“权限被拒绝”错误。 <br> 当你使用“根”用户进行访问时，也适用这种情况。 <br> 为了避免“权限被拒绝”问题，请在访问装入点之前确保 Windows Active Directory 包含 `pcuser`。 如果在遇到“权限被拒绝”问题后添加了 `pcuser`，请等待 24 小时，直到缓存条目被清除后，再尝试重新访问。 |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>SMB 和双协议卷的常见错误

|     错误条件    |     解决方法    |
|-|-|
| SMB 或双协议卷创建失败，并出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | 此错误指明 DNS 不可访问。 <br> 请考虑以下解决方案： <ul><li>检查 ADDS 和卷是否部署在同一区域中。</li> <li>检查 ADDS 和卷是否在使用相同的 VNet。 如果使用的 VNet 不同，请确保在 VNet 之间建立了对等互连。 请参阅 [Azure NetApp 文件网络规划指导原则](azure-netapp-files-network-topologies.md)。 </li> <li>DNS 服务器可能应用了网络安全组 (NSG)。  因此，它不允许流量流动。 在这种情况下，需要向 DNS 或 AD 打开 NSG，以连接到各种端口。 有关端口要求，请参阅 [Active Directory 连接要求](create-active-directory-connections.md#requirements-for-active-directory-connections)。 </li></ul> <br>同样的解决方案也适用于 Azure ADDS。 Azure ADDS 应部署在同一区域中。 VNet 应位于同一区域中，或与卷使用的 VNet 建立对等互连。 | 
| SMB 或双协议卷创建失败，并出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>确保所输入的用户名是正确无误的。 </li> <li>确保用户属于有权创建虚拟机帐户的管理员组。 </li> <li> 如果你使用 Azure ADDS，请确保用户属于 Azure AD 组 `Azure AD DC Administrators`。 </li></ul> | 
| SMB 或双协议卷创建失败，并出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.x.x.x, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | 请确保为建立 AD 联接输入的密码是正确无误的。 |
| SMB 或双协议卷创建失败，并出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | 请确保为建立 AD 联接指定的 OU 路径是正确无误的。 如果你使用 Azure ADDS，请确保组织单位路径为 `OU=AADDC Computers`。 |
| SMB 或双协议卷创建失败，并出现以下错误： <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL. Reason: LDAP Error: Local error occurred Details: Error: Machine account creation procedure failed. [nnn] Loaded the preliminary configuration. [nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn] Successfully connected to ip 10.x.x.x, port 389 using [nnn] Entry for host-address: 10.x.x.x not found in the current source: FILES. Ignoring and trying next available source [nnn] Source: DNS unavailable. Entry for host-address:10.x.x.x found in any of the available sources\n*[nnn] FAILURE: Unable to SASL bind to LDAP server using GSSAPI: local error [nnn] Additional info: SASL(-1): generic failure: GSSAPI Error: Unspecified GSS failure. Minor code may provide more information (Cannot determine realm for numeric host address) [nnn] Unable to connect to LDAP (Active Directory) service on contoso.com (Error: Local error) [nnn] Unable to make a connection (LDAP (Active Directory):contosa.com, result: 7643. `  | DNS 服务器上可能缺少 AD 主机的指针 (PTR) 记录。 你需要在 DNS 服务器上创建反向查找区域，然后在此反向查找区域中添加 AD 主机的 PTR 记录。 <br> 例如，假设 AD 虚拟机的 IP 地址为 `10.x.x.x`，AD 虚拟机的主机名（通过 `hostname` 命令找到）为 `AD1`，域名为 `contoso.com`。  添加到反向查找区域的 PTR 记录应是 `10.x.x.x` -> `contoso.com`。   | 
| SMB 或双协议卷创建失败，并出现以下错误： <br> `Failed to create the Active Directory machine account \"SMB-ANF-VOL\". Reason: Kerberos Error: KDC has no support for encryption type Details: Error: Machine account creation procedure failed [nnn]Loaded the preliminary configuration. [nnn]Successfully connected to ip 10.x.x.x, port 88 using TCP [nnn]FAILURE: Could not authenticate as 'contosa.com': KDC has no support for encryption type  (KRB5KDC_ERR_ETYPE_NOSUPP) ` | 请确保在 Active Directory 连接和服务帐户中都启用了 [AES 加密](./create-active-directory-connections.md#create-an-active-directory-connection)。 |
| SMB 或双协议卷创建失败，并出现以下错误： <br> `Failed to create the Active Directory machine account \"SMB-NTAP-VOL\". Reason: LDAP Error: Strong authentication is required Details: Error: Machine account creation procedure failed\n [ 338] Loaded the preliminary configuration.\n [ nnn] Successfully connected to ip 10.x.x.x, port 88 using TCP\n [ nnn ] Successfully connected to ip 10.x.x.x, port 389 using TCP\n [ 765] Unable to connect to LDAP (Active Directory) service on\n dc51.area51.com (Error: Strong(er) authentication\n required)\n*[ nnn] FAILURE: Unable to make a connection (LDAP (Active\n* Directory):contoso.com), result: 7609\n. "` | 没有选中“LDAP 签名”选项，但 AD 客户端有 LDAP 签名。  请[启用 LDAP 签名](create-active-directory-connections.md#create-an-active-directory-connection)，然后重试。 | 

## <a name="next-steps"></a>后续步骤  

* [创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [创建双协议卷](create-volumes-dual-protocol.md)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)