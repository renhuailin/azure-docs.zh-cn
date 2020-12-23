---
title: include 文件
description: include 文件
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 08/14/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f02fa49b62a2e3d617617a20518810209d3879b7
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "96025949"
---
以下配置用于执行下面的步骤：

- 计算机：Ubuntu Server 18.04
- 依赖项：strongSwan


使用以下命令安装所需的 strongSwan 配置：

```
sudo apt install strongswan
```

```
sudo apt install strongswan-pki
```

```
sudo apt install libstrongswan-extra-plugins
```

使用以下命令安装 Azure 命令行接口：

```
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
```

[有关如何安装 Azure CLI 的其他说明](/cli/azure/install-azure-cli-apt?view=azure-cli-latest)