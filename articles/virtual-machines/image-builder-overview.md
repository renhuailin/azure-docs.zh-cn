---
title: 了解 Azure 映像生成器
description: 详细了解 Azure 中虚拟机的 Azure 映像生成器。
author: kof-f
ms.author: kofiforson
ms.date: 05/24/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 805b2a8bf29edfd76804aec3607acc11155edd16
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697067"
---
# <a name="azure-image-builder-overview"></a>Azure 映像生成器概述

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

借助标准化虚拟机 (VM) 映像，组织可迁移到云，并确保在部署中保持一致。 映像通常包括预定义的安全性和配置设置，以及必需的软件。 设置自己的映像管道需要时间、基础结构和设置，但使用 Azure VM 映像生成器，只需提供一个描述映像的配置，将其提交给该服务，即可生成映像并进行分发。
 
通过 Azure VM 映像生成器（Azure 映像生成器），可从基于 Windows 或 Linux 的 Azure 市场映像、现有自定义映像开始，并开始添加自己的自定义项。 由于映像生成器在 [HashiCorp Packer](https://packer.io/) 上生成，你会发现一些相似性，但映像生成器具有托管服务的优势。 在 [Azure 共享映像库](shared-image-galleries.md)中，还可以指定要在其中托管映像的位置（作为托管映像或 VHD）。


## <a name="features"></a>功能

Azure 映像生成器支持以下功能：

- 创建基线映像，该映像包括最低安全性和企业配置，使部门可以进一步对其进行自定义。
- 集成核心应用程序，因此 VM 可以在创建后执行工作负载，或添加配置以支持 Azure 虚拟桌面映像。
- 修补现有映像，使用映像生成器可以持续修补现有的自定义映像。
- 将映像生成器连接到现有的虚拟网络，以便连接到现有的配置服务器（DSC、Chef、Puppet 等）、文件共享或任何其他可路由的服务器/服务。
- 与 Azure 共享映像库集成，以在全球范围内分发、更改和缩放映像，并提供映像管理系统。
- 与现有映像生成管道集成，只需从管道调用映像生成器，或者使用简单的映像生成器 Azure DevOps 任务即可。
- 将现有的映像自定义管道迁移到 Azure。 使用现有的脚本、命令和进程来自定义映像。
- 创建 VHD 格式的映像以支持 Azure Stack。
 

## <a name="regions"></a>区域

Azure 映像生成器服务在[这些](./linux/image-builder-json.md#location)区域可用。 可以在这些区域外分发映像。

## <a name="os-support"></a>OS 支持
AIB 将支持 Azure 市场基础 OS 映像：
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6、7.7
- CentOS 7.6、7.7
- SLES 12 SP4
- SLES 15、SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 多会话/Professional
- Windows 2016
- Windows 2019

我们列出了已测试并了解可以与 Azure 映像生成器一起工作的操作系统。 但是，Azure 映像生成器应可与市场中的任何 Linux 或 Windows 映像一起工作。

## <a name="how-it-works"></a>工作原理

Azure 映像生成器是一种完全托管的 Azure 服务，可通过 Azure 资源提供程序进行访问。 向服务提供配置，该配置指定要执行的源映像和自定义，以及要将新映像分发到的位置，下图显示了一个高级工作流：

![显示了源 (Windows/Linux)、自定义（Shell、PowerShell、Windows 重新启动和更新、添加文件）和使用Azure 共享映像库进行全局分发的 Azure 映像生成器过程的概念图](./media/image-builder-overview/image-builder-flow.png)

可以使用 PowerShell、Az CLI、ARM 模板和 Azure VM 映像生成器 DevOps 任务来传递模板配置，将其提交到服务时，我们会创建一个映像模板资源。 创建映像模板资源后，订阅中会显示创建的暂存资源组，格式为： IT_\<DestinationResourceGroup>\<TemplateName>\(GUID)。 暂存资源组包含 ScriptURI 属性中 File、Shell、PowerShell 自定义中引用的文件和脚本。

若要运行生成，你将在映像模板资源上调用 `Run`，然后服务将为生成部署额外的资源，如 VM、网络、磁盘、网络适配器等。如果在不使用现有 VNET 的情况下生成映像，映像生成器也会部署一个公共 IP 和 NSG，服务会使用 SSH 或 WinRM 连接到生成 VM。 如果选择现有 VNET，则该服务将使用 Azure 专用链接进行部署，并且不需要公共 IP 地址。有关映像生成器网络的更多详细信息，请查看[详细信息](./linux/image-builder-networking.md)。

生成完成后，将删除所有资源（暂存资源组和存储帐户除外）。若要删除这些资源，你将删除映像模板资源，也可以保留它们以再次运行生成。

本文档中有多个示例和分步指南，均引用了 [Azure 映像生成器 GitHub 存储库](https://github.com/azure/azvmimagebuilder)中的配置模板和解决方案。

### <a name="move-support"></a>移动支持
映像模板资源不可变，其中包含指向资源和暂存资源组的链接，因此资源类型不支持移动。 如果想要移动映像模板资源，请确保你有配置模板的副本（如果没有，可从资源中提取现有配置），使用新名称在新资源组中创建新的映像模板资源，然后删除以前的映像模板资源。 

## <a name="permissions"></a>权限
注册 (AIB) 时，这会授予 AIB 服务创建、管理和删除暂存资源组 (IT_*) 以及向其添加资源的权限，映像生成需要这些权限。 这是通过在成功注册期间在订阅中提供的 AIB 服务主体名称 (SPN) 来实现的。

若要允许 Azure VM 映像生成器将映像分发到托管映像或共享映像库，则需要创建一个拥有读取和写入映像权限的 Azure 用户分配的标识。 如果要访问 Azure 存储，则需要具有读取专用或公共容器的权限。

针对 [PowerShell](./linux/image-builder-permissions-powershell.md) 和 [AZ CLI](./linux/image-builder-permissions-cli.md) 的权限有更详细的解释。

## <a name="costs"></a>成本
通过 Azure 映像生成器创建、构建和存储映像时，会产生一些计算、网络和存储成本。 这些成本与手动创建自定义映像产生的成本类似。 对于资源，将按 Azure 费率向你收费。 

在映像创建过程中，会下载文件并将其存储在 `IT_<DestinationResourceGroup>_<TemplateName>` 资源组中，这会产生较低的存储成本。 如果不想保留这些文件，请在映像生成后删除“映像模板”。
 
映像生成器创建 D1v2 VM 大小的 VM、存储和 VM 所需的网络。 这些资源将在生成过程中持续存在，并将在映像生成器创建完映像后删除。 
 
Azure 映像生成器会将映像分发到所选区域，这可能会产生网络流出量费用。

## <a name="hyper-v-generation"></a>Hyper-V 代系
映像生成器目前仅本机支持将 Hyper-V 第 1 代 (Gen1) 映像创建到 Azure 共享映像库 (SIG) 或托管映像。 
 
## <a name="next-steps"></a>后续步骤 
 
若要试用 Azure 映像生成器，请参阅构建 [Linux](./linux/image-builder.md) 或 [Windows](./windows/image-builder.md) 映像的文章。
