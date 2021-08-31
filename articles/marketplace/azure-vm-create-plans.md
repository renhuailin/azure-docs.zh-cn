---
title: 在 Azure 市场中为虚拟机产品/服务创建计划
description: 在 Azure 市场中为虚拟机产品/服务创建计划。
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: iqshahmicrosoft
ms.author: iqshah
ms.date: 07/26/2021
ms.openlocfilehash: a974bf12873d46b7835d861b8c4d7007fb7e72ee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747248"
---
# <a name="create-plans-for-a-virtual-machine-offer"></a>为虚拟机产品/服务创建计划

在“计划概述”页上（从合作伙伴中心的左侧导航菜单中选择）可以在同一产品/服务内提供各种计划选项。 产品/服务需要至少一个计划（以前称为 SKU），计划可能在盈利受众、Azure 区域、功能或 VM 映像方面有所不同。

最多可为每个产品/服务创建 100 个计划；其中专用计划最多 45 个。 详细了解 [Microsoft 商业市场中的专用产品/服务](private-offers.md)中的专用计划。

创建计划后，选择“计划概述”选项卡以显示：

- 计划名称
- 许可证模型
- 受众（公共或专用）
- 当前发布状态
- 可用操作

此窗格上的可用操作因计划的当前状态而异。

- 如果计划状态为“草稿”，选择“删除草稿”。
- 如果计划状态为上线，选择“弃用计划”或“同步专用受众”。

## <a name="create-a-new-plan"></a>创建新计划

选择顶部的“+ 新建计划”。

在“新建计划”对话框中，为此产品/服务中的每个计划输入唯一的“计划 ID”。 客户可以在产品 Web 地址中看到此 ID。 只使用小写字母和数字、破折号或下划线字符，最多包含 50 个字符。

> [!NOTE]
> 选择“创建”后，便无法更改计划 ID。

输入“计划名称”。 客户在确定要在产品/服务中选择哪个计划时会看到此名称。 创建明确指出计划之间差异的唯一名称。    例如，可以输入具有即用即付、BYOL、高级和企业计划的 Windows Server。

选择“创建”。 这将打开“计划设置”页。

## <a name="plan-setup"></a>计划设置

为计划类型设置高级别的配置，指定它是否重复使用其他计划中的技术配置，并确定应在其中提供该计划的 Azure 区域。 此处的选择确定同一计划的其他窗格上显示的字段。

### <a name="azure-regions"></a>Azure 区域

你的计划必须至少在一个 Azure 区域中提供。

选择“Azure 全球”可向具有商业市场集成的所有 Azure 全球区域的客户提供你的计划。 有关详细信息，请参阅[地理区域可用性和货币支持](marketplace-geo-availability-currencies.md)。

选择“Azure 政府”可在 [Azure 政府](../azure-government/documentation-government-welcome.md)区域中提供你的计划。 此区域为来自美国联邦、州、地方或部落实体的客户，以及有资格为其提供服务的合作伙伴提供受控访问权限。 作为发布者，你负责任何符合性控制、安全措施和最佳做法。 Azure 政府使用物理隔离的数据中心和网络（只位于美国）。

