---
title: 如何在 Azure 中部署 OPC 孪生云依赖项 | Microsoft Docs
description: 本文介绍如何部署进行本地开发和调试所需的 OPC 孪生 Azure 依赖项。
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 42024fc506de7befed7c44ebcc410756b6f43a35
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105645520"
---
# <a name="deploying-dependencies-for-local-development"></a>为本地开发部署依赖项

> [!IMPORTANT]
> 当我们更新本文时，请参阅 [Azure 工业 IoT](https://azure.github.io/Industrial-IoT/) 来了解最新内容。

本文介绍如何仅部署执行本地开发和调试所需的 Azure 平台服务。   最后，你将部署一个资源组，其中包含本地开发和调试所需的一切。

## <a name="deploy-azure-platform-services"></a>部署 Azure 平台服务

1. 确保已安装 PowerShell 和 [AzureRM PowerShell](/powershell/azure/azurerm/install-azurerm-ps) 扩展。  打开命令提示符或终端并运行：

   ```bash
   git clone https://github.com/Azure/azure-iiot-components
   cd azure-iiot-components
   ```

   ```bash
   deploy -type local
   ```

2. 按照提示为部署的资源组分配名称。  该脚本仅将依赖项部署到 Azure 订阅中的此资源组，而不部署微服务。  该脚本还会在 Azure AD 中注册一个应用程序。  这是支持基于 OAUTH 的身份验证所必需的。  部署可能需要几分钟的时间。

3. 脚本完成后，可以选择保存 .env 文件。  .env 环境文件是要在开发计算机上运行的所有服务和工具的配置文件。  

## <a name="troubleshooting-deployment-failures"></a>排查部署失败的问题

### <a name="resource-group-name"></a>资源组名称

请确保使用简短且简单的资源组名称。  该名称还用于对资源进行命名，因此必须符合资源命名要求。  

### <a name="azure-active-directory-ad-registration"></a>Azure Active Directory (AD) 注册

部署脚本尝试在 Azure AD 中注册 Azure AD 应用程序。  根据对所选 Azure AD 租户的权限，此操作可能会失败。 有三个选项：

1. 如果你从租户列表中选择了一个 Azure AD 租户，请重启脚本并从列表中选择一个不同的租户。
2. 也可部署专用 Azure AD 租户，重启脚本并选择使用该租户。
3. 请继续操作，无需进行身份验证。  由于是在本地运行微服务，因此可以接受，但不能模拟生产环境。  

## <a name="next-steps"></a>后续步骤

现在，你已经将 OPC 孪生服务成功部署到现有项目中，接下来建议：

> [!div class="nextstepaction"]
> [了解如何部署 OPC 孪生模块](howto-opc-twin-deploy-modules.md)