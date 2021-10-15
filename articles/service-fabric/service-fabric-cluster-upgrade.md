---
title: 升级 Azure Service Fabric 群集
description: 了解用于更新 Azure Service Fabric 群集的选项
ms.topic: conceptual
ms.date: 03/26/2021
ms.openlocfilehash: 7fad28e0357b26b0b8753496310c7788f6dbb0c8
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401141"
---
# <a name="upgrading-and-updating-azure-service-fabric-clusters"></a>升级和更新 Azure Service Fabric 群集

Azure Service Fabric 群集是你拥有的但部分由 Microsoft 管理的资源。 本文介绍有关何时以及如何更新 Azure Service Fabric 群集的选项。

## <a name="automatic-versus-manual-upgrades"></a>自动与手动升级

确保 Service Fabric 群集始终运行[支持的运行时版本](service-fabric-versions.md)至关重要。 每次 Microsoft 宣布发布新版 Service Fabric 即标志着自该日期起至少 60 天以后结束对旧版本的支持。 新版本将[在 Service Fabric 团队博客](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)中宣布。

群集运行的版本过期前十四天，系统会生成运行状况事件，使群集进入警告运行状况状态。 在升级到支持的运行时版本之前，群集将保持警告状态。

可以将群集设置为在 Microsoft 发布自动 Service Fabric 升级时接收这些升级，也可以从当前支持的版本列表中手动进行选择。 Service Fabric 群集资源的“结构升级”部分中提供了这些选项。

:::image type="content" source="./media/service-fabric-cluster-upgrade/fabric-upgrade-mode.png" alt-text="在 Azure 门户中群集资源的“结构升级”部分选择自动升级或手动升级。":::

