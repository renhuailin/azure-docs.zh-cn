---
title: 获取无人机图像
description: 本文介绍如何从合作伙伴获取无人机航拍图像。
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 7ef68267fdd6f1072271f0a0d3f2ab1427ecd4c8
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108751258"
---
# <a name="get-drone-imagery-from-drone-partners"></a>从无人机合作伙伴获取无人机航拍图像

本文介绍如何将无人机航拍图像合作伙伴的正射影像数据引入 Azure FarmBeats Datahub。 正射影像是无人机收集的数据在经过几何纠正和拼接后生成的舶拍图示或图像。

目前支持以下图像合作伙伴。

  ![FarmBeats 无人机航拍图像合作伙伴](./media/get-drone-imagery-from-drone-partner/drone-partner-1.png)

将无人机航拍图像数据与 Azure FarmBeats 集成，可帮助将你在农场中指挥的无人机在飞行过程中收集的正射影像数据引入 Datahub。 提供数据后，可以在 FarmBeats Accelerator 中查看数据。 这些数据可用于数据融合以及人工智能和机器学习模型生成。

## <a name="before-you-begin"></a>准备阶段

  - 确保已安装 Azure FarmBeats。 有关如何安装 FarmBeats 的信息，请参阅[安装 Azure FarmBeats](install-azure-farmbeats.md)。
  - 确保在 FarmBeats 系统中定义了你需要其无人机航拍图像的农场。

## <a name="enable-drone-imagery-integration-with-farmbeats"></a>实现无人机航拍图像与 FarmBeats 的集成

向设备提供商提供以下信息以实现与 FarmBeats 的集成：
 - API 终结点
 - 租户 ID
 - 客户端 ID
 - 客户端机密

请执行以下步骤。

1. 下载此[脚本](https://aka.ms/farmbeatspartnerscript)并将其解压缩到本地驱动器。 该 zip 文件包含两个文件。
2. 登录到 [Azure 门户](https://portal.azure.com/)，并打开 Azure Cloud Shell。 此选项位于门户右上角的工具栏中。

    ![打开位于门户右上栏中的 Azure Cloud Shell](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

3. 确保环境设置为“PowerShell”。

    ![PowerShell 设置](./media/get-drone-imagery-from-drone-partner/power-shell-new-1.png)

4. 上传你在 Cloud Shell 实例中通过步骤 1 下载的两个文件。

    ![上传文件](./media/get-drone-imagery-from-drone-partner/power-shell-two-1.png)

5. 转到这些文件上传到的目录。 默认情况下，这些文件会上传到用户名下的主目录。
6. 运行以下脚本：

    ```azurepowershell-interactive

    ./generateCredentials.ps1

    ```

7. 按照屏幕上的说明捕获“API 终结点”、“租户 ID”、“客户端 ID”、“客户端机密”和“事件中心连接字符串”的值。

    将所需凭据输入到合作伙伴的无人机软件系统后，可以从 FarmBeats 系统导入所有农场。 然后，可以使用农场详细信息执行飞行路径规划和无人机图像采集。

    在无人机提供商的软件处理原始图像后，无人机软件系统会将拼接的正射影像和其他已处理的图像上传到 Datahub。

## <a name="view-drone-imagery"></a>查看无人机航拍图像

将数据发送到 FarmBeats Datahub 后，可以使用 FarmBeats Datahub API 查询场景存储。

或者，可以在“农场详细信息”页上查看最新的无人机图像。 若要查看图像，请执行以下步骤。

1. 选择已为其上传图像的农场。 此时会显示“农场详细信息”页。
2. 向下滚动到最新的“精确地图”部分。
3. 查看“无人机航拍图像”部分中的图像。

    ![“无人机航拍图像”部分](./media/get-drone-imagery-from-drone-partner/drone-imagery-1.png)

## <a name="download-drone-imagery"></a>下载无人机航拍图像

选择“无人机航拍图像”部分时会打开一个弹出窗口，其中显示了无人机正射影像的高分辨率图像。

![高分辨率正射影像](./media/get-drone-imagery-from-drone-partner/download-drone-imagery-1.png)

## <a name="view-all-drone-maps"></a>查看所有无人机地图

无人机提供商上传的文件和图像显示在“地图”部分。 选择“地图”部分，按“农场”进行筛选，然后选择要查看和下载的相应文件 。

  ![“地图”部分](./media/get-drone-imagery-from-drone-partner/view-drone-maps-1.png)

## <a name="next-steps"></a>后续步骤

了解如何使用 FarmBeats Datahub [API](rest-api-in-azure-farmbeats.md) 获取无人机航拍图像。
