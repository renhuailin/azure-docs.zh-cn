---
title: 提升私有云特权
titleSuffix: Azure VMware Solution by CloudSimple
description: 介绍如何提升私有云特权，以获得 vCenter 的管理功能
author: suzizuber
ms.author: v-szuber
ms.date: 06/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f12b6face019fcbcdc0e47710ead71df633dc0b3
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2021
ms.locfileid: "129614700"
---
# <a name="escalate-private-cloud-vcenter-privileges-from-the-cloudsimple-portal"></a>从 CloudSimple 门户提升私有云 vCenter 特权

若要获得私有云 vCenter 的管理访问权限，可以暂时提升 CloudSimple 特权。  可以使用提升后的特权来安装 VMware 解决方案、添加标识源及管理用户。

可以在 vCenter SSO 域上创建新用户，并授予其对 vCenter 的访问权限。  创建新用户时，请将其添加到 CloudSimple 内置组，以便访问 vCenter。  有关详细信息，请参阅 [VMware vCenter 的 CloudSimple 私有云特权模型](./learn-private-cloud-permissions.md)。

> [!CAUTION]
> 不要对管理组件进行任何配置更改。 在已提升特权状态期间执行的操作可能会对系统产生负面影响，或者导致系统不可用。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="escalate-privileges"></a>提升特权

1. 访问 [CloudSimple 门户](access-cloudsimple-portal.md)。

2. 打开“资源”页，选择要提升特权的私有云。

3. 在“更改 vSphere 特权”下的“摘要”页面底部附近，单击“升级”。

    ![更改 vSphere 特权](media/escalate-private-cloud-privilege.png)

4. 选择 vSphere 用户类型。  只能升级 `CloudOwner@cloudsimple.local` 本地用户。

5. 从下拉项中选择提升时间间隔。 选择允许你完成任务的最短时间。

6. 选中复选框以确认已了解风险。

    ![提升特权对话框](media/escalate-private-cloud-privilege-dialog.png)

7. 单击 **“确定”** 。

8. 升级过程可能需要几分钟时间。 完成后，请单击“确定”。

特权提升开始，并一直持续到所选间隔结束。  可以登录到私有云 vCenter 来执行管理任务。

> [!IMPORTANT]
> 只有一个用户能拥有已提升的特权。  必须先解除提升该用户的特权，才能升级其他用户的特权。

> [!CAUTION]
> 新用户只能添加到“云所有者组”、“云全局群集管理组”、“云全局存储管理组”、“云全局网络管理组”或“云全局 VM 管理组”。  添加到“管理员”组的用户将被自动删除。  只有服务帐户必须添加到“管理员”组，且服务帐户不得用于登录 vSphere web UI。

## <a name="extend-privilege-escalation"></a>延长特权提升

如果需要额外的时间来完成任务，则可以延长特权提升期限。  选择允许你完成管理任务的其他提升时间间隔。

1. 在 CloudSimple 门户中的“资源” > “私有云”上，选择要延长特权提升时间的私有云。

2. 在“摘要”选项卡的底部附近，单击“延长特权提升”。

    ![延长特权提升](media/de-escalate-private-cloud-privilege.png)

3. 从下拉项中选择提升时间间隔。 查看新的提升结束时间。

4. 单击“保存”以延长时间间隔。

## <a name="de-escalate-privileges"></a>解除提升特权

完成管理任务后，应解除提升特权。  

1. 在 CloudSimple 门户中的“资源” > “私有云”上，选择要解除提升特权的私有云。

2. 单击“解除提升”。

3. 单击 **“确定”** 。

> [!IMPORTANT]
> 为避免任何错误，请注销 vCenter，并在解除提升特权后再次登录。

## <a name="next-steps"></a>后续步骤

* [设置 vCenter 标识源以使用 Active Directory](./set-vcenter-identity.md)
* 安装备份解决方案以[备份工作负荷虚拟机](./backup-workloads-veeam.md)
