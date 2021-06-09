---
title: 在 Azure API for FHIR 中配置导出设置
description: 本文介绍如何在 Azure API for FHIR 中配置导出设置。
author: CaitlinV39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/11/2021
ms.author: cavoeg
ms.openlocfilehash: b245fcaecde13c4a751926e9b7bd639a43b36701
ms.sourcegitcommit: a434cfeee5f4ed01d6df897d01e569e213ad1e6f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/09/2021
ms.locfileid: "111809039"
---
# <a name="configure-export-setting-and-set-up-the-storage-account"></a>配置导出设置并设置存储帐户

适用于 FHIR 的 azure API 支持 $export 命令，该命令允许你将 Azure API for FHIR 帐户中的数据导出到存储帐户。

在 Azure API for FHIR 中配置导出涉及三个步骤：

1. 在 Azure API for FHIR 服务上启用托管标识。
2. 创建 Azure 存储帐户 (如果在) 之前未执行此操作，并将权限分配给存储帐户的 FHIR Azure API。
3. 选择 Azure API for FHIR 中的存储帐户作为导出存储帐户。

## <a name="enabling-managed-identity-on-azure-api-for-fhir"></a>在 Azure API for FHIR 上启用托管标识

配置用于 FHIR 的 Azure API 的第一步是在服务上启用系统范围的托管标识。 有关 Azure 中的托管标识的详细信息，请参阅 [关于 azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

为此，请参阅 Azure API for FHIR 服务，并选择 " **标识**"。 将状态更改为 **On** 将在 Azure API For FHIR Service 中启用托管标识。

![启用托管标识](media/export-data/fhir-mi-enabled.png)

现在，你可以创建一个存储帐户并向我们的服务分配权限，以转到下一步。

## <a name="adding-permission-to-storage-account"></a>向存储帐户添加权限

导出数据中的下一步是分配 Azure API for FHIR 服务的权限，以写入存储帐户。

创建存储帐户后，请在存储帐户中转到 **访问控制 (IAM)** ，然后选择 " **添加角色分配**"。 

有关在 Azure 门户中分配角色的详细信息，请参阅 [Azure 内置角色](../../role-based-access-control/role-assignments-portal.md)。

此处是将角色 [存储 Blob 数据参与者](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-contributor) 添加到服务名称，然后选择 " **保存**"。

![“添加角色分配”页](../../../includes/role-based-access-control/media/add-role-assignment-page.png)

现在，你已准备好在 Azure API for FHIR 中选择存储帐户作为 $export 的默认存储帐户。

## <a name="selecting-the-storage-account-for-export"></a>为 $export 选择存储帐户

最后一步是分配 azure API for FHIR 用于将数据导出到的 Azure 存储帐户。 若要执行此操作，请在 Azure API for FHIR 服务中，选择 " **集成** "，并选择存储帐户。

![FHIR 导出存储](media/export-data/fhir-export-storage.png)

完成最后一步后，便可以使用 $export 命令导出数据了。

> [!Note]
> 仅允许将与用于 FHIR 的 Azure API 相同的订阅中的存储帐户注册为 $export 操作的目标。

有关配置数据库设置、访问控制、启用诊断日志记录以及使用自定义标头向审核日志添加数据的详细信息，请参阅：

>[!div class="nextstepaction"]
>[其他设置](azure-api-for-fhir-additional-settings.md)
