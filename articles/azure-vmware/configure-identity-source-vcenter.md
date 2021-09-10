---
title: 为 vCenter 配置外部标识源
description: 了解如何为 vCenter 配置通过 LDAP 或 LDAPS 的 Active Directory 作为外部标识源。
ms.topic: how-to
ms.date: 08/31/2021
ms.openlocfilehash: 50a79c54c57649fd8dc565a7634823bf4a7a0f86
ms.sourcegitcommit: 851b75d0936bc7c2f8ada72834cb2d15779aeb69
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2021
ms.locfileid: "123315915"
---
# <a name="configure-external-identity-source-for-vcenter"></a>为 vCenter 配置外部标识源



[!INCLUDE [vcenter-access-identity-description](includes/vcenter-access-identity-description.md)]

>[!NOTE]
>运行命令按提交的顺序一次执行一个。

在本操作指南中，你将学习如何：

> [!div class="checklist"]
> * 列出与 vCenter SSO 集成的所有现有外部标识源
> * 添加通过 LDAP 的 Active Directory（带或不带 SSL） 
> * 将现有 AD 组添加到 cloudadmin 组
> * 从 cloudadmin 角色中删除 AD 组
> * 删除现有的外部标识源



## <a name="prerequisites"></a>先决条件

- 建立从本地网络到私有云的连接。

- 如果你有带 SSL 的 AD，请下载用于 AD 身份验证的证书，并将其作为 Blob 存储上传到 Azure 存储帐户。 然后，你将需要[使用共享访问签名 (SAS) 授予对 Azure 存储资源的访问权限](../storage/common/storage-sas-overview.md)。  

- 如果使用 FQDN，请在本地 AD 上启用 DNS 解析。

 

## <a name="list-external-identity"></a>列出外部标识



你将运行 `Get-ExternalIdentitySources` cmdlet，以列出已与 vCenter SSO 集成的所有外部标识源。

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“运行命令” > “包” > “Get-ExternalIdentitySources”。

   :::image type="content" source="media/run-command/run-command-overview.png" alt-text="显示如何访问可用的运行命令的屏幕截图。" lightbox="media/run-command/run-command-overview.png":::

1. 提供所需的值或更改默认值，然后选择“运行”。

   :::image type="content" source="media/run-command/run-command-get-external-identity-sources.png" alt-text="显示如何列出外部标识源的屏幕截图。":::
   
   | **字段** | 值 |
   | --- | --- |
   | **最长保留时间**  |cmdlet 输出的保持期。 默认值为 60。   |
   | **指定用于执行的名称**  | 字母数字名称，例如 getExternalIdentity。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。


## <a name="add-active-directory-over-ldap"></a>添加通过 LDAP 的 Active Directory

你将运行 `New-AvsLDAPIdentitySource` cmdlet，将通过 LDAP 的 AD 作为要与 SSO 配合使用的外部标识源添加到 vCenter 中。 

1. 选择“运行命令” > “包” > “New-AvsLDAPIdentitySource”。

1. 提供所需的值或更改默认值，然后选择“运行”。
   
   | **字段** | 值 |
   | --- | --- |
   | **名称**  | 外部标识源的用户友好名称，例如 avslap.local。  |
   | **DomainName**  | 域的 FQDN。    |
   | **DomainAlias**  | 对于 Active Directory 标识源，是域的 NetBIOS 名称。 如果你使用的是 SSPI 身份验证，请将 AD 域的 NetBIOS 名称添加为标识源的别名。      |
   | **PrimaryUrl**  | 外部标识源的主 URL，例如 ldap://yourserver:389。  |
   | **SecondaryURL**  | 辅助回退 URL（如果出现主 URL 故障）。  |
   | **BaseDNUsers**  |  查找有效用户的位置，例如 CN=users,DC=yourserver,DC=internal。  需要基准 DN 才能使用 LDAP 身份验证。  |
   | **BaseDNGroups**  | 查找组的位置，例如 CN=group1, DC=yourserver,DC= internal。 需要基准 DN 才能使用 LDAP 身份验证。  |
   | **凭据**  | 用于对 AD 源（不是 cloudadmin）进行身份验证的用户名和密码。  |
   | **GroupName**  | 用于在外部标识源中授予云管理员访问权限的组，例如 avs-admins。  |
   | **最长保留时间**  | cmdlet 输出的保持期。 默认值为 60。   |
   | **指定用于执行的名称**  | 字母数字名称，例如 addexternalIdentity。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。



## <a name="add-active-directory-over-ldap-with-ssl"></a>添加通过 LDAP 的 Active Directory（带 SSL）

你将运行 `New-AvsLDAPSIdentitySource` cmdlet，将通过 LDAP 的 AD（带 SSL）作为要与 SSO 配合使用的外部标识源添加到 vCenter 中。 

