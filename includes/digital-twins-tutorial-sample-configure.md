---
author: baanders
description: Azure 数字孪生教程的包含文件 - 配置示例项目
ms.service: digital-twins
ms.topic: include
ms.date: 5/25/2020
ms.author: baanders
ms.openlocfilehash: 60d65e530c9d98ac86f8d958072d2f26a68836fe
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/02/2021
ms.locfileid: "111350848"
---
## <a name="configure-the-sample-project"></a>配置示例项目

接下来，设置将与 Azure 数字孪生实例交互的示例客户端应用程序。

在计算机上，导航到先前从 [Azure 数字孪生端到端示例](/samples/azure-samples/digital-twins-samples/digital-twins-samples)下载的文件（如果尚未解压缩，则将其解压缩）。

进入文件夹后，导航到 AdtSampleApp。 在 Visual Studio 2019 中打开 AdtE2ESample.sln。 

在 Visual Studio 中，选择“SampleClientApp”>“appsettings.json”文件，在编辑窗口中将其打开。 该副本将充当预设置的 JSON 文件，其中包含运行项目所需的配置变量。

在文件正文中，将 `instanceUrl` 更改为 Azure 数字孪生实例主机名 URL（通过在主机名前面添加 https://，如下所示）。

```json
{
  "instanceUrl": "https://<your-Azure-Digital-Twins-instance-host-name>"
}
```

保存并关闭该文件。 

接下来，配置 appsettings.json 文件，在生成 SampleClientApp 时将其复制到输出目录 。 为此，请右键单击 appsettings.json 文件，然后选择“属性”。 在“属性”检查器中，查找“复制到输出目录”属性。 将其值更改为“如果较新则复制”（如果尚未设置为该值）。

:::image type="content" source="../articles/digital-twins/media/includes/copy-config.png" alt-text="Visual Studio 中的“解决方案资源管理器”屏幕截图，其中 appsettings.json 和“复制到输出目录”属性在“属性”中突出显示。" border="false" lightbox="../articles/digital-twins/media/includes/copy-config.png":::

在 Visual Studio 中将 AdtE2ESample 项目保持在打开状态，以便在本教程中继续使用该项目。

[!INCLUDE [Azure Digital Twins: local credentials prereq (outer)](digital-twins-local-credentials-outer.md)]
