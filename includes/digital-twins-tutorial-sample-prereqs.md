---
author: baanders
description: Azure 数字孪生教程的包含文件 - 示例项目的先决条件
ms.service: digital-twins
ms.topic: include
ms.date: 1/20/2021
ms.author: baanders
ms.openlocfilehash: 3a3e32a0ef85ba870cfe7af9639794632549f614
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350869"
---
## <a name="prerequisites"></a>先决条件

若要完成本教程中的步骤，需要先具备以下先决条件。 

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

### <a name="get-required-resources"></a>获取所需资源

若要完成本教程，请在开发计算机上安装 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 版本 16.5 或更高版本。 如果已安装了较旧的版本，可以在计算机上打开 *Visual Studio 安装程序* 应用，然后按照提示更新安装。

>[!NOTE]
> 确保 Visual Studio 2019 安装中包含 [Azure 开发工作负荷](/dotnet/azure/configure-visual-studio)。 此工作负荷使应用程序能够发布 Azure 函数并执行其他 Azure 开发任务。

本教程是在[使用 C# 编写的 Azure 数字孪生端到端示例项目](/samples/azure-samples/digital-twins-samples/digital-twins-samples)推动下编写的。 导航到示例链接，然后选择标题下方的“浏览代码”按钮，在你的计算机上获取示例项目。 此时会转到这些示例的 GitHub 存储库，你可以通过选择“代码”按钮和“下载 ZIP”将其下载为 .zip 。

:::image type="content" source="../articles/digital-twins/media/includes/download-repo-zip.png" alt-text="GitHub 上的 digital-twins-samples 存储库的屏幕截图，突出显示了将该存储库下载为 zip 的步骤。" lightbox="../articles/digital-twins/media/includes/download-repo-zip.png":::

这会将 .zip 文件夹以 digital-twins-samples-master.zip 形式下载到计算机上。 解压缩文件夹并提取文件。

### <a name="prepare-an-azure-digital-twins-instance"></a>准备 Azure 数字孪生实例

[!INCLUDE [Azure Digital Twins: instance prereq](digital-twins-prereq-instance.md)]
