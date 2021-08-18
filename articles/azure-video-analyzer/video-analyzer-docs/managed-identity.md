---
title: 具有 Azure 视频分析器的托管标识
description: 本主题说明如何使用具有 Azure 视频分析器的托管标识。
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 073f9ee8d039435b4cf8c7dac59831a2870b2ce0
ms.sourcegitcommit: 3941df51ce4fca760797fa4e09216fcfb5d2d8f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2021
ms.locfileid: "114601429"
---
# <a name="managed-identity"></a>托管标识

开发人员面临的一个共同挑战是如何管理密码和凭据，以确保不同服务之间的通信安全。 在 Azure 上，托管标识可为 Azure Active Directory (Azure AD) 中的 Azure 资源提供标识并使用它来获取 Azure AD 令牌，从而使开发人员无需管理凭据。

创建 Azure 视频分析器帐户时，必须将一个 Azure 存储帐户与其关联。 如果使用视频分析器录制某个摄像机上的实时视频，则该数据将以 Blob 形式存储在存储帐户的容器中。 必须使用托管标识向视频分析器帐户授予对存储帐户的适当访问权限，如下所示。


## <a name="user-assigned-managed-identity-for-video-analyzer"></a>用于视频分析器的用户分配的托管标识

1. [创建用户分配的托管标识](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

1. 创建 Azure 存储帐户

   [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](./includes/note-account-storage-same-subscription.md)]

1. 将用于上述存储帐户的[存储 Blob 数据参与者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)和[读取者](../../role-based-access-control/built-in-roles.md#reader)角色添加到托管标识。

1. 创建视频分析器帐户，将用户分配的托管标识和存储帐户作为值提供给相关属性。

然后视频分析器可以使用托管标识代表你访问存储帐户。

请参阅[此文](create-video-analyzer-account.md)，了解使用 Azure 门户来实现上述操作的示例。


## <a name="next-steps"></a>后续步骤

若要详细了解托管标识可以为你和你的 Azure 应用程序执行哪些操作，请参阅 [Azure AD 托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。
