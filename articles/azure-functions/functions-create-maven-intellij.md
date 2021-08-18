---
title: 在 Azure Functions 中使用 IntelliJ 创建 Java 函数
description: 了解如何使用 IntelliJ 创建 HTTP 触发的简单 Java 函数，然后发布该函数以在 Azure 的无服务器环境中运行。
author: yucwan
ms.topic: how-to
ms.date: 07/01/2018
ms.author: yucwan
ms.custom: mvc, devcenter, devx-track-java
ms.openlocfilehash: 595d7ff3f70a244f59f98d9b9603bf3b60d60c52
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/04/2021
ms.locfileid: "113287359"
---
# <a name="create-your-first-java-function-in-azure-using-intellij"></a>在 Azure 中使用 IntelliJ 创建你的第一个 Java 函数

本文介绍：
- 如何在 IntelliJ IDEA 项目中创建 HTTP 触发的 Java 函数。
- 在自己的计算机上的集成开发环境 (IDE) 中测试和调试项目的步骤。
- 将函数项目部署到 Azure Functions 的说明

<!-- TODO ![Access a Hello World function from the command line with cURL](media/functions-create-java-maven/hello-azure.png) -->

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="set-up-your-development-environment"></a>设置开发环境

若要使用 IntelliJ 创建 Java 函数并将其发布到 Azure，请安装以下软件：

