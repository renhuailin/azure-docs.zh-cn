---
title: 在虚拟网络中启用 Azure 机器学习工作室
titleSuffix: Azure Machine Learning
description: 了解如何配置 Azure 机器学习工作室来访问存储在虚拟网络内部的数据。
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: how-to
ms.reviewer: larryfr
ms.author: jhirono
author: jhirono
ms.date: 07/13/2021
ms.custom: contperf-fy20q4, tracking-python, security
ms.openlocfilehash: 6a10384757552108aefc3dd828bf0fd7ddce2f82
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427488"
---
# <a name="use-azure-machine-learning-studio-in-an-azure-virtual-network"></a>在 Azure 虚拟网络中使用 Azure 机器学习工作室

本文介绍如何在虚拟网络中使用 Azure 机器学习工作室。 工作室包括 AutoML、设计器和数据标签等功能。 

在虚拟网络中，默认情况下会禁用某些工作室功能。 若要重新启用这些功能，必须为计划在工作室中使用的存储帐户启用托管标识。 

在虚拟网络中，默认情况下禁用以下操作：

* 预览工作室中的数据。
* 在设计器中将数据可视化。
* 在设计器中部署模型。
* 提交 AutoML 试验。
* 启动标记项目。

工作室支持从虚拟网络中的以下数据存储类型读取数据：

* Azure 存储帐户（blob 和文件）
* Azure Data Lake Storage Gen1
* Azure Data Lake Storage Gen2
* Azure SQL 数据库

在本文中，学习如何：

> [!div class="checklist"]
> - 授予工作室访问存储在虚拟网络内部的数据的权限。
> - 从虚拟网络内部的资源访问工作室。
> - 了解工作室如何影响存储安全性。

> [!TIP]
> 本文是介绍如何保护 Azure 机器学习工作流的系列文章的一部分。 请参阅本系列中的其他文章：
>
> * [虚拟网络概述](how-to-network-security-overview.md)
> * [保护工作区资源](how-to-secure-workspace-vnet.md)
> * [保护训练环境](how-to-secure-training-vnet.md)
> * [保护推理环境](how-to-secure-inferencing-vnet.md)
> * [使用自定义 DNS](how-to-custom-dns.md)
> * [使用防火墙](how-to-access-azureml-behind-firewall.md)


## <a name="prerequisites"></a>先决条件

+ 阅读[网络安全概述](how-to-network-security-overview.md)，了解常见的虚拟网络方案和体系结构。

+ 要使用的预先存在的虚拟网络和子网。

+ 现有的[具有专用终结点的 Azure 机器学习工作区](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint)。

+ 现有的[已添加虚拟网络的 Azure 存储帐户](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts)。

## <a name="limitations"></a>限制

### <a name="azure-storage-account"></a>Azure 存储帐户

但有一个已知问题，即默认文件存储不会自动创建 `azureml-filestore` 文件夹，而提交 AutoML 试验又需要此文件夹。 当用户在创建工作区的过程中将现有文件存储设置为默认文件存储时，就会发生这种情况。

有两个选项可避免此问题：1) 使用在创建工作区的过程中系统自动为你创建的默认文件存储。 2) 若要使用自己的文件存储，请在创建工作区的过程中确保文件存储位于 VNet 外部。 创建工作区后，再将存储帐户添加到虚拟网络。

若要解决此问题，请从虚拟网络中删除文件存储帐户，然后将其重新添加到虚拟网络。
## <a name="datastore-azure-storage-account"></a>数据存储：Azure 存储帐户

使用以下步骤来启用对 Azure Blob 和文件存储中存储的数据的访问：

> [!TIP]
> 工作区的默认存储帐户不需要执行第一步。 对于 VNet 后面的所有存储帐户以及工作区使用的所有帐户（包括默认存储帐户）都需要执行其他步骤。

1. 如果存储帐户是工作区的默认存储，请跳过此步骤。 如果不是默认值，请为工作区托管标识授予 Azure 存储帐户的“存储 Blob 数据读取者”角色，以便它可以从 Blob 存储读取数据。

    有关详细信息，请参阅[Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)内置角色。

1. 向存储专用终结点授予工作区托管标识“读取者”角色。 如果存储服务使用专用终结点，请授予工作区托管标识对专用终结点的“读取者”访问权限。 Azure AD 中工作区托管标识与 Azure 机器学习工作区的名称相同。

    > [!TIP]
    > 存储帐户可以有多个专用终结点。 例如，一个存储帐户可能为 blob 和文件存储提供单独的专用终结点。 将托管标识添加到这两个终结点。

    有关详细信息，请参阅[读取者](../role-based-access-control/built-in-roles.md#reader)内置角色。

   <a id='enable-managed-identity'></a>
1. 对默认存储账户启用托管标识身份验证。 每个 Azure 机器学习工作区都有两个默认存储帐户：一个默认的 Blob 存储帐户和一个默认的文件存储帐户，这两个帐户都是在创建工作区时定义的。 还可以在“数据存储”管理页面中设置新的默认值。

    ![显示可在何处查找默认数据存储的屏幕截图](./media/how-to-enable-studio-virtual-network/default-datastores.png)

    下表说明了为什么必须为工作区默认存储帐户使用托管标识身份验证。

    |存储帐户  | 说明  |
    |---------|---------|
    |工作区默认 Blob 存储| 存储设计器中的模型资源。 对此存储帐户启用托管标识身份验证才能在设计器中部署模型。 <br> <br> 如果设计器管道使用已配置为使用托管标识的非默认数据存储，则可以可视化和运行该设计器管道。 但如果未在默认数据存储上启用托管标识就尝试部署定型模型，则无论是否正在使用任何其他数据存储，部署都会失败。|
    |工作区默认文件存储| 存储 AutoML 试验资产。 对此存储帐户启用托管标识身份验证才能提交 AutoML 试验。 |

1. 将数据存储配置为使用托管标识身份验证。 将 Azure 存储帐户添加到具有[服务终结点](how-to-secure-workspace-vnet.md?tabs=se#secure-azure-storage-accounts)或[专用终结点](how-to-secure-workspace-vnet.md?tabs=pe#secure-azure-storage-accounts)的虚拟网络后，必须配置数据存储才能使用[托管标识](../active-directory/managed-identities-azure-resources/overview.md)身份验证。 这样，工作室就可以访问存储帐户中的数据。

    Azure 机器学习使用[数据存储](concept-data.md#datastores)连接到存储帐户。 创建新的数据存储时，请使用以下步骤将数据存储配置为使用托管标识身份验证：

    1. 在工作室中，选择“数据存储”。

    1. 若要更新现有数据存储，请选择相应的数据存储并选择“更新凭据”。

        若要创建新的数据存储，请选择“+ 新建数据存储”。

    1. 在数据存储设置中，对于“在 Azure 机器学习工作室中使用工作区托管标识进行数据预览和分析”，选择“是” 。

        ![显示如何启用托管工作区标识的屏幕截图](./media/how-to-enable-studio-virtual-network/enable-managed-identity.png)

    这些步骤使用 Azure RBAC 将工作区托管标识作为“读取者”添加到新的存储服务。 “读取者”访问权限允许工作区查看资源，但不能进行更改。

## <a name="datastore-azure-data-lake-storage-gen1"></a>数据存储：Azure Data Lake Storage Gen1

使用 Azure Data Lake Storage Gen1 作为数据存储时，只能使用 POSIX 样式的访问控制列表。 可以向工作区托管标识分配对资源的访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅 [Azure Data Lake Storage Gen1 中的访问控制](../data-lake-store/data-lake-store-access-control.md)。

## <a name="datastore-azure-data-lake-storage-gen2"></a>数据存储：Azure Data Lake Storage Gen2

使用 Azure Data Lake Storage Gen2 作为数据存储时，可以使用 Azure RBAC 和 POSIX 样式的访问控制列表 (ACL) 来控制虚拟网络内的数据访问。

若要使用 Azure RBAC，请将工作区托管标识添加到 [Blob 数据读取者](../role-based-access-control/built-in-roles.md#storage-blob-data-reader)角色。 有关详细信息，请参阅 [Azure 基于角色的访问控制](../storage/blobs/data-lake-storage-access-control-model.md#role-based-access-control)。

若要使用 ACL，可以向工作区托管标识分配访问权限，就像向任何其他安全主体分配访问权限一样。 有关详细信息，请参阅[文件和目录上的访问控制列表](../storage/blobs/data-lake-storage-access-control.md#access-control-lists-on-files-and-directories)。

## <a name="datastore-azure-sql-database"></a>数据存储： Azure SQL 数据库

若要使用托管标识访问存储在 Azure SQL 数据库中的数据，必须创建一个映射到托管标识的 SQL 包含用户。 若要详细了解如何从外部提供程序创建用户，请参阅[创建映射到 Azure AD 标识的包含用户](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)。

创建 SQL 包含用户后，使用 [GRANT T-SQL 命令](/sql/t-sql/statements/grant-object-permissions-transact-sql)向该用户授予权限。

## <a name="intermediate-module-output"></a>中间模块输出

使用 Azure 机器学习设计器中间模块输出时，可以在设计器中指定任何模块的输出位置。 使用此功能可将中间数据集存储在单独的位置，以用于安全性、日志记录或审核目的。 若要指定输出，请使用以下步骤：

1. 选择要指定其输出的模块。
1. 在右侧出现的模块设置窗格中，选择“输出设置”。
1. 指定要用于每个模块输出的数据存储。

确保有权访问虚拟网络中的中间存储帐户。 否则，管道将会失败。

为中间存储帐户[启用托管标识身份验证](#enable-managed-identity)，以便可视化输出数据。
## <a name="access-the-studio-from-a-resource-inside-the-vnet"></a>从 VNet 内部的资源访问工作室

如果要从虚拟网络内的资源（例如，计算实例或虚拟机）访问工作室，则必须允许从虚拟网络到工作室的出站流量。 

例如，如果使用网络安全组 (NSG) 来限制出站流量，请将一条规则添加到 __服务标记__ 目标 __AzureFrontDoor.Frontend__。

## <a name="firewall-settings"></a>防火墙设置

一些存储服务（如 Azure 存储帐户）有防火墙设置，该设置适用于该特定服务实例的公共终结点。 通常情况下，可以通过该设置来允许/禁止从公共 Internet 的特定 IP 地址进行的访问。 使用 Azure 机器学习工作室时，不支持此功能。 使用 Azure 机器学习 SDK 或 CLI 时，支持此功能。

> [!TIP]
> 使用 Azure 防火墙服务时，支持 Azure 机器学习工作室。 有关详细信息，请参阅[使用防火墙后面的工作区](how-to-access-azureml-behind-firewall.md)。
## <a name="next-steps"></a>后续步骤

本文是介绍如何保护 Azure 机器学习工作流系列文章的一部分。 请参阅本系列中的其他文章：

* [虚拟网络概述](how-to-network-security-overview.md)
* [保护工作区资源](how-to-secure-workspace-vnet.md)
* [保护训练环境](how-to-secure-training-vnet.md)
* [保护推理环境](how-to-secure-inferencing-vnet.md)
* [使用自定义 DNS](how-to-custom-dns.md)
* [使用防火墙](how-to-access-azureml-behind-firewall.md)
