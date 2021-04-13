---
title: API 参考 - 人脸
titleSuffix: Azure Cognitive Services
description: 此 API 参考提供了有关人员、LargePersonGroup/PersonGroup、LargeFaceList/FaceList 和人脸算法 API 的信息。
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: reference
ms.date: 02/17/2021
ms.author: sbowles
ms.openlocfilehash: 712b18b471b7595f5278c77ead9f845048d4783e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105566092"
---
# <a name="face-api-reference-list"></a>人脸 API 参考列表

Azure 人脸是一项基于云的服务，可提供用于人脸检测和识别的算法。 人脸 API 包含以下类别：

- 人脸算法 API：涵盖[检测](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)、[查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)、[验证](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a)、[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)、[分组](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238)等核心功能。
- [FaceList API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b)：用来管理一个用于[查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)的 FaceList。
- [LargePersonGroup 人员 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599adcba3a7b9412a4d53f40)：用来管理一个用于[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)的 LargePersonGroup 人脸。
- [LargePersonGroup API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)：用来管理一个用于[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)的 LargePersonGroup 数据集。
- [LargeFaceList API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)：用来管理一个用于[查找相似](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237)的 LargeFaceList。
- [PersonGroup 人员 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)：用来管理一个用于[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)的 PersonGroup 人脸。
- [PersonGroup API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244)：用来管理一个用于[识别](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239)的 PersonGroup 数据集。
- [快照 API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/snapshot-take)：用于管理一个跨订阅进行数据迁移的快照。
