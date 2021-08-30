---
title: 管理 Confluent Cloud - Azure 合作伙伴解决方案
description: 本文介绍如何在 Azure 门户上管理 Confluent Cloud。 如何设置单一登录、删除 Confluent 组织并获得支持。
ms.service: partner-services
ms.topic: conceptual
ms.date: 06/07/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: c1e53382b6f399bccac53a75595eda4e61a915a4
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112520979"
---
# <a name="manage-the-confluent-cloud-resource"></a>管理 Confluent Cloud 资源

本文介绍如何在 Azure 上管理 Apache Kafka for Confluent Cloud 实例。 演示如何设置单一登录 (SSO) 和删除 Confluent 组织。

## <a name="single-sign-on"></a>单一登录

若要为组织实现 SSO，租户管理员可以导入库应用程序。 此步骤是可选的。 有关导入应用程序的信息，请参阅[快速入门：向 Azure Active Directory (Azure AD) 租户添加应用程序](../../active-directory/manage-apps/add-application-portal.md)。 租户管理员导入应用程序时，用户无需显式同意允许访问 Confluent Cloud 门户。

若要启用 SSO，请执行下列步骤：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 导航到 Confluent Cloud 资源的实例的“概述”。
1. 选择“在 Confluent Cloud 上管理”的链接。

   :::image type="content" source="media/manage/sso-link.png" alt-text="Confluent 门户单一登录。":::

1. 如果租户管理员未导入库应用程序进行 SSO 许可，请授予权限和许可。 仅需要在首次访问“在 Confluent Cloud 上管理”链接时执行此步骤。

   :::image type="content" source="media/manage/permissions-requested.png" alt-text="授予权限。":::

1. 选择用于单一登录到 Confluent Cloud 门户的 Azure AD 帐户。
1. 同意后，会重定向到 Confluent Cloud 门户。

## <a name="set-up-cluster"></a>设置群集

有关设置群集的信息，请参阅[在 Confluent Cloud 中创建群集 - Confluent 文档](https://docs.confluent.io/cloud/current/clusters/create-cluster.html)。

## <a name="delete-confluent-organization"></a>删除 Confluent 组织

如果不再需要 Confluent Cloud 资源，请删除 Azure 和 Confluent Cloud 中的资源。

### <a name="portal"></a>[Portal](#tab/azure-portal)

若要删除 Azure 中的资源，请执行以下操作：

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 选择 Confluent Cloud 资源或“资源类型”Confluent 组织的“所有资源”和“按名称筛选”。 或者，在 Azure 门户中，搜索资源名称。
1. 在资源的“概述”中，选择“删除”。

    :::image type="content" source="media/delete-resources-icon.png" alt-text="“资源删除”图标。":::

1. 若要确认删除，请键入资源名称，然后选择“删除”。

    :::image type="content" source="media/delete-resources-prompt.png" alt-text="提示确认资源删除。":::

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

首先为 Azure CLI 准备环境：

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

登录后，使用 [az confluent organization create](/cli/azure/confluent#az_confluent_organization_delete) 命令按名称删除组织资源：

```azurecli
az confluent organization delete --name "myOrganization" --resource-group "myResourceGroup"
```

或者按资源 ID 删除：

```azurecli
az confluent organization delete --id "/subscriptions/{SubID}/resourceGroups/{myResourceGroup}/providers/Microsoft.Confluent/organizations/{myOrganization}"
```

---

若要删除 Confluent Cloud 上的资源，请参阅 [Confluent Cloud 环境 - Confluent 文档](https://docs.confluent.io/current/cloud/using/environments.html)和 [Confluent Cloud 基础知识 - Confluent 文档](https://docs.confluent.io/current/cloud/using/cloud-basics.html)的文档。

将永久删除群集和群集中的所有数据。 如果合同包括数据保留条款，则 Confluent 会在[服务条款 - Confluent 文档](https://www.confluent.io/confluent-cloud-tos)中指定的时间段内保留数据。

在删除群集之前，会按比例计算使用费。 永久删除群集后，Confluent 会向你发送一封电子邮件确认函。

## <a name="next-steps"></a>后续步骤

有关故障排除的帮助，请参阅[对 Apache Kafka for Confluent Cloud 解决方案进行故障排除](troubleshoot.md)。

如果需要联系支持人员，请参阅[获取对 Confluent 云资源的支持](get-support.md)。
