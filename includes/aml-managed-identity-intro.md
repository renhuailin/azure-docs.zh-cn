---
title: include 文件
description: include 文件
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: 70b636b7bb508b71475a7464983b091d5d10e0e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "96026735"
---
 Azure 机器学习计算群集还支持使用[托管标识](../articles/active-directory/managed-identities-azure-resources/overview.md)来验证对 Azure 资源的访问，而不需要在代码中包含凭据。 托管标识分为两种类型：

* 系统分配的托管标识将在 Azure 机器学习计算群集上直接启用。 系统分配的标识的生命周期将直接绑定到计算群集。 如果计算群集遭删除，Azure 会自动清理 Azure AD 中的凭据和标识。
* 用户分配的托管标识是通过 Azure 托管标识服务提供的独立 Azure 资源。 可以将一个用户分配的托管标识分配给多个资源，并根据需要将其保留任意长的时间。