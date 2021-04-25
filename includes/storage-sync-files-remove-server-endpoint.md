---
title: include 文件
description: include 文件
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 05/31/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f730b2abad244132b06c0fa21f07a171559d22f7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "96309942"
---
注意：删除服务器终结点不同于重启服务器！ 在绝大多数情况下，删除或重新创建服务器终结点不是适合修复同步、云分层或 Azure 文件同步其他方面问题的解决方案。删除服务器终结点是一项破坏性操作。 当分层文件存在于服务器终结点命名空间之外时，它可能会导致数据丢失。 有关详细信息，请参阅[为什么分层文件存在于服务器终结点命名空间之外](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)。 或者，它可能会导致存在于服务器终结点命名空间内的分层文件无法访问。 重新创建服务器终结点后，这些问题并不会得到解决。 即使从未启用过云分层，分层文件也可能存在于服务器终结点命名空间中。 因此，建议不要删除服务器终结点，除非想对特定文件夹停用 Azure 文件同步，或者 Microsoft 工程师明确指示你采取该操作。 有关删除服务器终结点的详细信息，请参阅[删除服务器终结点](../articles/storage/files/storage-sync-files-server-endpoint.md#remove-a-server-endpoint)。    
