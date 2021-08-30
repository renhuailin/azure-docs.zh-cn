---
title: 使用 Azure CLI 获取访问令牌 - 用于 DICOM 服务的 Azure Healthcare APIs
description: 本文介绍如何使用 Azure CLI 获取用于 DICOM 服务的访问令牌。
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: b48a36a5f9549fef2d484dd79b938c1a8e833e8f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778473"
---
# <a name="get-access-token-for-the-dicom-service-using-azure-cli"></a>使用 Azure CLI 获取用于 DICOM 服务的访问令牌

> [!IMPORTANT]
> Azure Healthcare APIs 目前以预览版提供。 [Microsoft Azure 预览版的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)包含适用于 beta 版、预览版或其他尚未正式发布的 Azure 功能的其他法律条款。

在本文中，你将了解如何使用 Azure CLI 获取用于 DICOM 服务的访问令牌。 [部署 DICOM 服务](deploy-dicom-services-in-azure.md)时，需要配置一组有权访问该服务的用户或服务主体。 如果用户对象 ID 在允许的对象 ID 列表中，则可以使用通过 Azure CLI 获取的令牌来访问该服务。

## <a name="prerequisites"></a>先决条件

在 Azure Cloud Shell 中使用 Bash 环境。


[ ![启动 Azure Cloud Shell。](media/launch-cloud-shell.png) ](media/launch-cloud-shell.png#lightbox)

如果需要，请[安装](/cli/azure/install-azure-cli) Azure CLI 来运行 CLI 参考命令。

* 如果使用的是本地安装，请使用 [az login](/cli/azure/reference-index#az_login) 命令登录到 Azure CLI。 若要完成身份验证过程，请遵循终端中显示的步骤。 有关其他登录选项，请参阅[登录 Azure CLI](/cli/azure/authenticate-azure-cli)。
* 出现提示时，请在首次使用时安装 Azure CLI 扩展。 有关扩展详细信息，请参阅[使用 Azure CLI 的扩展](/cli/azure/azure-cli-extensions-overview)。
* 运行 [az version](/cli/azure/reference-index#az_version) 以查找安装的版本和依赖库。 若要升级到最新版本，请运行 [az upgrade](/cli/azure/reference-index#az_upgrade)。

## <a name="obtain-a-token"></a>获取令牌

DICOM 服务使用 `resource` 或 `Audience`，其 URI 与 DICOM 服务器的 URI (`https://dicom.healthcareapis.azure.com`) 相同。 可以使用以下命令获取令牌并将其存储在变量（名为 `$token`）中：


```Azure CLICopy
Try It
$token=$(az account get-access-token --resource=https://dicom.healthcareapis.azure.com --query accessToken --output tsv)
```

## <a name="use-with-the-dicom-service"></a>用于 DICOM 服务

```Azure CLICopy
Try It
curl -X GET --header "Authorization: Bearer $token"  https://<workspacename-dicomservicename>.dicom.azurehealthcareapis.com/v<version of REST API>/changefeed
```

## <a name="next-steps"></a>后续步骤

在本文中，你了解了如何使用 Azure CLI 获取用于 DICOM 服务的访问令牌。 

>[!div class="nextstepaction"]
>[DICOM 服务概述](dicom-services-overview.md)