---
ms.service: logic-apps
ms.topic: include
author: ecfan
ms.author: estfan
ms.date: 05/25/2021
ms.openlocfilehash: 88d0292048edcc6846d0e1637b79f115d1321e70
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110369095"
---
在多租户模型中，逻辑应用资源结构只能包含单个工作流。 由于这种一对一关系，逻辑应用和工作流通常被视为并同义。 但是，在单租户模型中，逻辑应用资源结构可以包含多个工作流。 这种一对多关系意味着，在同一个逻辑应用中，工作流可以共享并重用其他资源。 此外，由于此共享租用和彼此之间的邻近性，同一个逻辑应用和租户中的工作流可以提高性能。 此资源结构的外观和工作方式与 Azure Functions 相似，在 Azure Functions 中，函数应用可以托管多个函数。

有关组织工作流、性能以及在逻辑应用中进行扩展的详细信息和最佳做法，请查看 [Azure Functions 的指南](../articles/azure-functions/functions-best-practices.md)，该指南通常也适用于单租户 Azure 逻辑应用。