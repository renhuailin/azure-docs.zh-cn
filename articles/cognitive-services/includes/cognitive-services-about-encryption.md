---
title: include 文件
description: include 文件
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.custom: include
ms.openlocfilehash: 90d5aae559a317ed509d04c8db3310ff8a5de026
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "89069897"
---
## <a name="about-cognitive-services-encryption"></a>关于认知服务加密

数据使用符合 [FIPS 140-2](https://en.wikipedia.org/wiki/FIPS_140-2) 的 [256 位 AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 加密方法进行加密和解密。 加密和解密都是透明的，这意味着将替你管理加密和访问。 你的数据默认情况下就是安全的，你无需修改代码或应用程序，即可利用加密。

## <a name="about-encryption-key-management"></a>关于加密密钥管理

默认情况下，订阅使用 Microsoft 托管的加密密钥。 还有一个选项可用于通过你自己的密钥（称为“客户管理的密钥”(CMK)）来管理订阅。 CMK 在创建、轮换、禁用和撤销访问控制方面具有更大的灵活性。 此外，你还可以审核用于保护数据的加密密钥。 如果为订阅配置 CMK，则会获得双重加密，可以在通过 Azure Key Vault 来控制加密密钥的同时提供第二层保护。