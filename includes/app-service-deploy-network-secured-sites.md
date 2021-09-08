---
title: include 文件
description: include 文件
services: app-service
author: jasonfreeberg
ms.service: app-service
ms.topic: include
ms.date: 08/27/2021
ms.author: jafreebe
ms.custom: include file
ms.openlocfilehash: a3030eedcc6b00457338f4a71c27965261280a80
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225439"
---
根据你的 web 应用的网络配置，从本地环境对站点的直接访问可能会被阻止。 要在这种情况下部署代码，可以将 ZIP 发布到可从 web 应用访问的存储系统，并触发应用以从存储位置拉取 ZIP，而不是将 ZIP 推送到 web 应用 。 有关部署到网络安全的 web 应用的详细信息，请参阅[此文](https://azure.github.io/AppService/2021/03/01/deploying-to-network-secured-sites-2.html)。 
