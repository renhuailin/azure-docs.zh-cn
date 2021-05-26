---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 05/12/2021
ms.author: cephalin
ms.openlocfilehash: f400c7dbfcf3307ccc283a8e11c5d905dd152b85
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110371801"
---
## <a name="add-azure-cli-extensions"></a>添加 Azure CLI 扩展

在 [Azure Cloud Shell](../articles/cloud-shell/quickstart.md) 中启动 Bash 环境。

[![在新窗口中启动 Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com)

由于这些 CLI 命令尚未成为核心 CLI 集的一部分，因此请通过以下命令添加它们：

```azurecli-interactive
az extension add --upgrade --yes --name customlocation
az extension remove --name appservice-kube
az extension add --yes --source "https://aka.ms/appsvc/appservice_kube-latest-py2.py3-none-any.whl"
```