---
title: 为 Azure NetApp 文件排查 NFSv4.1 Kerberos 卷的问题 | Microsoft Docs
description: 介绍有助于为 Azure NetApp 文件排查 NFSv4.1 Kerberos 卷的问题的错误消息和解决方法。
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
ms.date: 01/12/2021
ms.author: b-juche
ms.openlocfilehash: 638607da02b1db4842cdc04f86a4fed1860c243f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "98134307"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>排查 NFSv4.1 Kerberos 卷的问题 

本文介绍在创建或管理 NFSv4.1 Kerberos 卷时可能会遇到的错误情况的解决方法。 

## <a name="error-conditions-and-resolutions"></a>错误情况和解决方法

|     错误条件    |     解决方法    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp 文件不支持适用于 NFSv3 卷的 Kerberos。 只有 NFSv4.1 协议支持 Kerberos。  |
|`This NetApp account has no configured Active Directory   connections`  |  请通过“KDC IP”和“AD 服务器名称”字段为 NetApp 帐户配置 Active Directory 。 有关说明，请参阅[配置 Azure 门户](configure-kerberos-encryption.md#configure-the-azure-portal)。 |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp 文件不支持将普通 NFSv4.1 卷转换为 Kerberos NFSv4.1 卷，反之亦然。 |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  示例： `smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> 确保正确设置 A/PTR 记录，并且这些记录存在于名为 `smb-test-64d9.contoso.com` 的服务器的 Active Directory 中。 <br> 在 NFS 客户端中，如果 `smb-test-64d9.contoso.com` 的 `nslookup` 解析为 IP 地址 IP1（即 `10.1.1.68`），则 IP1 的 `nslookup` 必须只解析为一条记录（即 `smb-test-64d9.contoso.com`）。 IP1 的 `nslookup` 不可解析为多个名称。 </li>  <li>使用 PowerShell 或 UI 为 AD 上 `NFS-<Smb NETBIOS NAME>-<few random characters>` 类型的 NFS 计算机帐户设置 AES-256。 <br> 示例命令： <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>确保 NFS 客户端、AD 和 Azure NetApp 文件存储软件的时间相互同步，并且偏差范围在五分钟之内。 </li>  <li>使用 `kinit <administrator>` 命令获取 NFS 客户端上的 Kerberos 票证。</li> <li>将 NFS 客户端主机名的字符数减少到 15 个以内，然后再次执行领域加入操作。 </li><li>重启 NFS 客户端和 `rpcgssd` 服务，如下所示。 该命令可能因 OS 而异。<br> RHEL 7： <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8： <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu： <br> （重启 `rpc-gssd` 服务。） <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | 该问题可能与 NFS 客户端问题相关。 请重启 NFS 客户端。    |
|`Hostname lookup failed`   | 你需要在 DNS 服务器上创建反向查找区域，然后在该反向查找区域中添加 AD 主机的 PTR 记录。 <br> 例如，假定 AD 计算机的 IP 地址为 `10.1.1.4`，该 AD 计算机的主机名（通过使用 hostname 命令找到的）为 `AD1`，并且域名为 `contoso.com`。 添加到反向查找区域的 PTR 记录应该是 `10.1.1.4 -> AD1.contoso.com`。 |
|`Volume creation fails due to unreachable DNS server`  | 有两个可能的解决方案： <br> <ul><li> 此错误表明无法访问 DNS。 原因可能会是 DNS IP 不正确或网络有问题。 请检查在 AD 连接中输入的 DNS IP 并确保该 IP 正确。 </li> <li> 确保 AD 和卷位于同一区域和同一 VNet 中。 如果它们在不同的 VNet 中，请确保在两个 VNet 之间建立 VNet 对等互连。 </li></ul> |
|NFSv4.1 Kerberos 卷创建失败，出现类似于以下示例的错误： <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP 错误，并且已创建 Kerberos 卷。 请使用正确的地址更新 KDC IP。 <br> 更新 KDC IP 后，此错误不会消失。 你需要重新创建该卷。 |

## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFSv4.1 Kerberos 加密](configure-kerberos-encryption.md)
