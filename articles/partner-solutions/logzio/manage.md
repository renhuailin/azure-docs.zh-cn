---
title: 管理 Azure 与 Logz.io 的集成 - Azure 合作伙伴解决方案
description: 了解如何管理 Azure 与 Logz.io 的集成。
author: tfitzmac
ms.topic: conceptual
ms.service: partner-services
ms.date: 07/28/2021
ms.author: tomfitz
ms.openlocfilehash: 0b00e1b4b5cf868fdd70909a1ce42f0861f2e0b2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778004"
---
# <a name="manage-the-logzio-integration-in-azure"></a>管理 Azure 中的 Logz.io 集成

本文介绍如何管理 Azure 与 Logz.io 的集成设置。

## <a name="resource-overview"></a>资源概述

Logz.io 的“概述”屏幕介绍了包含以下项目的资源详细信息：

- **资源组**
- **位置**
- **订阅**
- **标记**
- Logz.io SSO
- **计费期限**

根据你是选择加入还是选择退出单一登录 (SSO)，Logz.io SSO 链接会有所不同：

- 如果你选择加入 SSO：

    :::image type="content" source="./media/manage/sso-opt-in.png" alt-text="单一登录选择加入。":::

- 如果你选择退出 SSO：

    :::image type="content" source="./media/manage/sso-opt-out.png" alt-text="单一登录选择退出。":::

## <a name="reconfigure-rules-for-logs-and-metrics"></a>重新配置指标和日志的规则

