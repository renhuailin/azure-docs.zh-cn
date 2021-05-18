---
title: 排查 Azure Migrate 项目问题
description: 帮助您解决有关创建和管理 Azure Migrate 项目的问题。
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753376"
---
# <a name="troubleshoot-azure-migrate-projects"></a>排查 Azure Migrate 项目问题

本文帮助您解决有关创建和管理 [Azure Migrate](migrate-services-overview.md) 项目的问题。

## <a name="how-to-add-new-project"></a>如何添加新项目？

一个订阅中可以有多个 Azure Migrate 项目。 [了解如何](./create-manage-projects.md)首次创建项目或[添加其他](create-manage-projects.md#create-additional-projects)项目。

## <a name="what-azure-permissions-are-needed"></a>需要哪些 Azure 权限？

你需要订阅中的“参与者”或“所有者”权限才能创建 Azure Migrate 项目。

## <a name="cant-find-a-project"></a>找不到项目

查找现有 Azure Migrate 项目取决于你使用的是最新版本还是旧版本的 Azure Migrate。 [解决方法](create-manage-projects.md#find-a-project)。


## <a name="cant-find-a-geography"></a>找不到地理位置

可以在[公共](migrate-support-matrix.md#supported-geographies-public-cloud)和[政府云](migrate-support-matrix.md#supported-geographies-azure-government)的受支持地理位置中创建 Azure Migrate 项目。

## <a name="what-are-vm-limits"></a>VM 有哪些限制？

一个项目中最多可以评估 35,000 个 VMware VM 或 35,000 个 Hyper-V VM。 一个项目可以同时包含 VMware VM 和 Hyper-V VM，只要不超过评估限制即可。

## <a name="can-i-upgrade-old-project"></a>能否升级旧项目？

无法更新以前版本的 Azure Migrate 中的项目。 您需要[创建一个新项目](./create-manage-projects.md)，并向其中添加工具。

## <a name="cant-create-a-project"></a>无法创建项目

如果尝试创建项目并遇到部署错误：

- 如果是暂时性错误，请尝试再次创建项目。 在“部署”中，单击“重新部署”以重试。 
- 检查订阅中是否有参与者或所有者权限。
- 如果要在新添加的地理位置进行部署，请稍等片刻，然后重试。
- 如果收到错误“请求必须包含用户标识标头”，这可能表示无权访问组织的 Azure Active Directory (Azure AD) 的租户。 在这种情况下：
    - 首次添加到 Azure AD 租户时，你将收到加入租户的电子邮件邀请。
    - 接受被添加到租户的邀请。
    - 如果看不到电子邮件，请与有权访问该租户的用户联系，并请他们向你[重新发送邀请](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users)。
    - 收到邀请电子邮件后，打开电子邮件，然后选择接受邀请的链接。 然后，注销 Azure 门户并再次登录。 （刷新浏览器没有作用。）然后便可以开始创建迁移项目。

## <a name="how-do-i-delete-a-project"></a>如何删除项目

[请按照以下说明](create-manage-projects.md#delete-a-project)删除项目。 请注意，删除项目时，将删除项目中有关已发现的计算机的项目和元数据。

## <a name="added-tools-dont-show"></a>添加的工具没有显示

请确保已选择正确的项目。 在“Azure 迁移中心”>“服务器”或“数据库”中，单击屏幕右上角“迁移项目（更改）”旁边的“更改”。    选择正确的订阅和项目名称，然后单击“确定”。 此页面应刷新已添加的所选项目的工具。

## <a name="next-steps"></a>后续步骤

将[评估](how-to-assess.md)或[迁移](how-to-migrate.md)工具添加到 Azure Migrate 项目。