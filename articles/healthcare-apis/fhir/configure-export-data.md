---
title: 在中配置导出Azure API for FHIR
description: 本文介绍如何在中配置导出Azure API for FHIR
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: matjazl
ms.openlocfilehash: 20c08851d082702342b43bad12409cc71a0cae46
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062013"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>配置导出设置并设置存储帐户

Azure API for FHIR支持 $export 命令，该命令允许将数据从 Azure API for FHIR帐户导出到存储帐户。

在数据中配置导出涉及三Azure API for FHIR：

1. 在服务上启用Azure API for FHIR标识。
2. 如果在创建之前 (Azure 存储帐户，) 将权限分配给Azure API for FHIR帐户。
3. 选择存储帐户中的存储Azure API for FHIR导出存储帐户。

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>启用托管标识Azure API for FHIR

配置导出Azure API for FHIR的第一步是在服务上启用系统范围的托管标识。 有关 Azure 中的托管标识详细信息，请参阅 [关于 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

为此，请转到服务Azure API for FHIR并选择"标识 **"。** 将状态更改为 **"打开** "将在服务中启用Azure API for FHIR标识。

![启用托管标识](media/export-data/fhir-mi-enabled.png)

现在，可以通过创建存储帐户并将权限分配给我们的服务，进入下一步。

## <a name="adding-permission-to-storage-account"></a>向存储帐户添加权限

导出数据中的下一步是为服务分配Azure API for FHIR写入存储帐户的权限。

创建存储帐户后，转到存储帐户中的"访问控制 (**IAM**) ，然后选择"添加 **角色分配"。** 

有关在门户中分配角色Azure 门户，请参阅 [Azure 内置角色](../../role-based-access-control/role-assignments-portal.md)。

在此处，你将将角色"存储 Blob [数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor)"添加到我们的服务名称，然后选择"保存 **"。**

![“添加角色分配”页](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

现在，你已准备好在 Azure API for FHIR 中选择存储帐户作为存储帐户$export。

## <a name="selecting-the-storage-account-for-export"></a>为 $export 选择存储帐户

最后一步是分配 azure API for FHIR 用于将数据导出到的 Azure 存储帐户。 若要执行此操作，请在 Azure API for FHIR 服务中，选择 " **集成** "，并选择存储帐户。

![FHIR 导出存储](media/export-data/fhir-export-storage.png)

完成最后一步后，便可以使用 $export 命令导出数据了。

> [!Note]
> 仅允许将与用于 FHIR 的 Azure API 相同的订阅中的存储帐户注册为 $export 操作的目标。

有关配置数据库设置、访问控制、启用诊断日志记录以及使用自定义标头向审核日志添加数据的详细信息，请参阅：

>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)
