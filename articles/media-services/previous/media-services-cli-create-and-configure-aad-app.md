---
title: 使用 Azure CLI 创建 Azure AD 应用并配置为访问 Azure 媒体服务 API | Microsoft Docs
description: 本主题展示了如何使用 Azure CLI 创建 Azure AD 应用，并将它配置为访问 Azure 媒体服务 API。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: e7f964fa6c8975bfb765feec295dba1de642b09f
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/27/2021
ms.locfileid: "114706569"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>使用 Azure CLI 创建 Azure AD 应用并将其配置为访问媒体服务 API

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

[!INCLUDE [v2 deprecation notice](../latest/includes/v2-deprecation-notice.md)]

本主题展示了如何使用 Azure CLI 创建 Azure Active Directory (Azure AD) 应用程序和服务主体，以便访问 Azure 媒体服务资源。 

## <a name="prerequisites"></a>必备条件

- 一个 Azure 帐户。 有关详细信息，请参阅 [Azure 免费试用](https://azure.microsoft.com/pricing/free-trial/)。 
- 一个媒体服务帐户。 有关详细信息，请参阅[利用 Azure 门户创建 Azure 媒体服务帐户](media-services-portal-create-account.md)。

## <a name="use-the-azure-cloud-shell"></a>使用 Azure Cloud Shell

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 在门户的顶部导航窗格中启动 Cloud Shell。

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

有关详细信息，请参阅 [Azure Cloud Shell 概述](../../cloud-shell/overview.md)。

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>使用 Azure CLI 创建 Azure AD 应用并配置为访问媒体帐户
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

例如：

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

在此示例中，作用域  是媒体服务帐户的完整资源路径。 不过，作用域  可以为任意级别。

例如，作用域可以为下列级别之一：
 
* 订阅  级别。
* 资源组  级别。
* 资源  级别（例如，媒体帐户）。

有关详细信息，请参阅[使用 Azure CLI 创建 Azure 服务主体](/cli/azure/create-an-azure-service-principal-azure-cli)

另请参阅[使用 Azure CLI 添加或删除 Azure 角色分配](../../role-based-access-control/role-assignments-cli.md)。 

## <a name="next-steps"></a>后续步骤

开始[将文件上传到帐户](media-services-portal-upload-files.md)。
