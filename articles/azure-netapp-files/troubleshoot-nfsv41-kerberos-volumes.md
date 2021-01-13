---
title: 排查 Azure NetApp 文件的 NFSv 4.1 Kerberos 卷问题 |Microsoft Docs
description: 介绍可帮助你对 Azure NetApp 文件的 NFSv 4.1 Kerberos 卷问题进行故障排除的错误消息和解决方案。
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
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/12/2021
ms.locfileid: "98134307"
---
# <a name="troubleshoot-nfsv41-kerberos-volume-issues"></a>排查 NFSv 4.1 Kerberos 卷问题 

本文介绍创建或管理 NFSv 4.1 Kerberos 卷时可能遇到的错误情况的解决方法。 

## <a name="error-conditions-and-resolutions"></a>错误情况和解决方法

|     错误条件    |     解决方法    |
|-|-|
|`Error allocating volume - Export policy rules does not match kerberosEnabled flag` | Azure NetApp 文件不支持 NFSv3 卷的 Kerberos。 Kerberos 仅对 NFSv 4.1 协议受支持。  |
|`This NetApp account has no configured Active Directory   connections`  |  为包含字段 " **KDC IP** " 和 " **AD 服务器名称**" 的 NetApp 帐户配置 Active Directory。 有关说明，请参阅 [配置 Azure 门户](configure-kerberos-encryption.md#configure-the-azure-portal) 。 |
|`Mismatch between KerberosEnabled flag value and ExportPolicyRule's access type parameter values.`  | Azure NetApp 文件不支持将纯 NFSv 4.1 卷转换为 Kerberos NFSv 4.1 卷，反之亦然。 |
|`mount.nfs: access denied by server when mounting volume <SMB_SERVER_NAME-XXX.DOMAIN_NAME>/<VOLUME_NAME>` <br>  示例：`smb-test-64d9.contoso.com:/nfs41-vol101` | <ol><li> 请确保正确设置了 A/PTR 记录并且该记录存在于服务器名称的 Active Directory 中 `smb-test-64d9.contoso.com` 。 <br> 在 NFS 客户端中，如果 `nslookup` 的 `smb-test-64d9.contoso.com` 解析为 IP 地址 IP1 (即 `10.1.1.68`) ，则 IP1 的，则必须将 `nslookup` 解析为只有一条记录 (即 `smb-test-64d9.contoso.com`) 。 `nslookup` 的 IP1 *不得* 解析为多个名称。 </li>  <li>`NFS-<Smb NETBIOS NAME>-<few random characters>`使用 PowerShell 或 UI 在 AD 上使用类型为的 NFS 计算机帐户设置 AES-256。 <br> 示例命令： <ul><li>`Set-ADComputer <NFS_MACHINE_ACCOUNT_NAME> -KerberosEncryptionType AES256` </li><li>`Set-ADComputer NFS-SMB-TEST-64 -KerberosEncryptionType AES256` </li></ul> </li> <li>确保 NFS 客户端、AD 和 Azure NetApp 文件存储软件相互同步，并处于五分钟的偏差范围内。 </li>  <li>使用命令获取 NFS 客户端上的 Kerberos 票证 `kinit <administrator>` 。</li> <li>将 NFS 客户端主机名减少到不超过15个字符，然后再次执行 "领域联接"。 </li><li>按如下所示重新启动 NFS 客户端和 `rpcgssd` 服务。 该命令可能因操作系统而异。<br> RHEL 7： <br> `service nfs restart` <br> `service rpcgssd restart` <br> CentOS 8： <br> `systemctl enable nfs-client.target && systemctl start nfs-client.target` <br> Ubuntu： <br>  (重新启动 `rpc-gssd` 服务。 )  <br> `sudo systemctl start rpc-gssd.service` </ul>| 
|`mount.nfs: an incorrect mount option was specified`   | 此问题可能与 NFS 客户端问题相关。 重新启动 NFS 客户端。    |
|`Hostname lookup failed`   | 需要在 DNS 服务器上创建反向查找区域，然后在该反向查找区域中添加 AD 主机计算机的 PTR 记录。 <br> 例如，假设 AD 计算机的 IP 地址为 `10.1.1.4` ，使用 hostname 命令 (找到的 ad 计算机的主机名) 为 `AD1` ，域名为 `contoso.com` 。 添加到反向查找区域中的 PTR 记录应该是 `10.1.1.4 -> AD1.contoso.com` 。 |
|`Volume creation fails due to unreachable DNS server`  | 提供两个可能的解决方案： <br> <ul><li> 此错误表明 DNS 不可访问。 原因可能是 DNS IP 错误或网络问题。 检查在 AD 连接中输入的 DNS IP，并确保 IP 正确。 </li> <li> 请确保 AD 和卷位于同一区域和同一 VNet 中。 如果它们在不同的 Vnet 中，请确保在两个 Vnet 之间建立 VNet 对等互连。 </li></ul> |
|NFSv 4.1 Kerberos 卷创建失败，出现类似于以下示例的错误： <br> `Failed to enable NFS Kerberos on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". Failed to bind service principal name on LIF "svm_e719cde8d6d0413fbd6adac0636cdecb_7ad0b82e_73349613". SecD Error: server create fail join user auth.` |KDC IP 错误，已创建 Kerberos 卷。 使用正确的地址更新 KDC IP。 <br> 更新 KDC IP 后，此错误将不会消失。 需要重新创建卷。 |

## <a name="next-steps"></a>后续步骤  

* [为 Azure NetApp 文件配置 NFSv 4.1 Kerberos 加密](configure-kerberos-encryption.md)
