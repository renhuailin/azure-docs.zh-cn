---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 10/13/2020
ms.author: alkohli
ms.openlocfilehash: dc18efe03cbc8a3f657ae4afc781941e8e76611c
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96466317"
---
将数据移动到 Azure 中时，下面的警告适用。

- 建议不要将多台设备写入到同一容器中。
- 如果云中存在与正在复制的对象同名的现有 Azure 对象（例如 blob 或文件），则设备将覆盖云中的文件。
- 在共享文件夹下创建的任何空目录层次结构（不含任何文件）都不会上传到 blob 容器。
- 可借助文件资源管理器或通过命令行，使用拖放来复制数据。 如果正在复制的文件的总大小大于 10 GB，建议使用大容量复制程序，如 Robocopy 或 rsync。 大容量复制工具可重试复制操作以解决间歇性错误，并提供额外的复原能力。 如果通过 REST 使用 Blob 存储，则可以使用 AzCopy 或 Azure 存储资源管理器。
- 如果与 Azure 存储容器关联的共享上传的 blob 与创建时为该共享定义的 blob 类型不匹配，则不会更新此类 blob。 例如，在设备上创建块 Blob 共享。 将该共享与具有页 Blob 的现有云容器相关联。 刷新该共享以下载文件。 修改已作为页 Blob 存储在云中的某些已刷新文件。 你将看到上传失败。
- 不支持在共享中创建文件后重命名该文件。
- 从共享中删除文件并不删除存储帐户中的条目。
- 如果使用 rsync 复制数据，则 `rsync -a` 不支持选项。
