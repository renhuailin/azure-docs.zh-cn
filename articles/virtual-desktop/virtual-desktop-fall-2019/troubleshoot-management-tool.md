---
title: Windows 虚拟桌面（经典）管理工具 - Azure
description: 如何对 Windows 虚拟桌面（经典）管理工具的问题进行故障排除。
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 6f03284103b4a2aa6900bf1ba5c50a4688c50b0e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "88005476"
---
# <a name="troubleshoot-the-windows-virtual-desktop-classic-management-tool"></a>对 Windows 虚拟桌面管理工具进行故障排除

>[!IMPORTANT]
>本教程的内容适用于 Windows 虚拟桌面（经典），后者不支持 Azure 资源管理器 Windows 虚拟桌面对象。

本文介绍部署 Windows 虚拟桌面管理工具时可能出现的问题，以及如何修复这些问题。

## <a name="error-management-tool-services-configured-but-automated-setup-fails"></a>错误：已配置管理工具服务，但自动安装失败

如果成功设置了管理工具服务，但自动安装失败，则会看到此错误消息：

```console
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n "status": "Failed",\r\n "error": {\r\n "code": "ResourceDeploymentFailure",\r\n "message": "The resource operation completed with terminal provisioning state 'Failed'."\r\n }\r\n}"}]}
```

这通常意味着以下两点：

- 用户拥有订阅和租户级别全局管理员的所有者权限，但无法登录到 Azure。
- 用户的帐户设置已启用多重身份验证。

解决方法：

1. 确保为 Azure Active Directory 用户主体名称创建的用户具有“参与者”订阅级别。
2. 用 UPN 帐户登录到<portal.azure.com>以检查帐户设置，并确保未启用多重身份验证。 如果多重身份验证已打开，请将其关闭。
3. 访问 Windows 虚拟桌面许可页面，确保服务器和客户端应用程序已获得许可。
4. 如果问题仍然存在，请查看[部署管理工具](manage-resources-using-ui.md)教程，并重新部署工具。

## <a name="error-job-with-specified-id-already-exists"></a>错误：已存在具有指定 ID 的作业

如果用户看到错误消息“已存在具有指定 ID 的作业”，则是因为部署模板时没有在“Application name”参数中提供唯一名称。

若要解决此问题，请填写“Application name”参数，重新部署管理工具。

## <a name="delayed-consent-prompt-when-opening-management-tool"></a>打开管理工具时许可提示延迟出现

部署管理工具时，许可提示可能无法立即打开。 这意味着加载 Azure Web 应用服务所用的时间比平时长。 Azure Web 加载完成后应会出现提示。

## <a name="the-user-cant-deploy-the-management-tool-in-the-east-us-region"></a>用户无法在美国东部区域部署管理工具

如果客户将区域设置为美国东部，则无法部署管理工具。

若要解决此问题，请在其他区域部署管理工具。 在不同区域重新部署该工具不会影响用户体验。

## <a name="next-steps"></a>后续步骤

- 若要了解升级跟踪，请参阅[故障排除概述、反馈和支持](troubleshoot-set-up-overview-2019.md)。
- 若要了解如何报告有关 Windows 虚拟桌面工具的问题，请参阅[远程桌面服务的 ARM 模板](https://github.com/Azure/RDS-Templates/blob/master/README.md)。
- 若要了解如何部署管理工具，请参阅[部署管理工具](manage-resources-using-ui.md)。