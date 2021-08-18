---
title: 用于认知服务的客户管理的密钥
titleSuffix: Cognitive Services
description: 了解如何使用 Azure 门户通过 Azure Key Vault 来配置客户管理的密钥。 使用客户管理的密钥可以创建、轮换、禁用和撤销访问控制。
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 04/07/2021
ms.author: egeaney
ms.openlocfilehash: c45f7dd7daad911deff3c920222ec18118437692
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114220375"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-for-cognitive-services"></a>通过 Azure Key Vault 为认知服务配置客户管理的密钥

使用 Azure Key Vault 为认知服务启用客户管理的密钥的过程因产品而异。 使用以下链接获得特定服务的说明：

## <a name="vision"></a>影像

* [自定义视觉静态数据加密](../custom-vision-service/encrypt-data-at-rest.md)
* [人脸服务静态数据加密](../face/encrypt-data-at-rest.md)
* [表单识别器静态数据加密](../form-recognizer/encrypt-data-at-rest.md)

## <a name="language"></a>语言

* [语言理解服务静态数据加密](../LUIS/encrypt-data-at-rest.md)
* [QnA Maker 静态数据加密](../QnAMaker/encrypt-data-at-rest.md)
* [翻译器的静态数据加密](../translator/encrypt-data-at-rest.md)

## <a name="speech"></a>语音

* [对静态数据的语音加密](../speech-service/speech-encryption-of-data-at-rest.md)

## <a name="decision"></a>决策

* [内容审查器静态数据加密](../Content-Moderator/encrypt-data-at-rest.md)
* [个性化体验创建服务静态数据加密](../personalizer/encrypt-data-at-rest.md)

## <a name="next-steps"></a>后续步骤

* [什么是 Azure Key Vault？](../../key-vault/general/overview.md)
* [认知服务客户管理的密钥请求表单](https://aka.ms/cogsvc-cmk)