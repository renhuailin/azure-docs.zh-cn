---
title: 如何部署 OPC 保管库证书管理服务 - Azure | Microsoft Docs
description: 如何从头开始部署 OPC 保管库证书管理服务。
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b20f6318c2e6be701446e29ab93598752e93d287
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2021
ms.locfileid: "107870276"
---
# <a name="build-and-deploy-the-opc-vault-certificate-management-service"></a>生成并部署 OPC 保管库证书管理服务

> [!IMPORTANT]
> 当我们更新本文时，请参阅 [Azure 工业 IoT](https://azure.github.io/Industrial-IoT/) 来了解最新内容。

本文介绍如何在 Azure 中部署 OPC 保管库证书管理服务。

> [!NOTE]
> 有关详细信息，请查看 GitHub [OPC 保管库存储库](https://github.com/Azure/azure-iiot-opc-vault-service)。

## <a name="prerequisites"></a>先决条件

### <a name="install-required-software"></a>安装必需软件

当前，生成和部署操作仅限于 Windows。
这些示例都是针对 C# .NET Standard 编写的，你需要它来生成服务和示例以进行部署。
你所需适用于 .NET Standard 的所有工具附带了 .NET Core 工具。 请参阅 [.NET Core 入门](/dotnet/articles/core/getting-started)。

1. [安装 .NET Core 2.1+][dotnet-install]。
2. [安装 Docker][docker-url]（可选，仅当需要本地 Docker 版本时）。
4. 安装[适用于 PowerShell 的 Azure 命令行工具][powershell-install]。
5. 注册 [Azure 订阅][azure-free]。

### <a name="clone-the-repository"></a>克隆存储库

如果尚未这样做，请克隆此 GitHub 存储库。 打开命令提示符或终端，运行以下命令：

```bash
git clone https://github.com/Azure/azure-iiot-opc-vault-service
cd azure-iiot-opc-vault-service 
```

或者，可以直接在 Visual Studio 2017 中克隆存储库。

### <a name="build-and-deploy-the-azure-service-on-windows"></a>在 Windows 上生成和部署 Azure 服务

使用 PowerShell 脚本可以轻松地部署 OPC 保管库微服务和应用程序。

1. 在存储库根路径上打开 PowerShell 窗口。 
3. 转到部署文件夹 `cd deploy`。
3. 为 `myResourceGroup` 选择一个不太可能与其他已部署网页发生冲突的名称。 请参阅本文后面的“网站名称已被使用”部分。
5. 使用 `.\deploy.ps1` 开始部署以进行交互式安装，或输入完整的命令行：  
`.\deploy.ps1  -subscriptionName "MySubscriptionName" -resourceGroupLocation "East US" -tenantId "myTenantId" -resourceGroupName "myResourceGroup"`
7. 如果计划使用此部署进行开发，请添加 `-development 1` 以启用 Swagger UI，并部署调试版本。
6. 按照脚本中的说明登录到你的订阅，并提供其他信息。
9. 成功完成生成和部署操作后，应看到以下消息：
   ```
   To access the web client go to:
   https://myResourceGroup.azurewebsites.net

   To access the web service go to:
   https://myResourceGroup-service.azurewebsites.net

   To start the local docker GDS server:
   .\myResourceGroup-dockergds.cmd

   To start the local dotnet GDS server:
   .\myResourceGroup-gds.cmd
   ```

   > [!NOTE]
   > 如果出现问题，请参阅本文后面的“排查部署失败的问题”部分。

8. 打开你喜欢的浏览器，然后打开应用程序页面：`https://myResourceGroup.azurewebsites.net`
8. 在部署后，给 Web 应用和 OPC 保管库微服务几分钟的时间来预热。 在获取首个响应之前，Web 主页可能会在第一次使用时停止响应，最多可能持续一分钟。
11. 若要查看 Swagger API，请打开：`https://myResourceGroup-service.azurewebsites.net`
13. 若要使用 dotnet 启动本地 GDS 服务器，请启动 `.\myResourceGroup-gds.cmd`。 使用 Docker 启动 `.\myResourceGroup-dockergds.cmd`。

可以使用完全相同的设置重新部署生成。 请注意，此类操作会更新所有应用程序机密，并且可能会重置 Azure Active Directory (Azure AD) 应用程序注册中的某些设置。

还可以仅重新部署 Web 应用二进制文件。 使用参数 `-onlyBuild 1` 将服务和应用的新 zip 包部署到 Web 应用程序。

成功部署后，可以开始使用服务。 请查看[管理 OPC 保管库证书管理服务](howto-opc-vault-manage.md)。

## <a name="delete-the-services-from-the-subscription"></a>从订阅中删除服务

方法如下：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 转到部署了该服务的资源组。
3. 选择“删除资源组”并进行确认。
4. 一小段时间后，所有已部署的服务组件都将被删除。
5. 转到“Azure Active Directory” > “应用注册”。 
6. 每个部署的资源组应列出三个注册。 注册具有以下名称：`resourcegroup-client`、`resourcegroup-module` 和 `resourcegroup-service`。 分别删除每个注册。

现在会删除所有已部署的组件。

## <a name="troubleshooting-deployment-failures"></a>排查部署失败的问题

### <a name="resource-group-name"></a>资源组名称

使用简短且简单的资源组名称。 该名称还用于命名资源和服务 URL 前缀。 因此，它必须符合资源命名要求。  

### <a name="website-name-already-in-use"></a>网站名称已被使用

网站名称可能已被使用。 你需要使用一个不同的资源组名称。 部署脚本使用的主机名为：https：\//resourcegroupname.azurewebsites.net 和 https：\//resourgroupname-service.azurewebsites.net。
其他服务名称由短名称哈希的组合生成，不太可能与其他服务发生冲突。

### <a name="azure-ad-registration"></a>Azure AD 注册 

部署脚本尝试在 Azure AD 中注册三个 Azure AD 应用程序。 根据所选 Azure AD 租户中的权限，此操作可能会失败。 有两个选项：

- 如果已从租户列表中选择了一个 Azure AD 租户，请重新启动脚本，然后从列表中选择另一个租户。
- 或者，在另一个订阅中部署私有 Azure AD 租户。 重新启动脚本，并选择使用它。

## <a name="deployment-script-options"></a>部署脚本选项

该脚本采用以下参数：


```
-resourceGroupName
```

这可以是现有或新资源组的名称。

```
-subscriptionId
```


这是将在其中部署资源的订阅 ID。 可选。

```
-subscriptionName
```


或者，可以使用订阅名称。

```
-resourceGroupLocation
```


这是一个资源组的位置。 如果指定，则此参数将尝试在此位置创建新的资源组。 此参数也是可选的。


```
-tenantId
```


这是要使用的 Azure AD 租户。 

```
-development 0|1
```

这是为了进行开发而部署。 使用调试版本，并将 ASP.NET 环境设置为“开发”。 在 Visual Studio 2017 中创建 `.publishsettings` 用于导入，以允许它直接部署应用和服务。 此参数也是可选的。

```
-onlyBuild 0|1
```

这是为了仅重新生成和重新部署 Web 应用，以及重新生成 Docker 容器。 此参数也是可选的。

[azure-free]:https://azure.microsoft.com/free/
[powershell-install]:https://azure.microsoft.com/downloads/#powershell
[docker-url]: https://www.docker.com/
[dotnet-install]: https://dotnet.microsoft.com/download

## <a name="next-steps"></a>后续步骤

现在，你已了解如何从头开始部署 OPC 保管库，你可以：

> [!div class="nextstepaction"]
> [管理 OPC 保管库](howto-opc-vault-manage.md)