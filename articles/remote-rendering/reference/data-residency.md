---
title: 数据驻留
description: 介绍使用 Azure 远程渲染时的数据驻留。
author: rapete
ms.author: rapete
ms.date: 02/04/2021
ms.topic: reference
ms.openlocfilehash: f20b3bb3de877ac627f5f6909c98cb9e1553f2a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "99576869"
---
# <a name="azure-remote-rendering-data-residency"></a>Azure 远程渲染数据驻留 
本文介绍数据驻留的概念，以及它应用于 Azure 远程渲染的方式。 

## <a name="data-residency"></a>数据驻留 
Azure 远程渲染使用用户提供的 Azure Blob 容器存储模型。 该模型未被使用时，它将保留在用户提供的 Azure Blob 存储区域中。 当该模型用于渲染时，数据会被复制到用户在启动渲染会话时选择的区域。

## <a name="next-steps"></a>后续步骤
若要了解如何为 Azure 远程渲染设置 Azure Blob 存储容器，请查看[转换模型以进行渲染](../quickstarts/convert-model.md)。
