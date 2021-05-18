---
title: IoT 解决方案加速器常见问题解答 - Azure | Microsoft Docs
description: 本文解答了 IoT 解决方案加速器的常见问题。 它包括 GitHub 存储库的链接。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 02/15/2018
ms.author: dobett
ms.openlocfilehash: 1fd2b8461bd66c826dc4890c331b740c4703f896
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "96903983"
---
# <a name="frequently-asked-questions-for-iot-solution-accelerators"></a>IoT 解决方案加速器常见问题解答

另请参阅[连接工厂相关的常见问题解答](iot-accelerators-faq-cf.md)。

### <a name="where-can-i-find-the-source-code-for-the-solution-accelerators"></a>在哪里可以找到此解决方案加速器的源代码？

源代码存储在以下 GitHub 存储库中：

* [连接工厂解决方案加速器](https://github.com/Azure/azure-iot-connected-factory)
* [设备模拟解决方案加速器](https://github.com/Azure/device-simulation-dotnet)

### <a name="where-can-i-find-the-remote-monitoring-and-predictive-maintenance-solution-accelerators"></a>在哪里可以找到远程监视和预见性维护解决方案加速器？

自 2020 年 12 月 10 日起，远程监视和预测性维护加速器已从 [Azure IoT 解决方案加速器](https://www.azureiotsolutions.com/Accelerators)站点中删除，不再适用于新部署。 这两个加速器的 GitHub 存储库已存档。 代码仍可供任何人访问，但存储库不会产生任何新的贡献。

### <a name="what-happens-to-my-existing-remote-monitoring-and-predictive-maintenance-deployments"></a>现有远程监视和预见性维护部署会发生什么情况？

现有部署不会受到远程监视和预测维护解决方案加速器的删除影响，会继续工作。 分支存储库也不会受到影响。 GitHub 上的主存储库已存档。

### <a name="how-do-i-deploy-device-simulation-solution-accelerator"></a>如何实现部署设备模拟解决方案加速器？

若要部署设备模拟解决方案加速器，请参阅[设备模拟](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) GitHub 存储库。

### <a name="where-can-i-find-information-about-the-removed-solution-accelerators"></a>在哪里可以找到有关已删除解决方案加速器的信息？

请参阅以前版本站点上的以下页面：

* [远程监视](/previous-versions/azure/iot-accelerators/about-iot-accelerators)
* [预测性维护](/previous-versions/azure/iot-accelerators/about-iot-accelerators)

### <a name="what-sdks-can-i-use-to-develop-device-clients-for-the-solution-accelerators"></a>可以使用哪些 SDK 为解决方案加速器开发设备客户端？

可以在 [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub 存储库中找到各种语言（C、NET、Java、Node.js、Python）的 IoT 设备 SDK。

如果使用 DevKit 设备，可以在 [IoT DevKit SDK](https://github.com/Microsoft/devkit-sdk) GitHub 存储库中找到资源和示例。

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-azure-ad-tenant-how-do-i-complete-this-task"></a>我是服务管理员，我想要更改我的订阅与特定 Azure AD 租户之间的目录映射。 如何完成此任务？

请参阅[如何将现有订阅添加到 Azure AD 目录](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md#to-associate-an-existing-subscription-to-your-azure-ad-directory)

### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organizational-account"></a>我在使用组织帐户登录时要更改服务管理员或共同管理员

请参阅支持文章[使用组织帐户登录时更改服务管理员和共同管理员](https://azure.microsoft.com/support/changing-service-admin-and-co-admin)。

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>为何会出现以下错误？ “帐户没有创建解决方案的正确权限。 请咨询帐户管理员或使用其他帐户进行尝试。”

请查看以下指南示意图：

![权限流程图](media/iot-accelerators-faq/flowchart.png)

> [!NOTE]
> 如果在验证你是 Azure AD 租户的全局管理员和订阅的共同管理员后，仍看到此错误，请让帐户管理员删除该用户，并按以下顺序重新分配必要的权限。 首先，将用户添加为全局管理员，然后将用户添加为 Azure 订阅的共同管理员。 如果问题仍然存在，请联系[帮助和支持](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

### <a name="why-am-i-seeing-this-error-when-i-have-an-azure-subscription-an-azure-subscription-is-required-to-create-pre-configured-solutions-you-can-create-a-free-trial-account-in-just-a-couple-of-minutes"></a>为何在我具有 Azure 订阅时会出现以下错误？ “创建预配置解决方案需要 Azure 订阅。 只需几分钟即可创建一个免费试用帐户。”

如果确定拥有 Azure 订阅，请验证订阅的租户映射，并确保在下拉列表中选择正确的租户。 如果租户经验证是正确的，请按照上图，验证订阅与此 Azure AD 租户之间的映射。

### <a name="whats-the-difference-between-deleting-a-resource-group-in-the-azure-portal-and-clicking-delete-on-a-solution-accelerator-in-azureiotsolutionscom"></a>在 Azure 门户中删除资源组与在 azureiotsolutions.com 中对解决方案加速器单击删除之间的区别是什么？

* 如果在 [azureiotsolutions.com](https://www.azureiotsolutions.com/) 中删除解决方案加速器，则会删除在创建解决方案加速器时部署的所有资源。 如果向资源组添加了其他资源，则也会删除这些资源。
* 如果在 [Azure 门户](https://portal.azure.com)中删除资源组，则只会删除该资源组中的资源。 此外还需要删除与解决方案加速器关联的 Azure Active Directory 应用程序。

### <a name="can-i-continue-to-leverage-my-existing-investments-in-azure-iot-solution-accelerators"></a>是否可以继续利用 Azure IoT 解决方案加速器中的现有投资？

是。 现存的所有解决方案可继续在 Azure 订阅中工作，GitHub 中仍会提供其源代码。

### <a name="how-many-iot-hub-instances-can-i-provision-in-a-subscription"></a>在一个订阅中可以设置多少个 IoT 中心实例？

默认情况下，可[为每个订阅预配 10 个 IoT 中心](../azure-resource-manager/management/azure-subscription-service-limits.md#iot-hub-limits)。 可以创建 [Azure 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以提高此限制。 由于每个解决方案加速器将预配一个新的 IoT 中心，因此，在给定的订阅中，最多只能预配 10 个解决方案加速器。

### <a name="how-many-azure-cosmos-db-instances-can-i-provision-in-a-subscription"></a>在订阅中可以预配多少个 Azure Cosmos DB 实例？

50 个。 可以创建 [Azure 支持票证](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)提高此限制，但默认情况下，只能对每个订阅预配 50 个 Cosmos DB 实例。

### <a name="can-i-create-a-solution-accelerator-if-i-have-microsoft-azure-for-dreamspark"></a>如果我有 Microsoft Azure for DreamSpark，是否可以创建解决方案加速器？

> [!NOTE]
> Microsoft Azure for DreamSpark 现在称为 Microsoft Imagine 学生版。

目前无法使用 [Microsoft Azure for DreamSpark](https://azure.microsoft.com/pricing/member-offers/imagine/) 帐户创建解决方案加速器。 但是，可以在几分钟内创建一个 [Azure 免费试用帐户](https://azure.microsoft.com/free/)，以便创建解决方案加速器。

### <a name="how-do-i-delete-an-azure-ad-tenant"></a>如何删除 Azure AD 租户？

请参阅 Eric Golpe 的博客文章 [Walkthrough of Deleting an Azure AD Tenant](/archive/blogs/ericgolpe/walkthrough-of-deleting-an-azure-ad-tenant)（有关删除 Azure AD 租户的演练）。

### <a name="next-steps"></a>后续步骤

还可以浏览 IoT 解决方案加速器的一些其他特性和功能：

* [部署连接的工厂解决方案加速器](quickstart-connected-factory-deploy.md)
* [从头保障 IoT 的安全](../iot-fundamentals/iot-security-ground-up.md)