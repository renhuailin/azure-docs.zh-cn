---
author: sabbour
ms.author: asabbour
ms.date: 4/5/2020
ms.openlocfilehash: d888d4df3f578bf88f8f38cb979ed70f681c64e8
ms.sourcegitcommit: 4a54c268400b4158b78bb1d37235b79409cb5816
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2021
ms.locfileid: "108157612"
---
### <a name="create-the-cluster"></a>创建群集

按照教程[创建 Azure Red Hat OpenShift 群集](../tutorial-create-cluster.md)操作 如果选择在本地安装并使用 CLI，本教程要求运行 Azure CLI 2.6.0 或更高版本。 运行 `az --version` 即可查找版本。 如果需要进行安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli)。

### <a name="connect-to-the-cluster"></a>连接到群集

若要管理 Azure Red Hat OpenShift 群集，请使用 [oc](https://docs.openshift.com/container-platform/4.7/cli_reference/openshift_cli/getting-started-cli.html)（OpenShift 命令行客户端）。

> [!NOTE]
> 建议在 [Azure Cloud Shell](https://shell.azure.com/) 上[安装 OpenShift 命令行](../tutorial-connect-cluster.md)，并使用它来执行以下所有命令行操作。 从 shell.azure.com 或单击以下链接启动 shell：
>
> [![嵌入式启动](/azure/includes/media/cloud-shell-try-it/hdi-launch-cloud-shell.png "启动 Azure Cloud Shell")](https://shell.azure.com/bash)

按照教程操作，使用 Web 控制台和 OpenShift CLI 安装 CLI、检索群集凭据并[连接到群集](../tutorial-connect-cluster.md)。

登录后，应会显示一条消息，指示你正在使用 `default` 项目。

```output
Login successful.

You have access to 61 projects, the list has been suppressed. You can list all projects with 'oc projects'

Using project "default".
```