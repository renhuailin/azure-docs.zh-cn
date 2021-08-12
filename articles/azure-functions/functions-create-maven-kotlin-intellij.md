---
title: 在 Azure Functions 中使用 IntelliJ 创建 Kotlin 函数
description: 了解如何使用 IntelliJ 创建 HTTP 触发的简单 Kotlin 函数，然后发布该函数以在 Azure 的无服务器环境中运行。
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.openlocfilehash: f2ec995334069dc707c8eb09fae3601f58c6c549
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287251"
---
# <a name="create-your-first-kotlin-function-in-azure-using-intellij"></a>在 Azure 中使用 IntelliJ 创建第一个 Kotlin 函数

本文介绍如何在 IntelliJ IDEA 项目中创建 HTTP 触发的 Java 函数，在集成开发环境 (IDE) 中运行和调试项目，最后将函数项目部署到 Azure 中的函数应用。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

若要使用 IntelliJ 创建 Kotlin 函数并将其发布到 Azure，请安装以下软件：

- [Java 开发人员工具包](/azure/developer/java/fundamentals/java-support-on-azure) (JDK) 版本 8
- [Apache Maven](https://maven.apache.org) 3.0 或更高版本
- 带有 Maven 的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download) 社区版或旗舰版
- [Azure CLI](/cli/azure)
- Azure Functions Core Tools [版本 2.x](functions-run-local.md#v2)。 该工具为编写、运行和调试 Azure Functions 提供了本地开发环境。

> [!IMPORTANT]
> JAVA_HOME 环境变量必须设置为 JDK 的安装位置，以完成本文中的步骤。

## <a name="create-a-function-project"></a>创建函数项目

1. 在 IntelliJ IDEA 中选择“新建项目”  。  
1. 在“新建项目”窗口中选择左窗格中的“Maven”   。
1. 选中“从原型创建”复选框，然后针对 [azure-functions-kotlin-archetype](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype) 选择“添加原型”   。
1. 在“添加 Archetype”窗口中按以下说明填写各个字段  ：
    - _GroupId_：com.microsoft.azure
    - _ArtifactId_：azure-functions-kotlin-archetype
    - _版本_：使用[中心存储库](https://mvnrepository.com/artifact/com.microsoft.azure/azure-functions-kotlin-archetype)中的最新版本
    ![在 IntelliJ IDEA 中从原型创建 Maven 项目](media/functions-create-first-kotlin-intellij/functions-create-intellij.png)  
1. 选择“确定”  ，然后选择“下一步”  。
1. 输入当前项目的详细信息，并选择“完成”  。

Maven 在新文件夹中创建项目文件，文件名称与 _ArtifactId_ 值相同。 项目中生成的代码是一个简单的回显触发 HTTP 请求正文的 [HTTP 触发](./functions-bindings-http-webhook.md)函数。

## <a name="run-project-locally-in-the-ide"></a>在 IDE 中本地运行项目

> [!NOTE]
> 若要在本地运行和调试项目，请确保安装 [Azure Functions Core Tools 版本 2](functions-run-local.md#v2)。

1. 手动导入更改或启用[自动导入](https://www.jetbrains.com/help/idea/creating-and-optimizing-imports.html)。
1. 打开“Maven 项目”工具栏  。
1. 展开“生命周期”然后打开“包”   。 该解决方案是在新创建的目标目录中生成和打包的。
1. 展开“插件” > “azure-functions”，并打开“azure-functions:run”以启动 Azure Functions 本地运行时    。  
  ![Azure Functions 的 Maven 工具栏](media/functions-create-first-kotlin-intellij/functions-intellij-kotlin-maven-toolbar.png)  

1. 完成函数测试后关闭运行对话框。 一次只能有一个函数主机处于活动状态并在本地运行。

## <a name="debug-the-project-in-intellij"></a>在 IntelliJ 中调试项目

1. 若要在调试模式下启动函数主机，请在运行函数时添加 **-DenableDebug** 作为参数。 可以更改 [Maven 目标](https://www.jetbrains.com/help/idea/maven-support.html#run_goal)中的配置或在终端窗口中运行以下命令：  

   ```
   mvn azure-functions:run -DenableDebug
   ```

   此命令会让函数主机打开调试端口 5005。

1. 请在“运行”菜单中选择“编辑配置”   。
1. 选择“(+)”添加“远程”   。
1. 填写“名称”和“设置”字段，然后选择“确定”以保存配置    。
1. 在设置后，选择“调试<远程配置名称>”或在键盘上按 Shift+F9 以启动调试  。

   ![在 IntelliJ 中调试项目](media/functions-create-first-kotlin-intellij/debug-configuration-intellij.PNG)

1. 完成后，请停止调试器和正在运行的进程。 一次只能有一个函数主机处于活动状态并在本地运行。

## <a name="deploy-the-project-to-azure"></a>将项目部署到 Azure

1. 必须先[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)，然后才能将项目部署到 Azure 中的函数应用。

   ``` azurecli
   az login
   ```

1. 使用 `azure-functions:deploy` Maven 目标将代码部署到新的函数应用。 也可以在“Maven 项目”窗口中选择“azure-functions:deploy”选项  。

   ```
   mvn azure-functions:deploy
   ```

1. 成功部署函数应用后，在 Azure CLI 输出中找到 HTTP 触发器函数的 URL。

   ``` output
   [INFO] Successfully deployed Function App with package.
   [INFO] Deleting deployment package from Azure Storage...
   [INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
   [INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
   [INFO] ------------------------------------------------------------------------
   ```

## <a name="next-steps"></a>后续步骤

将第一个 Kotlin 函数应用部署到 Azure 后，接下来请查看 [Azure Functions Java 开发人员指南](functions-reference-java.md)，详细了解如何开发 Java 和 Kotlin 函数。
- 使用 `azure-functions:add` Maven 目标将具有不同触发器的其他函数应用添加到你的项目。
