---
title: 使用 Azure 中部署的 SSIS 包打开并保存文件
description: 了解将使用本地文件系统的 SSIS 包提升并移动到 Azure 中的 SSIS 时，如何在本地和 Azure 中打开并保存文件
ms.date: 06/27/2018
ms.topic: conceptual
ms.service: data-factory
ms.subservice: integration-services
author: swinarko
ms.author: sawinark
ms.reviewer: jburchel
ms.openlocfilehash: fe6640336093fd4a47c7f3a220293b720cf66587
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121731435"
---
# <a name="open-and-save-files-on-premises-and-in-azure-with-ssis-packages-deployed-in-azure"></a>使用 Azure 中部署的 SSIS 包在本地和 Azure 中打开并保存文件

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本文介绍将使用本地文件系统的 SSIS 包提升和移动到 Azure 中的 SSIS 时，如何在本地和 Azure 中打开并保存文件。

## <a name="save-temporary-files"></a>保存临时文件

如果在单个包执行期间需存储和处理临时文件，包可以使用 Azure-SSIS Integration Runtime 节点的当前工作目录 (`.`) 或临时文件夹 (`%TEMP%`)。

## <a name="use-on-premises-file-shares"></a>使用本地文件共享

要在将使用本地文件系统的包提升和迁移到 Azure 中的 SSIS 时继续使用本地文件共享，请执行以下操作：

1. 将文件从本地文件系统传输到本地文件共享。

2. 将本地文件共享联接到 Azure 虚拟网络。

3. 将 Azure-SSIS IR 联接到同一个虚拟网络。 有关详细信息，请参阅[将 Azure-SSIS 集成运行时加入虚拟网络](./join-azure-ssis-integration-runtime-virtual-network.md)。

4. 通过设置使用 Windows 身份验证的访问凭据，将 Azure-SSIS IR 连接到同一个虚拟网络内的本地文件共享。 有关详细信息，请参阅[使用 Windows 身份验证连接到数据和文件共享](ssis-azure-connect-with-windows-auth.md)。

5. 将包中的本地文件路径更新为指向本地文件共享的 UNC 路径。 例如，将 `C:\abc.txt` 更新为 `\\<on-prem-server-name>\<share-name>\abc.txt`。

## <a name="use-azure-file-shares"></a>使用 Azure 文件共享

若直接迁移将本地文件系统使用到 Azure 中 SSIS 时要使用 Azure 文件，请执行以下操作：

1. 将本地文件系统的文件传输到 Azure 文件。 有关详细信息，请参阅 [Azure 文件](https://azure.microsoft.com/services/storage/files/)。

2. 通过设置使用 Windows 身份验证的访问凭据，将 Azure-SSIS IR 连接到 Azure 文件。 有关详细信息，请参阅[使用 Windows 身份验证连接到数据和文件共享](ssis-azure-connect-with-windows-auth.md)。

3. 更新包中的本地文件路径到指向 Azure 文件的 UNC 路径。 例如，将 `C:\abc.txt` 更新为 `\\<storage-account-name>.file.core.windows.net\<share-name>\abc.txt`。

## <a name="next-steps"></a>后续步骤

- 部署包。 有关详细信息，请参阅[使用 SSMS 将 SSIS 项目部署到 Azure](/sql/integration-services/ssis-quickstart-deploy-ssms)。
- 运行包。 有关详细信息，请参阅[使用 SSMS 运行 Azure 中的 SSIS 包](/sql/integration-services/ssis-quickstart-run-ssms)。
- 计划包。 有关详细信息，请参阅[计划 Azure 中的 SSIS 包](/sql/integration-services/lift-shift/ssis-azure-schedule-packages-ssms)。