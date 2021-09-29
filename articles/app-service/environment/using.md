---
title: 使用应用服务环境
description: 了解如何使用应用服务环境来托管独立的应用程序。
author: ccompy
ms.assetid: 377fce0b-7dea-474a-b64b-7fbe78380554
ms.topic: article
ms.date: 07/06/2021
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 150e5d0e0d7360bcf9d4f42038e3ff49366d80a8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124767881"
---
# <a name="using-an-app-service-environment"></a>使用应用服务环境

> [!NOTE]
> 本文介绍与独立 v2 应用服务计划一起使用的应用服务环境 v3
> 

应用服务环境 (ASE) 是直接注入到你所选的 Azure 虚拟网络 (VNet) 中的 Azure 应用服务的单一租户部署。 它是一种仅由一位客户使用的系统。 部署到 ASE 中的应用受应用于 ASE 子网的网络功能的影响。 在这些网络功能的影响下，无需在应用中启用任何额外的功能。 

## <a name="create-an-app-in-an-ase"></a>在 ASE 中创建应用

在 ASE 中创建应用的过程与一般情况下创建应用的过程大致相同，只存在几处细微的差别。 创建新的应用服务计划时：

- 不要选择某个地理位置来部署应用，而应该选择 ASE 作为位置。
- 在 ASE 中创建的所有应用服务计划只能位于独立 v2 定价层中。

如果没有 ASE，可以根据[创建应用服务环境][MakeASE]中的说明创建一个。
在 ASE 中创建应用：

1. 选择“创建资源” > “Web + 移动” > “Web 应用”。
1. 选择一个订阅。
1. 输入新资源组的名称，或选择“使用现有”并从下拉列表中选择一个资源组。
1. 输入应用程序的名称。 如果已在 ASE 中选择了应用服务计划，则应用的域名会反映 ASE 的域名：![在 ASE 中创建应用][1]
1. 选择发布类型、堆栈和操作系统。
1. 选择区域。 此处，需要选择预先存在的应用服务环境 v3。  在应用创建过程中无法创建 ASEv3 
1. 在你的 ASE 中选择一个现有的应用服务计划，或创建新的计划。 如果要创建新应用，请选择应用服务计划所需的大小。 可为应用选择的唯一 SKU 是一个独立 v2 定价 SKU。 制定新的应用服务计划通常耗时不到 20 分钟。 
![独立 v2 定价层][2]
1. 选择“下一步: 监视”。如果想要对应用启用 App Insights，可在创建流期间在此处执行此操作。 
1.  选择“下一步: 标记”。向应用添加所需的任何标记  
1. 选择“查看 + 创建”，确保信息正确，然后选择“创建”。 

Windows 和 Linux 应用可以存在于同一 ASE 中，但不能存在于同一应用服务计划中。

## <a name="how-scale-works"></a>缩放的工作原理

每个应用服务应用在应用服务计划中运行。 应用服务环境保存应用服务计划，应用服务计划保存应用。 缩放某个应用时，也会缩放应用服务计划，以及同一计划中的所有应用。

缩放应用服务计划时，会自动添加所需的基础结构。 添加基础结构时，缩放操作存在一定的时间延迟。 缩放应用服务计划时，请求的任何相同 OS 和大小的其他缩放操作都将等待，直到第一个操作完成。 阻塞的缩放操作完成后，将同时处理所有排队的请求。 针对某个大小和 OS 的缩放操作不会阻止阻止其他大小和 OS 组合的缩放。 例如，如果你缩放 Windows I2v2 应用服务计划，则在该 ASE 中缩放 Windows I2v2 的任何其他请求将排队，直到该操作完成。 缩放通常耗时不到 20 分钟。 

在多租户应用服务中，缩放是即时发生的，因为有现成可用的资源池用于支持。 ASE 中没有此类缓冲区，而会根据需要分配资源。

## <a name="app-access"></a>应用访问

在使用内部 VIP 的 ASE 中，用于创建应用的域后缀是 .&lt;asename&gt;.appserviceenvironment.net。 如果 ASE 名为 my-ase 并且其中托管了名为 contoso 的应用，可通过以下 URL 访问该应用 ：

- contoso.my-ase.appserviceenvironment.net
- contoso.scm.my-ase.appserviceenvironment.net

