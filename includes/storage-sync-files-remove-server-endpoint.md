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
ms.openlocfilehash: d527af6607dfdaf2cf2b5de7b6adbfed40f74895
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113760102"
---
注意：删除服务器终结点不同于重启服务器！ 在绝大多数情况下，删除或重新创建服务器终结点不是适合修复同步、云分层或 Azure 文件同步其他方面问题的解决方案。删除服务器终结点是破坏性操作。 这在分层文件存在于服务器终结点命名空间之外时可能会导致数据丢失。 有关详细信息，请参阅[为什么分层文件存在于服务器终结点命名空间之外](../articles/storage/files/storage-files-faq.md#afs-tiered-files-out-of-endpoint)。 或者，这对于存在于服务器终结点命名空间之内的分层文件可能会导致文件无法访问。 重新创建服务器终结点后，这些问题并不会得到解决。 即使从未启用过云分层，分层文件也可能存在于服务器终结点命名空间中。 因此，建议不要删除服务器终结点，除非想对特定文件夹停用 Azure 文件同步，或者 Microsoft 工程师明确指示你采取该操作。 有关删除服务器终结点的详细信息，请参阅[删除服务器终结点](../articles/storage/file-sync/file-sync-server-endpoint-delete.md)。    
