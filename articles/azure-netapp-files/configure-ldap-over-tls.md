---
title: 为 Azure NetApp 文件配置基于 TLS 的 ADDS LDAP | Microsoft Docs
description: 介绍如何为 Azure NetApp 文件配置基于 TLS 的 ADDS LDAP，包括根 CA 证书管理。
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
ms.date: 05/05/2021
ms.author: b-juche
ms.openlocfilehash: e9a1b9148948319ebf05e2bcc9f5f306b2a21ec0
ms.sourcegitcommit: 89c4843ec85d1baea248e81724781d55bed86417
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108795945"
---
# <a name="configure-adds-ldap-over-tls-for-azure-netapp-files"></a>为 Azure NetApp 文件配置基于 TLS 的 ADDS LDAP

可以使用基于 TLS 的 LDAP 来保护 Azure NetApp 文件卷与 Active Directory LDAP 服务器之间的通信。  可为 Azure NetApp 文件的 NFS、SMB 和双重协议卷启用基于 TLS 的 LDAP。  

## <a name="considerations"></a>注意事项

* 如果使用 Azure Active Directory 域服务 (AADDS)，则不能启用 LDAP over TLS。 AADDS 使用 LDAPS（端口 636）而不是基于 TLS 的 LDAP（端口 389）来保护 LDAP 流量。  

## <a name="register-the-ldap-over-tls-feature"></a>注册基于 TLS 的 LDAP 功能 

基于 TLS 的 LDAP 功能目前为预览版。 如果你是第一次使用此功能，在使用之前需要注册此功能。

1.  注册此功能：

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```

2. 检查功能注册的状态： 

    > [!NOTE]
    > RegistrationState 可能会处于 `Registering` 状态长达 60 分钟，然后才更改为 `Registered` 状态。 请等到状态变为`Registered`后再继续。

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapOverTls
    ```
还可以使用 [Azure CLI 命令](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` 和 `az feature show` 注册功能并显示注册状态。 

## <a name="generate-and-export-root-ca-certificate"></a>生成和导出根 CA 证书 

如果你没有根 CA 证书，则需要生成一个证书，并将其导出以用于基于 TLS 的 LDAP 身份验证。 

1. 按照[安装证书颁发机构](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority)中所述安装并配置 ADDS 证书颁发机构。 

2. 按照[使用 MMC 管理单元查看证书](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in)中所述使用 MMC 管理单元和证书管理器工具。  
    使用“证书管理器”管理单元查找本地设备的根证书或颁发证书。 应通过以下设置之一运行“证书管理”管理单元命令：  
    * 一个已加入域并已安装根证书的基于 Windows 的客户端 
    * 域中包含根证书的另一台计算机  

3. 导出根 CA 证书。  
    可以从“个人”或“受信任的根证书颁发机构”目录导出根 CA 证书，如以下示例中所示：   
    ![显示个人证书的屏幕截图](../media/azure-netapp-files/personal-certificates.png)   
    ![显示受信任的根证书颁发机构的屏幕截图](../media/azure-netapp-files/trusted-root-certification-authorities.png)    

    确保以 Base-64 编码的 X.509 (.CER) 格式导出证书： 

    ![证书导出向导](../media/azure-netapp-files/certificate-export-wizard.png)

## <a name="enable-ldap-over-tls-and-upload-root-ca-certificate"></a>启用基于 TLS 的 LDAP 并上传根 CA 证书 

1. 转到用于卷的 NetApp 帐户，并单击“Active Directory 连接”。 然后单击“加入”以创建新的 AD 连接，或单击“编辑”以编辑现有的 AD 连接 。  

2. 在出现的“加入 Active Directory”或“编辑 Active Directory”窗口中，选中“基于 TLS 的 LDAP”复选框为卷启用基于 TLS 的 LDAP  。 然后单击“服务器根 CA 证书”，并上传[生成的根 CA 证书](#generate-and-export-root-ca-certificate)以用于基于 TLS 的 LDAP。  

    ![显示“基于 TLS 的 LDAP”选项的屏幕截图](../media/azure-netapp-files/ldap-over-tls-option.png)

    确保 DNS 可以解析证书颁发机构名称。 此名称显示在证书上的“颁发者”（英文版中为“Issued By”或“Issuer”）字段中：  

    ![显示证书信息的屏幕截图](../media/azure-netapp-files/certificate-information.png)

如果上传了无效的证书，并且你有现有的 AD 配置、SMB 卷或 Kerberos 卷，则会发生如下所示的错误：

`Error updating Active Directory settings The LDAP client configuration "ldapUserMappingConfig" for Vservers is an invalid configuration.`

若要解决该错误，请将有效的根 CA 证书上传到你的 NetApp 帐户，使 Windows Active Directory LDAP 服务器能够进行 LDAP 身份验证。

## <a name="next-steps"></a>后续步骤  

* [创建用于 Azure NetApp 文件的 NFS 卷](azure-netapp-files-create-volumes.md)
* [创建用于 Azure NetApp 文件的 SMB 卷](azure-netapp-files-create-volumes-smb.md) 
* [为 Azure NetApp 文件创建双重协议卷](create-volumes-dual-protocol.md)

