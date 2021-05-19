---
title: Kudu 服务概述
description: 了解支持在应用服务中进行持续部署的引擎及其功能。
ms.date: 03/17/2021
ms.topic: reference
ms.openlocfilehash: ad1456f1ca123127f3d84aa8195c99fc34872aee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "104608451"
---
# <a name="kudu-service-overview"></a>Kudu 服务概述

Kudu 是 [Azure 应用服务](overview.md)中与基于源代码管理的部署相关的许多功能以及 Dropbox 和 OneDrive 同步等其他部署方法背后的引擎。 

## <a name="access-kudu-for-your-app"></a>访问应用的 Kudu
每当创建应用时，应用服务都会为该应用创建一个由 HTTPS 保护的配套应用。 可在以下位置访问此 Kudu 应用：

- 不在隔离层中的应用：`https://<app-name>.scm.azurewebsites.net`
- 隔离层中的应用（应用服务环境）：`https://<app-name>.scm.<ase-name>.p.azurewebsites.net`

有关详细信息，请参阅[访问 Kudu 服务](https://github.com/projectkudu/kudu/wiki/Accessing-the-kudu-service)。

## <a name="kudu-features"></a>Kudu 功能

Kudu 提供有关应用服务应用的有用信息，例如：

- 应用设置
- 连接字符串
- 环境变量
- 服务器变量
- HTTP 头

它还提供其他功能，例如：

- 在 [Kudu 控制台](https://github.com/projectkudu/kudu/wiki/Kudu-console)中运行命令。
- 下载 IIS 诊断转储或 Docker 日志。
- 管理 IIS 进程和站点扩展。
- 为 Windows 应用添加部署 Webhook。
- 允许包含 `/ZipDeploy` 的 ZIP 部署 UI。
- 生成[自定义部署脚本](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script)。
- 允许使用 [REST API](https://github.com/projectkudu/kudu/wiki/REST-API) 访问。

## <a name="more-resources"></a>更多资源

Kudu 是一个[开放源代码项目](https://github.com/projectkudu/kudu)，它的文档位于 [Kudu Wiki](https://github.com/projectkudu/kudu/wiki)。

