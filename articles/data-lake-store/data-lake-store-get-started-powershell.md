---
title: 开始使用 Azure Data Lake Storage Gen1 - PowerShell | Microsoft Docs
description: 使用 Azure PowerShell 创建 Azure Data Lake Store Gen1 帐户并执行基本操作。
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 06/27/2018
ms.author: twooley
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 485b14f85d68290c5447c885b9bc4974318f7952
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92103724"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>通过 Azure PowerShell 开始使用 Azure Data Lake Storage Gen1

> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

了解如何使用 Azure PowerShell 创建 Azure Data Lake Storage Gen1 帐户并执行基本操作，如创建文件夹、上传和下载数据文件、删除帐户等。有关 Data Lake Storage Gen1 的详细信息，请参阅 [Data Lake Storage Gen1 概述](data-lake-store-overview.md)。

## <a name="prerequisites"></a>必备条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure 订阅**。 请参阅[获取 Azure 免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* **Azure PowerShell 1.0 或更高版本**。 请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/)。

## <a name="authentication"></a>身份验证

本文对 Data Lake Store Gen1 使用一种较为简单的身份验证方法，其中会提示输入 Azure 帐户凭据。 系统会根据登录用户的访问级别约束对 Data Lake Storage Gen1 帐户和文件系统的访问权限。 但是，也可以使用其他方法在 Data Lake Storage Gen1 中进行身份验证，即最终用户身份验证或服务到服务身份验证。 有关如何进行身份验证的说明和详细信息，请参阅[最终用户身份验证](data-lake-store-end-user-authenticate-using-active-directory.md)或[服务到服务身份验证](./data-lake-store-service-to-service-authenticate-using-active-directory.md)。

## <a name="create-a-data-lake-storage-gen1-account"></a>创建 Data Lake Storage Gen1 帐户

1. 从桌面打开新的 Windows PowerShell 窗口。 输入以下代码片段以登录到 Azure 帐户，设置订阅，并注册 Data Lake Store Gen1 提供程序。 当系统提示登录时，请确保以订阅管理员/所有者身份登录：

    ```PowerShell
    # Log in to your Azure account
    Connect-AzAccount

    # List all the subscriptions associated to your account
    Get-AzSubscription

    # Select a subscription
    Set-AzContext -SubscriptionId <subscription ID>

    # Register for Azure Data Lake Storage Gen1
    Register-AzResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
    ```

1. Data Lake Store Gen1 帐户与 Azure 资源组关联。 首先请创建资源组。

    ```PowerShell
    $resourceGroupName = "<your new resource group name>"
    New-AzResourceGroup -Name $resourceGroupName -Location "East US 2"
    ```

    ![创建 Azure 资源组](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "创建 Azure 资源组")

1. 创建 Data Lake Storage Gen1 帐户。 指定名称必须仅包含小写字母与数字。

    ```PowerShell
    $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
    New-AzDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"
    ```

    ![创建 Data Lake Storage Gen1 帐户](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "创建 Data Lake Storage Gen1 帐户")

1. 验证是否已成功创建帐户。

    ```PowerShell
    Test-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
    ```

    cmdlet 的输出应为 True。

## <a name="create-directory-structures"></a>创建目录结构

可以在 Data Lake Store Gen1 帐户下创建目录，用于管理和存储数据。

1. 指定根目录。

    ```PowerShell
    $myrootdir = "/"
    ```

1. 在指定根下，创建一个名为 **mynewdirectory** 的新目录。

    ```PowerShell
    New-AzDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
    ```

1. 验证是否已成功创建新目录。

    ```PowerShell
    Get-AzDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir
    ```

    显示的输出应该如以下屏幕截图所示：

    ![验证目录](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "验证目录")

## <a name="upload-data"></a>上传数据

可以直接将数据上传到 Data Lake Store Gen1 的根级别，也可以上传到在帐户中创建的目录。 此节中的代码片段演示了如何将一些示例数据上传到上一节中创建的目录 (**mynewdirectory**)。

如果正在查找一些示例数据进行上传，可以从 **Azure Data Lake Git 存储库** 获取 [Ambulance Data](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData)文件夹。 下载文件，并将其存储在计算机的本地目录中，如 C:\sampledata\。

```PowerShell
Import-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
   -Path "C:\sampledata\vehicle1_09142014.csv" `
   -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv
```

## <a name="rename-download-and-delete-data"></a>重命名、下载和删除数据

使用以下命令，重命名文件：

```PowerShell
Move-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv `
    -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

使用以下命令，下载文件：

```PowerShell
Export-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv `
    -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"
```

使用以下命令，删除文件：

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv
```

出现提示时，输入 **Y** 以删除项目。 如果有多个要删除的文件，可以用逗号分隔提供所有路径。

```PowerShell
Remove-AzDataLakeStoreItem -AccountName $dataLakeStorageGen1Name `
    -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv
```

## <a name="delete-your-account"></a>删除你的帐户

使用以下命令删除 Data Lake Storage Gen1 帐户。

```PowerShell
Remove-AzDataLakeStoreAccount -Name $dataLakeStorageGen1Name
```

出现提示时，输入 **Y** 删除帐户。

## <a name="next-steps"></a>后续步骤

* [将 PowerShell 与 Azure Data Lake Store Gen1 配合使用的性能优化指南](data-lake-store-performance-tuning-powershell.md)
* [使用 Azure Data Lake Storage Gen1 满足大数据要求](data-lake-store-data-scenarios.md)
* [保护 Data Lake Storage Gen1 中的数据](data-lake-store-secure-data.md)
* [将 Azure Data Lake Analytics 与 Data Lake Storage Gen1 配合使用](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [将 Azure HDInsight 与 Data Lake Storage Gen1 配合使用](data-lake-store-hdinsight-hadoop-use-portal.md)