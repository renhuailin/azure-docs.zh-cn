---
title: include 文件
description: include 文件
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/07/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: eff2a7eda24834389db7afc0a689767f20da045f
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423301"
---
## <a name="what-happens-to-my-app-during-deployment"></a>部署期间我的应用会发生什么情况？

所有官方支持的部署方法均会更改应用的 /home/site/wwwroot 文件夹中的文件。 这些文件用于运行你的应用。 因此，部署可能会因为锁定的文件而失败。 应用可能会在部署期间发生不可预测的行为，因为这些文件并非同时更新。 这种行为对于面向客户的应用来说是需要避免的。 可以通过几种方法避免这些问题：

- [直接从 ZIP 包运行应用](../articles/app-service/deploy-run-package.md)，而无需将其解压。
- 部署期间停止应用或为其启用脱机模式。 有关详细信息，请参阅[处理部署过程中锁定的文件](https://github.com/projectkudu/kudu/wiki/Dealing-with-locked-files-during-deployment)。
- 部署到[过渡槽](../articles/app-service/deploy-staging-slots.md)且开启了[自动交换](../articles/app-service/deploy-staging-slots.md#configure-auto-swap)。 
