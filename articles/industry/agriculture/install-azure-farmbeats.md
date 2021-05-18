---
title: 安装 Azure FarmBeats
description: 本文介绍如何在 Azure 订阅中安装 Azure FarmBeats
author: usha-rathnavel
ms.topic: article
ms.date: 1/17/2020
ms.author: atinb
ms.openlocfilehash: 5863dcc20fb13f1bb203c68ad168655371130601
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "92674560"
---
# <a name="install-azure-farmbeats"></a>安装 Azure FarmBeats

本文介绍如何在 Azure 订阅中安装 Azure FarmBeats。

Azure FarmBeats 是 Azure Marketplace 中提供的企业到企业产品/服务。 通过它可跨提供商聚合农业数据集以及生成可操作见解。 Azure FarmBeats 实现此目标的方式是使你可以基于融合的数据集构建人工智能 (AI) 或机器学习 (ML) 模型。 Azure FarmBeats 的两个主要组件包括：

- 数据中心：一个 API 层，可以跨不同提供商对各种农业数据集进行聚合、规范化和语境化。

- 加速器：基于数据中心构建的 Web 应用程序。 它可快速启动模型开发和可视化效果。 该加速器使用 Azure FarmBeats API 以图表的形式展示引入的传感器数据的可视化效果，并以地图的形式展示模型输出的可视化效果。

## <a name="general-information"></a>常规信息

### <a name="components-installed"></a>安装的组件

安装 Azure FarmBeats 时，会在 Azure 订阅中预配以下资源：

| 安装的 Azure 资源  | Azure FarmBeats 组件  |
|---------|---------|
| Application Insights   |      数据中心和加速器      |
| 应用服务     |     数据中心和加速器     |
| 应用服务计划   | 数据中心和加速器  |
| API 连接    |  数据中心       |
| Azure Redis 缓存       | 数据中心      |
| Azure Cosmos DB   |  数据中心       |
| Azure 数据工厂 V2       |     数据中心和加速器      |
| Azure Batch 帐户    | 数据中心   |
| Azure Key Vault |  数据中心和加速器        |
| Azure Maps 帐户       |     加速器    |
| 事件中心命名空间    |     数据中心      |
| 逻辑应用      |  数据中心       |
| 存储帐户      |     数据中心和加速器      |
| 时序见解     |    数据中心    |

### <a name="costs-incurred"></a>产生的成本

