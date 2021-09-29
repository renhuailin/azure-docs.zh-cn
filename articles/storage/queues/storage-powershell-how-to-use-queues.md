---
title: 如何通过 PowerShell 使用 Azure 队列存储 - Azure 存储
description: 通过 PowerShell 对 Azure 队列存储执行操作。 使用 Azure 队列存储，可以存储大量可通过 HTTP/HTTPS 访问的消息。
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 05/15/2019
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a607d1c494aee2881e623e01e6cd9637cb94dc9d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128582281"
---
# <a name="how-to-use-azure-queue-storage-from-powershell"></a>如何通过 PowerShell 使用 Azure 队列存储

Azure 队列存储是一项可存储大量消息的服务，用户可以通过 HTTP 或 HTTPS 从世界任何地方访问这些消息。 有关详细信息，请参阅 [Azure 队列存储简介](storage-queues-introduction.md)。 此操作指南文章介绍常见的队列存储操作。 你将学习如何执行以下操作：

> [!div class="checklist"]
>
> - 创建队列
> - 检索队列
> - 添加消息
> - 读取消息
> - 删除消息
> - 删除队列

本操作说明指南需要 Azure PowerShell (`Az`) 模块 v0.7 或更高版本。 运行 `Get-Module -ListAvailable Az` 以查找当前安装的版本。 如果需要进行升级，请参阅 [Install Azure PowerShell module](/powershell/azure/install-az-ps)（安装 Azure PowerShell 模块）。

队列的数据平面没有相应的 PowerShell cmdlet。 若要执行数据平面操作（如添加消息、读取消息和删除消息），必须使用 PowerShell 中公开的 .NET 存储客户端库。 创建消息对象，然后可以使用命令（例如 `AddMessage`）对该消息执行操作。 本文介绍如何执行该操作。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>登录 Azure

运行 `Connect-AzAccount` 命令以登录 Azure 订阅，并按照屏幕上的说明操作。

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>检索位置列表

如果你不知道要使用哪个位置，可以列出可用的位置。 显示列表后，找到要使用的位置。 本练习将使用 `eastus`。 将此内容存储在变量 `location` 中，以供以后使用。

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>创建资源组

使用 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) 命令创建资源组。

Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 将资源组名称存储在变量中，以供以后使用。 在此示例中，在 `eastus` 区域中创建了名为 `howtoqueuesrg` 的资源组。

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>创建存储帐户

使用 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) 创建具有本地冗余存储 (LRS) 的标准常规用途存储帐户。 获取用于定义要使用的存储帐户的存储帐户上下文。 对存储帐户执行操作时，引用上下文而不是重复提供凭据。

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>创建队列

以下示例首先使用存储帐户上下文（包括存储帐户名称及其访问密钥）与 Azure 存储建立连接。 接下来，它将调用 [New-AzStorageQueue](/powershell/module/az.storage/new-azstoragequeue) cmdlet 以创建名为“`howtoqueue`”的队列。

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

有关 Azure 队列存储的命名约定的信息，请参阅[命名队列和元数据](/rest/api/storageservices/naming-queues-and-metadata)。

## <a name="retrieve-a-queue"></a>检索队列

可以查询和检索存储帐户中的特定队列，或者所有队列的列表。 以下示例演示如何检索存储帐户中的所有队列以及特定队列；这两个命令都使用 [Get-AzStorageQueue](/powershell/module/az.storage/get-azstoragequeue) cmdlet。

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>向队列添加消息

影响队列中的实际消息的操作使用 PowerShell 中公开的 .NET 存储客户端库。 若要将消息添加到队列，请创建消息对象的新实例 [`Microsoft.Azure.Storage.Queue.CloudQueueMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) 类。 接下来，调用 [`AddMessage`](/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage) 方法。 可从字符串（UTF-8 格式）或字节数组创建 `CloudQueueMessage`。

以下示例演示如何向队列中添加消息。

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

如果使用 [Azure 存储资源管理器](https://storageexplorer.com)，可以连接到 Azure 帐户并查看存储帐户中的队列，然后在队列中向下钻取以查看队列中的消息。

## <a name="read-a-message-from-the-queue-then-delete-it"></a>从队列中读取一条消息，然后删除该消息

尽量以先进先出的顺序读取消息。 但不能保证完全这样做。 从队列中读取消息时，消息将对查看此队列的所有其他进程不可见。 这可确保如果代码因硬件或软件故障而无法处理消息，则代码的其他实例可以获取相同消息并重试。

**不可见超时** 定义了消息在再次可供处理之前保持不可见的时间。 默认为 30 秒。

代码分两步从队列中读取消息。 调用 [`Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) 方法时，将获取队列中的下一条消息。 从 `GetMessage` 返回的消息对于从此队列读取消息的任何其他代码都是不可见的。 若要完成从队列中删除消息的操作，请调用 [`Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage`](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage) 方法。

在以下示例中，你将读完三条队列消息，然后等待 10 秒（不可见超时）。 然后再次读取这三条消息，读取后，通过调用 `DeleteMessage` 删除该消息。 如果在删除消息后尝试读取队列，`$queueMessage` 将返回为 `$null`。

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>删除队列

若要删除队列及其中包含的所有消息，请调用 `Remove-AzStorageQueue` cmdlet。 以下示例演示如何使用 `Remove-AzStorageQueue` cmdlet 删除本练习中使用的特定队列。

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>清理资源

若要删除已在此练习中创建的所有资产，请删除该资源组。 这会一并删除组中包含的所有资源。 在这种情况下，它会删除创建的存储帐户以及资源组本身。

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>后续步骤

本操作指南文章介绍了使用 PowerShell 进行队列存储管理的基本知识，其中包括如何：

> [!div class="checklist"]
>
> - 创建队列
> - 检索队列
> - 添加消息
> - 读取下一条消息
> - 删除消息
> - 删除队列

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell 存储 cmdlet

- [存储 PowerShell cmdlet](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure 存储资源管理器

- [Microsoft Azure 存储资源管理器](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json)是 Microsoft 免费提供的独立应用，适用于在 Windows、macOS 和 Linux 上以可视方式处理 Azure 存储数据。
