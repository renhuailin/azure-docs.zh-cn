---
title: 规划 azure 托管应用程序的 Azure 应用程序产品/服务
description: 了解使用 Microsoft 合作伙伴中心的商业应用商店门户为新的 Azure 应用程序产品/服务创建托管应用程序计划所需的内容。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/06/2020
ms.openlocfilehash: 694f501efc565ed498c1c8d8e2e38326277e8605
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621410"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>规划 azure 托管应用程序的 Azure 应用程序产品/服务

Azure _托管应用程序_ 计划是在 azure Marketplace 中发布 azure 应用程序产品/服务的一种方法。 如果尚未执行此操作，请阅读 [为商业应用商店计划 Azure 应用程序优惠](plan-azure-application-offer.md)。

托管应用程序是通过 Azure Marketplace 部署并计费的服务。 用户看到的列表选项现已获取。

如果需要以下条件，请使用 Azure 应用程序：托管应用程序计划：

- 你将使用虚拟机 (VM) 或整个基础结构即服务 (IaaS) 解决方案为你的客户部署基于订阅的解决方案。
- 你或你的客户需要由合作伙伴管理解决方案。 例如，合作伙伴可以是系统集成商或托管服务提供商 (MSP) 。

## <a name="managed-application-offer-requirements"></a>托管应用程序提供要求

| 要求 | 详细信息 |
| ------------ | ------------- |
| Azure 订阅 | 托管应用程序必须部署到客户的订阅中，但可以由第三方管理。 |
| 计费和计量 | 客户的 Azure 订阅中提供了资源。 使用即用即付支付模型的 Vm 通过 Microsoft 与客户进行交易，并通过客户的 Azure 订阅进行计费。 <br><br> 对于自带许可证 Vm，Microsoft 会对客户订阅中产生的任何基础结构成本计费，但你直接向客户收取软件许可费用。 |
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参阅：<br> •为 Windows Vhd) [创建 AZURE VM 技术资产](./azure-vm-create-certification-faq.md#address-a-vulnerability-or-an-exploit-in-a-vm-offer) (。<br> • Linux Vhd) 的  [Azure (认可的 linux 分发](../virtual-machines/linux/endorsed-distros.md) 版。 |
| 客户使用情况归因 | 所有新 Azure 应用程序产品/服务还必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](azure-partner-customer-usage-attribution.md)。 有关客户使用情况和如何启用的详细信息，请参阅 [Azure 合作伙伴客户使用归属](azure-partner-customer-usage-attribution.md)。 |
| 部署包 | 你将需要一个允许客户部署你的计划的部署包。 如果创建的多个计划需要相同的技术配置，则可以使用相同的包。 有关详细信息，请参阅下一节：部署包。 |
|||

> [!NOTE]
> 必须通过 Azure Marketplace 部署托管应用程序。 如果客户沟通是个问题，请在启用 "潜在客户共享" 后与感兴趣的客户联系。

## <a name="deployment-package"></a>部署包

部署包包含此计划所需的所有模板文件以及任何其他资源，并打包为 .zip 文件。

所有 Azure 应用程序都必须在 .zip 存档的根文件夹中包括这两个文件：

- 资源管理器模板文件，名为 [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)。 此模板定义要部署到客户的 Azure 订阅中的资源。 有关资源管理器模板的示例，请参阅 [Azure 快速入门模板库](https://azure.microsoft.com/documentation/templates/) 或相应的 [GitHub： Azure 资源管理器快速入门模板](https://github.com/azure/azure-quickstart-templates) 存储库。
- 用于 Azure 应用程序创建体验的用户界面定义，名为 [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)。 在用户界面中，可以指定让使用者能够提供参数值的元素。

支持的最大文件大小为：

- 总压缩 .zip 存档大小为 1 Gb
- 对于 .zip 存档中的任何单个未压缩文件，最多 1 Gb

所有新 Azure 应用程序产品/服务还必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](azure-partner-customer-usage-attribution.md)。

## <a name="azure-regions"></a>Azure 区域

你可以将计划发布到 Azure 公共区域和/或 Azure 政府区域。 在发布到 [Azure 政府](../azure-government/documentation-government-manage-marketplace-partners.md)之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)中请求试用帐户。

作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。

有关商业应用商店支持的国家和地区的列表，请参阅 [地理可用性和货币支持](marketplace-geo-availability-currencies.md)。

Azure 政府服务处理受特定政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接仅对 Azure 政府客户可见。

## <a name="choose-who-can-see-your-plan"></a>选择可以查看你的计划的人员

你可以将每个计划配置为对 (公共) 的所有人都可见，或者配置为仅针对特定受众 (私有) 。 最多可以创建100个计划，最多可以有45个计划。 你可能想要创建一个专用计划，为特定客户提供不同的定价选项或技术配置。

你可以使用 Azure 订阅 Id 授予对私有计划的访问权限，其中包含分配给每个订阅 ID 的说明。 你最多可以手动添加10个订阅 Id，或使用最多添加10000个订阅 Id。CSV 文件。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

