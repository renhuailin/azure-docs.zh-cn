---
title: 使用 Azure 门户创建中继命名空间 | Microsoft Docs
description: 本文提供了一个演练，演示如何使用 Azure 门户创建中继命名空间。
ms.topic: conceptual
ms.date: 06/23/2021
ms.openlocfilehash: d275f760a72e0469a22e7b1861e49751a9b8350a
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114666279"
---
# <a name="create-a-relay-namespace-using-the-azure-portal"></a>使用 Azure 门户创建中继命名空间

命名空间是适用于所有 Azure 中继组件的作用域容器。 单个命名空间中可存在多个中继，命名空间通常用作应用程序容器。 目前有两种方法可用来创建中继命名空间：

1. Azure 门户（本文）。
2. [Azure 资源管理器](../azure-resource-manager/management/overview.md)模板。

## <a name="create-a-namespace-in-the-azure-portal"></a>在 Azure 门户中创建命名空间

[!INCLUDE [relay-create-namespace-portal](./includes/relay-create-namespace-portal.md)]

祝贺！ 现已创建中继命名空间。

## <a name="next-steps"></a>后续步骤

* [中继常见问题](relay-faq.yml)
* [.NET 入门](relay-hybrid-connections-dotnet-get-started.md)
* [节点入门](relay-hybrid-connections-node-get-started.md)

