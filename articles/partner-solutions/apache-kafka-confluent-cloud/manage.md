---
title: 管理 Confluent 云-Azure 合作伙伴解决方案
description: 本文介绍 Azure 门户上的 Confluent 云的管理。 如何设置单一登录、删除 Confluent 组织并获得支持。
ms.service: partner-services
ms.topic: conceptual
ms.date: 02/08/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: f8a54096ecda4729f7070120a02be3055f933cea
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/09/2021
ms.locfileid: "99989102"
---
# <a name="manage-the-confluent-cloud-resource"></a>管理 Confluent Cloud 资源

本文介绍如何在 Azure 上管理 Confluent 云的 Apache Kafka 实例。 它演示如何设置单一登录 (SSO) ，删除 Confluent 组织，并创建支持请求。

## <a name="single-sign-on"></a>单一登录

若要为你的组织实现 SSO，你的租户管理员可以导入库应用程序。 此步骤是可选的。 有关导入应用程序的信息，请参阅 [快速入门：将应用程序添加到 Azure Active Directory (Azure AD) 租户](../../active-directory/manage-apps/add-application-portal.md)。 当租户管理员导入应用程序时，用户无需显式同意允许 Confluent 云门户的访问。

若要启用 SSO，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 导航到 Confluent 云资源实例的 **概述** 。
1. 选择要 **在 Confluent Cloud 上管理** 的链接。

   :::image type="content" source="media/sso-link.png" alt-text="Confluent 门户单一登录。":::

1. 如果租户管理员未导入用于 SSO 许可的库应用程序，请授予权限和许可。 只有首次访问要 **在 Confluent Cloud 上管理** 的链接时，才需要执行此步骤。
1. 选择用于 Confluent 云门户单一登录的 Azure AD 帐户。
1. 提供同意后，会重定向到 Confluent 云门户。

## <a name="set-up-cluster"></a>设置群集

有关设置群集的信息，请参阅 [在 Confluent Confluent 文档中创建群集](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)。

## <a name="delete-confluent-organization"></a>删除 Confluent 组织

当你不再需要 Confluent 云资源时，请删除 Azure 中的资源和 Confluent Cloud。

删除 Azure 中的资源：

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 选择 "**所有资源**"，并按 Confluent 云资源或 **资源类型** _Confluent 组织_ 的 **名称进行筛选**。 或在 Azure 门户中，搜索资源名称。
1. 在资源的 " **概述** " 中，选择 " **删除**"。

    :::image type="content" source="media/delete-resources-icon.png" alt-text="资源删除图标。":::

1. 若要确认删除，请键入资源名称，然后选择 " **删除**"。

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="提示确认删除资源。":::

若要删除 Confluent 云上的资源，请参阅 [Confluent Cloud 环境的文档-Confluent 文档](https://docs.confluent.io/current/cloud/using/environments.html) 和 [Confluent Cloud 基础知识-Confluent 文档](https://docs.confluent.io/current/cloud/using/cloud-basics.html)。

群集和群集中的所有数据将被永久删除。 如果你的合同包含数据保留条款，则 Confluent 会在 [Confluent 文档](https://www.confluent.io/confluent-cloud-tos)中指定的时间段内保留数据。

按比例按比例计费，直到删除群集。 永久删除群集后，Confluent 会向你发送电子邮件确认。

## <a name="get-support"></a>获取支持

若要将支持请求提交到 Confluent，请联系 [Confluent 支持](https://support.confluent.io) 或通过门户提交请求，如下所示。

> [!NOTE]
> 用户首次登录到 Confluent 支持门户之前，请重置密码。 如果你没有使用 Confluent 云的帐户，请向发送电子邮件以 `cloud-support@confluent.io` 获取进一步的帮助。

在门户中，可以通过 Azure 帮助和支持提交请求，或直接从 Azure 上的 Confluent 的 Apache Kafka 实例中提交请求。

通过 Azure 帮助和支持提交请求：

1. 选择“帮助 + 支持”。
1. 选择 " **创建支持请求**"。
1. 在窗体中，选择 " **技术** " 作为 " **问题类型**"。 选择订阅。 在服务列表中，选择 **"Azure 上的 Confluent"**。

    :::image type="content" source="media/support-request-help.png" alt-text="通过 &quot;帮助&quot; 创建支持请求。":::

若要从资源提交请求，请执行以下步骤：

1. 在 Azure 门户中，选择 Confluent 组织。
1. 在屏幕左侧的菜单中，选择 " **新建支持请求**"。
1. 若要创建支持请求，请选择指向 **Confluent 门户** 的链接。

    :::image type="content" source="media/support-request.png" alt-text="创建实例的支持请求。":::

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅 [Confluent 云解决方案的故障排除 Apache Kafka](troubleshoot.md)。