通过云解决方案提供商计划的经销商 (CSP) 建立的 Azure 订阅不支持私有计划。 有关详细信息，请参阅 [Microsoft 商用 marketplace 中的专用产品/服务](private-offers.md)。

> [!NOTE]
> 如果发布专用计划，则可以在以后将其可见性更改为 "公共"。 但是，在发布公共计划后，不能将其可见性更改为私有。

## <a name="define-pricing"></a>定义定价

您必须为每个计划提供每月价格。 此价格不包括在此解决方案部署的资源所产生的任何 Azure 基础结构或即用即付软件成本中。

除了每月价格之外，还可以使用[按流量计费](partner-center-portal/azure-app-metered-billing.md)为非标准单位的消耗设置价格。 如果需要，可以将每月价格设置为零，专门使用按流量计费来收费。

价格设置为 USD (USD = 美国美元) 将在保存时使用当前汇率转换为所有选定市场的本地货币。 不过，您可以选择为每个市场设置客户价格。

## <a name="just-in-time-jit-access"></a>实时 (JIT) 访问

通过 JIT 访问可以对托管应用程序的资源请求提升的访问权限，以便进行故障排除或维护。 你始终对资源拥有只读访问权限，但在特定时间段内，可以获得更高访问权限。 有关详细信息，请参阅[对 Azure 托管应用程序启用和请求实时访问](../azure-resource-manager/managed-applications/request-just-in-time-access.md)。

> [!NOTE]
> 请确保更新 `createUiDefinition.json` 文件，以便支持此功能。

## <a name="deployment-mode"></a>部署模式

可以将托管应用程序计划配置为使用 **完整** 或 **增量** 部署模式。 在完整模式下，如果未在的 [mainTemplate.js](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)中定义资源，则客户重新部署应用程序将导致删除托管资源组中的资源。 在“增量模式”下，重新部署应用程序会使现有资源保持不变。 若要了解详细信息，请参阅 [Azure 资源管理器部署模式](../azure-resource-manager/templates/deployment-modes.md)。

## <a name="notification-endpoint-url"></a>通知终结点 URL

你可以选择提供 HTTPS Webhook 终结点，以接收有关计划的托管应用程序实例上的所有 CRUD 操作的通知。

## <a name="customize-allowed-customer-actions-optional"></a>自定义允许的客户操作 (可选) 

还可以选择指定客户可对托管资源执行的操作以及 `*/read` 默认提供的操作。

如果选择此选项，则需要提供控制操作或允许的数据操作，或同时提供两者。 有关详细信息，请参阅[了解 Azure 资源的拒绝分配](../role-based-access-control/deny-assignments.md)。 有关可用操作，请参阅 [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md)。 例如，若要允许使用者重启虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到允许的操作。

## <a name="choose-who-can-manage-the-application"></a>选择可以管理应用程序的人员

你必须指示哪些用户可以管理每个选定云中的托管应用程序： _公共 Azure_ 和 _azure 政府云_。 收集以下信息：

- **Azure Active Directory 租户 id** – AZURE AD 租户 id (也称为目录 ID) ，其中包含要向其授予权限的用户、组或应用程序的标识。 可以在 Azure 门户的 " [Azure Active Directory 的属性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)" 中找到 AZURE AD 租户 ID。
- **授权** –为你要授予对托管资源组的权限的每个用户、组或应用程序添加 AZURE ACTIVE DIRECTORY 对象 ID。 通过其主体 ID 标识用户，该 ID 可在 [Azure 门户上的 Azure Active Directory 用户边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到。

对于每个主体 ID，将 Azure AD (所有者或参与者) 中的某个内置角色关联起来。 你选择的角色将描述主体对客户订阅中的资源所具有的权限。 有关详细信息，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。 有关基于角色的访问控制 (RBAC) 的详细信息，请参阅 [Azure 门户中的 RBAC 入门](../role-based-access-control/overview.md)。

> [!NOTE]
> 尽管每个 Azure 区域最多可以添加100个授权，但通常可以更轻松地创建 Active Directory 的用户组，并在 "主体 ID" 中指定其 ID。 这使你可以在部署计划后向管理组中添加更多用户，并减少仅更新计划以添加更多授权的需求。

## <a name="policy-settings"></a>策略设置

可以将 [Azure 策略](../governance/policy/index.yml) 应用于托管应用程序，以指定所部署解决方案的符合性要求。 有关策略定义和参数值的格式，请参阅 [Azure Policy 示例](../governance/policy/samples/index.md)。

最多可以配置五个策略，每个策略类型只能有一个实例。 某些策略类型需要其他参数。

| 策略类型 | 需要策略参数 |
| ------------ | ------------- |
| Azure SQL 数据库加密 | 否 |
| Azure SQL Server 审核设置 | 是 |
| Azure Data Lake Store 加密 | 否 |
| 审核诊断设置 | 是 |
| 审核资源位置符合性 | 否 |
|||

对于添加的每个策略类型，必须关联标准或免费策略 SKU。 审核策略需选择标准 SKU。 策略名称限制为50个字符。

## <a name="next-steps"></a>后续步骤

- [如何在商业应用商店中创建 Azure 应用程序产品/服务](create-new-azure-apps-offer.md)
