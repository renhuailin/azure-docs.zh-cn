---
title: 了解 Azure HPC 缓存聚合命名空间
description: 如何为 Azure HPC 缓存规划虚拟命名空间
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/30/2020
ms.author: v-erkel
ms.openlocfilehash: 500cc23f2091c38006f7e58e150fb246f2a9ca64
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586898"
---
# <a name="plan-the-aggregated-namespace"></a>规划聚合命名空间

Azure HPC 缓存允许客户端通过一个虚拟命名空间访问各种存储系统，该命名空间隐藏了后端存储系统的详细信息。

添加存储目标后，可为存储目标设置一个或多个面向客户端的命名空间路径。 客户端计算机可装载此文件路径并向缓存发出文件读取请求，而不是直接装载存储系统。

由于 Azure HPC 缓存会管理此虚拟文件系统，因此你无需更改面向客户端的路径即可更改存储目标。 例如，无需重写客户端过程，就能将硬件存储系统替换为云存储。

## <a name="aggregated-namespace-example"></a>聚合命名空间示例

规划聚合命名空间，使客户端计算机能够方便地访问所需的信息，并且管理员和工作流工程师能够轻松地区分路径。

例如，假设某个系统中使用 Azure HPC 缓存实例来处理存储在 Azure Blob 中的数据。 在分析时需要存储在本地数据中心的模板文件。

模板数据存储在数据中心，此作业所需的信息存储在以下子目录中：

* /goldline/templates/acme2017/sku798
* /goldline/templates/acme2017/sku980

数据中心存储系统公开以下导出：

* */*
* /goldline
* /goldline/templates

已使用 [CLFSLoad 实用工具](hpc-cache-ingest.md#pre-load-data-in-blob-storage-with-clfsload)将要分析的数据复制到名为“sourcecollection”的 Azure Blob 存储容器。

为了能够通过缓存轻松访问数据，请考虑使用以下虚拟命名空间路径创建存储目标：

| 后端存储系统 <br/> （NFS 文件路径或 Blob 容器） | 虚拟命名空间路径 |
|-----------------------------------------|------------------------|
| /goldline/templates/acme2017/sku798     | /templates/sku798      |
| /goldline/templates/acme2017/sku980     | /templates/sku980      |
| sourcecollection                        | /source/               |

一个 NFS 存储目标可以有多个虚拟命名空间路径，前提是每个路径引用唯一的导出路径。 （请参阅 [NFS 命名空间路径](add-namespace-paths.md#nfs-namespace-paths)，了解有关将多个命名空间路径与 NFS 存储目标一起使用的更多信息。）

由于 NFS 源路径是同一导出的子目录，因此需要从同一存储目标定义多个命名空间路径。

| 存储目标主机名  | NFS 导出路径     | 子目录路径 | 命名空间路径    |
|--------------------------|---------------------|-------------------|-------------------|
| IP 地址或主机名 | /goldline/templates | acme2017/sku798   | /templates/sku798 |
| IP 地址或主机名 | /goldline/templates | acme2017/sku980   | /templates/sku980 |

客户端应用程序可以装载缓存，并轻松访问聚合命名空间文件路径 ``/source``、``/templates/sku798`` 和 ``/templates/sku980``。

另一种可能的方法是创建类似于 `/templates` 的、链接到父目录 `acme2017` 的虚拟路径，然后让客户端在装载缓存后导航到单个 `sku798` 和 `sku980` 目录。 但是，不能创建作为另一命名空间路径的子目录的命名空间路径。 因此，如果创建 `acme2017` 目录的路径，则不能同时创建用于直接访问其子目录的任何命名空间路径。

Azure HPC 缓存的“命名空间设置”页显示了面向客户端的文件系统，并允许你添加或编辑路径。 请参阅[设置聚合命名空间](add-namespace-paths.md)了解详细信息。

## <a name="next-steps"></a>后续步骤

确定如何设置虚拟文件系统后，请执行以下步骤来创建该系统：

* [创建存储目标](hpc-cache-add-storage.md)以将后端存储系统添加到 Azure HPC 缓存
* [添加命名空间路径](add-namespace-paths.md)以创建供客户端计算机用来访问文件的聚合命名空间