若要更改配置规则，在“Logz 配置”下，选择“日志和指标”。 有关详细信息，请参阅[配置日志](./create.md#configure-logs)。

:::image type="content" source="./media/manage/logs-metrics.png" alt-text="日志和指标配置。":::

## <a name="configure-diagnostic-settings"></a>配置诊断设置

若要配置诊断设置，请选择资源。 在左窗格中，选择“诊断设置”。

在“目标详细信息”列中，选中“发送到合作伙伴解决方案”选项以选择 Logz.io 作为目标。 此选项仅在创建 Logz 资源后可用。

:::image type="content" source="./media/manage/configure-diagnostics.png" alt-text="配置诊断设置。":::

## <a name="view-monitored-resources"></a>查看监视的资源

若要查看向 Logz.io 发送日志的资源的列表（通过配置的业务规则），请转到“Logz 配置”，然后选择“监视的资源”。 

:::image type="content" source="./media/manage/monitored-resources.png" alt-text="监视的资源配置。":::

可以按资源类型、资源组名称、位置以及资源是否正在发送日志来筛选该列表。

以下是“监视的资源”屏幕上显示的列的描述：

- 资源名称：Azure 资源名称。
- 资源类型：Azure 资源类型。
- 资源组：显示 Azure 资源的资源组名称。
- 区域：显示 Azure 资源的区域或位置。
- 发送到 Logz 的日志：指示资源是否正在将日志发送到 Logz.io。 如果资源不发送日志，则此字段指示日志不发送到 Logz.io 的适当原因。 原因可能包括：
  - 资源不支持发送日志：只有在此处定义的所有资源类型和[日志类别](../../azure-monitor/essentials/resource-logs-categories.md)的 Azure 资源日志可以配置为向 Logz.io 发送日志。
  - 达到了五个诊断设置的限制：每个 Azure 资源最多可以有五个诊断设置。 有关详细信息，请参阅[创建诊断设置以将平台日志和指标发送到不同的目标](../../azure-monitor/essentials/diagnostic-settings.md)。
  - 错误：错误会阻止将日志发送到 Logz.io。
  - 未配置日志：只有具有相应资源标记的资源可以将日志发送到 Logz.io。 请参阅[配置日志](./create.md#configure-logs)。
  - 区域不受支持：Azure 资源位于目前不将日志发送到 Logz.io 的区域。

若要编辑 Azure 资源的标记列表，请选择“编辑标记”。 你可以添加新的标记或删除现有标记。

## <a name="monitor-vm-using-logzio-agent"></a>使用 Logz.io 代理监视 VM

可以将 Logz.io 代理作为扩展安装在虚拟机 (VM) 上。 请转到“Logz 配置”，并选择“虚拟机代理”。 此屏幕显示订阅中所有 VM 的列表。

对于每个虚拟机，将显示以下数据：

- 资源名称：虚拟机名称。
- 资源类型：Azure 资源类型。
- 资源组：显示 Azure 资源的资源组名称。
- 区域：显示 Azure 资源的区域或位置。
- 代理版本：Logz.io 代理版本号。
- 扩展状态：指示 VM 上已安装或未安装 Logz.io 代理 。
- 发送到 Logz 的日志：指定 Logz.io 代理是否将日志发送到 Logz.io。

:::image type="content" source="./media/manage/vm-agent.png" alt-text="虚拟机代理。":::

### <a name="install-vm-agent"></a>安装 VM 代理

若要安装 VM 代理，请按以下步骤操作。

1. 选择要在其上安装 Logz.io 代理的 VM，然后选择“安装代理”。

   门户要求确认是否要安装具有默认身份验证的代理。

1. 选择“确定”开始安装。

   Azure 会在安装并预配代理之前，将状态显示为“正在安装”。

   安装 Logz.io 代理后，状态将更改为“已安装”。

1. 若要验证 Logz.io 代理已安装，请选择 VM，然后转到“扩展”窗口。

> [!NOTE]
> 安装 VM 代理后，如果 VM 停止，“发送到 Logz 的日志”列将显示该 VM 为“未发送”。

### <a name="uninstall-vm-agent"></a>卸载 VM 代理

你可以从“虚拟机代理”卸载 VM 上的 Logz.io 代理。 选择 VM 并卸载代理。

## <a name="create-logzio-sub-account"></a>创建 Logz.io 子帐户

只有 Logz.io 帐户的管理员才能创建子帐户。 默认情况下，Logz.io 帐户的创建者设置为管理员。 登录到 Logz.io 帐户以向其他用户授予管理员权限。

有关详细信息，请参阅 Logz.io 网站上的 [Logz.io 子帐户](https://logz.io/blog/diving-deeper-logz-io-sub-accounts/)。

你可以使用“概述” > “添加子帐户”按钮创建 Logz.io 子帐户。

:::image type="content" source="./media/manage/create-sub-account-overview.png" alt-text="从“概述”创建 Logz.io 子帐户。":::

另一种方法是转到“Logz 配置”并选择“添加子帐户” 。 按照提示设置子帐户，并通过子帐户启用日志传送。

:::image type="content" source="./media/manage/create-sub-account-logz.png" alt-text="从 Logz 配置创建 Logz.io 子帐户。":::

## <a name="delete-logzio-sub-account"></a>删除 Logz.io 子帐户

1. 对于 Logz.io 资源，请选择“Logz 配置”和“Logz 子帐户”。
1. 选择要删除的子帐户。
1. 选择“删除” 。

    :::image type="content" source="./media/manage/delete-sub-account-1.png" alt-text="从 Logz 配置中删除 Logz.io 子帐户。":::

1. 键入“是”以确认你要删除 Logz.io 资源。
1. 选择“删除” 。

    :::image type="content" source="./media/manage/delete-sub-account-2.png" alt-text="确认删除 Logz.io 子帐户。":::

日志不再发送到 Logz.io，并且 Logz.io 的所有计费都会停止。

## <a name="delete-logzio-account"></a>删除 Logz.io 帐户

1. 在 Logz.io 资源中，选择“概述”和“删除”。 
1. 确认你要删除 Logz.io 资源。
1. 选择“删除” 。

    :::image type="content" source="./media/manage/delete-resource-1.png" alt-text="从“概述”中删除 Logz.io 资源。":::

1. 键入“是”以确认你要删除 Logz.io 资源。
1. 选择“删除” 。

    :::image type="content" source="./media/manage/delete-resource-2.png" alt-text="确认删除 Logz.io 资源。":::

> [!NOTE]
> 只有在映射到主帐户的所有子帐户都已删除时，才会激活主帐户的“删除”选项。 若要详细了解如何删除子帐户，请参阅[删除 Logz.io 子帐户](#delete-logzio-sub-account)。

删除资源后，日志将不再发送到 Logz.io，且将通过 Azure 市场停止 Logz.io 的所有计费。

## <a name="get-support"></a>获取支持

若要联系有关 Azure Logz.io 集成的支持人员，请在“支持 + 故障排除”下选择“新建支持请求”。 通过选择此选项，你将转到 [Logz.io 门户](https://app.logz.io/)。 使用聊天中支持或向 [help@logz.io](mailto:help@logz.io) 发送电子邮件。

## <a name="next-steps"></a>后续步骤

- 若要解决集成问题，请参阅[故障排除](troubleshoot.md)。
