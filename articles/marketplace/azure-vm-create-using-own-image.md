---
title: 使用自己的映像在 Azure 市场中创建 Azure 虚拟机产品/服务
description: 使用自己的映像将虚拟机产品/服务发布到 Azure 市场。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: krsh
ms.author: krsh
ms.date: 07/22/2021
ms.openlocfilehash: 2ff120fbc95fc96bf39f1648f14a5bdf75ee0db2
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601162"
---
# <a name="create-a-virtual-machine-using-your-own-image"></a>使用自己的映像创建虚拟机

本文介绍如何发布内部构建的虚拟机 (VM) 映像。

## <a name="bring-your-image-into-azure"></a>将映像引入 Azure

将 VHD 上传到 Azure 共享映像库。

1. 在 Azure 门户上，搜索“共享映像库”。
2. 创建或使用现有共享映像库。 建议为要发布到市场中的映像创建单独的共享映像库。
3. 创建或使用现有的映像定义。
4. 选择“创建版本”。
5. 选择区域和映像版本。
6. 如果尚未将 VHD 上传到 Azure 门户，请选择“存储 blob (VHD)”为“源”，然后单击“浏览”。 如果尚未创建“存储帐户”和“存储容器”，则创建一个。 上传 VHD。
7. 选择“查看 + 创建”。 完成验证后，选择“创建”。

> [!TIP]
> 发布者帐户必须拥有“所有者”访问权限才能发布 SIG 映像。 根据需要，执行下一部分“设置适当的权限”中的步骤以授予访问权限。

## <a name="set-the-right-permissions"></a>设置适当的权限

如果合作伙伴中心帐户是托管共享映像库的订阅的所有者，则不需要进一步获取权限。

如果只有订阅的读取访问权限，请使用以下两个选项中的一个。

### <a name="option-one--ask-the-owner-to-grant-owner-permission"></a>选项 1 - 要求所有者授予所有者权限

所有者授予所有者权限的步骤如下：

1. 转到共享映像库 (SIG)。
2. 在左侧窗格中选择“访问控制”(IAM)。
3. 依次选择“添加”、“添加角色分配”。 <br>
    :::image type="content" source="media/create-vm/add-role-assignment.png" alt-text="已显示“添加角色分配”窗口。":::
1. 对于“角色”，请选择“所有者”。 
1. 对于“将访问权限分配给”，请选择“用户、组或服务主体”。 
1. 对于“选择”，输入映像发布者的 Azure 电子邮件。
1. 选择“保存”。

### <a name="option-two--run-a-command"></a>选项 2 - 运行命令

要求所有者运行其中任一命令（在任一情况下，使用创建共享映像库的订阅的 SusbscriptionId）。

```azurecli
az login
az provider register --namespace Microsoft.PartnerCenterIngestion --subscription {subscriptionId}
```

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionId {subscriptionId}
Register-AzResourceProvider -ProviderNamespace Microsoft.PartnerCenterIngestion
```

> [!NOTE]
> 无需生成 SAS URI，因为现在即使不使用 API 也可在合作伙伴中心发布共享映像库 (SIG) 映像。 <br/> <br/>如果要使用 API 进行发布，则需要生成 SAS URI 而不是使用 SIG，请参阅[如何为 VM 映像生成 SAS URI](azure-vm-get-sas-uri.md)。

## <a name="next-steps"></a>后续步骤

- [测试 VM 映像](azure-vm-image-test.md)，以确保它满足 Azure 市场发布要求（可选）。
- 如果不想测试 VM 映像，请登录到[合作伙伴中心](https://go.microsoft.com/fwlink/?linkid=2165935)并发布 SIG 映像。
- 如果在创建基于 Azure 的新 VHD 时遇到问题，请参阅 [Azure 市场 VM 常见问题解答](azure-vm-create-faq.yml)。