还可以设置群集升级模式，并[使用资源管理器模板](service-fabric-cluster-upgrade-version-azure.md#resource-manager-template)选择运行时版本。

自动升级是建议升级模式，因为此选项确保可群集保持受支持状态，并受益于最新修补程序和功能，同时还使你可以使用[波形部署](#wave-deployment-for-automatic-upgrades)策略以对工作负载中断最小的方法计划更新。

> [!NOTE]
> 如果将现有群集更改为自动模式，则该群集将在新版本开始的下一个升级阶段中注册。 新版本将[在 Service Fabric 团队博客](https://techcommunity.microsoft.com/t5/azure-service-fabric/bg-p/Service-Fabric)中宣布。 每个升级期间，尽可能选择最高升级路径，请参阅[支持的版本](service-fabric-versions.md)。 手动升级模式触发立即升级。 

## <a name="wave-deployment-for-automatic-upgrades"></a>自动升级的波形部署

使用波形部署时，可以根据工作负载，选择升级的成熟度来最大程度地减少升级对群集造成的中断。 例如，可以为各种 Service Fabric 群集设置测试 -> 暂存 -> 生产波形部署管道，以便在将运行时升级应用于生产工作负载之前测试其兼容性。

若要选择波形部署，请为群集指定以下波形值之一（在其部署模板中）：

* 波形 0：发布新的 Service Fabric 版本后，立即更新群集。 用于测试/开发群集。
* 波形 1：在发布新版本一周（七天）后更新群集。 用于预生产/暂存群集。
* 波形 2：在发布新版本两周（14 天）后更新群集。 用于生产群集。

如果群集升级失败，可以借助进一步帮助的链接注册电子邮件通知。 请参阅[自动升级的 Wave 部署](service-fabric-cluster-upgrade-version-azure.md#wave-deployment-for-automatic-upgrades)以便开始。

## <a name="phases-of-automatic-upgrade"></a>自动升级的阶段

Microsoft 将维护 Azure 群集中运行的 Service Fabric 运行时代码和配置。 我们根据需要，自动对软件执行受监视的升级。 升级的部分可能是代码和/或配置。 若要最大程度减小这些升级对应用程序的影响，请按以下阶段执行它们：

### <a name="phase-1-an-upgrade-is-performed-by-using-all-cluster-health-policies"></a>阶段 1：使用所有群集运行状况策略执行升级

在此阶段，升级过程将每次升级一个升级域，已在群集中运行的应用程序继续运行，而不会造成任何停机时间。 升级过程中遵守群集运行状况策略（适用于节点运行状况和应用程序运行状况）。

如果不符合现行的群集运行状况策略，则回退升级，并将电子邮件发送给订阅的所有者。 电子邮件中包含以下信息：

* 有关必须回滚群集升级的通知。
* 建议的补救措施（如果有）。
* 距离执行阶段 2 的天数 (*n*)。

如果有任何升级因为基础结构方面的原因而失败，我们将尝试多次执行同一升级。 自电子邮件发送日期的 n 天之后，我们将继续执行阶段 2。

如果符合群集运行状况策略，则升级被视为成功并标记为完成。 在此阶段进行初始升级或重新运行任何升级期间，可能发生这种情形。 不会对成功运行进行电子邮件确认，以避免发送过多的电子邮件。 收到电子邮件指示正常操作出现异常。 大多数群集升级预期都会成功，且不影响应用程序可用性。

### <a name="phase-2-an-upgrade-is-performed-by-using-default-health-policies-only"></a>阶段 2：仅使用默认运行状况策略执行升级

在此阶段设置好运行状况策略，以便在升级开始时运行正常的应用程序数目在升级过程中保持不变。 与阶段 1 一样，阶段 2 升级过程将每次升级一个升级域，已在群集中运行的应用程序继续运行，而不会造成任何停机时间。 在升级期间，将遵守群集运行状况策略（节点运行状况和所有在群集中运行的应用程序的运行状况的组合）。

如果不符合现行的群集运行状况策略，则回滚升级。 然后，系统会向订阅所有者发送一封电子邮件。 电子邮件中包含以下信息：

* 有关必须回滚群集升级的通知。
* 建议的补救措施（如果有）。
* 距离执行阶段 3 的天数 (n)。

如果有任何升级因为基础结构方面的原因而失败，我们将尝试多次执行同一升级。 将在 n 天结束前的几天发送提醒电子邮件。 自电子邮件发送日期的 n 天之后，我们将继续执行阶段 3。 必须认真看待阶段 2 发送的电子邮件并采取补救措施。

如果符合群集运行状况策略，则升级被视为成功并标记为完成。 在此阶段进行初始升级或重新运行任何升级期间，可能发生这种情形。 如果运行成功，将不发送任何电子邮件确认。

### <a name="phase-3-an-upgrade-is-performed-by-using-aggressive-health-policies"></a>阶段 3：使用积极的群集运行状况策略执行升级

此阶段中的这些运行状况策略旨在升级完成，而不反映应用程序的运行状况。 很少有群集升级在此阶段结束。 如果群集进入此阶段，则表示应用程序有可能变得不正常和/或失去可用性。

类似于另外两个阶段，阶段 3 每次升级一个升级域。

如果不符合现行的群集运行状况策略，则回滚升级。 如果有任何升级因为基础结构方面的原因而失败，我们将尝试多次执行同一升级。 之后，便会锁定群集，使它不再接收支持和/或升级。

系统会将包含此信息以及补救措施的电子邮件发送给订阅所有者。 预期不会有任何群集遇到阶段 3 失败的状况。

如果符合群集运行状况策略，则升级被视为成功并标记为完成。 在此阶段进行初始升级或重新运行任何升级期间，可能发生这种情形。 如果运行成功，将不发送任何电子邮件确认。

## <a name="custom-policies-for-manual-upgrades"></a>手动升级的自定义策略

可为手动群集升级指定自定义策略。 每次选择新的运行时版本时，都会应用这些策略，从而触发系统启动群集升级。 如果未重写这些策略，则会使用默认设置。 有关详细信息，请参阅[为手动升级设置自定义策略](service-fabric-cluster-upgrade-version-azure.md#custom-policies-for-manual-upgrades)。

## <a name="other-cluster-updates"></a>其他群集更新

除了升级运行时之外，可能还需要执行一些其他操作才能使群集保持最新状态，包括以下各项：

### <a name="managing-certificates"></a>管理证书

Service Fabric 使用创建群集时指定的 [X.509 服务器证书](service-fabric-cluster-security.md)，以保护群集节点之间的通信并对客户端进行身份验证。 可以在 [Azure 门户](https://portal.azure.com) 中添加、更新或删除群集和客户端的证书，也可以使用 PowerShell/Azure CLI 完成这些操作。  若要了解详细信息，请参阅[添加或删除证书](service-fabric-cluster-security-update-certs-azure.md)

### <a name="opening-application-ports"></a>打开应用程序端口

可以通过更改与节点类型关联的负载均衡器资源属性来更改应用程序端口。 可以使用 Azure 门户，也可以使用 PowerShell/Azure CLI。 有关详细信息，请参阅[打开群集的应用程序端口](create-load-balancer-rule.md)。

### <a name="defining-node-properties"></a>定义节点属性

有时，可能需要确保仅在群集中特定类型的节点上运行某些工作负荷。 例如，某些工作负荷可能需要 GPU 或 SSD，而有些则不用。 对于群集中的每个节点类型，可以向群集节点添加自定义节点属性。 放置约束是附加到单个服务的语句，这些服务专供 1 个或多个节点属性选择。 放置约束定义服务运行的位置。

有关使用放置约束、节点属性以及如何定义它们的详细信息，请参阅[节点属性和放置约束](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)。

### <a name="adding-capacity-metrics"></a>添加容量指标

对于每个节点类型，可以添加要在应用程序中用于报告负载的自定义容量度量值。 有关使用容量指标来报告负载的详细信息，请参阅 Service Fabric 群集 Resource Manager 文档[描述群集](service-fabric-cluster-resource-manager-cluster-description.md)，以及[指标和负载](service-fabric-cluster-resource-manager-metrics.md)。

### <a name="customizing-settings-for-your-cluster"></a>自定义群集的设置

可以在群集上自定义许多不同的配置设置，例如群集的可靠性级别和节点属性。 有关详细信息，请参阅 [Service Fabric 群集结构设置](service-fabric-cluster-fabric-settings.md)。

### <a name="upgrading-os-images-for-cluster-nodes"></a>为群集节点升级 OS 映像

最佳做法是为 Service Fabric 群集节点启用自动 OS 映像升级。 若要执行该操作，需要实现几个群集要求和步骤。 另一个选项是使用修补业务流程应用程序 (POA)，这是一个 Service Fabric 应用程序，可在 Service Fabric 群集中自动修补操作系统，而无需停机。 若要了解有关这些选项的详细信息，请参阅[在 Service Fabric 群集中修补 Windows 操作系统](how-to-patch-cluster-nodes-windows.md)。

## <a name="next-steps"></a>后续步骤

* [管理 Service Fabric 升级](service-fabric-cluster-upgrade-version-azure.md)
* 自定义 [Service Fabric 群集设置](service-fabric-cluster-fabric-settings.md)
* [扩展和缩减群集](service-fabric-cluster-scale-in-out.md)
* 了解[应用程序升级](service-fabric-application-upgrade.md)
