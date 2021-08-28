---
title: 如何为 Azure 自动化更新管理创建更新部署
description: 本文介绍如何计划更新部署并查看其状态。
services: automation
ms.subservice: update-management
ms.date: 06/24/2021
ms.topic: conceptual
ms.openlocfilehash: 2e9b10206a28072d37668207993007dc29320f04
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2021
ms.locfileid: "112679015"
---
# <a name="how-to-deploy-updates-and-review-results"></a>如何部署更新和查看结果

本文介绍如何计划更新部署并在部署完成后查看执行过程。 可以从所选的 Azure 虚拟机、所选的启用了 Arc 的服务器或所有已配置的虚拟机和服务器的自动化帐户配置更新部署。

在每个方案下，你创建的部署都以所选虚拟机或服务器为目标；在从自动化帐户创建部署的情况下，你可以以一台或多台虚拟机为目标。 从 Azure VM 或启用了 Arc 服务器计划更新部署时，步骤与从自动化帐户进行部署的步骤相同，但以下情况除外：

* 系统会根据计算机的 OS 自动预选择操作系统
* 要更新的目标计算机会自动设置为以其自身为目标
* 配置计划时，可以指定“立即更新”、只进行一次，或使用定期计划。

> [!IMPORTANT]
> 创建更新部署即表明你接受为其操作系统提供更新的公司提供的软件许可条款 (EULA) 中的条款。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