只有在与 ASE 位于同一虚拟网络中或以某种方式连接到该虚拟网络时，才能访问使用内部 VIP 的 ASE 上托管的应用。 也只有在位于同一虚拟网络中或以某种方式连接到该虚拟网络时，才可能进行发布。 

在具有外部VIP 的 ASE 中，用于创建应用的域后缀是 .&lt;asename&gt;.p.azurewebsites.net。 如果 ASE 名为 my-ase 并且其中托管了名为 contoso 的应用，可通过以下 URL 访问该应用 ：

- contoso.my-ase.p.azurewebsites.net
- contoso.scm.my-ase.p.azurewebsites.net

有关如何创建 ASE 的信息，请参阅[创建应用服务环境][MakeASE]。

SCM URL 用于访问 Kudu 控制台，也可用于通过 Web 部署发布应用。 有关 Kudu 控制台的信息，请参阅 [Azure 应用服务的 Kudu 控制台][Kudu]。 Kudu 控制台提供 Web UI，可在其中进行调试、上传文件、 编辑文件及执行其他许多操作。

### <a name="dns-configuration"></a>DNS 配置 

如果 ASE 是使用外部 VIP 创建的，则应用将自动置于公共 DNS 中。 如果 ASE 是使用内部 VIP 创建的，则你可能需要为它配置 DNS。 如果你已选择在创建 ASE 期间自动配置 Azure DNS 专用区域，则会在 ASE VNet 中配置 DNS。 如果你已选择“手动配置 DNS”，则需要使用自己的 DNS 服务器或配置Azure DNS专用区域。 若要查找 ASE 的入站地址，请转到“ASE 门户”>“IP 地址”UI。 

![IP 地址 UI][6]

如果要使用自己的 DNS 服务器，则需要添加以下记录：

1. 为 &lt;ASE 名称&gt;.appserviceenvironment.net 创建一个区域
1. 在该区域中，创建一条将 * 指向 ASE 所用入站 IP 地址的 A 记录
1. 在该区域中，创建一条将 @ 指向 ASE 所用入站 IP 地址的 A 记录
1. 在 &lt;ASE 名称&gt;.appserviceenvironment.net 中创建名为 scm 的区域
1. 在 scm 区域中创建 A 记录，该记录将 * 指向 ASE 使用的 IP 地址

在 Azure DNS 专用区域中配置 DNS：

1. 创建名为“&lt;ASE 名称&gt;.appserviceenvironment.net”的 Azure DNS 专用区域
1. 在该区域中，创建一条将 * 指向入站 IP 地址的 A 记录
1. 在该区域中，创建一条将 @ 指向入站 IP 地址的 A 记录
1. 在该区域中，创建一条将 *.scm 指向入站 IP 地址的 A 记录

ASE 默认域后缀的 DNS 设置不会将你的应用限制为只能由这些名称访问。 可在 ASE 中设置自定义域名而无需对应用进行任何验证。 如果随后想要创建名为 contoso.net 的区域，可执行此操作并将其指向入站 IP 地址。 自定义域名适用于应用请求，但不适用于 scm 站点。 scm 站点仅在 &lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net 中可用。 

## <a name="publishing"></a>发布

与多租户应用服务一样，在 ASE 中，可以使用以下方法发布应用：

- Web 部署
- 持续集成 (CI)
- 在 Kudu 控制台中拖放
- Visual Studio、Eclipse 或 IntelliJ IDEA 等 IDE

使用内部 VIP ASE 时，发布终结点只有通过入站地址才可用。 如果没有对入站地址的网络访问权限，则无法在该 ASE 上发布任何应用。  IDE 还必须拥有对 ASE 上入站地址的网络访问权限，然后才能直接向其进行发布。

在不进行额外更改的情况下，基于 Internet 的 CI 系统（例如 GitHub 和 Azure DevOps）不支持内部 VIP ASE，因为发布终结点不可通过 Internet 进行访问。 可以通过在包含 ASE 的虚拟网络中安装自承载发布代理，来实现从 Azure DevOps 发布到内部 VIP ASE。 

## <a name="storage"></a>存储

ASE 为其中的所有应用提供 1 TB 存储空间。 “独立”定价 SKU 中的一个应用服务计划限制为 250 GB。 在 ASE 中，每个应用服务计划增加了 250 GB 的存储空间，最大限制为 1 TB。 你可以有四个以上的应用服务计划，但没有超过 1 TB 限制的额外存储空间。

## <a name="logging"></a>日志记录

