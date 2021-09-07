---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d79d02054e21ba359637194d00fee9ac01a6d56
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078461"
---
1. 由于要部署 `main` 分支，因此需要将应用服务应用的默认部署分支设置为 `main`（请参阅[更改部署分支](../articles/app-service/deploy-local-git.md#change-deployment-branch)）。 在 Cloud Shell 中，使用 [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) 命令设置 `DEPLOYMENT_BRANCH` 应用设置。 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. 回到本地终端窗口，将 Azure 远程功能添加到本地 Git 存储库。 将 \<deploymentLocalGitUrl-from-create-step> 替换为在[创建 Web 应用](#create-a-web-app)中保存的 Git 远程 URL。

    ```bash
    git remote add azure <deploymentLocalGitUrl-from-create-step>
    ```

1. 使用以下命令推送到 Azure 远程库以部署应用。 当 Git 凭据管理器提示输入凭据时，请确保输入在 **配置部署用户** 中创建的凭据，而不是用于登录到 Azure 门户的凭据。

    ```bash
    git push azure main
    ```

    此命令可能需要花费几分钟时间运行。 运行时，该命令会显示类似于以下示例的信息：
