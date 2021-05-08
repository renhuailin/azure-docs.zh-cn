---
title: 使用 Azure CLI 注册对等互连服务预览连接
description: 了解如何使用 Azure CLI 注册对等互连服务连接
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/2/2020
ms.author: derekol
ms.openlocfilehash: e7b696ba052b2aca9e14628327c07275845607ad
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94540580"
---
# <a name="register-a-peering-service-connection-by-using-the-azure-cli"></a>使用 Azure CLI 注册对等互连服务连接

Azure 对等互连服务是一种网络服务，可增强客户与 Microsoft 云服务（例如 Microsoft 365、Dynamics 365、软件即服务 (SaaS) 服务、Azure 或可通过公共 Internet 访问的任何 Microsoft 服务）建立的连接。 在本文中，你将了解如何使用 Azure CLI 注册对等互连服务连接。

- 本文需要 Azure CLI 2.0.28 或更高版本。 运行 [az version](/cli/azure/reference-index#az_version) 以查找安装的版本和依赖库。 若要升级到最新版本，请运行 [az upgrade](/cli/azure/reference-index#az_upgrade)。

## <a name="prerequisites"></a>先决条件 

必须满足以下条件：

### <a name="azure-account"></a>Azure 帐户

必须具有使用中的有效 Microsoft Azure 帐户。 设置对等互连服务连接时需要使用此帐户。 对等互连服务是 Azure 订阅中的一种资源。

### <a name="connectivity-provider"></a>连接服务提供商

可以与 Internet 服务提供商或 Internet 交换合作伙伴合作，以获取对等互连服务，从而将你的网络连接到 Microsoft 网络。

请确保连接服务提供商与 Microsoft 是合作伙伴。

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- 本文需要 Azure CLI 2.0.28 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

### <a name="1-select-your-subscription"></a>1.选择订阅

选择要为其注册对等互连服务连接的订阅。

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

如果尚未有资源组，则在注册对等互连服务连接之前，必须先创建一个资源组。 可以运行以下命令来创建资源组：

```azurecli-interactive
az group create -n MyResourceGroup -l "West US"
```

### <a name="2-register-your-subscription-with-the-resource-provider-and-feature-flag"></a>2.将订阅注册到资源提供程序和功能标志

在继续执行使用 Azure CLI 注册对等互连服务连接的步骤之前，请使用 Azure CLI 将订阅注册到资源提供程序和功能标志。 此处指定了 Azure CLI 命令：

```azurecli-interactive

az feature register --namespace Microsoft.Peering --name AllowPeeringService

```

### <a name="3-register-the-peering-service-connection"></a>3.注册对等互连服务连接

使用以下命令集通过 Azure CLI 注册对等互连服务连接。 此示例注册名为 myPeeringService 的对等互连服务连接。

```azurecli-interactive
az peering service create : Create peering service\
  --location -l \
  --name myPeeringService\
  --resource-group -g MyResourceGroup\
  --peering-service-location\
  --peering-service-provider\
  --tags
```

### <a name="4-register-the-prefix"></a>4.注册前缀

执行以下命令，通过 Azure CLI 注册连接服务提供商提供的前缀。 此示例注册名为 myPrefix 的前缀。

```azurecli-interactive
az peering service prefix create \
  --name  myPrefix\
  --peering-service-name myPeeringService\
  --resource-group  -g myResourceGroup\
```

## <a name="next-steps"></a>后续步骤

- 若要了解对等互连服务连接的详细信息，请参阅[对等互连服务连接](connection.md)。
- 若要了解对等互连服务连接遥测数据，请参阅[对等互连服务连接遥测数据](connection-telemetry.md)。
- 若要度量遥测，请参阅[度量连接遥测](measure-connection-telemetry.md)。
- 若要使用 Azure PowerShell 注册连接，请参阅[注册对等互连服务连接 - Azure PowerShell](powershell.md)。
- 若要使用 Azure 门户注册连接，请参阅[注册对等互连服务连接 - Azure 门户](azure-portal.md)。
