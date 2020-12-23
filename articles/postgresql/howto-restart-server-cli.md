---
title: 重新启动服务器-Azure CLI-Azure Database for PostgreSQL-单一服务器
description: 本文介绍了如何使用 Azure CLI 重启 Azure Database for PostgreSQL - 单一服务器
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: f9881a4517f77587cecb2dcd04befaddb523965b
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94647641"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-cli"></a>使用 Azure CLI 重启 Azure Database for PostgreSQL - 单一服务器
本主题介绍如何重启 Azure Database for PostgreSQL 服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可以处理缩放 vCores 等先前请求的操作。
 
完成重启所需的时间取决于 PostgreSQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

## <a name="prerequisites"></a>必备条件
若要完成本操作说明指南：
- 创建 [Azure Database for PostgreSQL 服务器](quickstart-create-server-up-azure-cli.md)。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="restart-the-server"></a>重启服务器

使用以下命令重启服务器：

```azurecli-interactive
az postgres server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

了解[如何在 Azure Database for PostgreSQL 中设置参数](howto-configure-server-parameters-using-cli.md)
