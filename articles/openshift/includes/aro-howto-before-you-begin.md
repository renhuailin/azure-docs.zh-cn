---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: 1fded0ad08af4b1e5d915e32e09087c1a78bd318
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/15/2021
ms.locfileid: "107520506"
---
### <a name="create-the-cluster"></a>创建群集

按照教程[创建 Azure Red Hat OpenShift 群集](../tutorial-create-cluster.md)操作 如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Azure Red Hat OpenShift 群集，请使用 [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)（OpenShift 命令行客户端）。

> [!NOTE]
> 建议在 [Azure Cloud Shell](https://shell.azure.com/) 上[安装 OpenShift 命令行](../tutorial-connect-cluster.md)，并使用它来执行以下所有命令行操作。 从 shell.azure.com 或单击以下链接启动 shell：
>
> [![嵌入式启动](https://docs.microsoft.com/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "启动 Azure Cloud Shell")](https://shell.azure.com/bash)

按照教程操作，使用 Web 控制台和 OpenShift CLI 安装 CLI、检索群集凭据并[连接到群集](../tutorial-connect-cluster.md)。

登录后，应会显示一条消息，指示你正在使用 `default` 项目。

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```
