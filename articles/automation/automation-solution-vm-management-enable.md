---
title: 启用 Azure 自动化“在空闲时间启动/停止 VM”
description: 本文介绍如何为 Azure VM 启用“在空闲时间启动/停止 VM”功能。
services: automation
ms.subservice: process-automation
ms.date: 05/18/2021
ms.topic: conceptual
ms.openlocfilehash: 0db68a72b3b44f9febb7cdef546545b5b549ddae
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110069375"
---
# <a name="enable-startstop-vms-during-off-hours"></a>启用“在空闲时间启动/停止 VM”

按顺序执行本主题中的步骤，即可使用新的或现有的自动化帐户以及链接的 Log Analytics 工作区为 VM 启用“在空闲时间启动/停止 VM”功能。 完成设置过程后，请配置变量来自定义该功能。

>[!NOTE]
>若要将此功能用于经典 VM，则需要一个经典运行方式帐户，但默认情况下不会创建该帐户。 请参阅[创建经典运行方式帐户](automation-create-standalone-account.md#create-a-classic-run-as-account)。
>

## <a name="enable-and-configure"></a>启用和配置 

1. 登录 Azure [门户](https://portal.azure.com)。
2. 搜索并选择“自动化帐户”。
3. 在“自动化帐户”页上，从列表中选择你的自动化帐户。
4. 在自动化帐户中，选择“相关资源”下的“启动/停止 VM” 。 在此处，可以单击“详细了解和启用解决方案”。 如果已部署了该功能，可单击“管理解决方案”，在列表中找到该功能。

   ![从自动化帐户启用](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > 此外，你也可以单击“创建资源”，在 Azure 门户中的任意位置创建资源。 在“市场”页面中，键入类似于“启动”或“启动/停止”的关键字 。 开始键入时，会根据输入筛选该列表。 或者，可以键入功能完整名称中的一个或多个关键字，然后按 Enter。 在搜索结果中选择“在空闲时间启动/停止 VM”。

5. 在所选部署的“在空闲时间启动/停止 VM”页中查看摘要信息，然后单击“创建”。

   ![Azure 门户](media/automation-solution-vm-management/azure-portal-01.png)

   创建资源后，即会显示“添加解决方案”页面。 系统会提示你先配置该功能，然后才能将它导入到自动化帐户中。

   ![VM 管理中的“添加解决方案”页面](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

6. 在“添加解决方案”页面上，选择“工作区” 。 从列表中选择现有的 Log Analytics 工作区。 如果工作区所在的受支持区域中没有自动化帐户，可在下一步创建新的自动化帐户。 

   > [!NOTE]
   > 在启用功能时，只有某些区域支持链接 Log Analytics 工作区和自动化帐户。 有关支持的映射对的列表，请参阅[自动化帐户和 Log Analytics 工作区的区域映射](how-to/region-mappings.md)。

7. 在“添加解决方案”页上，如果工作区所在的受支持区域中没有可用的自动化帐户，请选择“自动化帐户” 。 可以创建要与工作区关联的新自动化帐户，方法是：选择“创建自动化帐户”，然后在“添加自动化帐户”页上的“名称”字段中提供该自动化帐户的名称  。

    系统会根据所选的 Log Analytics 工作区自动填充所有其他选项。 无法修改这些选项。 Azure 运行方式帐户是此功能为 Runbook 包含的默认身份验证方法。 
    
    单击“确定”后，系统会验证配置选项并创建自动化帐户。 可以在菜单中的“通知”下面跟踪操作进度。

8. 在“添加解决方案”页上，选择“配置参数”。 此时会显示“参数”页面。

    ![解决方案的“参数”页面](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

9. 为“目标资源组名称”字段指定一个值。 词字段将定义包含该功能要管理的 VM 的组名称。 你可以输入多个名称，使用逗号分隔（这些值不区分大小写）。 如果想要针对订阅中的所有资源组内的 VM，可以使用通配符。 这些值存储在 `External_Start_ResourceGroupNames` 和 `External_Stop_ResourceGroupNames` 变量中。

    > [!IMPORTANT]
    > “目标资源组名称”的默认值是 &ast; 。 该设置面向订阅中的所有 VM。 如果不希望该功能面向订阅中的所有 VM，请务必在选择计划前提供一个资源组名称列表。
  
10. 为“VM 排除列表(字符串)”字段指定一个值。 该值是目标资源组中的一个或多个虚拟机的名称。 你可以输入多个名称，使用逗号分隔（这些值不区分大小写）。 支持使用通配符。 该值存储在变量 `External_ExcludeVMNames` 中。
  
11. 使用“计划”字段来选择按功能进行 VM 管理的计划。 为你的计划选择一个开始日期和时间，以创建从所选时间开始的每日重复计划。 无法选择其他区域。 若要在配置功能后将计划配置为特定时区，请参阅[修改启动和关闭计划](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)。

12. 若要从[操作组](../azure-monitor/alerts/action-groups.md)接收电子邮件通知，请在“电子邮件通知”字段中接受默认值“是”，并提供有效的电子邮件地址 。 如果你选择了“否”，但后来想要接收电子邮件通知，则可以使用有效的电子邮件地址（以逗号分隔）更新创建的操作组。 在订阅中创建以下警报规则：

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

13. 在配置了功能所需的初始设置后，单击“确定”，以关闭“参数”页 。

14. 单击“创建”。 系统会验证所有设置，然后在订阅中部署该功能。 此过程需要几秒钟才能完成，可以在菜单中的“通知”下面跟踪进度。

    > [!NOTE]
    > 如果你具有 Azure 云解决方案提供商 (Azure CSP) 订阅，则部署完成之后，在自动化帐户中转到“共享资源”下的“变量”，然后将 [External_EnableClassicVMs](automation-solution-vm-management.md#variables) 变量设置为“False”。 这会使解决方案停止查找经典 VM 资源。

## <a name="create-alerts"></a>创建警报

“在空闲时间启动/停止 VM”不包括预定义的一组自动化作业警报。 请查看[将作业数据转发到 Azure Monitor 日志](automation-manage-send-joblogs-log-analytics.md#azure-monitor-log-records)，以了解从自动化帐户转发的与 runbook 作业结果相关的日志数据，以及如何创建作业失败警报来支持 DevOps 或操作流程和过程。

## <a name="next-steps"></a>后续步骤

* 要设置该功能，请参阅[配置“在空闲时间启动/停止 VM”](automation-solution-vm-management-config.md)。
* 若要解决功能问题，请参阅[“在空闲时间启动/停止 VM”问题的故障排除](troubleshoot/start-stop-vm.md)。
