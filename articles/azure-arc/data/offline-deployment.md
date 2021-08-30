---
title: 脱机部署
description: 脱机部署使你能够从专用容器注册表（而不是从 Microsoft Container Registry）拉取容器映像。
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e81ccc8ef133cdf7c454a7a50610a1bfdf2be442
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731263"
---
# <a name="offline-deployment-overview"></a>脱机部署概述

通常，在创建 Azure Arc 数据控制器、SQL 托管实例和 PostgreSQL 超大规模服务器组时使用的容器映像是直接从 Microsoft Container Registry (MCR) 提取的。 在某些情况下，要对其进行部署的环境无法连接到 Microsoft Container Registry。  对于这样的情况，可以使用一台能够访问 Microsoft Container Registry 的计算机拉取容器映像，然后标记容器映像并将其推送到一个专用容器注册表，该注册表可以从想要部署已启用 Azure Arc 的数据服务的环境中连接 。

由于为已启用 Azure Arc 的数据服务提供了每月更新并且有大量的容器映像，最好使用脚本执行将容器映像提取、标记并推送到专用容器注册表的过程。  此脚本可以自动执行，也可以手动运行。

可以在 Azure Arc GitHub 存储库中找到[示例脚本](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/scripts/pull-and-push-arc-data-services-images-to-private-registry.py)。

> [!NOTE]
> 此脚本需要安装 python 和 [Docker CLI](https://docs.docker.com/install/)。

脚本将以交互式方式提示你输入以下信息。  或者，如果你希望运行脚本而不显示交互式提示，则可以在运行脚本之前设置相应的环境变量。

|Prompt|环境变量|备注|
|---|---|---|
|提供源容器注册表 - 按 ENTER 可使用 `mcr.microsoft.com`|SOURCE_DOCKER_REGISTRY|通常，你将从 Microsoft Container Registry 中拉取映像，但如果使用不同的注册表参加个人预览版，则可以使用预览计划中提供给你的信息。|
|提供源容器注册表存储库 - 按 ENTER 可使用 `arcdata`：|SOURCE_DOCKER_REPOSITORY|如果要从 Microsoft Container Registry 进行拉取，存储库将为 `arcdata`。|
|提供源容器注册表的用户名 - 按 ENTER 可使用 none：|SOURCE_DOCKER_USERNAME|仅在从需要登录的源中拉取容器映像时才提供值。  Microsoft Container Registry 不需要进行登录。|
|提供源容器注册表的密码 - 按 ENTER 可使用 none：|SOURCE_DOCKER_PASSWORD|仅在从需要登录的源中拉取容器映像时才提供值。  Microsoft Container Registry 不需要进行登录。 这是一个屏蔽密码提示。  如果键入或粘贴密码，则不会显示密码。|
|为源中的映像提供容器映像标记，按 ENTER 以使用“`<current monthly release tag>`”：|SOURCE_DOCKER_TAG|默认标记名称将每月更新一次，以反映 Microsoft Container Registry 中最新版本的月份和年份。|
|提供目标容器注册表 DNS 名称或 IP 地址：|TARGET_DOCKER_REGISTRY|提供目标注册表 DNS 名称或 IP 地址。  这是将向其推送映像的注册表。|
|提供目标容器注册表存储库：|TARGET_DOCKER_REPOSITORY|这是将向其推送映像的目标注册表上的存储库。|
|提供目标容器注册表的用户名 - 按 enter 可使用 none：|TARGET_DOCKER_USERNAME|用于登录目标容器注册表的用户名（如果有）。|
|提供目标容器注册表的密码 - 按 enter 可使用 none：|TARGET_DOCKER_PASSWORD|用于登录目标容器注册表的密码（如果有）。 这是一个屏蔽密码提示。  如果键入或粘贴密码，则不会显示密码。|
|为目标中的映像提供容器映像标记：|TARGET_DOCKER_TAG|通常，你将使用与源相同的标记来避免混淆。|