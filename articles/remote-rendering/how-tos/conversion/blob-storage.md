---
title: 将 Azure Blob 存储用于模型转换
description: 介绍设置和使用 blob 存储进行模型转换的常见步骤。
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 629b48a378dd029d9952270e3c71762a5bb7aa82
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306715"
---
# <a name="use-azure-blob-storage-for-model-conversion"></a>将 Azure Blob 存储用于模型转换

[模型转换](model-conversion.md)服务需要访问 Azure Blob 存储，以便它能够检索输入数据并存储输出数据。 本文介绍如何执行最常见的步骤。

## <a name="prepare-azure-storage-accounts"></a>准备 Azure 存储帐户

- 创建一个存储帐户 (StorageV2)
- 在存储帐户中创建一个输入 blob 容器（例如名为“arrinput”）
- 在存储帐户中创建一个输出 blob 容器（例如名为“arroutput”）

> [!TIP]
> 有关如何设置存储帐户的分步说明，请参阅[快速入门：转换用于渲染的模型](../../quickstarts/convert-model.md)

可以使用以下工具之一创建存储帐户和 blob 容器：

- [Azure 门户](https://portal.azure.com)
- [az 命令行](/cli/azure/install-azure-cli)
- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
- SDK（C#、Python…）

## <a name="ensure-azure-remote-rendering-can-access-your-storage-account"></a>确保 Azure 远程渲染可以访问你的存储帐户

Azure 远程渲染需要从存储帐户检索模型数据并将数据写入回该帐户。

可以通过以下两种方式向 Azure 远程渲染授予对存储帐户的访问权限：

### <a name="connect-your-azure-storage-account-with-your-azure-remote-rendering-account"></a>将 Azure 存储帐户与 Azure 远程渲染帐户连接

按照[创建帐户](../create-an-account.md#link-storage-accounts)部分中给出的步骤进行操作。

### <a name="retrieve-sas-for-the-storage-containers"></a>检索存储容器的 SAS

存储访问签名 (SAS) 用于授予输入的读取访问权限和输出的写入访问权限。 建议每次转换模型时生成新的 URI。 由于 URI 会在一段时间后过期，因此将它们保留较长的时间可能会带来意外中断应用程序的风险。

有关 SAS 的详细信息，请参阅 [SAS 文档](../../../storage/common/storage-sas-overview.md)。

可以使用以下方法之一生成 SAS URI：

- az PowerShell 模块
  - 请参阅 [PowerShell 脚本示例](../../samples/powershell-example-scripts.md)
- [az 命令行](/cli/azure/install-azure-cli)
- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)
  - 右键单击容器“获取共享访问签名”（输入容器的读取及列出访问权限、输出容器的写入访问权限）
- SDK（C#、Python…）

在资产转换中使用共享访问签名的示例如 [Powershell 示例脚本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1 所示。

## <a name="upload-an-input-model"></a>上传输入模型

若要开始转换模型，需要使用以下选项之一上传模型：

- [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/) - 上传/下载/管理 azure blob 存储上的文件的便捷 UI
- [Azure 命令行](../../../storage/blobs/storage-quickstart-blobs-cli.md)
- [Azure PowerShell 模块](/powershell/azure/install-az-ps)
  - 请参阅 [PowerShell 脚本示例](../../samples/powershell-example-scripts.md)
- [使用存储 SDK（Python、C#…）](../../../storage/index.yml)
- [使用 Azure 存储 REST API](/rest/api/storageservices/blob-service-rest-api)

有关如何上传数据进行转换的示例，请参阅 [Powershell 示例脚本](../../samples/powershell-example-scripts.md#script-conversionps1)的 Conversion.ps1。

> [!Note]
> 上传输入模型时，请注意避免长文件名和/或文件夹结构，以避免服务上出现 [Windows 路径长度限制](https://docs.mxicrosoft.com/windows/win32/fileio/maximum-file-path-limitation)问题。 

## <a name="get-a-sas-uri-for-the-converted-model"></a>获取已转换模型的 SAS URI

此步骤类似于[检索用于存储容器的 SAS](#retrieve-sas-for-the-storage-containers)。 但这一次，你需要检索已写入输出容器的模型文件的 SAS URI。

例如，若要通过 [Azure 存储资源管理器](https://azure.microsoft.com/features/storage-explorer/)检索 SAS URI，请右键单击模型文件并选择“获取共享访问签名”。

如果尚未将存储帐户连接到 Azure 远程渲染帐户，则需要一个用于加载模型的共享访问签名 (SAS)。 可在[创建帐户](../create-an-account.md#link-storage-accounts)中了解如何连接帐户。

## <a name="next-steps"></a>后续步骤

- [配置模型转换](configure-model-conversion.md)
- [模型转换 REST API](conversion-rest-api.md)
