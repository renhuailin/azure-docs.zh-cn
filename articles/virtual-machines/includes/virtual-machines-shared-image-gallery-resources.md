---
title: include 文件
description: include 文件
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 7e5ab96e543a61cb45f3ebc6792b39a115916cd0
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123449435"
---
| 资源 | 说明|
|----------|------------|
| **映像源** | 这是可用于在映像库中创建“映像版本”的资源。 映像源可以是现有的 Azure VM（可以是[通用或专用](../shared-image-galleries.md#generalized-and-specialized-images)的）、托管映像、快照或其他映像库中的映像版本。 |
| **映像库** | 与 Azure 市场一样，**映像库** 是用于管理和共享映像的存储库，但你可以控制谁有权访问这些映像。 |
| **映像定义** | 映像定义在库中创建，携带有关该映像以及在内部使用该映像的要求的信息。 这包括了该映像是 Windows 还是 Linux 映像、发行说明以及最低和最高内存要求。 它是某种映像类型的定义。 |
| **映像版本** | 使用库时，将使用 **映像版本** 来创建 VM。 可根据环境的需要创建多个映像版本。 与托管映像一样，在使用 **映像版本** 创建 VM 时，将使用映像版本来创建 VM 的新磁盘。 可以多次使用映像版本。 |