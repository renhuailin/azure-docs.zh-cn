---
title: 使用 Azure IoT 解决方案站点 - Azure | Microsoft Docs
description: 介绍如何使用 AzureIoTSolutions.com 网站来部署解决方案加速器。
author: dominicbetts
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dobett
ms.openlocfilehash: 3251adb72adfb53209878fcfc1450518b9bc9f37
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743341"
---
# <a name="use-the-azureiotsolutionscom-site-to-deploy-your-solution-accelerator"></a>使用 azureiotsolutions.com 站点来部署解决方案加速器

可从 [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) 将 Azure IoT 解决方案加速器部署到 Azure 订阅。 AzureIoTSolutions.com 同时托管 Microsoft 开源解决方案加速器和合作伙伴解决方案加速器。 这些解决方案加速器适用于 [Azure IoT 参考体系结构](/azure/architecture/reference-architectures/iot)。 可使用该站点快速部署解决方案加速器作为演示或生产环境。

:::image type="content" source="media/iot-accelerators-permissions/iotsolutionscom.png" alt-text="IoT 解决方案主页":::

> [!TIP]
> 如果需要对部署过程进行更多控制，则可以使用 CLI 来部署解决方案加速器。

可在以下配置中部署解决方案加速器：

* 标准：扩展的基础结构部署，适用于开发生产环境。 Azure 容器服务将微服务部署到多个 Azure 虚拟机。 Kubernetes 会协调托管单个微服务的 Docker 容器。
* 基本：降低成本版本，适用于演示或部署测试。 所有微服务都部署到一个 Azure 虚拟机。
* 本地：用于测试和开发的本地计算机部署。 此方法将微服务部署到本地 Docker 容器，并连接到云中的 IoT 中心、Azure Cosmos DB 和 Azure 存储服务。

每个解决方案加速器都使用 IoT 中心、Azure 流分析和 Cosmos DB 等 Azure 服务的不同组合。 有关详细信息，请访问 [AzureIoTSolutions.com](https://www.azureiotsolutions.com/Accelerators) 并选择解决方案加速器。

## <a name="sign-in-at-azureiotsolutionscom"></a>登录 azureiotsolutions.com

必须先使用与 Azure 订阅关联的凭据登录 AzureIoTSolutions.com，然后才能部署解决方案加速器。 如果帐户与多个 Microsoft Azure Active Directory (AD) 租户关联，则可以使用 **帐户选择下拉列表** 选择要使用的目录。

部署解决方案加速器、管理用户以及管理 Azure 服务的权限取决于你在所选目录中的角色。 与解决方案加速器关联的常见 Azure AD 角色包括：

* 全局管理员：每个 Azure AD 租户可以有多个[全局管理员](../active-directory/roles/permissions-reference.md)：

  * 创建某个 Azure AD 租户时，默认情况下会成为该租户的全局管理员。
  * 全局管理员可以部署基本和标准解决方案加速器。

* 域用户：每个 Azure AD 租户可以有多个域用户。 域用户可以部署基本解决方案加速器。

* 来宾用户：每个 Azure AD 租户可以有多个来宾用户。 来宾用户不能在 Azure AD 租户中部署解决方案加速器。

有关 Azure AD 中用户及角色的详细信息，请参阅以下资源：

* [在 Azure Active Directory 中创建用户](../active-directory/fundamentals/active-directory-users-profile-azure-portal.md)
* [将用户分配到应用](../active-directory/manage-apps/assign-user-or-group-access-portal.md)

## <a name="choose-your-device"></a>选择设备

AzureIoTSolutions.com 站点链接到 [Azure IoT 认证设备目录](https://devicecatalog.azure.com/)。

该目录列出数百个 IoT 认证硬件设备，可供连接到解决方案加速器，以开始生成 IoT 解决方案。

![设备目录](media/iot-accelerators-permissions/devicecatalog.png)

如果你是硬件制造商，请单击“成为合作伙伴”，了解如何成为 Microsoft IoT 认证计划的合作伙伴。

## <a name="next-steps"></a>后续步骤

若要试用 IoT 解决方案加速器之一，请参阅快速入门：[尝试使用联网工厂解决方案](quickstart-connected-factory-deploy.md)。
