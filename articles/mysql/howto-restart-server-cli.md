---
title: 重启服务器 - Azure CLI - Azure Database for MySQL
description: 本文介绍了如何使用 Azure CLI 重启 Azure Database for MySQL 服务器。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 76ee5e413d325672ac1e87f62bbfa2996ab44831
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/30/2021
ms.locfileid: "122652602"
---
# <a name="restart-azure-database-for-mysql-server-using-the-azure-cli"></a>使用 Azure CLI 重启 Azure Database for MySQL 服务器

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]
本主题介绍如何重启 Azure Database for MySQL 服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可能正在处理先前请求的操作（例如缩放 vCore）。

完成重启所需的时间取决于 MySQL 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

## <a name="prerequisites"></a>先决条件

若要完成本操作说明指南：

- 需要 [Azure Database for MySQL 服务器](quickstart-create-server-up-azure-cli.md)。
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本 2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。

## <a name="restart-the-server"></a>重启服务器

使用以下命令重启服务器：

```azurecli-interactive
az mysql server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

了解[如何在 Azure Database for MySQL 中设置参数](howto-configure-server-parameters-using-cli.md)