可将 ASE 与 Azure Monitor 相集成，以将有关 ASE 的日志发送到 Azure 存储、Azure 事件中心或 Log Analytics。 当前会记录以下项：

|场景 |Message |
|----------|--------|
|ASE 子网空间几乎已耗尽 | 指定的 ASE 位于一个几乎耗尽了空间的子网中。 还剩下 {0} 个地址。 一旦这些地址耗尽，ASE 就无法缩放。  |
|ASE 即将达到实例总数限制 | 指定的 ASE 即将达到 ASE 的实例总数限制。 它当前包含 {0} 个应用服务计划实例，最多有 200个实例。 |
|ASE 已挂起 | 指定的 ASE 已挂起。 ASE 挂起的可能原因是帐户不足，或虚拟网络配置无效。 解决根本原因并恢复 ASE，以继续为流量提供服务。 |
|ASE 升级已启动 | 已开始对指定的 ASE 进行平台升级。 预期缩放操作会出现延迟。 |
|ASE 升级已完成 | 对指定的 ASE 进行平台升级已完成。 |
|应用服务计划创建已开始 | 应用服务计划 ({0}) 创建已开始。 期望状态：{1} I{2}v2 辅助角色。
|缩放操作已完成 | 应用服务计划 ({0}) 创建已结束。 当前状态：{1} I{2}v2 辅助角色。 |
|缩放操作失败 | 应用服务计划 ({0}) 创建已失败。 这可能是由于 ASE 在实例数最高时运行，或者子网地址不足。 |
|缩放操作已启动 | 应用服务计划 ({0}) 已开始缩放。 当前状态：{1} I(2)v2。 期望状态：{3} I{4}v2 辅助角色。|
|缩放操作已完成 | 应用服务计划 ({0}) 已完成缩放。 当前状态：{1} I{2}v2 辅助角色。 |
|缩放操作已中断 | 缩放时应用服务计划 ({0}) 中断。 先前期望状态：{1} I{2}v2 辅助角色。 新的期望状态：{3} I{4}v2 辅助角色。 |
|缩放操作失败 | 应用服务计划 ({0}) 无法缩放。 当前状态：{1} I{2}v2 辅助角色。 |

若要在 ASE 中启用日志记录：

1. 在门户中转到“诊断设置”。
1. 选择“添加诊断设置”。
1. 提供日志集成的名称。
1. 选择并配置所需的日志目标。
1. 选择“AppServiceEnvironmentPlatformLogs”。
![ASE 诊断日志设置][4]

如果与 Log Analytics 集成，可通过以下方式查看日志：在 ASE 门户中选择“日志”，并针对“AppServiceEnvironmentPlatformLogs”创建查询。  仅当 ASE 具有将触发日志的事件时，才发出日志。 如果 ASE 没有此类事件，则不会有任何日志。 若要快速查看 Log Analytics 工作区中的日志示例，请使用 ASE 中的应用服务计划执行缩放操作。 然后，你可以针对 AppServiceEnvironmentPlatformLogs 运行查询来查看这些日志。 

### <a name="creating-an-alert"></a>创建警报

若要针对日志创建警报，请按[使用 Azure Monitor 创建、查看和管理日志警报](../../azure-monitor/alerts/alerts-log.md)中的说明操作。 简单地说：

* 在 ASE 门户中打开“警报”页面
* 选择“新建警报规则”
* 选择资源作为 Log Analytics 工作区
* 使用自定义日志搜索设置条件，以使用类似于“AppServiceEnvironmentPlatformLogs”的查询，其中，ResultDescription 包含“已开始缩放”或任何所需的内容。 根据需要设置阈值。 
* 根据需要添加或创建操作组。 你可以在操作组中定义对警报的响应，如发送电子邮件或短信
* 为警报命名并保存。

## <a name="internal-encryption"></a>内部加密

应用服务环境作为一个黑框系统运行，你将看不到系统中的内部组件或通信。 为了实现更高的吞吐量，默认情况下，在内部组件之间不启用加密。 系统很安全，因为流量无法访问，不管你是要监视流量还是要访问流量。 如果你的符合性要求必须从端到端对数据路径进行完全加密，则可在 ASE 的配置 UI中启用此功能。

![启用内部加密][5]

