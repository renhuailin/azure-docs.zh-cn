---
title: 创建 Azure 服务主体
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/01/2020
ms.author: pafarley
ms.openlocfilehash: 99487fe962228f775582aa2f0a6fb60fe52862ac
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95098132"
---
## <a name="create-an-azure-service-principal"></a>创建 Azure 服务主体

若要使应用程序与 Azure 帐户交互，需要使用 Azure 服务主体来管理权限。 请按照[创建 Azure 服务主体](/powershell/azure/create-azure-service-principal-azureps?view=azps-4.4.0&viewFallbackFrom=azps-3.3.0)中的说明操作。

创建服务主体时，你会看到它有一个机密值、一个 ID 和一个应用程序 ID。 将应用程序 ID 和机密保存到某个临时位置，以供后续步骤使用。