+ 具有活动订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。
+ [Azure 支持的 Java 开发工具包 (JDK)](/azure/developer/java/fundamentals/java-support-on-azure)（适用于 Java 8）
+ 已安装的 [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) 旗舰版或社区版
+ [Maven 3.5.0+](https://maven.apache.org/download.cgi)
+ 最新的 [Function Core Tools](https://github.com/Azure/azure-functions-core-tools)


## <a name="installation-and-sign-in"></a>安装和登录

1. 在 IntelliJ IDEA 的“设置/首选项”对话框中 (Ctrl+Alt+S) 中，选择“插件”。 然后，在“市场”中找到“Azure Toolkit for IntelliJ”并单击“安装”。   安装后，单击“重启”以激活该插件。 

    ![市场中的 Azure Toolkit for IntelliJ 插件][marketplace]

2. 若要登录到你的 Azure 帐户，请打开边栏中的“Azure 资源管理器”，然后单击顶部栏中的“Azure 登录”图标（或者在 IDEA 菜单中选择“工具”>“Azure”>“Azure 登录”）。  
    ![“IntelliJ Azure 登录”命令][intellij-azure-login]

3. 在“Azure 登录”窗口中选择“设备登录”，然后单击“登录”（[其他登录选项](/azure/developer/java/toolkit-for-intellij/sign-in-instructions)）。  

   ![“Azure 登录”窗口，其中已选择“设备登录”][intellij-azure-popup]

4. 在“Azure 设备登录”对话框中单击“复制并打开”。 

   ![“Azure 登录”对话框窗口][intellij-azure-copycode]

5. 在浏览器中粘贴设备代码（在最后一个步骤中单击“复制并打开”时已复制），然后单击“下一步”。 

   ![设备登录浏览器][intellij-azure-link-ms-account]

6. 在“选择订阅”对话框中选择要使用的订阅，并单击“选择” 。

   ![“选择订阅”对话框][intellij-azure-login-select-subs]
   
## <a name="create-your-local-project"></a>创建本地项目

本节介绍如何使用 Azure Toolkit for IntelliJ 创建一个本地 Azure Functions 项目。 稍后在本文中，你要将函数代码发布到 Azure。 

1. 打开“欢迎使用 IntelliJ”对话框，选择“创建新项目”以打开“新建项目”向导，然后选择“Azure Functions”。

    ![创建函数项目](media/functions-create-first-java-intellij/create-functions-project.png)

1. 选择“HTTP 触发器”，然后单击“下一步”并按照向导的指示完成以下页面中的所有配置；确认项目位置，然后单击“完成”；然后，Intellj IDEA 将打开新项目。

    ![创建函数项目 - 完成](media/functions-create-first-java-intellij/create-functions-project-finish.png)

## <a name="run-the-project-locally"></a>在本地运行项目

1. 导航到 `src/main/java/org/example/functions/HttpTriggerFunction.java`，查看生成的代码。 在第 17 行旁，你可看到一个绿色的“运行”按钮。单击它并选择“运行 azure-function-exam…”，你即可看到函数应用在本地运行，并带有一些日志  。

    ![在本地运行的项目](media/functions-create-first-java-intellij/local-run-functions-project.png)

    ![在本地运行的项目 - 输出](media/functions-create-first-java-intellij/local-run-functions-output.png)

1. 可通过从浏览器访问打印的终结点（如 `http://localhost:7071/api/HttpTrigger-Java?name=Azure`）来试用该函数。

    ![在本地运行的函数 - 测试结果](media/functions-create-first-java-intellij/local-run-functions-test.png)

1. 日志还会在 IDEA 中输出。现在，单击“停止”按钮，停止该函数应用。

    ![在本地运行的函数 - 测试日志](media/functions-create-first-java-intellij/local-run-functions-log.png)

## <a name="debug-the-project-locally"></a>在本地调试项目

1. 若要在本地调试项目中的函数代码，请在工具栏中选择“调试”按钮。 如果未看到工具栏，请通过选择“视图” > “外观” > “工具栏”来启用它。  

    ![在本地调试函数应用 - 按钮](media/functions-create-first-java-intellij/local-debug-functions-button.png)

1. 单击文件 `src/main/java/org/example/functions/HttpTriggerFunction.java` 的第 20 行，添加一个断点，然后再次访问终结点 `http://localhost:7071/api/HttpTrigger-Java?name=Azure`，你会发现断点已命中。可试用更多调试功能，如“单步执行”、“监视”和“评估”   。 单击“停止”按钮，停止调试会话。

    ![在本地调试函数应用 - 中断](media/functions-create-first-java-intellij/local-debug-functions-break.png)

## <a name="deploy-your-project-to-azure"></a>将项目部署到 Azure

1. 在 IntelliJ 项目资源管理器中右键单击你的项目，选择“Azure”->“部署到 Azure Functions”

    ![将项目部署到 Azure](media/functions-create-first-java-intellij/deploy-functions-to-azure.png)

1. 如果还没有任何 Function App，请在“函数”行中单击 *+* 。 请键入函数应用名称并选择正确的平台，此处只需接受默认值即可。 单击“确定”，你刚刚创建的新函数应用会自动选中。 单击“运行”以部署函数。

    ![在 Azure 中创建函数应用](media/functions-create-first-java-intellij/deploy-functions-create-app.png)

    ![将函数应用部署到 Azure - 日志](media/functions-create-first-java-intellij/deploy-functions-log.png)

## <a name="manage-function-apps-from-idea"></a>通过 IDEA 管理函数应用

1. 可在 IDEA 中通过“Azure 资源管理器”来管理函数应用。单击“函数应用”，即可在此处看到你的所有函数应用 。

    ![在资源管理器中查看函数应用](media/functions-create-first-java-intellij/explorer-view-functions.png)

1. 单击以选择函数应用之一，然后右键单击它，选择“显示属性”可打开详细信息页。 

    ![显示函数应用属性](media/functions-create-first-java-intellij/explorer-functions-show-properties.png)

1. 右键单击 HttpTrigger-Java 函数应用，然后选择“触发器函数”，你可看到浏览器打开了触发器 URL。

    ![屏幕截图显示带有 U R L 的浏览器。](media/functions-create-first-java-intellij/explorer-trigger-functions.png)

## <a name="add-more-functions-to-the-project"></a>向项目中添加更多函数

1. 右键单击 org.example.functions 包，然后选择“新建”->“Azure 函数类”。 

    ![向项目添加函数 - 项](media/functions-create-first-java-intellij/add-functions-entry.png)

1. 在“创建函数类”向导中填写类名 HttpTest并选择 HttpTrigger，单击“确定”即可创建。采用这种方式，你可根据需要创建新的函数。

    ![屏幕截图显示“创建函数类”对话框。](media/functions-create-first-java-intellij/add-functions-trigger.png)
    
    ![向项目添加函数 - 输出](media/functions-create-first-java-intellij/add-functions-output.png)

## <a name="cleaning-up-functions"></a>清理函数

1. 在 Azure 资源管理器中删除函数
      
      ![屏幕截图显示从上下文菜单中选择的“删除”。](media/functions-create-first-java-intellij/delete-function.png)
      

## <a name="next-steps"></a>后续步骤

你已使用 HTTP 触发的函数创建了一个 Java 项目，在本地计算机上运行了该项目，并将其部署到了 Azure。 现在，通过以下方式扩展函数

> [!div class="nextstepaction"]
> [添加 Azure 存储队列输出绑定](./functions-add-output-binding-storage-queue-java.md)


[marketplace]:./media/functions-create-first-java-intellij/marketplace.png
[intellij-azure-login]: media/functions-create-first-java-intellij/intellij-azure-login.png
[intellij-azure-popup]: media/functions-create-first-java-intellij/intellij-azure-login-popup.png
[intellij-azure-copycode]: media/functions-create-first-java-intellij/intellij-azure-login-copyopen.png
[intellij-azure-link-ms-account]: media/functions-create-first-java-intellij/intellij-azure-login-linkms-account.png
[intellij-azure-login-select-subs]: media/functions-create-first-java-intellij/intellij-azure-login-selectsubs.png
