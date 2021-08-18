---
title: 修改 Azure 文件同步拓扑 | Microsoft Docs
description: 有关如何修改 Azure 文件同步同步拓扑的指南
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 4/23/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 51fc737284e62d1efd3a947e29ea71a4bb2594a0
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113768667"
---
# <a name="modify-your-azure-file-sync-topology"></a>修改 Azure 文件同步拓扑

本文介绍了客户希望修改其 Azure 文件同步拓扑的最常见方法，以及如何执行此操作的建议。 若要修改 Azure 文件同步拓扑，请参阅下方的最佳做法，以避免错误和/或可能发生的数据丢失。

## <a name="migrate-a-server-endpoint-to-a-different-azure-file-sync-storage-sync-service"></a>将服务器终结点迁移到其他 Azure 文件同步存储同步服务

一旦确保本地服务器上的数据为最新数据，应取消预配服务器终结点。 有关详细指导，请参阅[取消预配 Azure 文件同步终结点](./file-sync-server-endpoint-delete.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)。 然后，在目标同步组和存储同步服务中重新预配。

若要将与服务器关联的所有服务器终结点迁移到不同的同步组或存储同步服务，请参阅[取消预配与已注册服务器关联的所有服务器终结点](#deprovision-all-server-endpoints-associated-with-a-registered-server)。

## <a name="change-the-granularity-of-a-server-endpoint"></a>更改服务器终结点的粒度

确认本地服务器上的数据为最新数据后（参阅[取消预配 Azure 文件同步服务器终结点](./file-sync-server-endpoint-delete.md#scenario-2-you-intend-to-delete-your-server-endpoint-and-stop-using-this-specific-azure-file-share)），取消预配服务器终结点。 然后重新设置所需的粒度。

## <a name="deprovision-azure-file-sync-topology"></a>取消设置 Azure 文件同步拓扑

Azure 文件同步资源必须按特定顺序取消预配：服务器终结点、同步组，然后是存储服务。 虽然下文记录了整个流程，但你可能会停在任何所需级别。 

首先，导航到 Azure 门户中的存储同步服务资源，并选择存储同步服务中的同步组。 按照[取消预配 Azure 文件同步服务器终结点](./file-sync-server-endpoint-delete.md)中的步骤进行操作，以确保删除服务器终结点时保持数据的完整性和可用性。 若要取消预配同步组或存储同步服务，必须删除所有服务器终结点。 如果只打算删除特定服务器终结点，则可以在此处停止操作。 

删除同步组中的所有服务器终结点后，删除云终结点。 

然后，删除同步组。 

重复这些步骤，即可删除存储同步服务中的所有同步组。 删除该存储同步服务中的所有同步组后，请删除存储同步服务资源。

## <a name="change-a-server-endpoint-path"></a>更改服务器终结点路径

服务器终结点路径是不可变的属性。 在服务器上选择其他位置将影响旧位置、Azure 文件共享和新位置的数据。 如果只是更改路径，则大多数这些行为都是未定义的。 只能删除服务器终结点，然后使用新路径创建新的服务器终结点。 请仔细考虑服务器的同步状态，以找到合适的时间执行此大型更改。

删除服务器终结点并不简单，如果以错误的方式删除，可能会导致数据丢失。 [删除服务器终结点一文](file-sync-server-endpoint-delete.md)将指导你完成该过程。

如果当前正在使用 D 驱动器且计划迁移到云，请参阅[将 VM 的 D: 驱动器设为数据磁盘 - Azure 虚拟机](../../virtual-machines/windows/change-drive-letter.md)。

## <a name="rename-a-sync-group"></a>重命名同步组

无法重命名同步组。 其名称是用于存储和管理子资源云终结点和服务器终结点的 URL 的一部分。 创建资源时，要谨慎选择名称。

## <a name="deprovision-all-server-endpoints-associated-with-a-registered-server"></a>取消预配与已注册服务器关联的所有服务器终结点

若要在取消预配前确保数据安全且完全更新，请参阅[取消预配 Azure 文件同步服务器终结点](./file-sync-server-endpoint-delete.md)。

不应批量删除所有服务器终结点，除非这是在服务器上和云中具有可释放数据的测试部署。 从 Azure 文件同步“存储同步服务”注销服务器会导致批量删除所有服务器终结点。 使用此方法可能会造成数据丢失。 

若要注销服务器而不顾负面影响，请导航到存储同步服务资源，然后转到“已注册服务器”选项卡。选择要注销的服务器，然后选择“注销服务器” 。 将删除与该服务器关联的所有服务器终结点。

## <a name="next-steps"></a>后续步骤
* [取消预配 Azure 文件同步服务器终结点](./file-sync-server-endpoint-delete.md)


