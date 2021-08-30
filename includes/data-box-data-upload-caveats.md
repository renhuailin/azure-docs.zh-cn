---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 08/16/2021
ms.author: alkohli
ms.openlocfilehash: e73b49b92894d2871e474f42ad4ba31a7161677b
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2021
ms.locfileid: "122262405"
---
- 容器、共享和文件夹：
  - 不要直接将文件复制到任何预创建的共享。 需要在共享下创建文件夹，然后将文件复制到该文件夹。
  - StorageAccount_BlockBlob 和 StorageAccount_PageBlob 下的文件夹为容器。 例如，容器创建为 StorageAccount_BlockBlob/container 和 StorageAccount_PageBlob/container。
  - 直接在 StorageAccount_AzFile 下创建的每个文件夹都将转换为 Azure 文件共享。
  - Azure Blob 存储不支持目录。 如果在 StorageAccount_BlockBlob 文件夹下创建文件夹，将以 blob 的名义创建虚拟文件夹。 对于 Azure 文件，将维护实际的目录结构。
- 合并文件夹内容：
  - 写入到 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 共享中的每个文件将分别上传为块 blob 和页 blob。
  - 如果文件夹与现有容器同名，则文件夹的内容将与容器的内容合并。 云中尚未有的文件或 Blob 将添加到容器中。 如果某个文件或 Blob 与容器中已有的文件或 Blob 同名，则会覆盖现有文件或 Blob。
  - 在 StorageAccount_BlockBlob 和 StorageAccount_PageBlob 文件夹下创建的任何空目录层次结构（没有任何文件）都不会上传。
- 上传管理： 
  - 为了提高数据上传期间的性能，建议[在存储帐户上启用大型文件共享并将共享容量增大到 100 TiB](../articles/storage/files/storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)。 只有具有本地冗余存储 (LRS) 的存储帐户才支持大型文件共享。
  - 如果将数据上传到 Azure 时发生任何错误，则会在目标存储帐户中创建一个错误日志。 当上传完成时，可以找到此错误日志的路径，并且可以查看此日志来采取纠正措施。 在验证上传的数据之前，不要删除源中的数据。
  - 根据[保留 Azure Data Box 的文件 ACL、属性和时间戳](../articles/databox/data-box-file-acls-preservation.md)中的指南将数据上传到 Azure 文件存储时，可以保留文件元数据和 NTFS 权限。
  - 文件的层次结构在上传到云时保持不变。 例如，你在以下路径复制了一个文件：`<container folder>\A\B\C.txt`。 此文件将上传到云中相同的虚拟路径。
  