登录到 [Azure 门户](https://portal.azure.com)

## <a name="schedule-an-update-deployment"></a>计划更新部署

计划某个更新部署会创建链接到 Patch-MicrosoftOMSComputers Runbook 的[计划](../shared-resources/schedules.md)资源，该 Runbook 处理目标计算机上的更新部署。 必须计划一个遵循发布时间和服务时段的部署，以便安装更新。 你可以选择要在部署中包括的更新类型。 例如，可包括关键或安全更新，不包括更新汇总。

>[!NOTE]
>如果在创建部署后通过 Azure 门户或 PowerShell 删除了该计划资源，该删除操作会破坏计划的更新部署，在你尝试通过门户重新配置该计划资源时会出现错误。 只能通过删除相应的部署计划来删除计划资源。  

若要计划新的更新部署，请执行以下步骤。 下面的步骤适用于所有方案，但在配置部署计划时有细微差别，具体取决于所选的资源（即自动化帐户、启用了 Arc 的服务器、Azure VM）。

1. 在门户中，若要为以下资源计划部署，请执行相应的操作：

   * 若要为一台或多台计算机计划部署，请导航到“自动化帐户”，然后从列表中选择启用了“更新管理”的自动化帐户。
   * 若要为 Azure VM 计划部署，请导航到“虚拟机”，然后从列表中选择你的 VM。
   * 对于启用了 Arc 的服务器，请导航到“服务器 - Azure Arc”，然后从列表中选择服务器。

2. 根据所选资源，若要导航到“更新管理”，请执行以下操作：

   * 如果选择了自动化帐户，请转到“更新管理”下的“更新管理”，然后选择“计划更新部署”  。
   * 如果选择了 Azure VM，请转到“来宾 + 主机更新”，然后选择“转到更新管理”。
   * 如果选择启用了 Arc 的服务器，请转到“更新管理”，然后选择“计划更新部署”。

3. 在“新建更新部署”下的“名称”字段中，输入部署的唯一名称 。

4. 选择更新部署的目标操作系统。

    > [!NOTE]
    > 如果选择 Azure VM 或启用了 Arc 的服务器，则此选项不可用。 系统会自动识别操作系统。

5. 在“要更新的组”区域中，定义一个组合了订阅、资源组、位置和标记的查询，以生成要包括在部署中的 Azure VM 动态组。 要了解详细信息，请参阅[将动态组与更新管理配合使用](configure-groups.md)。

    > [!NOTE]
    > 如果选择 Azure VM 或启用了 Arc 的服务器，则此选项不可用。 计算机会自动成为已计划部署的目标。

   > [!IMPORTANT]
   > 在生成 Azure VM 的动态组时，更新管理仅支持最多 500 次查询，这些查询包括组范围内的订阅或资源组。

6. 在“要更新的计算机”区域，选择已保存的搜索、已导入的组或者从下拉菜单中选择“计算机”并选择各个计算机 。 通过此选项可以查看每台计算机的 Log Analytics 代理的准备情况。 若要了解在 Azure Monitor 日志中创建计算机组的不同方法，请参阅 [Azure Monitor 日志中的计算机组](../../azure-monitor/logs/computer-groups.md)。 最多可以在计划的更新部署中包括 1000 台计算机。

    > [!NOTE]
    > 如果选择 Azure VM 或启用了 Arc 的服务器，则此选项不可用。 计算机会自动成为已计划部署的目标。

7. 使用“更新分类”区域为产品指定[更新分类](view-update-assessments.md#work-with-update-classifications)。 对于每个产品，请取消选择所有受支持的更新分类，但要包含在更新部署中的分类除外。

   :::image type="content" source="./media/deploy-updates/update-classifications-example.png" alt-text="显示具体更新分类的选择的示例。":::

    如果你的部署打算只应用一组选定的更新，那么在配置“包含/排除更新”选项时，有必要取消选择所有预先选择的更新分类，如下一步所述。 这将确保在目标计算机上只安装你指定添加在此部署中的更新。

   >[!NOTE]
   > 在 CentOS 的 RTM 版本上无法按更新分类部署更新。 要为 CentOS 正确部署更新，请选择所有分类以确保应用更新。 当前没有受支持的方法可用来在 CentOS 上提供原生分类数据。 有关[更新分类](overview.md#update-classifications)的详细信息，请参阅下面的内容。

   >[!NOTE]
   > 对于更新管理支持的 Linux 发行版，按更新分类部署更新可能无法正常工作。 这是由 OVAL 文件的命名架构确定的问题导致的，会阻止更新管理根据筛选规则正确匹配分类。 由于在安全更新评估中使用的逻辑不同，结果可能与部署期间应用的安全更新不同。 如果将分类设置为“严重”和“安全性”，则更新部署会按预期工作。 在评估期间，只有“更新的分类”会受影响。
   >
   > Windows Server 计算机的更新管理不受影响；更新分类和部署将不变。

8. 使用“包含/排除更新”区域可从部署中添加或排除选定的更新。 对于 Windows 更新，可以在“包括/排除”页上输入知识库文章 ID 号来进行包括或排除操作。 对于支持的 Linux 发行版，请指定包名称。

   :::image type="content" source="./media/deploy-updates/include-specific-updates-example.png" alt-text="显示如何包含具体更新的示例。":::

   > [!IMPORTANT]
   > 请记住，排除项会替代包含项。 例如，如果定义了 `*` 排除规则，更新管理会从安装中排除所有修补程序或包。 已排除的修补程序仍显示为计算机缺少此项。 对于 Linux 计算机，如果包含一个具有依赖包的包，而该依赖包已被排除，则更新管理不会安装主包。

   > [!NOTE]
   > 你不能指定将已被取代的更新包含在更新部署中。

   下面是一些示例场景，可帮助你了解如何在更新部署中同时使用包含/排除和更新分类：

   * 如果你只需要安装一个特定的更新列表，则不应该选择任何“更新分类”，而是使用“包含”选项提供要应用的更新列表。

   * 如果你只需要安装安全更新和关键更新，以及一个或多个可选驱动程序更新，应在“更新分类”下选择“安全”和“关键”。 然后，对于“包含”选项，指定驱动程序更新。

   * 如果你只需要安装安全更新和关键更新，但是跳过适用于 Python 的一个或多个更新以避免破坏旧版应用程序，应在“更新分类”下选择“安全”和“关键”。 然后，对于“排除”选项，添加要跳过的 Python 包。

9. 选择“计划设置”。 默认开始时间为晚于当前时间 30 分钟。 可以将开始时间设置为 10 分钟之后的任何将来时间。

    > [!NOTE]
    > 如果选择启用了 Arc 的服务器，则此选项将不同。 可以选择“立即更新”或将开始时间设置为 20 分钟之后的将来时间。

10. 使用“定期”指定是部署一次还是使用定期计划，然后选择“确定” 。

11. 在“前脚本 + 后脚本”区域中，选择要在部署前和部署后运行的脚本。 若要了解详细信息，请参阅[管理前脚本和后脚本](pre-post-scripts.md)。

12. 使用“维护时段(分钟)”字段来指定安装更新的允许时长。 指定维护时段时，请考虑以下详细信息：

    * 维护时段控制安装的更新数量。
    * 如果维护时段即将结束，更新管理不会停止安装新的更新。
    * 如果超出了维护时段，更新管理不会终止正在进行的更新。 不会尝试要安装的其余任何更新。 如果这种情况持续发生，则应重新评估维护时段的时长。
    * 如果超出了 Windows 上的维护时段，通常是因为服务包更新需要很长时间才能安装。

    > [!NOTE]
    > 若要避免在 Ubuntu 上的维护时段外应用更新，请重新配置 `Unattended-Upgrade` 包，禁用自动更新。 有关如何配置此包的信息，请参阅 [Ubuntu Server 指南中的自动更新主题](https://help.ubuntu.com/lts/serverguide/automatic-updates.html)。

13. 使用“重新启动选项”字段指定部署过程中处理重新启动的方式。 提供了以下选项： 
    * 必要时重新启动（默认）
    * 永远重启
    * 永不重启
    * 仅重新启动；此选项不安装更新

    > [!NOTE]
    > 如果“重新启动选项”设置为“永不重新启动”，则[用于管理重启的注册表项](/windows/deployment/update/waas-restart#registry-keys-used-to-manage-restart)下列出的注册表项可能会导致重新启动事件 。

14. 配置部署计划后，选择“创建”。

    ![更新“计划设置”窗格](./media/deploy-updates/manageupdates-schedule-win.png)

    > [!NOTE]
    > 为选定的启用了 Arc 的服务器配置好部署计划后，请选择“查看 + 创建”。

15. 此时会回到状态仪表板。 选择“部署计划”以显示已创建的部署计划。 最多会列出 500 个计划。 如果计划超过 500 个，并且你想要查看完整列表，请参阅[软件更新配置 - 列出](/rest/api/automation/softwareupdateconfigurations/list) REST API 方法。 指定 API 版本 2019-06-01 或更高版本。

## <a name="schedule-an-update-deployment-programmatically"></a>以编程方式计划更新部署

若要了解如何使用 REST API 创建更新部署，请参阅[软件更新配置 - 创建](/rest/api/automation/softwareupdateconfigurations/create)。

可以使用示例 runbook 创建每周更新部署。 若要了解有关此 Runbook 的详细信息，请参阅[为资源组中的一个或多个 VM 创建每周更新部署](https://github.com/azureautomation/create-a-weekly-update-deployment-for-one-or-more-vms-in-a-resource-group)。

## <a name="check-deployment-status"></a>检查部署状态

在计划的部署开始后，可以在“更新管理”下的“历史记录”选项卡上查看其状态 。 部署当前正在运行时，其状态为“正在进行”。 部署成功完成后，状态将更改为“成功”。 如果部署中有一个或多个更新已失败，状态将是“失败”。

## <a name="view-results-of-a-completed-update-deployment"></a>查看已完成的更新部署的结果

部署完成后，可选择它以查看其结果。

[ ![特定部署的更新部署状态仪表板](./media/deploy-updates/manageupdates-view-results.png)](./media/deploy-updates/manageupdates-view-results-expanded.png#lightbox)

“更新结果”下的摘要提供了目标 VM 上的更新和部署结果的总数。 右侧的表显示了更新的细目以及每个更新的安装结果。

可用值有：

* **未尝试** - 由于定义的维护时段时长不足，因为而未安装该更新。
* **未选择** - 未选择要部署的更新。
* **成功** - 更新成功。
* **失败** - 更新失败。

选择“所有日志”来查看部署创建的所有日志条目。

选择“输出”，查看负责管理目标 VM 更新部署的 runbook 的作业流。

若要查看有关部署中错误的详细信息，请选择“错误”。

## <a name="next-steps"></a>后续步骤

* 若要了解如何创建警报来就更新部署结果进行通知，请参阅[为更新管理创建警报](configure-alerts.md)。
* 若要对常规更新管理错误进行故障排除，请参阅[排查更新管理问题](../troubleshoot/update-management.md)。
