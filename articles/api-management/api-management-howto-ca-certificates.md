---
title: 添加自定义 CA 证书 - Azure API 管理 | Microsoft Docs
description: 了解如何在 Azure API 管理中添加自定义 CA 证书。 还可查看说明了解如何删除证书。
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: how-to
ms.date: 06/01/2021
ms.author: danlep
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e92d4a6c81376a9dbd004ac7fee978fa4d67e9a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128628812"
---
# <a name="how-to-add-a-custom-ca-certificate-in-azure-api-management"></a>如何在 Azure API 管理中添加自定义 CA 证书

Azure API 管理允许在受信任的根证书和中间证书存储中的计算机上安装 CA 证书。 如果服务需要自定义 CA 证书，则应使用此功能。

本文介绍如何在 Azure 门户中管理 Azure API 管理服务实例的 CA 证书。 例如，如果使用自签名客户端证书，可以将自定义受信任的根证书上传到 API 管理。 

上传到 API 管理的 CA 证书只能用于托管 API 管理网关的证书验证。 如果使用[自承载网关](self-hosted-gateway-overview.md)，请了解如何[为自承载网关创建自定义 CA](#create-custom-ca-for-self-hosted-gateway)，本文稍后将作介绍。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="upload-a-ca-certificate"></a><a name="step1"> </a>上传 CA 证书

:::image type="content" source="media/api-management-howto-ca-certificates/00.png" alt-text="Azure 门户中的 CA 证书":::

请按照以下步骤来上传新的 CA 证书。 如果尚未创建 API 管理服务实例，请参阅教程[创建 API 管理服务实例](get-started-create-service-instance.md)。

1. 在 Azure 门户中导航到 Azure API 管理服务实例。

1. 在菜单的“安全”下，选择“证书”>“CA 证书”>“+ 添加”。

1. 浏览证书 .cer 文件并选定证书存储。 只需要公钥，因此密码是可选的。

    :::image type="content" source="media/api-management-howto-ca-certificates/02.png" alt-text="在 Azure 门户中添加 CA 证书"::: 

1. 选择“保存”。  此操作可能需要几分钟的时间。

> [!NOTE]
> 也可以使用 `New-AzApiManagementSystemCertificate` PowerShell 命令上传 CA 证书。

## <a name="delete-a-ca-certificate"></a><a name="step1a"> </a>删除 CA 证书

选择证书，然后选择上下文菜单 (...) 中的“删除”。

## <a name="create-custom-ca-for-self-hosted-gateway"></a>为自承载网关创建自定义 CA 

如果使用[自承载网关](self-hosted-gateway-overview.md)，则不支持使用上传到 API 管理服务的 CA 根证书验证服务器和客户端证书。 若要建立信任，请配置特定的客户端证书，使其被网关作为一个自定义的证书颁发机构所信任。

使用[网关证书颁发机构](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority) REST API 为自承载网关创建和管理自定义 CA。 创建自定义 CA 的步骤：

1. 向 API 管理实例[添加证书](api-management-howto-mutual-certificates.md) .pfx 文件。
1. 使用[网关证书颁发机构 - 创建或更新](/rest/api/apimanagement/2021-01-01-preview/gateway-certificate-authority/create-or-update) REST API，将证书与自管理网关关联。

[Upload a CA certificate]: #step1
[Delete a CA certificate]: #step1a