1. 下载用于 AD 身份验证的证书，并将其作为 Blob 存储上传到 Azure 存储帐户。  

1. [使用共享访问签名 (SAS) 授予对 Azure 存储资源的访问权限](../storage/common/storage-sas-overview.md)。  
   
1. 选择“运行命令” > “包” > “New-AvsLDAPSIdentitySource”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **名称**  | 外部标识源的用户友好名称，例如 avslap.local。  |
   | **DomainName**  | 域的 FQDN。   |
   | **DomainAlias**  | 对于 Active Directory 标识源，是域的 NetBIOS 名称。 如果你使用的是 SSPI 身份验证，请将 AD 域的 NetBIOS 名称添加为标识源的别名。     |
   | **PrimaryUrl**  | 外部标识源的主 URL，例如 ldap://yourserver:389。  |
   | **SecondaryURL**  | 辅助回退 URL（如果出现主 URL 故障）。  |
   | **BaseDNUsers**  |  查找有效用户的位置，例如 CN=users,DC=yourserver,DC=internal。  需要基准 DN 才能使用 LDAP 身份验证。  |
   | **BaseDNGroups**  | 查找组的位置，例如 CN=group1, DC=yourserver,DC= internal。 需要基准 DN 才能使用 LDAP 身份验证。  |
   | **凭据**  | 用于对 AD 源（不是 cloudadmin）进行身份验证的用户名和密码。  |
   | **CertificateSAS** | SAS 字符串的路径，其中包含用于对 AD 源进行身份验证的证书。  |
   | **GroupName**  | 用于在外部标识源中授予云管理员访问权限的组，例如 avs-admins。  |
   | **最长保留时间**  | cmdlet 输出的保持期。 默认值为 60。   |
   | **指定用于执行的名称**  | 字母数字名称，例如 addexternalIdentity。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。




## <a name="add-existing-ad-group-to-cloudadmin-group"></a>将现有 AD 组添加到 cloudadmin 组

你将运行 `Add-GroupToCloudAdmins` cmdlet，将现有 AD 组添加到 cloudadmin 组。 此组中的用户拥有与 vCenter SSO 中定义的 cloudadmin (cloudadmin@vsphere.local) 角色相同的权限。

1. 选择“运行命令” > “包” > “Add-GroupToCloudAdmins”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **GroupName**  | 要添加的组的名称，例如 VcAdminGroup。  |
   | **最长保留时间**  | cmdlet 输出的保持期。 默认值为 60。   |
   | **指定用于执行的名称**  | 字母数字名称，例如 addADgroup。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。




## <a name="remove-ad-group-from-the-cloudadmin-role"></a>从 cloudadmin 角色中删除 AD 组

你将运行 `Remove-GroupFromCloudAdmins` cmdlet，从 cloudadmin 角色中删除指定的 AD 组。 

1. 选择“运行命令” > “包” > “Remove-GroupFromCloudAdmins”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **GroupName**  | 要删除的组的名称，例如 VcAdminGroup。  |
   | **最长保留时间**  | cmdlet 输出的保持期。 默认值为 60。   |
   | **指定用于执行的名称**  | 字母数字名称，例如 removeADgroup。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。






## <a name="remove-existing-external-identity-sources"></a>删除现有的外部标识源

你将运行 `Remove-ExternalIdentitySources` cmdlet，批量删除所有现有的外部标识源。 

1. 选择“运行命令” > “包” > “Remove-ExternalIdentitySources”。

1. 提供所需的值或更改默认值，然后选择“运行”。

   | **字段** | 值 |
   | --- | --- |
   | **最长保留时间**  | cmdlet 输出的保持期。 默认值为 60。   |
   | **指定用于执行的名称**  | 字母数字名称，例如 remove_externalIdentity。  |
   | **超时**  |  一个时间段，在该时间段后 cmdlet 将退出（如果需要太长时间才能完成）。  |

1. 检查“通知”以查看进度。


## <a name="next-steps"></a>后续步骤

现在你已经了解了如何配置 LDAP 和 LDAPS，可以进一步了解：

- [如何配置存储策略](configure-storage-policy.md) - 部署到 vSAN 数据存储的每个 VM 都分配有至少一个 VM 存储策略。 可以在 VM 的初始部署中或在执行其他 VM 操作（例如克隆或迁移）时分配 VM 存储策略。

- [Azure VMware 解决方案标识概念](concepts-identity.md) - 使用 vCenter 管理虚拟机 (VM) 工作负载，并使用 NSX-T Manager 来管理和扩展私有云。 访问和身份管理对 vCenter 使用 CloudAdmin 角色，对 NSX-T Manager 使用受限的管理员权限。 

 