这会对前端和辅助角色之间的 ASE 中的内部网络流量进行加密，还会对页面文件和辅助角色磁盘进行加密。 启用 InternalEncryption clusterSetting 后，可能会影响系统性能。 进行更改以启用 InternalEncryption 后，ASE 会处于不稳定状态，直到更改传播完毕。 更改的传播可能需要几个小时才能完成，具体取决于 ASE 中有多少实例。 强烈建议不要在它仍处于使用状态的情况下在 ASE 上启用它。 如果需要对主动使用的 ASE 启用此操作，强烈建议将流量转移到备份环境，直到操作完成。

## <a name="upgrade-preference"></a>升级首选项

如果你有多个 ASE，你可能希望先升级某些 ASE，再升级其他 ASE。 可通过 ASE 门户启用此行为。  在“配置”下，可选择设置“升级首选项”。 三个可能的值为：

- **无**：Azure 将在非特定的批次中升级 ASE。 此值为默认值。
- **Early**：ASE 将在应用服务升级过程的上半阶段升级。
- **Late**：ASE 将在应用服务升级过程的下半阶段升级。

选择所需值，然后选择“保存”。  任何 ASE 的默认值均为“无”。

![ASE 配置门户][5]

upgradePreferences 功能在有多个 ASE 时最有意义，因为“早期”ASE 将在“晚期”ASE 之前升级。 当你有多个 ASE 时，应将开发和测试 ASE 设置为“早期”，而将生产 ASE 设置为“晚期”。

## <a name="delete-an-ase"></a>删除 ASE

若要删除 ASE，请执行以下操作：

1. 选择“应用服务环境”窗格顶部的“删除”。 
1. 输入 ASE 的名称，确认想要将它删除。 删除 ASE 时，会同时删除它包含的所有内容。
![ASE 删除][3]
1. 选择“确定”。

## <a name="pricing"></a>定价 

使用 ASEv3 时，有一种不同的定价模式，具体取决于你的 ASE 部署类型。 三种定价模式如下： 

- ASEv3：如果 ASE 是空的，则收费方式如同你有一个包含 Windows I1v2 实例的 ASP。 一个实例的费用并非附加性费用，这笔费用只会在 ASE 为空时应用。
- 可用性区域 ASEv3：至少有 9 笔 Windows I1v2 实例费用。 如果有 9 个或更多个应用服务计划实例，则可用性区域支持不收取额外的费用。 此外，AZ ASEv3 中的所有应用服务计划的最小实例计数为 3，以确保每个可用性区域中都有一个实例。 随着计划的横向扩展，这些计划将分散到各个可用性区域。 
- 专用主机 ASEv3：使用专用主机部署时，你需要按照创建 ASEv3 时的定价支付两个专用主机的费用，并在缩放时按照较小百分比的“独立 V2”费率支付每个核心的费用。

已提供独立 v2 的预留实例定价，并在[如何将预留折扣应用于 Azure 应用服务][reservedinstances]中进行了介绍。 [应用服务定价][pricing]的“独立 v2 计划”下提供了定价和预留实例定价。 

<!--Image references-->

[1]: ./media/using/using-appcreate.png
[2]: ./media/using/using-appcreate-skus.png
[3]: ./media/using/using-delete.png
[4]: ./media/using/using-logsetup.png
[4]: ./media/using/using-logs.png
[5]: ./media/using/using-configuration.png
[6]: ./media/using/using-ip-addresses.png

<!--Links-->

[Intro]: ./overview.md
[MakeASE]: ./creation.md
[ASENetwork]: ./networking.md
[UsingASE]: ./using.md
[UDRs]: ../../virtual-network/virtual-networks-udr-overview.md
[NSGs]: ../../virtual-network/network-security-groups-overview.md
[Pricing]: https://azure.microsoft.com/pricing/details/app-service/
[ARMOverview]: ../../azure-resource-manager/management/overview.md
[ConfigureSSL]: ../configure-ssl-certificate.md
[Kudu]: https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[AppDeploy]: ../deploy-local-git.md
[ASEWAF]: app-service-app-service-environment-web-application-firewall.md
[AppGW]: ../../web-application-firewall/ag/ag-overview.md
[logalerts]: ../../azure-monitor/alerts/alerts-log.md
[reservedinstances]: ../../cost-management-billing/reservations/reservation-discount-app-service.md#how-reservation-discounts-apply-to-isolated-v2-instances
[pricing]: https://azure.microsoft.com/pricing/details/app-service/windows/