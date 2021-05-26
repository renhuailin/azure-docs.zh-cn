---
title: 配置 QnA Maker 服务 - QnA Maker
description: 本文档概述了 QnA Maker 资源的高级配置。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 05/04/2021
ms.openlocfilehash: 0eaff84368327da7ebef11d53338f13ee6f8cdb4
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/25/2021
ms.locfileid: "110376352"
---
# <a name="configure-qna-maker-resources"></a>配置 QnA Maker 资源

用户可以将 QnA Maker 配置为使用不同的认知搜索资源。 如果使用的是 QnA Maker 正式版，则还可以配置应用服务设置。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

## <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>将 QnA Maker 配置为使用不同的认知搜索资源

> [!NOTE]
> 如果更改与 QnA Maker 关联的 Azure 搜索服务，你将无法访问其中已存在的所有知识库。 在更改 Azure 搜索服务之前，请务必导出现有知识库。

如果你通过门户创建 QnA 服务及其依赖项（如搜索），则系统会为你创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用先前存在的搜索服务，并删除刚刚创建的搜索服务。

QnA Maker 的 **应用服务** 资源使用认知搜索资源。 若要更改 QnA Maker 使用的认知搜索资源，需要在 Azure 门户中更改设置。

1. 获取你想让 QnA Maker 使用的认知搜索资源的 **管理密钥** 和 **名称**。

1. 登录到 [Azure 门户](https://portal.azure.com)，找到与你的 QnA Maker 资源关联的 **应用服务**。 两者具有相同的名称。

1. 依次选择“设置”、“配置”。 这将显示 QnA Maker 的应用服务的所有现有设置。

    > [!div class="mx-imgBorder"]
    > ![Azure 门户的屏幕截图，其中显示了应用服务配置设置](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 更改以下项的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新设置，你需要重启应用服务。 依次选择“概述”、“重启”。

    > [!div class="mx-imgBorder"]
    > ![Azure 门户的屏幕截图，其中显示了如何在更改配置设置后重启应用服务](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果通过 Azure 资源管理器模板创建 QnA 服务，则可以创建所有资源，并控制应用服务的创建以使用现有搜索服务。

详细了解如何配置应用服务的[应用程序设置](../../../app-service/configure-common.md#configure-app-settings)。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

### <a name="configure-text-analytics-resource-with-custom-question-answering-feature-to-use-a-different-cognitive-search-resource"></a>配置具有自定义问答功能的文本分析资源以使用不同的认知搜索资源

> [!NOTE]
> 如果更改与文本分析关联的 Azure 搜索服务，你将无法访问其中已存在的所有知识库。 在更改 Azure 搜索服务之前，请务必导出现有知识库。


如果你通过门户创建文本分析资源及其依赖项（如搜索），则系统会为你创建一个搜索服务并将其关联到文本分析资源。 创建这些资源后，可以在“功能”选项卡中更新搜索资源。

1.  转到 Azure 门户中的文本分析资源。

2.  选择“功能”，然后选择要与文本分析资源关联的 Azure 认知搜索服务。

> [!div class="mx-imgBorder"]
> ![将 QnA 添加到 TA](../media/qnamaker-how-to-upgrade-qnamaker/update-custom-qna-feature.png)

3.  单击“保存”。

---

## <a name="get-the-latest-runtime-updates"></a>获取最新的运行时更新

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

QnAMaker 运行时是在 Azure 门户中[创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md)时部署的 Azure 应用服务实例的一部分。 对运行时的更新定期进行。 在 2019 年 4 月站点扩展版本（版本 5+）后，QnA Maker 应用服务实例采用自动更新模式。 此更新经过设计，可以在升级过程中进行更新而不会导致停机。

可以在 https://www.qnamaker.ai/UserSettings 中检查你的当前版本。 如果你的版本低于 5.x，则必须重启应用服务才能应用最新更新：

1. 在 [Azure 门户](https://portal.azure.com)中转到你的 QnAMaker 服务（资源组）。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择应用服务实例并打开“概述”部分。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker 应用服务实例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重启应用服务。 更新过程应当会在几秒内完成。 在此重启期间，最终用户将无法使用任何使用此 QnAMaker 服务的依赖应用程序或机器人。

    ![QnAMaker 应用服务实例的重启](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)
    
# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务未部署自定义问答。

---

## <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>配置应用服务空闲设置以避免超时

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

用于为已发布的知识库提供 QnA Maker 预测运行时的应用服务有一个空闲超时配置，该配置默认为在服务空闲时自动超时。 对于 QnA Maker 来说，这意味着你的预测运行时 generateAnswer API 在没有流量时偶尔会超时。

若要使预测终结点应用保持负载状态（即使没有流量），请将空闲设置设置为始终启用。

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择你的 QnA Maker 资源的应用服务。 它的名称将与 QnA Maker 资源相同，但应用服务的 **类型** 不同。
1. 找到“设置”，然后选择“配置”。
1. 在“配置”窗格中，选择“常规设置”，然后找到“始终启用”并选择“启用”作为值。 

    > [!div class="mx-imgBorder"]
    > ![在“配置”窗格中，选择“常规设置”，然后找到“始终启用”并选择“启用”作为值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 选择“保存”  以保存配置。
1. 系统会询问你是否要重启应用以使用新设置。 选择“继续”。

详细了解如何配置应用服务的[常规设置](../../../app-service/configure-common.md#configure-general-settings)。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务未部署自定义问答。

---

## <a name="business-continuity-with-traffic-manager"></a>通过流量管理器实现业务连续性

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 计划](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上述高级想法如下：

1. 在 [Azure 配对区域](../../../best-practices-availability-paired-regions.md)中设置两个并行 [QnA Maker 服务](set-up-qnamaker-service-azure.md)。

1. [备份](../../../app-service/manage-backup.md)你的主要 QnA Maker 应用服务并在辅助设置中[还原](../../../app-service/web-sites-restore.md)它。 这将确保两个设置使用相同的主机名和密钥。

1. 使主要和辅助 Azure 搜索索引保持同步。使用[此处](https://github.com/pchoudhari/QnAMakerBackupRestore)的 GitHub 示例了解如何备份/还原 Azure 索引。

1. 使用[连续导出](../../../azure-monitor/app/export-telemetry.md)备份 Application Insights。

1. 主要和辅助堆栈设置完成后，使用[流量管理器](../../../traffic-manager/traffic-manager-overview.md)配置两个终结点并设置路由方法。

1. 你需要为流量管理器终结点创建传输层安全性 (TLS)（之前称为安全套接字层 (SSL)）证书。 在你的应用服务中[绑定 TLS/SSL 证书](../../../app-service/configure-ssl-bindings.md)。

1. 最后，在机器人或应用中使用流量管理器终结点。

# <a name="custom-question-answering-preview-release"></a>[自定义问答（预览版）](#tab/v2)

应用服务未部署自定义问答。

---