在发布到 [Azure 政府](../azure-government/documentation-government-manage-marketplace-partners.md)之前，请在环境中测试和验证计划，因为某些终结点可能会有所不同。 若要设置和测试计划，请从 [Microsoft Azure 政府试用版](https://azure.microsoft.com/global-infrastructure/government/request/)页面中请求试用帐户。

> [!NOTE]
> 在计划已发布并在特定 Azure 区域中可用后，无法删除该区域。

### <a name="azure-government-certifications"></a>Azure 政府认证

仅当选择“Azure 政府”作为上一部分中的 Azure 区域时，此选项才可见。

Azure 政府服务处理符合特定政府法规和要求的数据。 例如，FedRAMP、NIST 800.171 (DIB)、ITAR、IRS 1075、DoD L4 和 CJIS。 若要了解这些计划的认证，可以提供多达 100 个链接来描述它们。 这些链接可以直接链接到计划上的列表，也可以是你自己网站上关于这些认证的符合性说明的链接。 这些链接只对 Azure 政府客户可见。

选择“保存草稿”，然后继续转到左侧导航计划菜单的下一个选项卡“计划列表”。

## <a name="plan-listing"></a>计划一览

配置计划的列表详细信息。 此窗格显示可能与相同产品/服务中的其他计划不同的特定信息。

### <a name="plan-name"></a>计划名称

此字段自动填充了你在创建计划时为计划命名的名称。 此名称在 Azure 市场中显示为此计划的标题。 它最多包含 100 个字符。

### <a name="plan-summary"></a>计划摘要

提供计划（而不是产品/服务）的简短摘要。 此摘要限制为 100 个字符。

### <a name="plan-description"></a>计划说明

描述此软件计划的独特之处，以及产品/服务内的计划之间的差异。 仅介绍计划，不介绍产品/服务。 计划说明最多可以包含 2,000 个字符。

选择“保存草稿”，然后继续转到左侧导航计划菜单中的下一个选项卡“定价和可用性”。

## <a name="pricing-and-availability"></a>定价和可用性

此窗格中，将配置：

- 提供此计划的市场。 每个计划必须至少在一个[市场](marketplace-geo-availability-currencies.md)中提供。
- 每小时价格。
- 是让计划对所有人可见，还是仅对特定客户（专用受众）可见。

### <a name="markets"></a>市场

每个计划必须至少在一个市场中提供。 默认情况下，会选择大多数市场。 若要编辑列表，请选择“编辑市场”，然后根据计划在该位置是否可供（或不可供）购买，选中或清除每个市场位置对应的复选框。 所选市场中的用户仍可将产品/服务部署到[“计划设置”](#plan-setup)部分中选择的所有 Azure 区域。

选择“仅选择 Microsoft 税收减免”以仅选择 Microsoft 会代表你减免销售和使用税的国家/地区。  发布到中国的计划限制为免费计划或自带许可 (BYOL) 计划。

如果已采用美元 (USD) 货币为计划设置了价格并添加另一个市场位置，则系统根据当前汇率计算用于新市场的价格。 请始终在发布之前查看用于每个市场的价格。 保存更改后，选择“导出价格(.xlsx)”来查看定价。

删除市场时，该市场中使用活动部署的客户将无法创建新部署或扩展其现有部署。 现有部署不受影响。

选择“保存”以继续操作。

### <a name="pricing"></a>定价

对于“许可证模型”，选择“根据使用量每月计费计划”以配置此计划的定价，或选择“自带许可”让客户通过现有许可证使用此计划。

对于根据使用量每月计费计划，请使用以下三个定价条目选项之一：

- “根据核心” - 提供每个核心的定价（美元）。 Microsoft 计算每个核心大小的定价，并使用当前汇率将其转换为本地货币。
- “根据核心大小” - 提供每个核心大小的定价（美元）。 Microsoft 计算定价，并使用当前汇率将其转换为本地货币。
- “根据市场和核心大小” - 为所有市场提供每个核心大小的定价。 可以从电子表格导入价格。

输入“每个核心的价格”，然后选择“每个核心大小的价格”以查看按小时计算的价格表。

> [!NOTE]
> 保存定价更改，实现定价数据的导出。 发布计划中某个市场的价格后，无法再更改。 若要确保价格发布之前是正确的，请导出定价电子表格，并查看每个市场中的价格。

### <a name="free-trial"></a>免费试用版

可以为客户提供一个月、三个月或六个月的“免费试用版”。

### <a name="plan-visibility"></a>计划可见性

可以设计每个计划，使其对每个人可见，或仅对预选受众可见。 使用 Azure 订阅 ID 在此受限受众中分配成员身份。

**公共**：所有人都可以查看计划。

**专用**：仅对预选受众可以查看计划。 将其发布为专用计划后，可以更新受众或将其更改为公共。 使计划为公众所见后，该计划必须保持公共状态。 不能将其更改回专用计划。

使用“Azure 订阅 ID”分配将有权访问此专用计划的受众。 根据需要，为已分配的每个 Azure 订阅 ID 提供“说明”。 手动添加最多 10 个订阅 ID，或如果导入 CSV 电子表格添加最多 20,000 个 ID。 Azure 订阅 ID 表示为 GUID，所有字母必须为小写。

> [!NOTE]
> 专用或受限受众与在“预览”窗格上定义的预览受众不同。 预览受众可以在向 Azure 市场实时发布产品/服务之前来访问它。 尽管专用受众选项仅适用于特定计划，但预览受众可以查看所有专用或公共计划以进行验证。

通过云解决方案提供商计划 (CSP) 的经销商建立的 Azure 订阅不支持专用产品/服务。

### <a name="hide-plan"></a>隐藏计划

如果虚拟机仅在通过另一个解决方案模板或托管应用程序引用时才被间接使用，请选中此复选框以发布虚拟机，但向可能直接搜索或浏览的客户隐藏它。

任何 Azure 客户都可以使用 PowerShell 或 CLI 部署该产品/服务。  如果希望向有限的一组客户提供此产品/服务，请将计划设置为“专用”。 

隐藏的计划不会生成预览链接。 不过，你可以通过[按以下步骤操作](azure-vm-create-faq.yml#how-do-i-test-a-hidden-preview-image-)来测试它们。 

选择“保存草稿”，然后继续转到左侧导航计划菜单的下一个选项卡“技术配置”。

## <a name="technical-configuration"></a>技术配置

提供与此计划关联的映像和其他技术属性。

### <a name="reuse-technical-configuration"></a>重复使用技术配置

如果有多个相同类型的计划，并且它们用的包完全相同，请选择“此计划重复使用其他计划中的技术配置”。 使用此选项，可以为此产品/服务选择相同类型的其他计划，并重复使用其技术配置。

### <a name="operating-system"></a>操作系统

选择“Windows”或“Linux”操作系统系列。

选择 Windows“版本”或 Linux“供应商”。

输入操作系统的“OS 友好名称”。 此名称对客户可见。

### <a name="recommended-vm-sizes"></a>建议的 VM 大小

选择链接，以选择在 Azure 市场上显示的最多六个建议的虚拟机大小。

### <a name="open-ports"></a>打开端口

在部署的虚拟机上添加开放的公共或专用端口。

### <a name="properties"></a>属性

下面是可以为 VM 选择的属性列表。

- 支持备份：如果映像支持 Azure VM 备份，请启用此属性。 详细了解 [Azure VM 备份](../backup/backup-azure-vms-introduction.md)。

- 支持加速网络：如果此计划的 VM 映像支持对 VM 的单根 I/O 虚拟化 (SR-IOV)，则启用此属性，从而在网络接口上实现低延迟和高吞吐量。 详细了解[加速网络](https://go.microsoft.com/fwlink/?linkid=2124513)。

- 支持 cloud-init 配置：如果此计划中的映像支持 cloud-init 后期部署脚本，则启用此属性。 详细了解 [cloud-init 配置](../virtual-machines/linux/using-cloud-init.md)。

- 支持热补丁：Windows Server Azure 版本支持热补丁。 详细了解[热补丁](../automanage/automanage-hotpatch.md)。

- 支持扩展：如果该计划中的映像支持扩展，则启用此属性。 扩展是小型应用程序，用于在 Azure VM 上提供部署后配置和自动化。 详细了解 [Azure 虚拟机扩展](./azure-vm-create-certification-faq.yml#vm-extensions)。

- 是网络虚拟设备：如果该产品是网络虚拟设备，则启用此属性。 网络虚拟设备是执行一个或多个网络功能的产品，例如负载均衡器、VPN 网关、防火墙或应用程序网关。 详细了解[网络虚拟设备](https://go.microsoft.com/fwlink/?linkid=2155373)。

- 远程桌面或 SSH 已禁用：如果使用这些映像部署的虚拟机不允许客户使用远程桌面或 SSH 访问它，请启用此属性。 详细了解[锁定的 VM 镜像](./azure-vm-create-certification-faq.yml#locked-down-or-ssh-disabled-offer)。

- 需要自定义 ARM 模板进行部署：如果该计划中的映像只能使用自定义 ARM 模板进行部署，则启用此属性。 若要了解更多信息，请参阅[排查虚拟机认证问题中的自定义模板部分](./azure-vm-create-certification-faq.yml#custom-templates)。

### <a name="generations"></a>代数

生成虚拟机将定义它使用的虚拟硬件。 根据客户的需要，你可以发布第 1 代 VM、第 2 代 VM 或两者。

1. 创建新产品/服务时，选择一种“生成类型”并输入所需的详细信息：

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-1.png" alt-text="合作伙伴中心中代数详细信息部分的视图。":::

2. 若要向计划添加另一代，请选择“添加代数”...

    :::image type="content" source="./media/create-vm/azure-vm-generations-add.png" alt-text="“添加代数”链接的视图。":::

    ...并输入所需的详细信息：

    :::image type="content" source="./media/create-vm/azure-vm-generations-image-details-3.png" alt-text="代数详细信息窗口的视图。":::

<!--    The **Generation ID** you choose will be visible to customers in places such as product URLs and ARM templates (if applicable). Use only lowercase, alphanumeric characters, dashes, or underscores; it cannot be modified once published.
-->
3. 若要更新已发布第 1 代的现有 VM，请在“技术配置”页上编辑详细信息。

若要详细了解第 1 代和第 2 代功能之间的差异，请参阅 [Azure 上的第 2 代 VM 支持](../virtual-machines/generation-2.md)。

### <a name="vm-images"></a>VM 映像

为虚拟机映像提供磁盘版本和共享访问签名 (SAS) URI。 为每个 VM 映像添加多达 16 个数据磁盘。 在指定的提交中，每个计划仅提供一个新的映像版本。 映像发布后，无法对其进行编辑，但可以将其删除。 删除版本会阻止新用户和现有用户部署已删除版本的新实例。

上图显示了两个必填字段：

- “磁盘版本”：正在提供的映像的版本。
- **OS VHD 链接**：存储在 Azure 共享映像库中的映像。 了解如何在[共享映像库](azure-vm-create-using-approved-base.md#capture-image)中捕获映像。

数据磁盘（选择“添加数据磁盘（最多 16 个）”）也是存储在其 Azure 存储帐户中的 VHD 共享访问签名 URI。 计划中的每次提交只添加一个映像。

无论使用何种操作系统，都仅添加解决方案所需的最少数据磁盘数。 客户无法在部署时删除映像中包含的磁盘，但始终可以在部署期间或之后添加磁盘。

> [!NOTE]
> 如果使用 SAS 提供映像并拥有数据磁盘，则还需要将它们作为 SAS URI 提供。 如果使用共享映像，它们将作为共享映像库中映像的一部分被捕获。 将产品/服务发布到 Azure 市场后，可以从 Azure 存储或共享映像库中删除映像。

选择“保存草稿”，然后选择左上方的“←计划概述”以查看刚创建的计划。

VM 映像发布后，可以从 Azure 存储中删除映像。

## <a name="next-steps"></a>后续步骤

- [通过 CSP 转售](azure-vm-create-resell-csp.md)