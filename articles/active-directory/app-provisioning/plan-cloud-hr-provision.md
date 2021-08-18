---
title: 计划云 HR 应用程序到 Azure Active Directory 的用户预配
description: 本文介绍将云 HR 系统（如 Workday 和 SuccessFactors）与 Azure Active Directory 集成的部署过程。 将 Azure AD 与云 HR 系统集成会生成一个完整的标识生命周期管理系统。
services: active-directory
author: kenwith
manager: mtillman
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: conceptual
ms.workload: identity
ms.date: 07/13/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 12b909b467b7f53d568e80f60c8b30f701820216
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113728509"
---
# <a name="plan-cloud-hr-application-to-azure-active-directory-user-provisioning"></a>计划云 HR 应用程序到 Azure Active Directory 的用户预配

过去，IT 人员一直依靠手动方法来创建、更新和删除员工。 他们使用上传 CSV 文件或自定义脚本等方法来同步员工数据。 这些预配过程容易出错、不安全且难以管理。

为了管理员工、供应商或临时工作人员的标识生命周期，[Azure Active Directory (Azure AD) 用户预配服务](../app-provisioning/user-provisioning.md)提供了与基于云的人力资源 (HR) 应用程序的集成。 这些应用程序包括 Workday 或 SuccessFactors。

Azure AD 使用此集成来启用以下云 HR 应用程序（应用）工作流：

- **将用户预配到 Active Directory：** 将选定的用户集从云 HR 应用预配到一个或多个 Active Directory 域中。
- **将仅限云的用户预配到 Azure AD：** 在未使用 Active Directory 的情况下，将用户直接从云 HR 应用预配到 Azure AD。
- **写回云 HR 应用：** 将电子邮件地址和用户名属性从 Azure AD 写回云 HR 应用。

> [!NOTE]
> 此部署计划显示了如何使用 Azure AD 用户预配来部署云 HR 应用工作流。 有关如何将自动用户预配部署到软件即服务 (SaaS) 应用的信息，请参阅[计划自动用户预配部署](./plan-auto-user-provisioning.md)。

## <a name="enabled-hr-scenarios"></a>支持的 HR 场景

Azure AD 用户预配服务可实现以下基于 HR 的标识生命周期管理方案的自动化：

- **招聘新员工：** 将新员工添加到云 HR 应用后，会在 Active Directory 和 Azure AD 中自动创建用户帐户，并可以选择将电子邮件地址和用户名属性写回云 HR 应用。
- **员工属性和个人资料更新：** 在云 HR 应用中更新员工记录（如姓名、职称或上司）后，Active Directory 和 Azure AD 中会自动更新相应员工的用户帐户。
- **员工离职：** 当员工在云 HR 中为离职状态时，Active Directory 和 Azure AD 中会自动禁用相应员工的用户帐户。
- **员工重新雇用：** 当员工在云 HR 中的状态为重新雇用时，其旧帐户可以自动重新激活或重新预配到 Active Directory 和 Azure AD。

## <a name="who-is-this-integration-best-suited-for"></a>谁最适合使用此集成？

云 HR 应用与 Azure AD 用户预配的集成非常适合满足以下条件的组织：

- 想要一个基于云的预构建解决方案，以进行云 HR 用户预配。
- 要求直接将用户从云 HR 应用预配到 Active Directory 或 Azure AD。
- 要求使用从云 HR 应用获取的数据预配用户。
- 要求仅基于云 HR 应用中检测到的更改信息，将加入、移动和离开的用户同步到一个或多个 Active Directory 林、域和 OU。
- 将 Microsoft 365 用于电子邮件。

## <a name="learn"></a>Learn

用户预配为正在进行的标识管理创建基础。 它增强了依赖于权威标识数据的业务进程的质量。

### <a name="terms"></a>术语

本文使用了以下术语：

- “源系统”： Azure AD 预配的用户的存储库。 例如，诸如 Workday 或 SuccessFactors 等云 HR 应用。
- “目标系统”： Azure AD 预配的用户的存储库。 例如 Active Directory、Azure AD、Microsoft 365 或其他 SaaS 应用程序。
- “Joiners-Movers-Leavers 进程”：用于雇佣新员工、调动和离职的一种术语，使用云 HR 应用作为记录系统。 当服务成功地将所需的属性预配到目标系统时，该进程完成。

### <a name="key-benefits"></a>主要优点

HR 驱动的 IT 预配的这一功能提供了以下重要的业务优势：

- **提高工作效率：** 现在你可以自动分配用户帐户和 Microsoft 365 许可证，并提供对密钥组的访问权限。 自动分配使新员工可以立即访问其作业工具并提高工作效率。
- **管理风险：** 可以使用从云 HR 应用流入的数据，根据员工状态或组成员身份自动进行更改，从而提高安全性。 自动化更改可确保在用户转岗或从组织离职时，用户标识以及用户对关键应用的访问权限会自动更新。
- **解决合规性和管理问题：** Azure AD 支持由源系统和目标系统的应用执行的用户预配请求的原生审核日志。 通过审核，可以在单个屏幕上跟踪谁有权访问该应用。
- **管理成本：** 自动预配可以避免与手动预配相关的低效和人为错误，从而降低成本。 不再需要像使用旧版和过时的平台一样需要随时构建自定义开发的用户预配解决方案。

### <a name="licensing"></a>许可

若要配置云 HR 应用到 Azure AD 用户预配集成，需要一个有效的 [Azure AD Premium 许可证](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing)和一个云 HR 应用的许可证，如 Workday 或 SuccessFactors。

还需要一个有效的 Azure AD Premium P1 或更高版本的订阅许可证，适用于将从 云 HR 应用获得并预配到Active Directory 或 Azure AD 中的每个用户。 云 HR 应用中拥有的任何不正确的许可证数可能会导致用户预配时出现错误。

