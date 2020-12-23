---
title: 重新启动服务器-Azure CLI-Azure Database for MariaDB
description: 本文介绍了如何使用 Azure CLI 重启 Azure Database for MariaDB 服务器。
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 3/18/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8ae69adfe83b871eb29c85fc4d03e817026ec006
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2020
ms.locfileid: "94541362"
---
# <a name="restart-azure-database-for-mariadb-server-using-the-azure-cli"></a>使用 Azure CLI 重启 Azure Database for MariaDB 服务器
本主题介绍了如何重启 Azure Database for MariaDB 服务器。 出于维护原因，可能需要重启服务器，这会在服务器执行操作时导致短暂中断。

如果服务处于繁忙状态，则会阻止重启服务器。 例如，服务可以处理缩放 vCores 等先前请求的操作。

完成重启所需的时间取决于 MariaDB 恢复过程。 若要减少重启时间，建议在重启之前尽量减少服务器上发生的活动量。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>必备条件

若要完成本操作说明指南：

- 需要 [Azure Database for MariaDB 服务器](quickstart-create-mariadb-server-database-using-azure-cli.md)。
 
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

- 本文需要 Azure CLI 版本2.0 或更高版本。 如果使用 Azure Cloud Shell，则最新版本已安装。


## <a name="restart-the-server"></a>重启服务器

使用以下命令重启服务器：

```azurecli-interactive
az mariadb server restart --name mydemoserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>后续步骤

了解[如何在 Azure Database for MariaDB 中设置参数](howto-configure-server-parameters-cli.md)
