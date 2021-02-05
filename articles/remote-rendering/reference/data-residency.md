---
title: 数据驻留
description: 介绍使用 Azure 远程呈现时的数据驻留。
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576869"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure 远程呈现数据驻留 
本文介绍数据驻留的概念，以及如何将其应用到 Azure 远程呈现。 

## <a name="data-residency"></a>数据驻留 
Azure 远程呈现使用用户为模型存储提供的 Azure Blob 容器。 如果未使用该模型，则该模型将保留在用户提供的 Azure Blob 存储区域中。 当模型用于呈现时，数据将复制到用户启动呈现会话时选择的区域。

## <a name="next-steps"></a>后续步骤
若要了解如何为 Azure 远程呈现设置 Azure Blob 存储容器，请查看 [转换模型以进行呈现](../quickstarts/convert-model.md)。
