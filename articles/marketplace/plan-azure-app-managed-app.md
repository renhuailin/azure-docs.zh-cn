---
title: 为 Azure 应用程序产品/服务创建 Azure 托管应用程序计划
description: 访问 Microsoft 合作伙伴中心的商业市场门户，了解为新的 Azure 应用程序产品/服务创建托管应用程序计划所需内容。
author: aarathin
ms.author: aarathin
ms.reviewer: dannyevers
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 082b943aef3f82898b80d23d33a90d3f5ec3ebc6
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515126"
---
# <a name="plan-an-azure-managed-application-for-an-azure-application-offer"></a>为 Azure 应用程序产品/服务创建 Azure 托管应用程序计划

Azure“托管应用程序”计划是一种在 Azure 市场发布 Azure 应用程序产品/服务的方法。 如果尚未执行此操作，请参阅[为商业市场创建 Azure 应用程序产品/服务计划](plan-azure-application-offer.md)。

托管应用程序是通过 Azure 市场部署并计费的交易产品/服务。 用户看到的列表选项为“立即获取”。

如果需要下列条件，请使用 Azure 应用程序：托管应用程序计划：

- 使用虚拟机 (VM) 或基于基础结构即服务 (IaaS) 的完整解决方案为客户部署基于订阅的解决方案。
- 你或你的客户需要由合作伙伴来管理该解决方案。 例如，合作伙伴可以是系统集成商或托管服务提供商 (MSP)。

## <a name="managed-application-offer-requirements"></a>托管应用程序产品/服务要求

| 要求 | 详细信息 |
| ------------ | ------------- |
| Azure 订阅 | 托管应用程序必须部署到客户的订阅，但可由第三方管理。 |
| 计费和计量 | 在客户的 Azure 订阅中提供资源。 使用即用即付付款模型的 VM 通过 Microsoft 与客户交易并通过客户的 Azure 订阅计费。 <br><br> 对于自带许可的 VM，Microsoft 会对客户订阅产生的任何基础结构成本计费，但由你直接参与客户的软件许可证费用交易。 |
| 与 Azure 兼容的虚拟硬盘 (VHD) | 必须基于 Windows 或 Linux 构建 VM。 有关详细信息，请参阅：<br> * [创建 Azure VM 技术资产](./azure-vm-create-certification-faq.yml#address-a-vulnerability-or-an-exploit-in-a-vm-offer)（针对 Windows VHD）。<br> *  [Azure 支持的 Linux 发行版](../virtual-machines/linux/endorsed-distros.md)（针对 Linux VHD）。 |
| 客户使用情况归因 | 所有新 Azure 应用程序产品/服务还必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](azure-partner-customer-usage-attribution.md)。 有关客户使用情况属性及其启用方式，请参阅[Azure 合作伙伴客户使用情况属性](azure-partner-customer-usage-attribution.md)。 |
| 部署包 | 你将需要一个部署包，以供客户部署计划。 如果创建的多个计划需要采用相同技术配置，则可以使用相同的包。 有关详细信息，请参阅下一部分：部署包。 |
|||

> [!NOTE]
> 托管应用程序必须可以通过 Azure 市场来部署。 如果担心客户沟通方面的问题，请在启用潜在客户分享后与感兴趣的客户联系。

## <a name="usage-of-azure-kubernetes-service-aks-and-containers-in-managed-application"></a>在托管应用程序中使用 Azure Kubernetes 服务 (AKS) 和容器

### <a name="azure-application-offers-fall-into-two-categories"></a>Azure 应用程序产品/服务分为两类

- 解决方案模板 - 发布服务器不可访问
- 托管应用程序 - 发布服务器可通过部署时由客户授权的预定义授权访问

**解决方案模板：** 客户部署后，发布服务器无法更改解决方案模板产品/服务。 因此，此产品/服务类别中目前不允许预配容器和 Azure Kubernetes 服务 (AKS) 资源。

**托管应用程序：** 托管应用程序产品/服务允许发布服务器访问和控制在客户订阅中部署时创建的资源。 因此，此产品/服务类别中允许预配容器和 Azure Kubernetes 服务 (AKS) 资源 *<u></u>* 。

### <a name="rules-and-known-issues-for-aks-and-containers-in-managed-applications"></a>托管应用程序中 AKS 和容器的规则和已知问题

