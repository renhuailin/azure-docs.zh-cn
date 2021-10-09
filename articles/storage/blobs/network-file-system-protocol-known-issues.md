---
title: Azure Blob 存储中 NFS 3.0 的已知问题
description: 了解 Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的限制和已知问题。
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 09/08/2021
ms.author: normesta
ms.reviewer: yzheng
ms.openlocfilehash: 6e5949f22000c930f101ae36042587d97a5293a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664858"
---
# <a name="known-issues-with-network-file-system-nfs-30-protocol-support-in-azure-blob-storage"></a>Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的已知问题

本文介绍 Azure Blob 存储中网络文件系统 (NFS) 3.0 协议支持的限制和已知问题。

> [!IMPORTANT]
> 由于你必须启用帐户的分层命名空间功能才能使用 NFS 3.0，因此 [Azure Data Lake Storage Gen2 的已知问题](data-lake-storage-known-issues.md)一文中描述的所有已知问题也适用于你的帐户。

## <a name="nfs-30-support"></a>NFS 3.0 支持

- 无法在现有存储帐户上启用 NFS 3.0 支持。

- 启用 NFS 3.0 支持后，无法在存储帐户中将其禁用。

## <a name="nfs-30-features"></a>NFS 3.0 功能

尚不支持以下 NFS 3.0 功能。

- 基于 UDP 的 NFS 3.0。 仅支持基于 TCP 的 NFS 3.0。

- 使用网络锁定管理器 (NLM) 锁定文件。 装载命令必须包含 `-o nolock` 参数。

- 装载子目录。 只能装载根目录（容器）。

- 列出装载（例如：通过使用 `showmount -a` 命令）

- 列出导出（例如：通过使用 `showmount -e` 命令）

- 硬链接

- 以只读方式导出容器

## <a name="nfs-30-clients"></a>NFS 3.0 客户端

Windows 客户端尚不支持 NFS

## <a name="blob-storage-features"></a>Blob 存储功能

启用 NFS 3.0 协议支持后，某些 Blob 存储功能将完全受支持，但某些功能可能仅在预览级别受支持，或者根本不受支持。

要查看启用了 NFS 3.0 支持的帐户如何支持每个 Blob 存储功能，请参阅 [Azure 存储帐户中的 Blob 存储功能支持](storage-feature-support-in-storage-accounts.md)。

## <a name="see-also"></a>另请参阅

- [Azure Blob 存储中的网络文件系统 (NFS) 3.0 协议支持](network-file-system-protocol-support.md)
- [使用网络文件系统 (NFS) 3.0 协议装载 Blob 存储](network-file-system-protocol-support-how-to.md)
