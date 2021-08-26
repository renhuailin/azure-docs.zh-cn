---
title: 教程：生成模拟设备数据 - Azure IoT Edge 上的机器学习
description: 教程 - 创建生成模拟遥测数据的虚拟设备，这些数据稍后可用于训练机器学习模型。
author: kgremban
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e6383ff947832fa94d0e65b3a021d99ede6247bc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726422"
---
# <a name="tutorial-generate-simulated-device-data"></a>教程：生成模拟设备数据

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

在本文中，我们将使用机器学习训练数据来模拟设备向 Azure IoT 中心发送遥测数据。 如简介中所述，本教程使用[涡扇发动机降级模拟数据集](https://c3.nasa.gov/dashlink/resources/139/)来模拟来自一组飞机发动机数据，以进行训练和测试。

在试验方案中，我们知道：

* 数据集由多个多变量时序组成。
* 每个数据集分为训练子集和测试子集。
* 每个时序来自不同的发动机。
* 每个发动机开始时都有不同程度的初始磨损和制造变化。

在本教程中，我们使用单个数据集 (FD003) 的训练数据子集。

在实际情况下，每个发动机都是独立的 IoT 设备。 假设你没有连接到 Internet 的涡扇发动机集合可用，我们将生成这些设备的软件替代项。

模拟器是一个 C# 程序，它使用 IoT 中心 API 以编程方式向 IoT 中心注册虚拟设备。 然后，我们从 NASA 提供的数据子集读取每个设备的数据，并使用模拟的 IoT 设备将其发送到 IoT 中心。 本教程这一部分的所有代码都可以在存储库的 DeviceHarness 目录中找到。

DeviceHarness 项目是一个以 C# 编写的 .NET Core 项目，包含四个类：

* **Program：** 执行入口点，负责处理用户输入和整体协调。
* **TrainingFileManager：** 负责读取并分析所选的数据文件。
* **CycleData：** 表示转换为消息格式的文件中的单行数据。
* **TurbofanDevice：** 负责创建与数据中的单个设备（时序）相对应的 IoT 设备，并将数据传输到 IoT 中心。

本文中所述的任务大约需要约 20 分钟才能完成。

与在此步骤中的工作等效的实际操作可能会由设备开发人员和云开发人员执行。

本教程的此部分介绍了如何：

> [!div class="checklist"]
>
> * 将外部项目纳入开发环境。
> * 使用示例 DeviceHarness 项目生成模拟的 IoT 设备数据。
> * 在 IoT 中心查看生成的数据。

## <a name="prerequisites"></a>先决条件

有一系列教程介绍如何在 IoT Edge 上使用 Azure 机器学习，本文是其中的一篇。 这一系列中的每篇文章都环环相扣，后一篇以前一篇为基础。 如果你是直接转到本文的，请访问本系列的[第一篇文章](tutorial-machine-learning-edge-01-intro.md)。

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>配置 Visual Studio Code 并生成 DeviceHarness 项目

1. 打开到开发 VM 的远程桌面会话。

1. 在 Visual Studio Code 中打开 `C:\source\IoTEdgeAndMlSample\DeviceHarness` 文件夹。

1. 由于你是第一次在此计算机上使用扩展，因此某些扩展将更新并安装它们的依赖项。 系统可能会提示你更新扩展。 如果是，请选择“重新加载窗口”  。

   如果输出窗口中显示了 OmniSharp 错误，则需要卸载 C# 扩展。

1. 系统将提示你为 DeviceHarness 添加所需资产。 选择“是”以添加  。

   * 可能需要几秒钟才会显示通知。
   * 如果错过了此通知，请检查右下角中的铃铛图标。

   ![VS Code 扩展弹出窗口](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. 选择“还原”以还原包依赖项  。

   ![VS Code 还原提示符](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   如果未收到这些通知，请关闭 Visual Studio Code，删除 `C:\source\IoTEdgeAndMlSample\DeviceHarness` 中的 bin 和 obj 目录，打开 Visual Studio Code，然后重新打开 DeviceHarness 文件夹。

1. 通过触发生成来验证环境设置是否正确，方法是按 **Ctrl** + **Shift** + **B** 或选择“终端” > “运行生成任务”   。

1. 系统将提示选择要运行的生成任务。 选择“生成”  。

1. 生成运行，并输出成功消息。

   ![生成成功输出消息](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. 可以通过选择“终端” > “配置默认生成任务...”然后从提示中选择“生成”，使此生成变为默认生成任务    。

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>连接到 IoT 中心并运行 DeviceHarness

在项目生成时，连接到 IoT 中心以访问连接字符串并监视数据生成的进度。

### <a name="sign-in-to-azure-in-visual-studio-code"></a>在 Visual Studio Code 中登录到 Azure

1. 通过打开命令面板，在 Visual Studio Code 中登录到 Azure 订阅，方法是按 `Ctrl + Shift + P` 或选择“视图” > “命令面板”   。

1. 搜索“Azure:  登录”命令。

   将打开一个浏览器窗口，并提示你输入登录信息。 当重定向到成功页时，可以关闭浏览器。

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>连接到 IoT 中心并检索中心连接字符串

1. 在 Visual Studio Code 资源管理器的底部，选择“Azure IoT 中心”框架将其展开  。

1. 在扩展框架中，单击“选择 IoT 中心”  。

1. 出现提示时，选择 Azure 订阅，然后选择 IoT 中心。

1. 单击“Azure IoT 中心”右侧的“...”查看更多操作。   选择“复制 IoT 中心连接字符串”  。

   ![复制 IoT 中心连接字符串](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>运行 DeviceHarness 项目

1. 选择“视图” > “终端”，以打开 Visual Studio Code 终端   。

   如果没有看到提示符，请按 Enter。

1. 在终端中输入 `dotnet run`。

1. 当系统提示输入 IoT 中心连接字符串时，粘贴上一部分中复制的连接字符串。

1. 在“Azure IoT 中心设备”框架中，单击刷新按钮  。

   ![刷新 IoT 中心设备列表](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. 请注意，设备已添加到 IoT 中心，并且设备显示为绿色，表示正在通过该设备发送数据。 设备在将消息发送到 IoT 中心后会断开连接，并显示为蓝色。

1. 可以通过右键单击任何设备并选择“开始监视内置事件终结点”，查看发送到中心的消息  。 这些消息将显示在 Visual Studio Code 的输出窗格中。

1. 单击“Azure IoT 中心”输出窗格，并选择“停止监视内置事件终结点”，可停止监视   。

1. 等待应用程序运行完成，这需要几分钟时间。

## <a name="check-iot-hub-for-activity"></a>在 IoT 中心检查活动

DeviceHarness 发送的数据将进入 IoT 中心，在其中可以通过 Azure 门户验证这些数据。

1. 打开 [Azure 门户](https://portal.azure.com/)，导航到为本教程创建的 IoT 中心。

1. 在左侧窗格菜单中的“监视”，选择“指标”。  

1. 在图表定义页上单击“指标”下拉列表，向下滚动列表，然后选择“路由: 已传送到存储的数据”。   图表应会显示将数据路由到存储的峰值时间。

   ![图表显示将数据传送到存储的峰值时间](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>在 Azure 存储中验证数据

我们刚刚发送到 IoT 中心的数据已路由到我们在上一篇文章中创建的存储容器。 让我们在存储帐户中查看该数据。

1. 在 Azure 门户中导航到存储帐户。

1. 从存储帐户导航栏中，选择“存储资源管理器(预览版)”  。

1. 在存储资源管理器中，依次选择“Blob 容器”、`devicedata`。 

1. 在内容窗格中，单击由 IoT 中心名称后接年、月、日和小时构成的文件夹。 你将看到多个文件夹，表示数据写入时的分钟。

   ![在 Blob 存储中查看文件夹](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. 单击其中一个文件夹，以查找标记为“00”和“01”的对应于分区的数据文件   。

1. 这些文件是以 [Avro](https://avro.apache.org/) 格式编写的。 双击其中一个文件打开另一个浏览器标签页，其中会呈现部分数据。 如果系统提示在程序中打开该文件，可以选择 VS Code，该文件将正确呈现。

1. 现在无需尝试读取或解释数据，我们将在下一篇文章中来执行此操作。

## <a name="clean-up-resources"></a>清理资源

本教程是一系列文章的一部分，其中每篇文章都基于前一篇文章中介绍的内容。 在完成最后一个教程之前，请等待清理所有资源。

## <a name="next-steps"></a>后续步骤

在本文中，我们使用 .NET Core 项目创建了一组虚拟 IoT 设备，并通过这些设备将数据发送到了 IoT 中心和 Azure 存储容器。 此项目模拟一个真实场景，其中，物理 IoT 设备依次将数据发送到 IoT 中心和特选存储。 此数据包括传感器读数、操作设置、故障信号和模式等。 收集足够的数据后，我们使用它来训练模型，以预测设备的剩余使用寿命 (RUL)。 在下一篇文章中，我们将演示此机器学习。

继续了解下一篇文章，用数据训练机器学习模型。

> [!div class="nextstepaction"]
> [训练和部署 Azure 机器学习模型](tutorial-machine-learning-edge-04-train-model.md)
