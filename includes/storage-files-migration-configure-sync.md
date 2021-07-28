---
title: include 文件
description: include 文件
services: storage
author: fauhse
ms.service: storage
ms.topic: include
ms.date: 2/20/2020
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: f4baa89757d4ae93af8c1067cf6ef4617ce95e5a
ms.sourcegitcommit: ba8f0365b192f6f708eb8ce7aadb134ef8eda326
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/08/2021
ms.locfileid: "109644940"
---
此步骤将前面步骤中在 Windows Server 实例中设置的所有资源和文件夹绑定在一起。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 找到你的存储同步服务资源。
1. 在每个 Azure 文件共享的存储同步服务资源中创建一个新的同步组。 在 Azure 文件同步术语中，Azure 文件共享是你在创建同步组时所述的同步拓扑中的云终结点。 在创建同步组时，为它提供一个熟悉的名称，便于你识别在那里同步的是哪组文件。 请确保引用具有匹配名称的 Azure 文件共享。
1. 创建同步组后，该同步组的行会显示在同步组列表中。 选择名称（链接）可显示同步组的内容。 你将在“云终结点”下看到你的 Azure 文件共享。
1. 找到“添加服务器终结点”按钮。 你在本地服务器上预配的文件夹将成为此服务器终结点的路径。
