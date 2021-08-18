---
title: 在 macOS 上装载 SMB Azure 文件共享 | Microsoft Docs
description: 了解如何使用 Finder 或 Terminal 在 macOS 中通过 SMB 装载 Azure 文件共享。 Azure 文件是 Microsoft 推出的易用云文件系统。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f4bbaed3cf35d25a2441a27755c73dc144a07510
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/15/2021
ms.locfileid: "112117774"
---
# <a name="mount-smb-azure-file-share-on-macos"></a>在 macOS 上装载 SMB Azure 文件共享
[Azure 文件](storage-files-introduction.md)是 Microsoft 推出的易用云文件系统。 Azure 文件共享可以通过 macOS High Sierra 10.13+ 使用行业标准 SMB 3 协议进行装载。 本文介绍如何使用两种不同的方法通过 Finder UI 和 Terminal 在 macOS 中装载 Azure 文件共享。

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>在 macOS 中装载 Azure 文件共享的先决条件
* **存储帐户名称**：需提供存储帐户的名称才能装载 Azure 文件共享。

* **存储帐户密钥**：需提供主要（或辅助）存储密钥才能装载 Azure 文件共享。 目前不支持使用 SAS 密钥进行装载。

* 确保端口 445 处于打开状态：SMB 通过 TCP 端口 445 通信。 在客户端计算机 (Mac) 上检查，确保防火墙未阻止 TCP 端口 445。

## <a name="applies-to"></a>适用于
| 文件共享类型 | SMB | NFS |
|-|:-:|:-:|
| 标准文件共享 (GPv2)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 标准文件共享 (GPv2)、GRS/GZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |
| 高级文件共享 (FileStorage)、LRS/ZRS | ![是](../media/icons/yes-icon.png) | ![否](../media/icons/no-icon.png) |

## <a name="mount-an-azure-file-share-via-finder"></a>通过 Finder 装载 Azure 文件共享
1. 打开 Finder：默认情况下，Finder 在 macOS 中处于打开状态，但你可以通过单击 Dock 中的“macOS 笑脸图标”来确保它是当前选定的应用程序：  
    ![macOS 笑脸图标](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **在“前往”菜单中选择“连接到服务器”** ：使用先决条件中的 UNC 路径，将开头的双反斜杠 (`\\`) 转换为 `smb://`，将所有其他的反斜杠 (`\`) 转换为斜杠 (`/`)。 你的链接应如下所示：![“连接到服务器”对话框](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **当系统提示你输入用户名和密码时，使用存储帐户名称和存储帐户密钥**：单击“连接到服务器”对话框中的“连接”时，系统会提示你输入用户名和密码（系统会使用你的 macOS 用户名自动填充此用户名字段）。 可以选择将存储帐户名称/存储帐户密钥置于 macOS 密钥链中。

4. **根据需要使用 Azure 文件共享**：使用共享名称和存储帐户密钥替换用户名和密码以后，系统就会装载共享。 可以像通常情况下使用本地文件夹/文件共享一样使用此功能，例如，可以将文件拖放到文件共享中：

    ![已装载 Azure 文件共享的快照](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>通过 Terminal 装载 Azure 文件共享
1. 将 `<storage-account-name>`、`<storage-account-key>` 和 `<share-name>` 替换为适合你的环境的值。

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **根据需要使用 Azure 文件共享**：Azure 文件共享将装载到上一命令指定的装载点。  

    ![已装载 Azure 文件共享的快照](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>后续步骤
* [将 Mac 连接到共享计算机和服务器 - Apple 支持](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)