Azure FarmBeats 的成本是基础 Azure 服务成本的总和。 Azure 服务的定价信息可以使用[定价计算器](https://azure.microsoft.com/pricing/calculator)进行计算。 总体安装的实际成本因使用情况而有所不同。 这两个组件的稳定状态成本为：

- 数据中心 - 每天少于 10 美元
- 加速器 - 每天少于 2 美元

### <a name="regions-supported"></a>支持的区域

目前，在以下区域的公有云环境中支持 Azure FarmBeats：

- 澳大利亚东部
- 美国中部
- 美国东部
- 美国东部 2
- 美国西部
- 美国西部 2
- 北欧
- 西欧
- 东亚
- 东南亚

### <a name="time-taken"></a>所用时间

Azure FarmBeats 的整个设置（包括准备和安装）所用时间会少于一小时。

## <a name="prerequisites"></a>先决条件

在开始实际安装 Azure FarmBeats 之前，需要完成以下步骤：

### <a name="verify-permissions"></a>验证权限

需要在 Azure 租户中具有以下权限才能安装 Azure FarmBeats：

- 租户 - AAD 应用创建者
- 订阅 - 所有者
- 要安装 FarmBeats 的资源组 - 所有者

[创建 AAD 应用程序](#create-an-aad-application)步骤需要前两个权限。 如果需要，可以让具有相应权限的人员创建 AAD 应用程序。

从市场运行 FarmBeats 安装的人员需要是安装 FarmBeats 的资源组的所有者。 对于订阅所有者，创建资源组时会自动发生这种情况。 对于其他人，请预先创建资源组，并请订阅所有者使你成为资源组的所有者。

可以按照 [Azure 基于角色的访问控制](../../role-based-access-control/check-access.md)中的说明，在 Azure 门户中验证访问权限。

### <a name="decide-subscription-and-region"></a>确定订阅和区域

需要 Azure 订阅 ID 和要安装 Azure FarmBeats 的区域。 选择[支持的区域](#regions-supported)部分中列出的区域之一。

记下 Azure 订阅 ID 和 Azure 区域 。

### <a name="create-an-aad-application"></a>创建 AAD 应用程序

Azure FarmBeats 需要创建和注册 Azure Active Directory 应用程序。 若要成功运行 AAD 创建脚本，需要以下权限：

- 租户 - AAD 应用创建者
- 订阅 - 所有者

使用 PowerShell 环境在 Cloud Shell 实例中运行以下步骤。 系统会提示首次用户选择订阅并创建存储帐户。 按照说明完成设置。

1. 下载 [AAD 应用生成器脚本](https://aka.ms/FarmBeatsAADScript)

    ```azurepowershell-interactive
        wget -q https://aka.ms/FarmBeatsAADScript -O ./create_aad_script.ps1
    ```

2. 默认情况下，该文件会下载到主目录。 导航到该目录。

    ```azurepowershell-interactive
        cd
    ```

3. 运行 AAD 脚本

    ```azurepowershell-interactive
        ./create_aad_script.ps1
    ```

4. 该脚本要求提供以下三个输入：

    - FarmBeats 网站名称：这是 FarmBeats Web 应用程序的唯一 URL 前缀。 如果前缀已被采用，则该脚本会出错。安装后，FarmBeats 部署可通过 https://\<FarmBeats-website-name>.azurewebsites.net 进行访问，swagger API 会处于 https://\<FarmBeats-website-name>-api.azurewebsites.net

    - Azure 登录 ID：为要添加为 FarmBeats 管理员的用户提供 Azure 登录 ID。 此用户随后可以向其他用户授予访问 FarmBeats Web 应用程序的访问权限。 登录 ID 通常采用 john.doe@domain.com 的形式。 也支持 Azure UPN。

    - 订阅 ID：这是要在其中安装 Azure FarmBeats 的订阅的 ID

5. AAD 脚本大约需要 2 分钟的时间来运行并将值输出在屏幕上以及输出到同一目录中的 json 文件。 如果让其他人运行该脚本，请要求他们与你共享此输出。

### <a name="create-sentinel-account"></a>创建 Sentinel 帐户

Azure FarmBeats 设置使你可以获取欧洲空间代理商的 [Sentinel-2](https://scihub.copernicus.eu/) 卫星任务为你的农场提供的卫星影像。 若要配置此设置，需要 Sentinel 帐户。

按照以下步骤使用 Sentinel 创建免费帐户：

1. 转到官方[注册](https://aka.ms/SentinelRegistration)页。
2. 提供所需的详细信息（名字、姓氏、用户名、密码和电子邮件 ID）并填写表单。
3. 验证链接会发送到注册的电子邮件 ID。 选择电子邮件中提供的链接并完成验证。

注册过程已完成。 验证也完成后，记下 Sentinel 用户名和 Sentinel 密码 。

## <a name="install"></a>安装

现在即可安装 FarmBeats。 按照以下步骤开始安装：

1. 登录到 Azure 门户。 在右上角选择自己的帐户，然后切换到要在其中安装 Azure FarmBeats 的 Azure AD 租户。

2. 转到门户中的 Azure 市场，在市场中搜索“Azure FarmBeats”。

3. 此时将显示新窗口，其中包含 Azure FarmBeats 概述。 选择“创建”  。

4. 此时将显示新窗口。 通过选择正确的订阅、资源组以及要将 Azure FarmBeats 安装到其中的位置来完成注册过程。

5. 在“FarmBeats 服务警报”部分中提供应接收与 Azure FarmBeats 相关的任何服务警报的电子邮件地址。 选择页面底部的“下一步”，以进入“依赖项”选项卡 。

    ![“基本信息”选项卡](./media/install-azure-farmbeats/create-azure-farmbeats-basics.png)

6. 将 [AAD 脚本](#create-an-aad-application)输出中的各个条目复制到 AAD 应用程序部分中的输入。

7. 在“Sentinel 帐户”部分中输入 [Sentinel 帐户](#create-sentinel-account)用户名和密码。 选择“下一步”，以进入“查看 + 创建”选项卡 。

    ![Dependencies Tab](./media/install-azure-farmbeats/create-azure-farmbeats-dependencies.png)

8. 验证输入的详细信息后，选择“确定”。 此时将显示使用条款页。 查看条款并选择“创建”以开始安装。 将重定向到可以跟踪安装进度的页。

安装完成后，可以通过导航到在安装过程中提供的网站名称来验证安装并开始使用 FarmBeats 门户： https://\<FarmBeats-website-name>.azurewebsites.net。 应会看到 FarmBeats 用户界面，其中包含用于创建农场的选项。

可以在 https://\<FarmBeats-website-name>-api.azurewebsites.net/swagger 找到数据中心。 在此处你会看到不同的 FarmBeats API 对象并对 API 执行 REST 操作。

## <a name="upgrade"></a>升级

若要将 FarmBeats 升级到最新版本，请使用 PowerShell 环境在 Cloud Shell 实例中运行以下步骤。 用户需要是在其中安装 FarmBeats 的订阅的所有者。

系统会提示首次用户选择订阅并创建存储帐户。 按照说明完成设置。

1. 下载[升级脚本](https://aka.ms/FarmBeatsUpgradeScript)

    ```azurepowershell-interactive
        wget –q https://aka.ms/FarmBeatsUpgradeScript -O ./upgrade-farmbeats.ps1
    ```

2. 默认情况下，该文件会下载到主目录。 导航到该目录。

    ```azurepowershell-interactive
        cd
    ```

3. 运行升级脚本

    ```azurepowershell-interactive
        ./upgrade-farmbeats.ps1 -InputFilePath [Path to input.json file]
    ```

文件 input.json 的路径是可选的。 如果未指定，则该脚本会要求提供所有必需输入。 升级应在大约 30 分钟内完成。

## <a name="uninstall"></a>卸载

若要卸载 Azure FarmBeats 数据中心或加速器，请完成以下步骤：

1. 登录 Azure 门户并删除在其中安装这些组件的资源组。

2. 转到 Azure Active Directory 并删除链接到 Azure FarmBeats 安装的 Azure AD 应用程序。

## <a name="next-steps"></a>后续步骤

你已了解如何在 Azure 订阅中安装 Azure FarmBeats。 现在需了解如何向 Azure FarmBeats 实例[添加用户](manage-users-in-azure-farmbeats.md#manage-users)。