### <a name="prerequisites"></a>先决条件

- Azure AD [混合标识管理员](../roles/permissions-reference.md#hybrid-identity-administrator)配置 Azure AD Connect 预配代理。
- Azure AD [应用管理员](../roles/permissions-reference.md#application-administrator)角色在 Azure 门户中配置预配应用
- 云 HR 应用的测试和生产实例。
- 云 HR 应用中拥有管理员权限，可创建系统集成用户，并可做出更改以便出于测试目的测试员工数据。
- 要将用户预配到 Active Directory，需要运行 Windows Server 2016 或更高版本的服务器，才能托管 Azure AD Connect 预配代理。 此服务器应该是基于 Active Directory 管理层模型的第 0 层服务器。
- [Azure AD Connect](../hybrid/whatis-azure-ad-connect.md)，用于在 Active Directory 与 Azure AD 之间同步用户。

### <a name="training-resources"></a>培训资源

| **资源** | **链接和说明** |
|:-|:-|
| 视频 | [Active Azure Directory 中的用户预配是什么？](https://youtu.be/_ZjARPpI6NI) |
| | [如何在 Active Azure Directory 中部署用户预配](https://youtu.be/pKzyts6kfrw) |
| 教程 | [有关如何将 SaaS 应用程序与 Azure AD 集成的教程列表](../saas-apps/tutorial-list.md) |
| | [教程：针对自动用户预配来配置 Workday](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |
| 常见问题解答 | [自动用户预配](../app-provisioning/user-provisioning.md#what-applications-and-systems-can-i-use-with-azure-ad-automatic-user-provisioning) |
| | [从 Workday 到 Azure AD 的预配](../saas-apps/workday-inbound-tutorial.md#frequently-asked-questions-faq) |

### <a name="solution-architecture"></a>解决方案体系结构

以下示例介绍了适用于常见混合环境的端到端用户预配解决方案体系结构，并且包括：

- “从云 HR 应用到 Active Directory 的权威 HR 数据流。” 在此流中，HR 事件（Joiners-Movers-Leavers 进程）在云 HR 应用租户中启动。 Azure AD 预配服务和 Azure AD Connect 预配代理将用户数据从云 HR 应用租户预配到 Active Directory 中。 根据事件，可能会导致在 Active Directory 中创建、更新、启用和禁用操作。
- “与 Azure AD 同步，并将电子邮件和用户名从本地 Active Directory 写回云 HR 应用。” 在 Active Directory 中更新帐户后，它将通过 Azure AD Connect 与 Azure AD 同步。 将电子邮件地址和用户名属性写回云 HR 应用租户。

![工作流关系图](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img1.png)

#### <a name="description-of-workflow"></a>工作流的说明

图中指示了以下关键步骤：  

1. “HR团队”在云 HR 应用租户中执行事务。
2. “Azure AD 预配服务”运行来自云 HR 应用租户的计划周期，并标识需要处理以便与 Active Directory 同步的更改。
3. “Azure AD 预配服务”使用包含 Active Directory 帐户创建、更新、启用和禁用操作的请求有效负载来调用 Azure AD Connect 预配代理。
4. “Azure AD Connect 预配代理”使用服务帐户管理 Active Directory 帐户数据。
5. “Azure AD Connect”运行增量[同步](../hybrid/how-to-connect-sync-whatis.md)以获取 Active Directory 中的更新。
6. “Active Directory”更新与 Azure AD 同步。
7. “Azure AD 预配服务”将电子邮件属性和用户名从 Azure AD 写回云 HR 应用租户。

## <a name="plan-the-deployment-project"></a>规划部署项目

在环境中确定此部署的策略时，请考虑组织的需求。

### <a name="engage-the-right-stakeholders"></a>让合适的利益干系人参与

如果技术项目失败，它们通常是由于在影响、结果和责任方面不符合预期而导致的。 为了避免这些缺陷，请[确保让合适的利益干系人参与](../fundamentals/active-directory-deployment-plans.md)。 此外，请确保对项目中的利益干系人角色非常熟悉。 记录利益干系人及其项目输入和责任。

包括 HR 组织的代表，其可以提供现有 HR 业务流程的输入和工作人员标识以及作业数据处理要求。

### <a name="plan-communications"></a>规划沟通

通信对于任何新服务的成功至关重要。 主动与用户交流其体验发生改变的时间和方式。 如果遇到问题，请让他们知道如何获得支持。

### <a name="plan-a-pilot"></a>规划试点

将 HR 业务流程和标识工作流从云 HR 应用集成到目标系统需要进行大量的数据验证、数据转换、数据清理和端到端测试，然后才能将解决方案部署到生产中。

请先在[试点环境](../fundamentals/active-directory-deployment-plans.md#best-practices-for-a-pilot)中运行初始配置，然后再将其扩展到生产中的所有用户。

## <a name="select-cloud-hr-provisioning-connector-apps"></a>选择云 HR 预配连接器应用

为了便于云 HR 应用与 Active Directory 之间的 Azure AD 预配工作流，可从 Azure AD 应用库添加多个预配连接器应用：

- “云 HR 应用到 Active Directory 用户预配”：此预配连接器应用可便于从云 HR 应用到单个 Active Directory 域的用户帐户预配。 如果你有多个域，可以针对需要预配到的每个 Active Directory 域，从 Azure AD 应用库添加此应用的一个实例。
- “云 HR 应用到 Azure AD 用户预配”-尽管 Azure AD Connect 是用于将 Active Directory 用户同步到 Azure AD 的工具，但是此预配连接器应用可用于帮助将仅限云的用户从云 HR 应用预配到单个 Azure AD 租户。
- “云 HR 应用回写”：此预配连接器应用便于将用户电子邮件地址从 Azure AD 回写到云 HR 应用。

例如，下图列出了 Azure AD 应用库中可用的 Workday 连接器应用。

![Azure Active Directory 门户应用库](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img2.png)

### <a name="decision-flow-chart"></a>决策流程图

使用下面的决策流程图来确定哪些云 HR 预配应用与你的方案相关。

![决策流程图](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img3.png)

## <a name="design-the-azure-ad-connect-provisioning-agent-deployment-topology"></a>设计 Azure AD Connect 预配代理部署拓扑

云 HR 应用和 Active Directory 之间的预配集成需要四个组件：

- 云 HR 应用租户
- 预配连接器应用
- Azure AD Connect 预配代理
- Active Directory 域

Azure AD Connect 预配代理部署拓扑取决于你计划集成的云 HR 应用租户和 Active Directory 子域的数量。 如果有多个 Active Directory 域，它取决于 Active Directory 域是连续的还是[非连续](/windows-server/identity/ad-ds/plan/disjoint-namespace)的。

根据决策，选择以下部署方案之一：

- 单个云 HR 应用租户 -> 受信任林中的目标单个或多个 Active Directory 子域
- 单个云 HR 应用租户 -> 非连续 Active Directory 林中的目标多个子域

### <a name="single-cloud-hr-app-tenant---target-single-or-multiple-active-directory-child-domains-in-a-trusted-forest"></a>单个云 HR 应用租户 -> 受信任林中的目标单个或多个 Active Directory 子域

建议采用以下生产配置：

|要求|建议|
|:-|:-|
|要部署的 Azure AD Connect 预配代理数|2个（用于高可用性和故障转移）
|要配置的预配连接器应用数|每个子域一个应用|
|用于 Azure AD Connect 预配代理的服务器主机|Windows Server 2016，可以视线分析地理定位的 Active Directory 域控制器</br>可以与 Azure AD Connect 服务共存|

![到本地代理的流](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img4.png)

### <a name="single-cloud-hr-app-tenant---target-multiple-child-domains-in-a-disjoint-active-directory-forest"></a>单个云 HR 应用租户 -> 非连续 Active Directory 林中的目标多个子域

此方案涉及将用户从云 HR 应用预配到非连续的 Active Directory 林中的域。

建议采用以下生产配置：

|要求|建议|
|:-|:-|
|要在本地部署的 Azure AD Connect 预配代理数|每个非连续 Active Directory 林部署2个|
|要配置的预配连接器应用数|每个子域一个应用|
|用于 Azure AD Connect 预配代理的服务器主机|Windows Server 2016，可以视线分析地理定位的 Active Directory 域控制器</br>可以与 Azure AD Connect 服务共存|

![单个云 HR 应用租户非连续 Active Directory 林](media/plan-cloud-hr-provision/plan-cloudhr-provisioning-img5.png)

### <a name="azure-ad-connect-provisioning-agent-requirements"></a>Azure AD Connect 预配代理要求

云 HR 应用到 Active Directory 用户预配解决方案要求在运行 Windows Server 2016 或更高版本的服务器上部署一个或多个 Azure AD Connect 预配代理。 服务器必须至少具有 4-GB RAM 和 .NET 4.7.1 + 运行时。 确保主机服务器具有对目标 Active Directory 域的网络访问权限。

为准备本地环境，Azure AD Connect 预配代理配置向导将代理注册到 Azure AD 租户， [打开端口](../app-proxy/application-proxy-add-on-premises-application.md#open-ports)，[允许访问 URL](../app-proxy/application-proxy-add-on-premises-application.md#allow-access-to-urls)，并支持[出站 HTTPS 代理配置](../saas-apps/workday-inbound-tutorial.md#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)。

预配代理会配置 [全局托管服务帐户 (GMSA)](../cloud-sync/how-to-prerequisites.md#group-managed-service-accounts)，以与 Active Directory 域通信。 若要使用非 GMSA 服务帐户进行预配，可以[跳过 GMSA 配置](../cloud-sync/how-to-manage-registry-options.md#skip-gmsa-configuration)，在配置过程中指定服务帐户。 

可以选择应处理预配请求的域控制器。 如果有多个地理上分散的域控制器，请将预配代理安装在与首选域控制器相同的站点中。 此定位提高端到端解决方案的可靠性和性能。

为实现高可用性，可以部署多个 Azure AD Connect 预配代理。 注册代理以处理同一组本地 Active Directory 域。

## <a name="design-hr-provisioning-app-deployment-topology"></a>设计 HR 预配应用部署拓扑

根据入站用户预配配置中涉及的 Active Directory 域数，你可以考虑以下其中一种部署拓扑。 每个拓扑图都使用示例部署方案来突出显示配置相关方面。 使用与部署要求非常相似的示例来确定可满足需求的配置。 

### <a name="deployment-topology-1-single-app-to-provision-all-users-from-cloud-hr-to-single-on-premises-active-directory-domain"></a>部署拓扑 1：通过单个应用将云 HR 中的所有用户预配到单个本地 Active Directory 域

这是最常见的部署拓扑。 如果需要将云 HR 中的所有用户预配到单个 AD 域，并让所有用户应用相同的预配规则，可以使用此拓扑。 

:::image type="content" source="media/plan-cloud-hr-provision/topology-1-single-app-with-single-ad-domain.png" alt-text="通过单个应用将 Cloud HR 中的用户预配到单个 AD 域的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-1-single-app-with-single-ad-domain.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的预配代理节点。 
* 使用[预配代理配置向导](../cloud-sync/how-to-install.md#install-the-agent)将 AD 域注册到 Azure AD 租户。 
* 配置预配应用时，从已注册域的下拉列表中选择 AD 域。 
* 若使用范围筛选器，请配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 

### <a name="deployment-topology-2-separate-apps-to-provision-distinct-user-sets-from-cloud-hr-to-single-on-premises-active-directory-domain"></a>部署拓扑 2：通过不同应用将云 HR 中的不同用户集预配到单一本地 Active Directory 域

此拓扑支持满足业务要求，其中属性映射和预配逻辑会因用户类型（员工/合同工）、用户所在位置或用户所属业务部门而存在差异。 借助此拓扑，你还可以根据部门或国家/地区委托入站用户预配的管理与维护工作。

:::image type="content" source="media/plan-cloud-hr-provision/topology-2-separate-apps-with-single-ad-domain.png" alt-text="通过不同应用将云 HR 中的用户预配到单一 AD 域的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-2-separate-apps-with-single-ad-domain.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的预配代理节点。 
* 为要预配的每一个不同用户集创建 HR2AD 预配应用。 
* 使用预配应用中的[范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)来定义要由每个应用处理的用户。 
* 若要处理需要跨不同用户集（例如，向作为员工的经理报告的合同工）解析管理员引用的情况，可以创建单独的 HR2AD 预配应用，以仅更新 manager 属性。 将此应用的范围设置为所有用户。 
* 配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 

> [!NOTE] 
> 若没有测试用 AD 域且使用 AD 中的 TEST OU 容器，可以使用此拓扑创建两个单独的应用“HR2AD (Prod)”和“HR2AD (Test)”。 先使用 HR2AD (Test) 应用测试属性映射更改，然后再将属性映射更改提升到 HR2AD (Prod) 应用。  

### <a name="deployment-topology-3-separate-apps-to-provision-distinct-user-sets-from-cloud-hr-to-multiple-on-premises-active-directory-domains-no-cross-domain-visibility"></a>部署拓扑 3：通过不同应用将云 HR 中的不同用户集预配到多个本地 Active Directory 域（不具跨域可见性）

如果管理员始终与用户存在于同一域中，并且不需要从林中查找 userPrincipalName、samAccountName 和 mail   等属性的唯一 ID 生成规则，可以使用此拓扑可以管理属于同一林的多个独立子 AD 域。 借助此拓扑，你还可以按域边界灵活委派每项预配作业的管理工作。 

例如：下图显示的即为针对每个地理区域（北美 (NA)、欧洲、中东和非洲 (EMEA) 以及亚太 (APAC)）设置预配应用。 根据不同位置，用户将被预配到相应的 AD 域。 预配应用的管理工作是可以委派的，如此 EMEA 管理员便可独立管理 EMEA 区域内用户的预配配置。  

:::image type="content" source="media/plan-cloud-hr-provision/topology-3-separate-apps-with-multiple-ad-domains-no-cross-domain.png" alt-text="通过不同应用将云 HR 中的用户预配到多个 AD 域的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-3-separate-apps-with-multiple-ad-domains-no-cross-domain.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的预配代理节点。 
* 使用[预配代理配置向导](../cloud-sync/how-to-install.md#install-the-agent)将所有子 AD 域注册到 Azure AD 租户。 
* 为每个目标域创建单独的 HR2AD 预配应用。 
* 配置预配应用时，从可用 AD 域的下拉列表中选择相应的子 AD 域。 
* 使用预配应用中的[范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)来定义要由每个应用处理的用户。 
* 配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 


### <a name="deployment-topology-4-separate-apps-to-provision-distinct-user-sets-from-cloud-hr-to-multiple-on-premises-active-directory-domains-with-cross-domain-visibility"></a>部署拓扑 4：通过不同应用将云 HR 中的不同用户集预配到多个本地 Active Directory 域（具有跨域可见性）

如果用户的管理员可能存在于不同的域中，并且需要从林中查找 userPrincipalName、samAccountName 和 mail   等属性的唯一 ID 生成规则，可以使用此拓扑可以管理属于同一林的多个独立子 AD 域。 

例如：下图显示的即为针对每个地理区域（北美 (NA)、欧洲、中东和非洲 (EMEA) 以及亚太 (APAC)）设置预配应用。 根据不同位置，用户将被预配到相应的 AD 域。 启用预配代理上的引用跟踪，即可处理跨域管理员引用及从林中查找。 

:::image type="content" source="media/plan-cloud-hr-provision/topology-4-separate-apps-with-multiple-ad-domains-cross-domain.png" alt-text="通过不同应用将云 HR 中的用户预配到多个 AD 域（支持跨域）的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-4-separate-apps-with-multiple-ad-domains-cross-domain.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的预配代理节点。 
* 在预配代理上配置[引用跟踪](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)。 
* 使用[预配代理配置向导](../cloud-sync/how-to-install.md#install-the-agent)将父 AD 域及所有子 AD 域注册到 Azure AD 租户。 
* 为每个目标域创建单独的 HR2AD 预配应用。 
* 配置每个预配应用时，从可用 AD 域的下拉列表中选择父 AD 域。 这可以确保在为 userPrincipalName、samAccountName 和 mail   等属性生成唯一值时，执行从林中查找。
* 使用 parentDistinguishedName 与表达式映射在适当的子域和 [OU 容器](#configure-active-directory-ou-container-assignment)中动态创建用户。 
* 使用预配应用中的[范围筛选器](define-conditional-rules-for-provisioning-user-accounts.md)来定义要由每个应用处理的用户。 
* 若要解析跨域管理员引用，可以创建单独的 HR2AD 预配应用，以仅更新 manager 属性。 将此应用的范围设置为所有用户。 
* 配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 

### <a name="deployment-topology-5-single-app-to-provision-all-users-from-cloud-hr-to-multiple-on-premises-active-directory-domains-with-cross-domain-visibility"></a>部署拓扑 5：通过单个应用将云 HR 中的所有用户预配到多个本地 Active Directory 域（具有跨域可见性）

若要使用单个预配应用来管理属于所有父 AD 域和子 AD 域的用户，可以使用此拓扑。 如果预配规则在所有域间保持一致，并且不需要针对预配作业实施管理委派，则建议采用此拓扑。 此拓扑支持解析跨域管理员引用，并可执行从林中检查唯一性。 

例如：下图显示的即为，使用单个预配应用管理三个不同子域中的用户（按区域分组：北美 (NA)、欧洲、中东和非洲 (EMEA) 以及亚太 (APAC)）。 parentDistinguishedName 的属性映射将用于在相应子域中动态创建用户。 启用预配代理上的引用跟踪，即可处理跨域管理员引用及从林中查找。 

:::image type="content" source="media/plan-cloud-hr-provision/topology-5-single-app-with-multiple-ad-domains-cross-domain.png" alt-text="通过单个应用将云 HR 中的用户预配到多个 AD 域（支持跨域）的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-5-single-app-with-multiple-ad-domains-cross-domain.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的预配代理节点。 
* 在预配代理上配置[引用跟踪](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)。 
* 使用[预配代理配置向导](../cloud-sync/how-to-install.md#install-the-agent)将父 AD 域及所有子 AD 域注册到 Azure AD 租户。 
* 为整个林创建单个 HR2AD 预配应用。 
* 配置预配应用时，从可用 AD 域的下拉列表中选择父 AD 域。 这可以确保在为 userPrincipalName、samAccountName 和 mail   等属性生成唯一值时，执行从林中查找。
* 使用 parentDistinguishedName 与表达式映射在适当的子域和 [OU 容器](#configure-active-directory-ou-container-assignment)中动态创建用户。 
* 若使用范围筛选器，请配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 

### <a name="deployment-topology-6-separate-apps-to-provision-distinct-users-from-cloud-hr-to-disconnected-on-premises-active-directory-forests"></a>部署拓扑 6：通过不同应用将云 HR 中的不同用户预配到已断开连接的本地 Active Directory 林

若你的 IT 基础结构包含断开连接/不相接的 AD 林，并且你需要根据业务附属关系将用户预配到不同的林，可以使用此拓扑。 举例来说：需要将为子公司 Contoso 工作的用户预配到 contoso.com 域中，以及将为子公司 Fabrikam 工作的用户预配到 fabrikam.com 域中。 

:::image type="content" source="media/plan-cloud-hr-provision/topology-6-separate-apps-with-disconnected-ad-forests.png" alt-text="通过不同应用将云 HR 中的用户预配到已断开连接的 AD 林的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-6-separate-apps-with-disconnected-ad-forests.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的不同预配代理集（每个林一个集）。 
* 创建两个不同的预配应用（每个林一个应用）。 
* 若需要解析林中的跨域引用，请在预配代理上启用[引用跟踪](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)。 
* 为每个断开连接的林创建单独的 HR2AD 预配应用。 
* 配置每个预配应用时，从可用 AD 域名的下拉列表中选择适当的父 AD 域。 
* 配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 

### <a name="deployment-topology-7-separate-apps-to-provision-distinct-users-from-multiple-cloud-hr-to-disconnected-on-premises-active-directory-forests"></a>部署拓扑 7：通过不同应用将多个云 HR 中的不同用户预配到已断开连接的本地 Active Directory 林

大型组织普遍会建立多个 HR 系统。 在企业并购（合并和收购）方案中，你可能需要将本地 Active Directory 连接至多个 HR 源。 如果你有多个 HR 源，并且希望将这些 HR 源中的标识数据传输至相同或不同的本地 Active Directory 域，建议采用下方拓扑。  

:::image type="content" source="media/plan-cloud-hr-provision/topology-7-separate-apps-from-multiple-hr-to-disconnected-ad-forests.png" alt-text="通过不同应用将多个云 HR 中的用户预配到已断开连接的 AD 林的屏幕截图" lightbox="media/plan-cloud-hr-provision/topology-7-separate-apps-from-multiple-hr-to-disconnected-ad-forests.png":::

最重要的配置方面
* 设置两个适用高可用性和故障转移的不同预配代理集（每个林一个集）。 
* 若需要解析林中的跨域引用，请在预配代理上启用[引用跟踪](../cloud-sync/how-to-manage-registry-options.md#configure-referral-chasing)。 
* 为每个 HR 系统和本地 Active Directory 组合创建单独的 HR2AD 预配应用。
* 配置每个预配应用时，从可用 AD 域名的下拉列表中选择适当的父 AD 域。 
* 配置[跳过范围外删除标志](skip-out-of-scope-deletions.md)以防止系统意外停用帐户。 

## <a name="plan-scoping-filters-and-attribute-mapping"></a>规划范围筛选器和属性映射

当你启用从云 HR 应用到 Active Directory 或 Azure AD 的预配时，Azure 门户通过属性映射来控制属性值。

### <a name="define-scoping-filters"></a>定义范围筛选器

使用[范围筛选器](../app-provisioning/define-conditional-rules-for-provisioning-user-accounts.md)定义基于属性的规则，这些规则确定哪些用户应该从云 HR 应用到 Active Directory 或 Azure AD 进行预配。

启动 Joiners 进程时，请收集以下要求：

- 云 HR 应用是否用于使员工和临时工都可以使用？
- 你是否打算使用云 HR 应用到 Azure AD 用户预配来管理员工和临时工？
- 你是否打算仅为云 HR 应用用户的子集推出云 HR 应用到 Azure AD 用户预配？ 例如，可能只是员工。

根据你的要求，在配置属性映射时，可以设置“源对象范围”字段以选择云 HR 应用中的哪些用户集应在预配到 Active Directory 的范围内。 有关详细信息，请参阅适用于常用范围筛选器的云 HR 应用教程。

### <a name="determine-matching-attributes"></a>确定匹配属性

利用预配，可以匹配源系统和目标系统之间的现有帐户。 当将云 HR 应用与 Azure AD 预配服务集成时，可以[配置属性映射](../app-provisioning/configure-automatic-user-provisioning-portal.md#mappings)以确定应当从云 HR 应用流向 Active Directory 或 Azure AD 的用户数据。

启动 Joiners 进程时，请收集以下要求：

- 此云 HR 应用中用于标识每个用户的唯一 ID 是什么？
- 从标识生命周期角度看，如何处理重新雇佣的员工？ 重新雇佣的员工是否保留其旧的员工 ID？
- 你是否会提前处理日后招聘的员工并提前为他们创建 Active Directory 帐户？
- 从标识生命周期角度来看，如何处理员工到临时工的转换？
- 转换后的用户是保留旧的 Active Directory 帐户还是获取新帐户？

根据您的要求，Azure AD 通过提供常量值或[为属性映射编写表达式](../app-provisioning/functions-for-customizing-application-data.md)支持直接的属性到属性映射。 这种灵活性使你可以全面控制在目标应用属性中填充的内容。 可以使用 [Microsoft Graph API](../app-provisioning/export-import-provisioning-configuration.md) 和 Graph Explorer 将用户预配属性映射和架构导出到 JSON 文件并将其导回到 Azure AD 中。

默认情况下，代表唯一员工 ID 的云 HR 应用中的属性用作“映射到 Active Directory 中的唯一属性”的匹配属性。 例如，在 Workday 应用方案中，“workday”“WorkerID”属性映射到 Active Directory“雇员 ID”属性。  

可以设置多个匹配属性并分配匹配优先级。 它们按匹配优先级来评估。 一旦找到匹配，就不会进一步评估其他匹配属性。

还可以[自定义默认属性映射](../app-provisioning/customize-application-attributes.md#understanding-attribute-mapping-types)，例如更改或删除现有属性映射。 还可以根据业务需求创建新的属性映射。 有关详细信息，请参阅云 HR 应用教程（例如 [Workday](../saas-apps/workday-inbound-tutorial.md#managing-your-configuration)），获取要映射的自定义属性列表。

### <a name="determine-user-account-status"></a>确定用户帐户状态

默认情况下，预配连接器应用会将 HR 用户配置文件状态映射到 Active Directory 或 Azure AD 中的用户帐户状态，以确定是要启用还是禁用该用户帐户。

启动 Joiners-Leavers 进程时，请收集以下要求。

| 进程 | 要求 |
| - | - |
| **Joiners** | 从标识生命周期角度看，如何处理重新雇佣的员工？ 重新雇佣的员工是否保留其旧的员工 ID？ |
| | 你是否会提前处理日后招聘的员工并提前为他们创建 Active Directory 帐户？ 这些帐户是被创建为启用还是禁用状态？ |
| | 从标识生命周期角度来看，如何处理员工到临时工的转换？ |
| | 转换后的用户是保留旧的 Active Directory 帐户，还是获取新帐户？ |
| **Leavers** | Active Directory 中的员工和临时工作人员的离职处理方式是否不同？ |
| | 处理用户离职会考虑哪些生效日期？ |
| | 员工和临时工作人员转换如何影响现有 Active Directory 帐户？ |
| | 如何处理 Active Directory 中的 Rescind 操作？ 如果在 Active Directory 中创建将来的招聘作为 Joiner 进程的一部分，则需要处理 Rescind 操作。 |

根据你的要求，可以使用 [Azure AD 表达式](../app-provisioning/functions-for-customizing-application-data.md)自定义映射逻辑，以便基于数据点的组合启用或禁用 Active Directory 帐户。

### <a name="map-cloud-hr-app-to-active-directory-user-attributes"></a>将云 HR 应用映射到 Active Directory 用户属性

每个云 HR 应用都附带了默认的云 HR 应用到 Active Directory 映射。

启动 Joiners-Movers-Leavers 进程时，请收集以下要求。

| 进程 | 要求 |
| - | - |
| **Joiners** | Active Directory 帐户创建过程是手动、自动还是部分自动？ |
| | 你是否打算将自定义属性从云 HR 应用传播到 Active Directory？ |
| **Movers** | 当 Movers 操作在云 HR 应用中发生时，你想要处理哪些属性？ |
| | 在用户更新时是否执行任何特定属性验证？ 如果是，请提供详细信息。 |
| **Leavers** | Active Directory 中的员工和临时工作人员的离职处理方式是否不同？ |
| | 处理用户离职会考虑哪些生效日期？ |
| | 员工和临时工作人员转换如何影响现有 Active Directory 帐户？ |

根据你的要求，你可以修改映射以满足你的集成目标。 有关详细信息，请参阅云 HR 应用教程（例如 [Workday](../saas-apps/workday-inbound-tutorial.md#part-4-configure-attribute-mappings)），获取要映射的自定义属性列表。

### <a name="generate-a-unique-attribute-value"></a>生成唯一属性值

当启动 Joiners 进程时，你可能需要在设置具有唯一约束的 CN、samAccountName 和 UPN 等属性时生成唯一属性值。

Azure AD 函数 [SelectUniqueValues](../app-provisioning/functions-for-customizing-application-data.md#selectuniquevalue) 评估每个规则，然后检查为目标系统中的唯一性生成的值。 例如，请参阅[为 userPrincipalName (UPN) 属性生成唯一值](../app-provisioning/functions-for-customizing-application-data.md#generate-unique-value-for-userprincipalname-upn-attribute)。

> [!NOTE]
> 目前只有“Workday 到 Azure Active Directory 的用户预配”和“SAP SuccessFactors 到 Active Directory 的用户预配”支持使用此函数。 它不能与其他预配应用一起使用。

### <a name="configure-active-directory-ou-container-assignment"></a>配置 Active Directory OU 容器分配

将 Active Directory 用户帐户放置到基于业务单元、位置和部门的容器中是一个常见的要求。 启动 Movers 进程时，如果存在监管组织更改，则可能需要在 Active Directory 中将用户从一个 OU 移动到另一个 OU。

使用 [Switch()](../app-provisioning/functions-for-customizing-application-data.md#switch) 函数配置 OU 分配的业务逻辑，并将其映射到 Active Directory 属性“parentDistinguishedName”。

例如，如果要基于 HR 属性“城市”在 OU 中创建用户，可以使用以下表达式：

`
Switch([Municipality], "OU=Default,OU=Users,DC=contoso,DC=com", "Dallas", "OU=Dallas,OU=Users,DC=contoso,DC=com", "Austin", "OU=Austin,OU=Users,DC=contoso,DC=com", "Seattle", "OU=Seattle,OU=Users,DC=contoso,DC=com", "London", "OU=London,OU=Users,DC=contoso,DC=com")
`

在此表达式中，如果“城市”值为达拉斯、奥斯汀、西雅图或伦敦，则将在相应的 OU 中创建用户帐户。 如果没有匹配项，则会在默认 OU 中创建帐户。

## <a name="plan-for-password-delivery-of-new-user-accounts"></a>规划新用户帐户的密码发送

启动 Joiners 进程时，需要设置和发送新用户帐户的临时密码。 利用云 HR 到 Azure AD 用户预配，可以在第一天向用户推出 Azure AD [自助式密码重置](../authentication/tutorial-enable-sspr.md) (SSPR) 功能。

SSPR 是 IT 管理员允许用户重置其密码或解锁其帐户的简单方式。 可以从云 HR 应用到 Active Directory 预配“移动电话号码”属性，以便并将其与 Azure AD 同步。 在“移动电话号码”属性在 Azure AD 中之后，可以为用户的帐户启用 SSPR。 新用户在第一天可以使用已注册和已验证的移动电话号码进行身份验证。 请参阅 [SSPR 文档](../authentication/howto-sspr-authenticationdata.md)，详细了解如何预先填充身份验证联系信息。 

## <a name="plan-for-initial-cycle"></a>规划初始周期

首次运行 Azure AD 预配服务时，它会对云 HR 应用执行[初始周期](../app-provisioning/how-provisioning-works.md#initial-cycle)，以创建云 HR 应用中所有用户对象的快照。 初始周期所用的时间直接取决于源系统中有多少用户。 使用超过 100000 个用户的某些云 HR 应用租户的初始周期可能需要较长时间。

“对于大型云 HR 应用租户（> 30000 用户），”在进行阶段运行初始周期。 仅在验证针对不同用户预配方案在 Active Directory 中设置正确属性后，才启动增量更新。 遵循此处的顺序。

1. 通过设置[范围筛选器](#plan-scoping-filters-and-attribute-mapping)，仅为一组有限的用户运行初始周期。
2. 验证 Active Directory 帐户预配以及为首次运行选择的用户设置的属性值。 如果结果满足您的预期，请展开范围筛选器以逐步包含更多用户，并验证第二次运行的结果。

对测试用户的初始周期的结果感到满意后，开始[增量更新](../app-provisioning/how-provisioning-works.md#incremental-cycles)。

## <a name="plan-testing-and-security"></a>规划测试和安全性

在从初始试点到启用用户预配的部署的每个阶段，，确保测试结果是否符合预期并审核预配周期。

### <a name="plan-testing"></a>规划测试

在配置云 HR 应用到 Azure AD 用户预配后，运行测试用例以验证此解决方案是否满足组织的要求。

|方案|预期结果|
|:-|:-|
|在云 HR 应用中雇用新员工。| -在 Active Directory 中预配用户帐户。</br>-用户可以登录 Active Directory 域应用并执行所需的操作。</br>-如果已配置 Azure AD Connect 同步，则还会在 Azure AD 中创建用户帐户。
|用户在云 HR 应用中离职。|-在 Active Directory 中禁用用户帐户。</br>-用户无法登录到受 Active Directory 保护的任何企业应用。
|用户监管组织在云 HR 应用中更新。|根据属性映射，用户帐户在 Active Directory 中从一个 OU 移动到另一个 OU。|
|HR 更新云 HR 应用中用户的经理。|Active Directory 中的经理字段被更新以反映新经理的姓名。|
|HR 将员工重新雇佣为新角色。|行为取决于云 HR 应用的配置方式，以生成员工 ID：</br>-如果为重新雇佣的员工重复使用旧的员工 ID，则连接器将为用户启用现有的 Active Directory 帐户。</br>-如果重新雇佣的员工获取新的员工 ID，则连接器为用户创建新的 Active Directory 帐户。|
|HR 将员工转换为合同工作人员，反之亦然。|为新角色创建新的 Active Directory 帐户，并在转换生效日期禁用旧帐户。|

使用以前的结果来确定如何根据已建立的时间线将自动用户预配实现过渡到生产。

> [!TIP]
> 使用生产数据刷新测试环境时，使用数据缩减和数据清理等技术来删除或掩盖敏感的个人数据，以遵守隐私和安全标准。 

### <a name="plan-security"></a>规划安全性

在部署新服务的过程中，通常需要安全评审。 如果需要安全评审或未执行安全评审，请参阅许多 Azure AD [白皮书](https://www.microsoft.com/download/details.aspx?id=36391)，这些白皮书提供标识即服务的概述。

### <a name="plan-rollback"></a>规划回滚

云 HR 用户预配实现在生产环境中可能无法正常工作。 如果是这样，以下回滚步骤可以帮助您恢复到以前已知的良好状态。

1. 查看[预配摘要报告](../app-provisioning/check-status-user-account-provisioning.md#getting-provisioning-reports-from-the-azure-portal)和[预配日志](../app-provisioning/check-status-user-account-provisioning.md#provisioning-logs-preview)，以确定对受影响的用户或组执行了哪些不正确的操作。 有关预配摘要报告和日志的详细信息，请参阅[管理云 HR 应用用户预配](#manage-your-configuration)。
2. 受影响的用户或组的上一个已知良好状态可以通过预配审核日志来确定，也可以通过查看目标系统 (Azure AD 或 Active Directory) 来确定。
3. 通过使用上一个已知良好状态值，与应用所有者合作来更新直接受影响的用户或组。

## <a name="deploy-the-cloud-hr-app"></a>部署云 HR 应用

选择符合解决方案要求的云 HR 应用。

“Workday”：若要将工作人员个人资料从 Workday 导入 Active Directory 和 Azure AD，请参阅[教程：为自动用户预配配置 Workday](../saas-apps/workday-inbound-tutorial.md#planning-your-deployment)。 任选地，也可以将电子邮件地址、用户名和电话号码写回 Workday。

“SAP SuccessFactors”：若要将工作人员个人资料从 SuccessFactors 导入 Active Directory 和 Azure AD，请参阅[教程：为自动用户预配配置 SAP SuccessFactors](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md)。 任选地，也可以将电子邮件地址和用户名写回 SuccessFactors。

## <a name="manage-your-configuration"></a>管理你的配置

Azure AD 可以通过审核日志和报表，进一步深入了解你的组织的用户预配使用情况和运行状况。

### <a name="gain-insights-from-reports-and-logs"></a>从报表和日志获取见解

在成功完成[初始周期](../app-provisioning/how-provisioning-works.md#initial-cycle)后，Azure AD 预配服务将继续按特定于每个应用的教程中定义的时间间隔无限期地运行连续的增量更新，直到发生以下事件之一：

- 已手动停止此服务。 通过使用 [Azure 门户](https://portal.azure.com/)或适当的的 [Microsoft Graph API](/graph/api/resources/synchronization-overview) 命令触发了新的初始周期。
- 由于属性映射或范围筛选器发生更改而触发了新的初始周期。
- 预配进程由于高错误率而进入隔离状态。 它保持隔离超过四周，随后自动禁用。

若要查看这些事件以及由预配服务执行的所有其他活动，请 [了解如何查看日志并获取有关预配活动的报告](../app-provisioning/check-status-user-account-provisioning.md)。

#### <a name="azure-monitor-logs"></a>Azure Monitor 日志

Azure AD 审核日志中记录了用户预配服务执行的所有操作。 可以将 Azure AD 审核日志路由至 Azure Monitor 日志以进行进一步分析。 通过 Azure Monitor 日志（也称为 Log Analytics 工作区），可以查询数据以查找事件、分析趋势，并执行各种数据源间的关联。 观看该[视频](https://youtu.be/MP5IaCTwkQg)，以了解在实际用户方案中将 Azure Monitor 日志用于 Azure AD 日志的优点。

安装[用于 Azure AD 活动日志的 Log Analytics 视图](../reports-monitoring/howto-install-use-log-analytics-views.md)，以访问有关环境中关于预配事件的[预建报表](https://github.com/AzureAD/Deployment-Plans/tree/master/Log%20Analytics%20Views)。

有关详细信息，请参阅如何[分析 Azure Monitor 日志中的 Azure AD 活动日志](../reports-monitoring/howto-analyze-activity-logs-log-analytics.md)。

### <a name="manage-personal-data"></a>管理个人数据

Windows server 上安装的 Azure AD Connect 预配代理在 Windows 事件日志中创建日志，该日志可能包含个人数据，具体取决于你的云 HR 应用到 Active Directory 属性映射。 若要遵守用户隐私义务，请设置 Windows 计划任务以清除事件日志，并确保不会将任何数据保留超过48小时。

Azure AD 预配服务不会在超过30天后生成报表、执行分析，或提供见解，因为服务不会存储、处理或保留超过30天的任何数据。

### <a name="troubleshoot"></a>疑难解答

若要解决在预配期间可能会出现的任何问题，请参阅以下文章：

- [为 Azure AD 库应用程序配置用户预配时遇到的问题](application-provisioning-config-problem.md)
- [将特性从本地 Active Directory 同步到 Azure AD 以预配到应用程序](user-provisioning-sync-attributes-for-mapping.md)
- [配置 Azure Active Directory 库应用程序的用户预配时遇到保存管理员凭据问题](./user-provisioning.md)
- [没有为任何用户预配 Azure AD 库应用程序](application-provisioning-config-problem-no-users-provisioned.md)
- [预配到 Azure AD 库应用程序的用户组错误](../manage-apps/add-application-portal-assign-users.md)
- [设置 Windows 事件查看器来排查代理方面的问题](../saas-apps/workday-inbound-tutorial.md#setting-up-windows-event-viewer-for-agent-troubleshooting)
- [设置 Azure 门户审核日志来排查服务方面的问题](../saas-apps/workday-inbound-tutorial.md#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
- [了解 AD 用户帐户创建操作的日志](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-ad-user-account-create-operations)
- [了解经理更新操作的日志](../saas-apps/workday-inbound-tutorial.md#understanding-logs-for-manager-update-operations)
- [解决常见错误](../saas-apps/workday-inbound-tutorial.md#resolving-commonly-encountered-errors)

### <a name="next-steps"></a>后续步骤

- [为属性映射编写表达式](functions-for-customizing-application-data.md)
- [Azure AD 同步 API 概述](/graph/api/resources/synchronization-overview)
- [跳过删除超出范围的用户帐户](skip-out-of-scope-deletions.md)
- [Azure AD Connect 预配代理：版本发布历史记录](provisioning-agent-release-version-history.md)