- AKS 节点资源组不会继承拒绝分配作为 Azure 托管应用程序的一部分。 这意味着，客户将对 AKS 资源包括在托管应用程序中时创建的 AKS 节点资源组具有完全访问权限，同时受管理资源组将具有适当的拒绝分配。
 
- 发布服务器可包括 Helm 图表和其它脚本作为 Azure 托管应用程序的一部分。 但是，该产品/服务将被视为常规托管应用程序部署，并且在部署时不会自动进行容器特定的处理或 Helm 图表安装。 发布服务器负责在部署时使用 VM 自定义脚本扩展或 Azure 部署脚本等常用技术，或在部署后执行相关脚本。
 
- 与常规 Azure 托管应用程序相同，发布服务器也要负责确保解决方案成功部署，并且正确配置、保护和操作所有组件。 例如，发布服务器可使用自己的容器注册表作为映像源，但完全负责容器安全性和正在进行的漏洞扫描。

> [!NOTE]
> 当市场提供官方容器应用程序产品/服务类型时，可能会撤销对 Azure 托管应用程序产品/服务中的容器和 AKS 的支持。 届时，可能需要使用新的产品/服务类型发布所有未来的产品/服务，并且现有产品/服务可能需要迁移到新的产品/服务类型并停用。

## <a name="deployment-package"></a>部署包

部署包涵盖该计划所需的所有模板文件和任何其他资源，打包为 .zip 文件。

所有 Azure 应用程序的 .zip 存档根文件夹必须涵盖这两个文件：

- 资源管理器模板文件，名为 [mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template)。 此模板定义要部署到客户的 Azure 订阅中的资源。 有关资源管理器模板的示例，请参阅[Azure 快速启动模板库](https://azure.microsoft.com/resources/templates/)或对应的[GitHub：Azure 资源管理器快速启动模板](https://github.com/azure/azure-quickstart-templates)存储库。
- 用于 Azure 应用程序创建体验的用户界面定义，名为 [createUiDefinition.json](../azure-resource-manager/managed-applications/create-uidefinition-overview.md)。 在用户界面中，可以指定让使用者能够提供参数值的元素。

支持的最大文件大小为：

- 压缩 .zip 存档大小最大为 1 Gb
- .zip 存档中任何单个解压缩文件最大为 1 Gb

所有新 Azure 应用程序产品/服务还必须包含一个 [Azure 合作伙伴客户使用情况属性 GUID](azure-partner-customer-usage-attribution.md)。

## <a name="azure-regions"></a>Azure 区域

可以将计划发布到 Azure 公共区域和、Azure 政府区域或两者。 在发布到 [Azure 政府](../azure-government/documentation-government-manage-marketplace-partners.md)之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)中请求试用帐户。

作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（仅位于美国）。

有关商业市场支持的国家和地区列表，请参阅[地理可用性和货币支持](marketplace-geo-availability-currencies.md)。

Azure 政府服务处理受特定政府法规和要求约束的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接仅对 Azure 政府客户可见。

## <a name="choose-who-can-see-your-plan"></a>选择计划开放对象

可以将每个计划设为对所有人可见（公用）或仅特定受众可见（专用）。 最多可以创建 100 个计划，其中最多可以有 45 个设为专用。 你可能想创建专用计划为特定客户提供不同的定价选项或技术配置。

你可以使用 Azure 订阅 ID 授予专用计划的访问权限，可选择包括分配的每个订阅 ID 的相关说明。 最多可以手动添加 10 个订阅 ID，使用 .CSV 文件最多可添加 10,000 个订阅 ID。 Azure 订阅 ID 表示为 GUID，字母必须为小写。

通过云解决方案提供商计划 (CSP) 经销商建立的 Azure 订阅不支持专用计划。 有关详细信息，请参阅[Microsoft 商用市场中的专用产品/服务](private-offers.md)。

> [!NOTE]
> 如果发布专用计划，以后仍可将其设为公用。 但发布公用计划后无法改设为专用。

## <a name="define-pricing"></a>定义定价

你必须提供每个计划的每月价格。 此价格不包括在此解决方案部署的资源所产生的任何 Azure 基础结构或即用即付软件成本中。

除了每月价格之外，还可以使用[按流量计费](marketplace-metering-service-apis.md)为非标准单位的消耗设置价格。 如果需要，可以将每月价格设置为零，专门使用按流量计费来收费。

以 USD（USD=美元）设置的价格在保存时会使用当前汇率转换为所有选定市场的当地币种。 但你也可选择针对各个市场设置客户价格。

