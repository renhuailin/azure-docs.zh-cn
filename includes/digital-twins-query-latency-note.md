---
author: baanders
description: 查询 API 延迟说明的 include 文件
ms.service: digital-twins
ms.topic: include
ms.date: 09/21/2021
ms.author: baanders
ms.openlocfilehash: 2e538b6f0197f408be649bd76cb4a15fbe22b4bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128557645"
---
>[!NOTE]
>对图表中的数据进行更改后，可能会有长达 10 秒的延迟才会在查询中反映更改。 
>
>[DigitalTwins API](../articles/digital-twins/concepts-apis-sdks.md#overview-data-plane-apis) 会立即反映更改，因此如果需要即时响应，请使用 API 请求 ([DigitalTwins GetById](/rest/api/digital-twins/dataplane/twins/digitaltwins_getbyid)) 或 SDK 调用 ([GetDigitalTwin](/dotnet/api/azure.digitaltwins.core.digitaltwinsclient.getdigitaltwin?view=azure-dotnet&preserve-view=true)) 获取孪生数据，而不是查询。