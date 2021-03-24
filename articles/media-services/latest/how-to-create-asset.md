---
title: 将内容上传到资产 CLI
description: 本主题中的 Azure CLI 脚本演示如何创建媒体服务资产供上传内容使用。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ''
ms.service: media-services
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/16/2021
ms.author: inhenkel
ms.custom: devx-track-azurecli
ms.openlocfilehash: 3923394e76d5ce4001d3652ae9cbc263df08e4e2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "100545609"
---
# <a name="create-an-asset"></a>创建资产

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

本文介绍如何创建媒体服务资产。  你将使用资产来保存用于编码和流式传输的媒体内容。  若要了解有关媒体服务资产的详细信息，请参阅 [Azure 媒体服务 v3 中的资产](assets-concept.md)

## <a name="prerequisites"></a>先决条件

按照[创建媒体服务帐户](./create-account-howto.md)中的步骤操作，创建所需的媒体服务帐户和资源组以创建资产。

## <a name="methods"></a>方法

## <a name="cli"></a>[CLI](#tab/cli/)

[!INCLUDE [Create an asset with CLI](./includes/task-create-asset-cli.md)]

## <a name="example-script"></a>示例脚本

[!code-azurecli-interactive[main](../../../cli_scripts/media-services/create-asset/Create-Asset.sh "Create an asset")]

## <a name="rest"></a>[REST](#tab/rest/)

### <a name="using-rest"></a>使用 REST

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-rest.md)]

### <a name="using-curl"></a>使用 cURL

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-curl.md)]

## <a name="net"></a>[.NET](#tab/net/)

[!INCLUDE [media-services-cli-instructions.md](./includes/task-create-asset-dotnet.md)]

---

## <a name="next-steps"></a>后续步骤

[媒体服务概述](media-services-overview.md)
