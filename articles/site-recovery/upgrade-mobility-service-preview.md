---
title: 升级移动服务和设备组件 - 预览版
description: 本文介绍移动代理的自动更新以及手动更新所涉及的过程 - 预览版。
ms.service: site-recovery
ms.topic: article
ms.date: 09/01/2021
ms.openlocfilehash: 97cfba3e14268385632a47a5898b21e8d68811cb
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123539724"
---
# <a name="upgrade-mobility-service-and-appliance-components-preview"></a>升级移动服务和设备组件（预览版）

在此预览版中，无需维护源计算机根/管理员凭据，即可执行升级。 只有初始安装代理时，才需要凭据。 完成后，可以删除凭据。


## <a name="update-mobility-agent-automatically"></a>自动更新移动代理

默认情况下，保管库上已启用自动更新。 如果有新版本可用，系统会在每天当地时间凌晨 12:00 触发自动更新。

> [!NOTE]
> 如果使用个人预览版 BITS，则系统会阻止受保护计算机自动更新。 请确保使用新预览版设备，在计算机上重新设置 Site Recovery。

若要使用最新功能、增强功能和修补程序，建议在“移动代理升级设置（预览版）”中选择“允许 Site Recovery 管理”选项。 自动更新不需要重新启动，也不会影响虚拟机的持续复制。 自动更新还可确保自动更新保管库中的所有复制设备。

![开启移动代理的自动更新](./media/upgrade-mobility-service-preview/automatic-updates-on.png)

若要关闭自动更新，请切换“允许 Site Recovery 管理”按钮。

![关闭移动代理的自动更新](./media/upgrade-mobility-service-preview/automatic-updates-off.png)


## <a name="update-mobility-agent-manually"></a>手动更新移动代理

如果已关闭移动代理的自动更新，则可使用以下过程手动更新代理：

### <a name="upgrade-mobility-agent-on-multiple-protected-items"></a>升级多个受保护项的移动代理

若要手动更新多个受保护项的移动代理，请执行以下步骤：

1. 导航到“恢复服务保管库” > “复制的项”，单击“有新的 Site Recovery 移动代理更新可用”。 单击以进行安装。

   ![手动更新多个受保护项的移动代理](./media/upgrade-mobility-service-preview/agent-update.png)

2. 选择要更新的源计算机，然后单击“确定”。

   >[!NOTE]
   >如果不满足升级移动服务的先决条件，则无法选择虚拟机。 请参阅有关[解决方法](#resolve-blocking-issues-for-agent-upgrade)的信息。


4. 启动升级后，系统会在保管库中为每个升级操作创建一个 Site Recovery 作业，用户可通过导航到“监视” > “Site Recovery 作业”对其进行跟踪。

### <a name="update-mobility-agent-for-a-single-protected-machine"></a>更新单个受保护计算机的移动代理

若要更新一个受保护项的移动代理，请执行以下步骤：
1. 导航到“恢复服务保管库” > “复制的项”，选择一个虚拟机。
2. 在虚拟机的“概述”边栏选项卡中，在“代理版本”旁查看移动代理的当前版本。 如果有新更新可用，则状态会更新为“已有新的更新”。

   ![手动更新单个受保护项的移动代理](./media/upgrade-mobility-service-preview/agent-version.png)

3. 单击“已有新的更新”，将显示最新可用版本。 单击“更新到此版本”以启动更新作业。

   ![移动代理更新详细信息](./media/upgrade-mobility-service-preview/agent-update-details.png)

   > [!NOTE]
   > 如果升级被阻止，请按[此处](#resolve-blocking-issues-for-agent-upgrade)所述检查并解决错误。

## <a name="mobility-agent-on-latest-version"></a>最新版本的移动代理

在移动代理更新为最新版本或自动更新为最新版本后，状态会显示为“最新”。

### <a name="resolve-blocking-issues-for-agent-upgrade"></a>解决阻止代理升级问题

如果不满足升级移动代理的先决条件，则无法更新虚拟机。 必须解决这些问题才能继续升级。

需要预先解决的问题包括但不限于：

- 受保护计算机强制重启挂起。

- 复制设备的版本不兼容。

- 复制设备组件（代理服务器或进程服务器）无法与 Azure 服务通信。

- 受保护计算机上的移动代理无法与复制设备通信。

如果存在上述任何问题，状态会更新为“无法更新到最新版本”。 单击此状态可查看阻止更新的原因以及解决问题的建议操作。

>[!NOTE]
>根据阻止原因解决问题后，请等待 30 分钟，然后重试操作。 在 Site Recovery 服务中更新最新信息需要一些时间。

### <a name="mobility-agent-upgrade-job-failure"></a>移动代理升级作业失败

如果移动代理升级操作失败（手动触发或自动升级操作），系统会在作业中更新失败原因。 用户需解决错误，然后重试操作。

若要查看失败错误，可以导航到 Site Recovery 作业，单击特定作业以获取错误的解决方法。 或者，可以使用以下步骤：

1. 导航到“复制的项”部分，选择特定虚拟机。

2. 在“概述”边栏选项卡中的“代理版本”旁查看移动代理的当前版本。

3. 在当前版本旁，状态会更新为“更新失败”消息。 单击此状态以重试更新操作。

4.  系统会提供指向上次升级作业的链接。 单击此作业以导航到特定作业。

5. 解决上次作业的错误问题。

在解决上次失败作业的错误问题后，触发更新操作。

## <a name="upgrade-appliance"></a>升级设备

默认情况下，已在设备上启用自动更新。 如果有任何组件的新版本可用，系统会在每天当地时间凌晨 12:00 触发自动更新。

若要检查其中任何组件的更新状态，请导航到设备服务器，并打开“Microsoft Azure 设备配置管理器”。 导航到“设备组件”，并将其展开以查看所有组件及其版本的列表。

如果其中有组件需要更新，则“状态”会反映相应的内容。 单击状态消息可升级组件。

  ![复制设备组件](./media/upgrade-mobility-service-preview/appliance-components.png)

### <a name="resolve-issues-with-component-upgrade"></a>解决组件升级问题

如果不满足升级其中任何组件的先决条件，则无法进行更新。 原因/需要预先解决的问题包括但不限于：

- 复制设备的其中一个组件版本不兼容。

- 复制设备无法与 Azure 服务通信。

如果存在上述任何问题，状态会更新为“无法更新到最新版本”。 单击此状态可查看阻止更新的原因以及解决问题的建议操作。 根据阻止原因解决问题后，请手动尝试更新。
