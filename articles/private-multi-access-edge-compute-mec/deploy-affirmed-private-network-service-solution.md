---
title: 在 Azure 上部署 Affirmed 专用网络服务
description: 了解如何在 Azure 上部署 Affirmed 专用网络服务解决方案
author: KumudD
ms.service: private-multi-access-edge-compute-mec
ms.topic: how-to
ms.date: 06/16/2021
ms.author: hollycl
ms.openlocfilehash: d3c027a7d4006a947f520dee8406e40a30f1b304
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112458445"
---
# <a name="deploy-affirmed-private-network-service-on-azure"></a>在 Azure 上部署 Affirmed 专用网络服务

本文简要介绍了通过 Microsoft Azure 市场在 Azure Stack Edge 设备上部署 Affirmed 专用网络服务 (APNS) 解决方案的流程。

下图显示了 Affirmed 专用网络服务的系统体系结构，包括部署所需的资源。

![Affirmed 专用网络服务部署](media/deploy-affirmed-private-network-service/deploy-affirmed-private-network-service.png)

## <a name="collect-required-information"></a>收集所需信息

要部署 APNS，必须具有以下资源：

- 已配置的 Azure 网络功能管理器 - 一个设备对象，用作 Azure Stack Edge 设备的数字孪生体。 

- 完全部署的 Azure Stack Edge 和 NetFoundry VM。 

- Affirmed 管理系统 VM 产品/服务和 APNS 托管应用程序的订阅审批。 

- 具有活动订阅和以下访问权限的 Azure 帐户：  

    - 资源组的内置“所有者”角色。 

    - 订阅的内置“托管应用程序参与者”角色。 

    - 要加入的虚拟网络和子网（打开端口 tcp/443 和 tcp/8443）。 

    - 5 个虚拟子网的 IP 地址。 

    - Affirmed 发布工程提供的有效 SAS 令牌。  

    - 在部署过程中要编程的管理用户名/密码。 
    
## <a name="deploy-apns"></a>部署 APNS

若要使用所有必需资源和所需的相关信息部署 APNS 托管应用程序，请从 Microsoft Azure 市场中选择 APNS 托管应用程序。 部署 APNS 时，会自动为你创建所有所需的资源，这些资源包含在受管理资源组中。

完成以下过程以部署 APNS：
1.  打开 Azure 门户，然后选择“创建资源”。
2.  在搜索栏中输入“APNS”并按 Enter 键。
3.  选择“查看专用产品/服务”。 
    > [!NOTE]
    > 在选择“查看专用产品/服务”之前，将不会显示 APNS 托管应用程序。
4.  从“专用产品/服务”下拉菜单中选择“创建”，然后选择要部署的选项。
5.  完成应用程序设置、网络设置，然后审阅并创建。
6.  选择“部署”。

## <a name="next-steps"></a>后续步骤

- 有关在 Azure Stack Edge 设备上部署 APNS 并配置 NetFoundry 设置的分步说明，请查看 [Affirmed 专用网络服务部署指南](https://go.microsoft.com/fwlink/?linkid=2165732)。
- 有关操作员用于部署、监视和管理专用移动核心网络的编程 GUI 驱动门户的信息，请参阅 [Affirmed 专用网络服务管理器用户指南](https://go.microsoft.com/fwlink/?linkid=2165932)。
- 详细了解 [Azure 上的 Affirmed 专用网络服务](affirmed-private-network-service-overview.md)。
