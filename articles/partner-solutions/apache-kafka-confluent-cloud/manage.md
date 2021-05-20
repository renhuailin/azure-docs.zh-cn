---
title: 管理 Confluent Cloud - Azure 合作伙伴解决方案
description: 本文介绍如何在 Azure 门户上管理 Confluent Cloud。 如何设置单一登录、删除 Confluent 组织并获得支持。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "99989102"
---
# <a name="manage-the-confluent-cloud-resource"></a>管理 Confluent Cloud 资源

本文介绍如何在 Azure 上管理 Apache Kafka for Confluent Cloud 实例。 它演示如何设置单一登录 (SSO)、删除 Confluent 组织并创建支持请求。

## <a name="single-sign-on"></a>单一登录

若要为组织实现 SSO，租户管理员可以导入库应用程序。 此步骤是可选的。 有关导入应用程序的信息，请参阅[快速入门：向 Azure Active Directory (Azure AD) 租户添加应用程序](../../active-directory/manage-apps/add-application-portal.md)。 租户管理员导入应用程序时，用户无需显式同意允许访问 Confluent Cloud 门户。

若要启用 SSO，请执行下列步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到 Confluent Cloud 资源的实例的“概述”。
1. 选择“在 Confluent Cloud 上管理”的链接。

   :::image type="content" source="media/sso-link.png" alt-text="Confluent 门户单一登录。":::

1. 如果租户管理员未导入库应用程序进行 SSO 许可，请授予权限和许可。 仅需要在首次访问“在 Confluent Cloud 上管理”链接时执行此步骤。
1. 选择用于单一登录到 Confluent Cloud 门户的 Azure AD 帐户。
1. 同意后，会重定向到 Confluent Cloud 门户。

## <a name="set-up-cluster"></a>设置群集

有关设置群集的信息，请参阅[在 Confluent Cloud 中创建群集 - Confluent 文档](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)。

## <a name="delete-confluent-organization"></a>删除 Confluent 组织

如果不再需要 Confluent Cloud 资源，请删除 Azure 和 Confluent Cloud 中的资源。

若要删除 Azure 中的资源，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择 Confluent Cloud 资源或“资源类型”Confluent 组织的“所有资源”和“按名称筛选”。 或者，在 Azure 门户中，搜索资源名称。
1. 在资源的“概述”中，选择“删除”。

    :::image type="content" source="media/delete-resources-icon.png" alt-text="“资源删除”图标。":::

1. 若要确认删除，请键入资源名称，然后选择“删除”。

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="提示确认资源删除。":::

若要删除 Confluent Cloud 上的资源，请参阅 [Confluent Cloud 环境 - Confluent 文档](https://docs.confluent.io/current/cloud/using/environments.html)和 [Confluent Cloud 基础知识 - Confluent 文档](https://docs.confluent.io/current/cloud/using/cloud-basics.html)的文档。

将永久删除群集和群集中的所有数据。 如果合同包括数据保留条款，则 Confluent 会在[服务条款 - Confluent 文档](https://www.confluent.io/confluent-cloud-tos)中指定的时间段内保留数据。

在删除群集之前，会按比例计算使用费。 永久删除群集后，Confluent 会向你发送一封电子邮件确认函。

## <a name="get-support"></a>获取支持

若要向 Confluent 提交支持请求，请联系 [Confluent 支持人员](https://support.confluent.io)，或通过门户提交请求，如下所示。

> [!NOTE]
> 若是首次使用的用户，请重置密码，然后再登录到 Confluent 支持门户。 如果没有 Confluent Cloud 帐户，请发送电子邮件至 `cloud-support@confluent.io` 以寻求进一步的帮助。

在门户中，可以通过 Azure 帮助和支持提交请求，也可以直接从 Azure 上的 Apache Kafka for Confluent Cloud 的实例提交请求。

若要通过 Azure 帮助和支持提交请求，请执行以下操作：

1. 选择“帮助 + 支持”。
1. 选择“创建支持请求”。
1. 在窗体中，为“问题类型”选择“技术”。 选择订阅。 在服务列表中，选择“Azure 上的 Confluent”。

    :::image type="content" source="media/support-request-help.png" alt-text="从帮助创建支持请求。":::

若要从资源提交请求，请执行以下步骤：

1. 在 Azure 门户中，选择 Confluent 组织。
1. 在屏幕左侧的菜单中，选择“新建支持请求”。
1. 若要创建支持请求，请选择“Confluent 门户”的链接。

    :::image type="content" source="media/support-request.png" alt-text="从实例创建支持请求。":::

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。
