---
title: 使用 Azure 资源管理器模板创建或修改实验室
description: 了解如何将 Azure 资源管理器模板与 PowerShell 配合使用，以在开发测试实验室中自动创建或修改实验室
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: dac336902588655bc78bc593c408a10cfdf0b8af
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128591551"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>使用 Azure 资源管理器模板和 PowerShell 自动创建或修改实验室

开发测试实验室提供许多 Azure 资源管理器模板和 PowerShell 脚本，有助于快速且自动创建新实验室或修改现有实验室，并部署这些资源。

本文有助于指导用户完成使用这些模板和脚本自动创建、修改和部署实验室的过程。 本文还介绍在哪里可以找到关于如何在开发测试实验室中使用 PowerShell 执行一些常见任务的详细信息。

## <a name="step-1-gather-your-templates-and-scripts"></a>步骤 1：收集模板和脚本
可以在公共 [GitHub 存储库](https://github.com/Azure/azure-devtestlab)中找到预制的 [Azure 资源管理器模板](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)和 [PowerShell 脚本](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)。 按原样使用它们，或对它们进行自定义以满足需求，然后将它们存储在[专用 Git 存储库](devtest-lab-add-artifact-repo.md)中。

## <a name="step-2-modify-your-azure-resource-manager-template"></a>步骤 2：修改 Azure 资源管理器模板
如果之前从未创建过模板，则可以按照[创建第一个 Azure 资源管理器模板](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)中的步骤进行操作。

此外，[创建 Azure 资源管理器模板的最佳做法](../azure-resource-manager/templates/best-practices.md)提供许多指导原则和建议，可帮助创建可靠且易用的 Azure 资源管理器模板。 通常情况下，将使用所提供的方法或示例之一的变体，并根据需要修改模板。

## <a name="step-3-deploy-resources-with-powershell"></a>步骤 3：通过 PowerShell 部署资源
自定义模板和脚本后，请遵循[通过 Resource Manager 模板和 Azure PowerShell 部署资源](../azure-resource-manager/templates/deploy-powershell.md)的必需步骤操作。 本文提供关于将 Azure PowerShell 与 Azure 资源管理器模板配合使用以将资源部署到 Azure 的常规信息。


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>在开发测试实验室中可以使用 PowerShell 执行的常见任务
使用 PowerShell 可以自动执行其他许多常见任务。 文档的以下部分概述了执行这些任务所需的步骤。

* [使用 PowerShell 基于 VHD 文件创建自定义映像](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [使用 PowerShell 将 VHD 文件上传到实验室的存储帐户](devtest-lab-upload-vhd-using-powershell.md)
* [使用 PowerShell 将外部用户添加到实验室](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [使用 PowerShell 创建实验室自定义角色](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>后续步骤
* 了解如何创建存储自定义模板或脚本的[专用 Git 存储库](devtest-lab-add-artifact-repo.md)。
* 浏览 [Azure 快速入门模板库中的 Azure 资源管理器模板](https://github.com/Azure/azure-quickstart-templates)。