## <a name="just-in-time-jit-access"></a>实时 (JIT) 访问

通过 JIT 访问可以对托管应用程序的资源请求提升的访问权限，以便进行故障排除或维护。 你始终对资源拥有只读访问权限，但在特定时间段内，可以获得更高访问权限。 有关详细信息，请参阅[对 Azure 托管应用程序启用和请求实时访问](../azure-resource-manager/managed-applications/request-just-in-time-access.md)。

> [!NOTE]
> 请确保更新 `createUiDefinition.json` 文件，以便支持此功能。

## <a name="deployment-mode"></a>部署模式

可以将托管应用程序计划配置为使用“完整”或“增量”部署模式。 在完整模式下，若[mainTemplate.json](../azure-resource-manager/managed-applications/publish-service-catalog-app.md?tabs=azure-powershell#create-the-arm-template) 未定义资源，客户重新部署应用程序会导致托管资源组被删。 在“增量模式”下，重新部署应用程序会使现有资源保持不变。 若要了解更多信息，请参阅 [Azure 资源管理器部署模式](../azure-resource-manager/templates/deployment-modes.md)。

## <a name="notification-endpoint-url"></a>通知终结点 URL

可提供 HTTPS Webhook 终结点，用以接收有关此计划托管应用程序实例上的所有 CRUD 操作通知。

## <a name="customize-allowed-customer-actions-optional"></a>自定义允许的客户操作（可选）

除了默认可用的`*/read`操作外，还可指定客户可以对托管资源执行的操作。

如果选择此选项，则需要提供控制操作和/或允许的数据操作。 有关详细信息，请参阅[了解 Azure 资源的拒绝分配](../role-based-access-control/deny-assignments.md)。 有关可用操作，请参阅 [Azure 资源管理器资源提供程序操作](../role-based-access-control/resource-provider-operations.md)。 例如，若要允许使用者重启虚拟机，请将 `Microsoft.Compute/virtualMachines/restart/action` 添加到允许的操作。

## <a name="choose-who-can-manage-the-application"></a>选择应用程序管理人员

你必须指出哪些人员可以管理每个选定云中的托管应用程序：_公共 Azure_ 和 _Azure 政府云_。 收集以下信息：

- “Azure Active Directory 租户 ID” – Azure AD 租户 ID （也称作目录 ID）包含要向其授予权限的用户、组合或应用程序标识。 可以在 Azure 门户的 [Azure Active Directory 属性](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Properties)中找到 Azure AD 租户 ID。
- “授权”–添加要向其授予托管资源组权限的用户、组合或应用程序的 Azure Active Directory 对象 ID。 通过其主体 ID 标识用户，该 ID 可在 [Azure 门户上的 Azure Active Directory 用户边栏选项卡](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)中找到。

每个主体 ID 需关联 Azure AD 内置角色之一（“所有者”或“参与者”）。 选定角色描述主体对客户订阅中的资源拥有的权限。 有关详细信息，请参阅 [Azure 内置角色](../role-based-access-control/built-in-roles.md)。 有关基于角色的访问控制 (RBAC) 的详细信息，请参阅 [Azure 门户中的 RBAC 入门](../role-based-access-control/overview.md)。

> [!NOTE]
> 尽管每个 Azure 区域最多可添加 100 个授权，但通常更简便的操作是创建 Active Directory 用户组并在“主体 ID”中指定其 ID。 这样你可以在部署计划之后向管理组添加更多用户，并减少以添加更多授权为唯一目的的计划更新需求。

## <a name="policy-settings"></a>策略设置

将[Azure 策略](../governance/policy/index.yml)用于托管应用程序，以便指定已部署解决方案的合规性要求。 有关策略定义和参数值的格式，请参阅 [Azure Policy 示例](../governance/policy/samples/index.md)。

最多可以配置五个策略，每个策略类型仅有一个实例。 某些策略类型需要附加参数。

| 策略类型 | 必需策略参数 |
| ------------ | ------------- |
| Azure SQL 数据库加密 | 否 |
| Azure SQL Server 审核设置 | 是 |
| Azure Data Lake Store 加密 | 否 |
| 审核诊断设置 | 是 |
| 审核资源位置合规性 | 否 |
|||

添加的每个策略类型必须关联标准或免费策略 SKU。 审核策略需选择标准 SKU。 策略名称限定为 50 个字符。

## <a name="next-steps"></a>后续步骤

- [创建 Azure 应用程序产品/服务](azure-app-offer-setup.md)
