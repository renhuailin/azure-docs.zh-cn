---
title: 排查 Azure NetApp 文件的 SMB 或双重协议卷问题 |Microsoft Docs
description: 介绍可帮助你解决 Azure NetApp 文件的 SMB 或双重协议问题的错误消息和解决方案。
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
ms.date: 02/02/2021
ms.author: b-juche
ms.openlocfilehash: dbc9f466437a575866c33219ff11af2d85d9a58b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100571078"
---
# <a name="troubleshoot-smb-or-dual-protocol-volumes"></a>SMB 或双重协议卷故障排除

本文介绍创建或管理双协议卷时可能遇到的错误情况的解决方法。

## <a name="errors-for-dual-protocol-volumes"></a>双重协议卷的错误

|     错误条件    |     解决方法    |
|-|-|
| 启用 LDAP over TLS，并且双重协议卷创建失败并出现错误 `This Active Directory has no Server root CA Certificate` 。    |     如果在创建双协议卷时发生此错误，请确保在 NetApp 帐户中上传根 CA 证书。    |
| 双重协议卷创建失败并出现错误 `Failed to validate LDAP configuration, try again after correcting LDAP configuration` 。    |  DNS 服务器上可能缺少 AD 主机计算机 (PTR) 记录的指针。 需要在 DNS 服务器上创建反向查找区域，然后在该反向查找区域中添加 AD 主机计算机的 PTR 记录。 <br> 例如，假设 AD 计算机的 IP 地址为 `10.X.X.X` ，使用命令)  (找到的 ad 计算机的主机名 `hostname` `AD1` ，并且域名为 `contoso.com` 。  添加到反向查找区域中的 PTR 记录应该是 `10.X.X.X`  ->  `contoso.com` 。   |
| 双重协议卷创建失败并出现错误 `Failed to create the Active Directory machine account \\\"TESTAD-C8DD\\\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\\n [ 434] Loaded the preliminary configuration.\\n [ 537] Successfully connected to ip 10.X.X.X, port 88 using TCP\\n**[ 950] FAILURE` 。 |     此错误表明 Active Directory 加入到 NetApp 帐户时 AD 密码不正确。 请用正确的密码更新 AD 连接，然后重试。 |
| 双重协议卷创建失败并出现错误 `Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available` 。 |   此错误表明 DNS 不可访问。 原因可能是 DNS IP 不正确，或者存在网络问题。 检查在 AD 连接中输入的 DNS IP，并确保 IP 正确。 <br> 此外，请确保 AD 和卷位于同一区域和同一 VNet 中。 如果它们在不同的 Vnet 中，请确保在两个 Vnet 之间建立 VNet 对等互连。|
| 在装载双协议卷时，权限被拒绝。 | 双协议卷支持 NFS 协议和 SMB 协议。  当你尝试访问 UNIX 系统上装入的卷时，系统将尝试将你使用的 UNIX 用户映射到 Windows 用户。 如果未找到映射，则会发生 "权限被拒绝" 错误。 <br> 当你使用 "root" 用户进行访问时，这种情况也适用。 <br> 若要避免 "权限被拒绝" 问题，请确保在 `pcuser` 访问装入点之前 Windows Active Directory 包含。 如果在 `pcuser` 遇到 "权限被拒绝" 问题后添加，请等待24小时，以便在再次尝试访问之前清除缓存条目。 |

## <a name="common-errors-for-smb-and-dual-protocol-volumes"></a>SMB 和双协议卷的常见错误

|     错误条件    |     解决方法    |
|-|-|
| SMB 或双重协议卷创建失败，出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Could not query DNS server. Verify that the network configuration is correct and that DNS servers are available."}]}` | 此错误表示 DNS 不可访问。 <br> 请考虑以下解决方案： <ul><li>如果 "添加"，则检查是否正在同一区域部署卷。</li> <li>检查是否添加了，并使用了同一 VNet。 如果它们使用的是不同的 Vnet，请确保 Vnet 彼此对等互连。 请参阅 [Azure NetApp 文件网络规划指南](azure-netapp-files-network-topologies.md)。 </li> <li>DNS 服务器可能 (Nsg) 应用了网络安全组。  因此，它不允许流量流动。 在这种情况下，请打开 DNS 或 AD 的 Nsg，以连接到各种端口。 有关端口要求，请参阅 [Active Directory 连接的要求](create-active-directory-connections.md#requirements-for-active-directory-connections)。 </li></ul> <br>适用于 Azure 的相同解决方案添加了。 Azure 添加应该部署在同一区域中。 VNet 应与卷使用的 VNet 位于同一区域或对等互连中。 | 
| SMB 或双重协议卷创建失败，出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-C1C8\". Reason: Kerberos Error: Invalid credentials were given Details: Error: Machine account creation procedure failed\n [ 563] Loaded the preliminary configuration.\n**[ 670] FAILURE: Could not authenticate as 'test@contoso.com':\n** Unknown user (KRB5KDC_ERR_C_PRINCIPAL_UNKNOWN)\n. "}]}`  |  <ul><li>请确保输入的用户名正确。 </li> <li>确保该用户是有权创建计算机帐户的管理员组的成员。 </li> <li> 如果使用 Azure 添加，请确保该用户是 Azure AD 组的一部分 `Azure AD DC Administrators` 。 </li></ul> | 
| SMB 或双重协议卷创建失败，出现以下错误： <br> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError", "message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-A452\". Reason: Kerberos Error: Pre-authentication information was invalid Details: Error: Machine account creation procedure failed\n [ 567] Loaded the preliminary configuration.\n [ 671] Successfully connected to ip 10.X.X.X, port 88 using TCP\n**[ 1099] FAILURE: Could not authenticate as\n** 'user@contoso.com': CIFS server account password does\n** not match password stored in Active Directory\n** (KRB5KDC_ERR_PREAUTH_FAILED)\n. "}]}` | 请确保为加入 AD 连接输入的密码正确无误。 |
| SMB 或双重协议卷创建失败，出现以下错误： `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.","details":[{"code":"InternalServerError","message":"Error when creating - Failed to create the Active Directory machine account \"SMBTESTAD-D9A2\". Reason: SecD Error: ou not found Details: Error: Machine account creation procedure failed\n [ 561] Loaded the preliminary configuration.\n [ 665] Successfully connected to ip 10.X.X.X, port 88 using TCP\n [ 1039] Successfully connected to ip 10.x.x.x, port 389 using TCP\n**[ 1147] FAILURE: Specifed OU 'OU=AADDC Com' does not exist in\n** contoso.com\n. "}]}` | 请确保指定用于联接 AD 连接的 OU 路径正确。 如果使用 Azure 添加，请确保组织单位路径为 `OU=AADDC Computers` 。 |

## <a name="next-steps"></a>后续步骤  

* [创建 SMB 卷](azure-netapp-files-create-volumes-smb.md)
* [创建双协议卷](create-volumes-dual-protocol.md)
* [为 Azure NetApp 文件配置 NFS 客户端](configure-nfs-clients.md)
