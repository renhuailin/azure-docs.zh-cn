---
title: 在 FHIR 服务中配置导出设置
description: 本文介绍如何在 FHIR 服务中配置导出设置
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: 327a36714b822669cbc41fc8b8fb195bed4872ed
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778488"
---
# <a name="configure-export-settings-and-set-up-a-storage-account"></a>配置导出设置并设置存储帐户

FHIR 服务支持 $export 命令，该命令允许你将数据从 FHIR 服务帐户导出到存储帐户。

在 FHIR 服务中配置导出涉及三个步骤：

1. 在 FHIR 服务上启用托管标识。
2. 创建 Azure 存储帐户（如果尚未这样做），并为 FHIR 服务分配对该存储帐户的权限。
3. 在 FHIR 服务中选择该存储帐户作为导出存储帐户。

## <a name="enabling-managed-identity-on-fhir-service"></a>在 FHIR 服务上启用托管标识

配置 FHIR 服务进行导出的第一步是在服务上启用系统范围的托管标识。 有关 Azure 中的托管标识的详细信息，请参阅[关于 Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

为此，请转到 FHIR 服务并选择“标识”。 将状态更改为“开”会在 FHIR 服务中启用托管标识。

![启用托管标识](media/export-data/fhir-mi-enabled.png)

现在，可以通过创建存储帐户来进入下一步，并且向我们的服务分配权限。

## <a name="adding-permission-to-storage-account"></a>添加对存储帐户的权限

导出数据的下一步是为 FHIR 服务分配对存储帐户的写入权限。

创建存储帐户后，请转到存储帐户中的“访问控制(IAM)”，然后选择“添加角色分配”。  

若要详细了解如何在 Azure 门户中分配角色，请参阅 [Azure 内置角色](../../role-based-access-control/role-assignments-portal.md)。

在这里，需将[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)角色添加到我们的服务名称，然后选择“保存”。

![“添加角色分配”页](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

现在可以在 FHIR 服务中选择该存储帐户作为 $export 操作的默认存储帐户了。

## <a name="selecting-the-storage-account-for-export"></a>选择 $export 操作的存储帐户

最后一步是分配供 FHIR 服务用于导出数据的 Azure 存储帐户。 为此，请转到 FHIR 服务中的“集成”并选择该存储帐户。

![FHIR 导出存储](media/export-data/fhir-export-storage.png)

完成这最后一个步骤后，现在可以使用 $export 命令来导出数据了。

> [!Note]
> 只允许将与 FHIR 服务相同的订阅中的存储帐户注册为 $export 操作的目标。