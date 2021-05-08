---
title: 修改 Azure 文件同步拓扑 | Microsoft Docs
description: 有关如何修改 Azure 文件同步同步拓扑的指南
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4977b56265f542f8f8ff13693abb468bc695a384
ms.sourcegitcommit: 18cd3c1c8cc47258c6a1a04e0e03d6248c52ef24
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/25/2021
ms.locfileid: "107992720"
---
# <a name="modify-your-azure-file-sync-topology"></a>修改 Azure 文件同步拓扑

本文介绍了客户希望修改其 Azure 文件同步拓扑的最常见方法，以及如何执行此操作的建议。 若要修改 Azure 文件同步拓扑，请参阅下方的最佳实践，以避免错误和/或可能发生的数据丢失。

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>将服务器终结点迁移到其他 Azure 文件同步存储同步服务

一旦确保本地服务器上的数据为最新数据，应取消预配服务器终结点。 有关如何执行此操作的指导，请参阅[取消预配 Azure 文件同步服务器终结点](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)。 然后，在目标同步组和存储同步服务中重新预配。

若要将与服务器关联的所有服务器终结点迁移到不同的同步组或存储同步服务，请参阅[取消预配与已注册服务器关联的所有服务器终结点](#deprovision-all-server-endpoints-associated-with-a-registered-server)。

## <a name="change-the-granularity-of-a-server-endpoint"></a>更改服务器终结点的粒度

确认本地服务器上的数据为最新数据后（参阅[取消预配 Azure 文件同步服务器终结点](./file-sync-deprovision-server-endpoint.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)），取消预配服务器终结点。 然后重新设置所需的粒度。

## <a name="deprovision-azure-file-sync-topology"></a>取消设置 Azure 文件同步拓扑

Azure 文件同步资源必须按特定顺序取消预配：服务器终结点、同步组，然后是存储服务。 虽然下文记录了整个流程，但你可能会停在任何所需级别。 

首先，导航到 Azure 门户中的存储同步服务资源，并选择存储同步服务中的同步组。 按照[取消预配 Azure 文件同步服务器终结点](./file-sync-deprovision-server-endpoint.md)中的步骤进行操作，以确保删除服务器终结点时保持数据的完整性和可用性。 若要取消预配同步组或存储同步服务，必须删除所有服务器终结点。 如果只打算删除特定服务器终结点，则可以在此处停止操作。 

删除同步组中的所有服务器终结点后，删除云终结点。 

然后，删除同步组。 

重复这些步骤，即可删除存储同步服务中的所有同步组。 删除该存储同步服务中的所有同步组后，请删除存储同步服务资源。

## <a name="rename-a-server-endpoint-path-or-sync-group"></a>重命名服务器终结点路径或同步组

目前，不支持此功能。 

如果当前正在使用 D 驱动器且计划迁移到云，请参阅[将 VM 的 D: 驱动器设为数据磁盘 - Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/windows/change-drive-letter)。

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>取消预配与已注册服务器关联的所有服务器终结点

若要在取消预配前确保数据安全且完全更新，请参阅[取消预配 Azure 文件同步服务器终结点](./file-sync-deprovision-server-endpoint.md)。

导航到存储同步服务资源，然后转到“已注册服务器”选项卡。选择要取消注册的服务器，然后选择“注销服务器”。 此操作将立即取消预配与该服务器关联的所有服务器终结点。

## <a name="next-steps"></a>后续步骤
* [取消预配 Azure 文件同步服务器终结点](./file-sync-deprovision-server-endpoint.md)



