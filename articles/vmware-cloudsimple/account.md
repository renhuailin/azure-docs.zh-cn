---
title: 帐户管理 - Azure VMware Solution by CloudSimple 门户
description: 介绍如何在 Azure VMware Solution by CloudSimple 门户中管理帐户
author: shortpatti
ms.author: v-patsho
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: f215eff7f651bce0c4948417761a305dadfc20ba
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108182635"
---
# <a name="manage-accounts-on-the-azure-vmware-solution-by-cloudsimple-portal"></a>在 Azure VMware Solution by CloudSimple 门户中管理帐户

当你创建 CloudSimple 服务时，它将在 CloudSimple 上创建帐户。 该帐户与服务所在的 Azure 订阅相关联。 订阅中拥有所有者和参与者角色的所有用户都可以访问 CloudSimple 门户。 在帐户页上可以找到与 CloudSimple 服务关联的 Azure 订阅 ID 和租户 ID。

若要在 CloudSimple 门户中管理帐户，请[访问门户](access-cloudsimple-portal.md)，并在侧边菜单中选择“帐户”。

选择“摘要”以查看有关公司的 CloudSimple 配置的信息。 将显示云配置的当前容量，其中包括私有云的数量、总存储、vSphere 群集配置、节点数和计算核心数。 如果当前配置不满足你的所有需求，则包含一个购买其他节点的链接。

## <a name="email-alerts"></a>电子邮件警报

你可以添加任何用户的电子邮件地址，以便在私有云配置发生更改时向其发出通知。

1. 在“其他电子邮件警报”区域中，单击“新建”。
2. 输入电子邮件地址。
3. 按“返回”。  

若要删除条目，请单击“X”。

## <a name="cloudsimple-operator-access"></a>CloudSimple 操作员访问

操作员访问设置允许支持工程师登录你的 CloudSimple 门户，从而帮助你进行故障排除。  默认情况下，此设置处于启用状态。 支持工程师在登录到你的客户帐户后执行的所有操作都将被记录，供你在“活动” > “审核”页上查看。

单击“CloudSimple 操作员访问已启用”切换按钮，打开或关闭访问权限。
