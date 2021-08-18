---
title: 如何为 Azure Functions 配置虚拟网络
description: 本文介绍如何为 Azure Functions 执行某些虚拟网络任务。
ms.topic: conceptual
ms.date: 3/13/2021
ms.custom: template-how-to
ms.openlocfilehash: 1e04a43a7a9b2eb8b3d29be6ced0d92d8bcc8308
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202915"
---
# <a name="how-to-configure-azure-functions-with-a-virtual-network"></a>如何为 Azure Functions 配置虚拟网络

可以将函数应用配置为连接到虚拟网络并在其上运行，本文介绍如何执行与之相关的任务。 有关如何保护存储帐户的深入教程，请参阅[“连接虚拟网络”教程](functions-create-vnet.md)。 若要详细了解 Azure Functions 和网络，请参阅 [Azure Functions 网络选项](functions-networking-options.md)。

## <a name="restrict-your-storage-account-to-a-virtual-network"></a>将存储帐户限制到虚拟网络中 

创建函数应用时，必须创建或链接到支持 Blob、队列和表存储的常规用途的 Azure 存储帐户。 可以将此存储帐户替换为服务终结点或专用终结点所保护的存储帐户。 使用专用终结点配置存储帐户时，对函数应用的公共访问自动禁用，只能通过虚拟网络访问函数应用。 

> [!NOTE]  
> 此功能目前适用于专用（应用服务）计划中 Windows 虚拟网络支持的所有 SKU，并且适用于 Windows Elastic 高级计划。 对于 Linux 虚拟网络支持的 SKU，专用 DNS 也支持该功能。 不支持 Linux 计划的消耗和自定义 DNS。 

若要使用限制到专用网络的存储帐户来设置函数，请执行以下操作：

1. 使用未启用服务终结点的存储帐户创建一个函数。

1. 将该函数配置为连接到你的虚拟网络。

1. 创建或配置另一存储帐户。  此存储帐户将是我们使用服务终结点保护的存储帐户，可连接我们的函数。

1. 在受保护的存储帐户中[创建文件共享](../storage/files/storage-how-to-create-file-share.md#create-a-file-share)。

1. 为此存储帐户启用服务终结点或专用终结点。  
    * 如果使用专用终结点连接，该存储帐户将需要一个用于 `file` 和 `blob` 子资源的专用终结点。  如果使用某些功能（如 Durable Functions），则还需要可通过专用终结点连接访问的 `queue` 和 `table`。
    * 如果使用服务终结点，请为存储帐户启用专用于函数应用的子网。

1. 将函数应用存储帐户中的文件和 blob 内容复制到受保护的存储帐户和文件共享。

1. 复制此存储帐户的连接字符串。

1. 将函数应用的“配置”下的“应用程序设置”更新为以下内容：

    | 设置名称 | 值 | 注释 |
    |----|----|----|
    | `AzureWebJobsStorage`| 存储连接字符串 | 这是受保护的存储帐户的连接字符串。 |
    | `WEBSITE_CONTENTAZUREFILECONNECTIONSTRING` |  存储连接字符串 | 这是受保护的存储帐户的连接字符串。 |
    | `WEBSITE_CONTENTSHARE` | 文件共享 | 在项目部署文件所在的受保护的存储帐户中创建的文件共享的名称。 |
    | `WEBSITE_CONTENTOVERVNET` | 1 | 新设置 |
    | `WEBSITE_VNET_ROUTE_ALL` | 1 | 强制所有出站流量都通过虚拟网络。 在存储帐户使用专用终结点连接时，需要此设置。 |
    | `WEBSITE_DNS_SERVER` | `168.63.129.16` | 该应用使用的 DNS 服务器。 在存储帐户使用专用终结点连接时，需要此设置。 |

1. 选择“保存”，以保存应用程序设置。 更改应用设置会导致该应用重启。  

函数应用在重启后现在已连接到受保护的存储帐户。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [Azure Functions 网络选项](functions-networking-options.md)

