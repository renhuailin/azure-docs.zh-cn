---
title: 保存和分发图像
description: 本文介绍在 Azure 开发测试实验室中保存已创建的虚拟机 (VM) 中自定义映像的步骤。
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: d89a792e66d227eaeb20e48c80d432aa0cf56871
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128623563"
---
# <a name="save-custom-images-and-distribute-to-multiple-labs"></a>保存自定义映像并分发到多个实验室
本文介绍保存已创建的虚拟机 (VM) 中自定义映像的步骤。 此外，还介绍如何将这些自定义映像分发到组织中的其他开发测试实验室。

## <a name="prerequisites"></a>先决条件
应已准备好以下各项：

- Azure 开发测试实验室中用于映像中心的实验室。
- 用于实现映像中心自动化的 Azure DevOps 项目。
- 包含脚本和配置的源代码位置（在本例中，位置是上一步提到的同一 DevOps 项目）。
- 用于协调 Azure PowerShell 任务的生成定义。

如果需要，请按照[从 Azure DevOps 运行映像中心](image-factory-set-up-devops-lab.md)中的步骤创建或设置这些项目。 

## <a name="save-vms-as-generalized-vhds"></a>将 VM 保存为通用 VHD
将现有 VM 保存为通用 VHD。  下面是一个使用 PowerShell 脚本将现有 VM 保存为通用 VHD 的示例。 要使用它，首先要向生成定义中另外添加一个 Azure PowerShell 任务，如下图所示：

![添加 Azure PowerShell 步骤](./media/save-distribute-custom-images/powershell-step.png)

在列表中显示新任务后，选择该项以便填写所有详细信息，如下图所示： 

![PowerShell 设置](./media/save-distribute-custom-images/powershell-settings.png)


## <a name="generalized-vs-specialized-custom-images"></a>通用与专用自定义映像
在 [Azure 门户](https://portal.azure.com)中，根据虚拟机创建自定义映像时，可以选择创建通用还是专用自定义映像。

- 专用自定义映像：未在计算机上运行 Sysprep/取消设置。 这表示，映像是现有虚拟机上操作系统磁盘的精确副本（快照）。  根据此自定义映像创建新计算机时，将存在相同的文件、应用程序、用户帐户、计算机名等。
- 通用自定义映像：已在计算机上运行 Sysprep/取消设置。  此进程运行时会删除用户帐户、删除计算机名、删除用户注册表配置单元等，其旨在使映像通用化，以便在创建其他虚拟机时能够对其进行自定义。  通过运行 sysprep 对虚拟机通用化时，该进程会破坏当前的虚拟机，使其不能再正常工作。

在映像中心用于捕获自定义映像快照的脚本会保存上一步中创建的任何虚拟机的 VHD（根据 Azure 中资源的标记进行标识）。

## <a name="update-configuration-for-distributing-images"></a>更新分发映像的配置
在此过程中，下一步要将自定义映像从映像中心实验室推送到需要它们的任何其他实验室。 此过程的核心部分在于 labs.json 配置文件。 在映像中心包含的“配置”文件夹中可找到该文件。

配置文件 labs.json 中列出了两项关键内容：

- 使用订阅 ID 和实验室名称唯一地标识特定目标实验室。
- 应作为配置根的相对路径推送到实验室的特定映像集。 还可以指定整个文件夹（以获取该文件夹中的所有映像）。

下面是一个列出了两个实验室的 labs.json 文件示例。 在本例中，需要将映像分发到两个不同的实验室。

```json
{
   "Labs": [
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2012R2",
               "Win2016/Datacenter.json"
         ]
      },
      {
         "SubscriptionId": "<subscription ID that contains the lab>",
         "LabName": "<Name of the DevTest Lab>",
         "ImagePaths": [
               "Win2016/Datacenter.json"
         ]
      }
   ]
}
```

## <a name="create-a-build-task"></a>创建生成任务
按照本文前面所述的相同步骤，向生成定义中另外添加一个 Azure Powershell 生成任务。 填写详细信息，如下图所示： 

![分发映像的生成任务](./media/save-distribute-custom-images/second-build-task-powershell.png)

参数包括：`-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(DevTestLabName) -maxConcurrentJobs 20`

该任务将获取映像中心存在的任何自定义映像，并将它们推送到 Labs.json 中定义的任何实验室。

## <a name="queue-the-build"></a>让生成排队
分发生成任务完成后，排队一个新的生成以确保一切正常。 成功完成生成后，进入 Labs.json 配置文件的目标实验室中将显示新的自定义映像。

## <a name="next-steps"></a>后续步骤
在本系列的下一篇文章中，将使用保留策略和清理步骤更新映像中心：[设置保留策略和运行清理脚本](image-factory-set-retention-policy-cleanup.md)。
