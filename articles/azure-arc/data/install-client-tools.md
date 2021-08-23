---
title: 安装客户端工具
description: 安装 azdata、kubectl、Azure CLI、psql、Azure Data Studio（预览体检成员）以及 Azure Data Studio 的 Arc 扩展
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e328c65eb7453a8e80faa315c0b038e8fa6f13e1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750285"
---
# <a name="install-client-tools-for-deploying-and-managing-azure-arc-enabled-data-services"></a>安装用于部署和管理已启用 Azure Arc 的数据服务的客户端工具

本文介绍了一些资源，这些资源用于安装可管理已启用 Arc 的数据服务的工具。

> [!IMPORTANT]
> 如果要更新到新版本，请将 Azure Data Studio、Azure Data Studio 的 Azure Arc 扩展、Azure (`az`) 命令行界面 (CLI) 以及 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)] 更新到最新版本。
>
> [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)] 

[Azure CLI (`az`) 的 `arcdata` 扩展](reference/reference-az-arcdata-dc.md)将替换已启用 Arc 的数据服务的 `azdata`。

## <a name="tools-for-creating-and-managing-azure-arc-enabled-data-services"></a>用于创建和管理已启用 Azure Arc 的数据服务的工具

下表列出了创建和管理已启用 Azure Arc 的数据服务所需的常用工具以及这些工具的安装方法：

| 工具 | 必选 | 说明 | 安装 |
|---|---|---|---|
| Azure CLI (`az`)<sup>1</sup> | 是 | 用于管理 Azure 服务的新式命令行接口。 通常用于管理 Azure 服务，特别是使用 CLI 或脚本管理已启用 Arc 的数据服务，适用于间接连接模式（现已推出）和直接连接模式（即将推出）。 （[了解详细信息](/cli/azure/)）。 | [安装](/cli/azure/install-azure-cli) |
| 适用于 Azure (`az`) CLI 的 `arcdata` 扩展 | 是 | 用于管理已启用 Arc 的数据服务的命令行工具，作为 Azure CLI (`az`) 的扩展 | [安装](install-arcdata-extension.md) |
| Azure Data Studio | 是 | 丰富的体验工具，用于连接和查询各种数据库，包括 Azure SQL、SQL Server、PostrgreSQL 和 MySQL。 Azure Data Studio 的扩展为已启用 Azure Arc 的数据服务提供管理体验。 | [安装](/sql/azure-data-studio/download-azure-data-studio) |
| Azure Data Studio 的 Azure Arc 扩展 | 是 | 为已启用 Azure Arc 的数据服务提供管理体验的 Azure Data Studio 扩展。| 从 Azure Data Studio 中的扩展库安装。|
| Azure Data Studio 中的 PostgreSQL 扩展 | 否 | Azure Data Studio 的 PostgreSQL 扩展，为 PostgreSQL 提供管理功能。 | <!--{need link} [Install](../azure-data-studio/data-virtualization-extension.md) --> 从 Azure Data Studio 中的扩展库安装。|
| Kubernetes CLI (kubectl)<sup>2</sup> | 是 | 用于管理 Kubernetes 群集的命令行工具（[了解详细信息](https://kubernetes.io/docs/tasks/tools/install-kubectl/)）。 | [Windows](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-with-powershell-from-psgallery) \| [Linux](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-using-native-package-management) |
| curl <sup>3</sup> | 对于部分示例脚本是必需的。 | 使用 URL 传输数据的命令行工具。 | [Windows](https://curl.haxx.se/windows/) \| Linux：安装 curl 包 |
| oc | 是 Red Hat OpenShift 和 Azure Redhat OpenShift 部署所必需的。 |`oc` 是 Open Shift 命令行接口 (CLI)。 | [安装 CLI](https://docs.openshift.com/container-platform/4.6/cli_reference/openshift_cli/getting-started-cli.html#installing-the-cli)



<sup>1</sup> 必须使用 Azure CLI 2.26.0 或更高版本。 如果需要，可运行 `az --version` 来查找版本。

<sup>2</sup> 必须使用 `kubectl` 1.19 或更高版本。 此外，`kubectl` 的版本应该加或减 Kubernetes 群集的一个次要版本。 要在 `kubectl` 客户端上安装特定版本，请参阅[通过 curl 安装 `kubectl` 二进制文件](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-binary-using-curl)（在 Windows 10 上，使用 cmd.exe 而不是 Windows PowerShell 来运行 curl）。

<sup>3</sup> 若要使用 PowerShell，则 curl 是 Invoke-WebRequest cmdlet 的别名。

## <a name="next-steps"></a>后续步骤

[创建 Azure Arc 数据控制器](create-data-controller.md)
