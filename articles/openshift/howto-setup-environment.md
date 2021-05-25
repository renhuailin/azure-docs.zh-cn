---
title: 设置 Azure Red Hat OpenShift 开发环境
description: 下面是使用 Microsoft Azure Red Hat OpenShift 的先决条件。
keywords: red hat openshift setup set up
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.custom: devx-track-azurecli
ms.openlocfilehash: c253c6bf81305b9b336525c20980cf9599463648
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209860"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>设置 Azure Red Hat OpenShift 开发环境

> [!IMPORTANT]
> Azure Red Hat OpenShift 3.11 将于 2022 年 6 月 30 日停用。 对新建 Azure Red Hat OpenShift 3.11 群集的支持会持续到 2020 年 11 月 30 日。 在停用之后，剩余的 Azure Red Hat OpenShift 3.11 群集将会关闭，以防出现安全漏洞。
> 
> 请按照本指南[创建 Azure Red Hat OpenShift 4 群集](tutorial-create-cluster.md)。
> 如有具体问题，[请与我们联系](mailto:arofeedback@microsoft.com)。

若要生成并运行 Microsoft Azure Red Hat OpenShift 应用程序，需要执行以下操作：

* 安装 Azure CLI 版本 2.0.65（或更高版本）（或使用 Azure Cloud Shell）。
* 注册 `AROGA` 功能和关联的资源提供程序。
* 创建 Azure Active Directory (Azure AD) 租户。
* 创建 Azure AD 应用程序对象。
* 创建 Azure AD 用户。

以下说明将指导你完成所有这些先决条件。

## <a name="install-the-azure-cli"></a>安装 Azure CLI

Azure Red Hat OpenShift 需要 Azure CLI 版本 2.0.65 或更高版本。 如果已安装 Azure CLI，可以运行以下命令来检查已安装的版本：

```azurecli
az --version
```

输出的第一行包含 CLI 版本，例如 `azure-cli (2.0.65)`。

如果需要全新安装或升级，请参阅[安装 Azure CLI](/cli/azure/install-azure-cli) 的说明。

或者，可以使用 [Azure Cloud Shell](../cloud-shell/overview.md)。 使用 Azure Cloud Shell 时，请确保选择 Bash 环境（如果计划按照[创建和管理 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)教程系列中的说明进行操作）。

## <a name="register-providers-and-features"></a>注册提供程序和功能

在部署第一个 Azure Red Hat OpenShift 群集之前，必须将 `Microsoft.ContainerService AROGA` 功能、`Microsoft.Solutions`、`Microsoft.Compute`、`Microsoft.Storage`、`Microsoft.KeyVault` 和 `Microsoft.Network` 提供程序手动注册到你的订阅。

若要手动注册这些提供程序和功能，请从 Bash shell（如果已安装 CLI）或者从 Azure 门户中的 Azure Cloud Shell (Bash) 会话按照以下说明操作：

1. 如果有多个 Azure 订阅，请指定相关订阅 ID：

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. 注册 Microsoft.ContainerService AROGA 功能：

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. 注册 Microsoft.Storage 提供程序：

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. 注册 Microsoft.Compute 提供程序：

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. 注册 Microsoft.Solutions 提供程序：

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. 注册 Microsoft.Network 提供程序：

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. 注册 Microsoft.KeyVault 提供程序：

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. 刷新 Microsoft.ContainerService 资源提供程序的注册：

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>创建 Azure Active Directory (Azure AD) 租户

Azure Red Hat OpenShift 服务需要一个关联的 Azure Active Directory (Azure AD) 租户，来表示组织及其与 Microsoft 的关系。 使用 Azure AD 租户可以注册、生成和管理应用，以及使用其他 Azure 服务。

如果没有要用作 Azure Red Hat OpenShift 群集租户的 Azure AD，或者想要创建用于测试的租户，请先按照[为 Azure Red Hat OpenShift 群集创建 Azure AD 租户](howto-create-tenant.md)中的说明进行操作，然后再继续阅读本指南。

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>创建 Azure AD 用户、安全组和应用程序对象

Azure Red Hat OpenShift 需要相应权限才能在群集上执行任务，例如配置存储。 这些权限通过[服务主体](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)表示。 还需要创建一个新的 Active Directory 用户，用于测试在 Azure Red Hat OpenShift 群集上运行的应用。

按照[创建 Azure AD 应用对象和用户](howto-aad-app-configuration.md)中的说明创建服务主体，为应用生成客户端密码和身份验证回调 URL，并创建新的 Azure AD 安全组和用户以访问群集。

## <a name="next-steps"></a>后续步骤

现在可以随时使用 Azure Red Hat OpenShift！

试用教程：
> [!div class="nextstepaction"]
> [创建 Azure Red Hat OpenShift 群集](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli