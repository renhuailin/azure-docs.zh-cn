---
title: 如何将 OPC 孪生体模块部署到现有的 Azure 项目 | Microsoft Docs
description: 本文介绍了如何将 OPC 孪生体部署到现有项目。 你还可以了解如何排查部署失败问题。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 4535818898459f1eb183026629bc338524c1efd9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645518"
---
# <a name="deploy-opc-twin-to-an-existing-project"></a>将 OPC 孪生体部署到现有项目

> [!IMPORTANT]
> 当我们更新本文时，请参阅 [Azure 工业 IoT](https://azure.github.io/Industrial-IoT/) 来了解最新内容。

OPC 孪生体模块在 IoT Edge 上运行，并向 OPC 孪生体和注册表服务提供多个边缘服务。

OPC 孪生体微服务通过 OPC 孪生体 IoT Edge 模块为工厂操作员与车间的 OPC UA 服务器设备之间的通信提供便利。 微服务通过其 REST API 公开 OPC UA 服务（浏览、读取、写入和执行）。 

OPC UA 设备注册表微服务提供对已注册的 OPC UA 应用程序及其终结点的访问。 操作员和管理员可以注册和注销新的 OPC UA 应用程序，以及浏览现有的 OPC UA 应用程序，包括其终结点。 除了应用程序和终结点管理外，注册表服务还将已注册的 OPC 孪生体 IoT Edge 模块编入目录。 服务 API 提供对边缘模块功能的控制，例如，启动或停止服务器发现（扫描服务），或激活可使用 OPC 孪生体微服务访问的新终结点孪生体。

该模块的核心是“主管”标识。 主管管理终结点孪生体，后者对应于使用相应的 OPC UA 注册表 API 激活的 OPC UA 服务器终结点。 此终结点孪生体将从在云中运行的 OPC 孪生体微服务接收的 OPC UA JSON 转换为 OPC UA 二进制消息，这些消息通过有状态安全通道发送到托管终结点。 主管还提供发现服务，用于将设备发现事件发送到 OPC UA 设备加入服务进行处理，这些事件会导致在其中更新 OPC UA 注册表。  本文介绍了如何将 OPC 孪生体模块部署到现有项目。

> [!NOTE]
> 若要详细了解部署细节和说明，请参阅 GitHub [存储库](https://github.com/Azure/azure-iiot-opc-twin-module)。

## <a name="prerequisites"></a>先决条件

确保已安装 PowerShell 和 [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) 扩展。 如果尚未这样做，请克隆此 GitHub 存储库。 在 PowerShell 中运行以下命令：

```powershell
git clone --recursive https://github.com/Azure/azure-iiot-components.git
cd azure-iiot-components
```

## <a name="deploy-industrial-iot-services-to-azure"></a>将工业 IoT 服务部署到 Azure

1. 在你的 PowerShell 会话中，运行以下命令：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy.cmd
    ```

2. 按照提示为部署的资源组分配一个名称，为网站分配一个名称。   该脚本将微服务及其 Azure 平台依赖项部署到 Azure 订阅中的资源组。  该脚本还会在 Azure Active Directory (AAD) 租户中注册一个应用程序，以支持基于 OAUTH 的身份验证。  部署将需要几分钟的时间。  成功部署解决方案后你将看到的内容的示例：

   ![将工业 IoT OPC 孪生体部署到现有项目](media/howto-opc-twin-deploy-existing/opc-twin-deploy-existing1.png)

   输出包括公共终结点的 URL。 

3. 在脚本成功完成后，选择是否要保存 `.env` 文件。  如果要使用诸如控制台之类的工具连接到云终结点，或者部署用于开发和调试的模块，则你需要 `.env` 环境文件。

## <a name="troubleshooting-deployment-failures"></a>排查部署失败的问题

### <a name="resource-group-name"></a>资源组名称

请确保使用简短且简单的资源组名称。  该名称还用于对资源进行命名，因此必须符合资源命名要求。  

### <a name="website-name-already-in-use"></a>网站名称已被使用

网站的名称可能已被使用。  如果遇到此错误，则需要使用其他应用程序名称。

### <a name="azure-active-directory-aad-registration"></a>Azure Active Directory (AAD) 注册

部署脚本尝试在 Azure Active Directory 中注册两个 AAD 应用程序。  部署可能会失败，具体取决于你对所选 AAD 租户的权限。 有两个选项：

* 如果你从租户列表中选择了一个 AAD 租户，请重启脚本并从列表中选择一个不同的租户。
* 另外，还可以在另一个订阅中部署专用 AAD 租户，重启脚本，然后选择使用该租户。

> [!WARNING]
> 未经身份验证，切勿继续操作。  如果你选择未经身份验证就这样做，则任何人都可以在未经身份验证的情况下从 Internet 访问你的 OPC 孪生体终结点。   你始终可以选择[“local”部署选项](howto-opc-twin-deploy-dependencies.md)来开始部署。

## <a name="deploy-an-all-in-one-industrial-iot-services-demo"></a>部署一体化工业 IoT 服务演示版

你还可以部署一体化演示版，而不是仅部署服务和依赖项。  一体化演示版包含三台 OPC UA 服务器、OPC 孪生体模块、所有微服务，以及一个示例 Web 应用程序。  它用于演示。

1. 请确保你有存储库的克隆（参见上文）。 在存储库的根目录中打开 PowerShell 提示符，并运行以下命令：

    ```powershell
    set-executionpolicy -ExecutionPolicy Unrestricted -Scope Process
    .\deploy -type demo
    ```

2. 按照提示为资源组分配一个新名称，为网站分配一个名称。  成功部署后，该脚本会显示 Web 应用程序终结点的 URL。

## <a name="deployment-script-options"></a>部署脚本选项

该脚本采用以下参数：

```powershell
-type
```

部署类型（vm、local、demo）

```powershell
-resourceGroupName
```

可以是现有的或新的资源组的名称。

```powershell
-subscriptionId
```

（可选）将在其中部署资源的订阅 ID。

```powershell
-subscriptionName
```

或订阅名称。

```powershell
-resourceGroupLocation
```

（可选）资源组位置。 如果指定此项，则会尝试在此位置创建新的资源组。

```powershell
-aadApplicationName
```

AAD 应用程序注册时采用的名称。

```powershell
-tenantId
```

要使用的 AAD 租户。

```powershell
-credentials
```

## <a name="next-steps"></a>后续步骤

现在，你已了解如何将 OPC 孪生体部署到现有项目，下面是建议的后续步骤：

> [!div class="nextstepaction"]
> [保护 OPC UA 客户端和 OPC UA PLC 的通信](howto-opc-vault-secure.md)