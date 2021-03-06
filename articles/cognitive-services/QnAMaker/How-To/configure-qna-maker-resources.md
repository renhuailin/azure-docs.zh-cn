---
title: 配置 QnA Maker 服务-QnA Maker
description: 本文档概述了 QnA Maker 资源的高级配置。
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 02/18/2021
ms.openlocfilehash: 3c6f75eafad51c99f60b78ce49862d2488d5926f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2021
ms.locfileid: "102220116"
---
# <a name="configure-qna-maker-resources"></a>配置 QnA Maker 资源

用户可以将 QnA Maker 配置为使用不同的认知搜索资源。 如果使用 QnA Maker GA，他们还可以配置应用服务设置。

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA（稳定版本）](#tab/v1)

### <a name="configure-qna-maker-to-use-different-cognitive-search-resource"></a>将 QnA Maker 配置为使用不同的认知搜索资源

如果通过门户创建 QnA 服务及其依赖项 (如搜索) ，系统会为您创建一个搜索服务并将其链接到 QnA Maker 服务。 创建这些资源后，你可以更新应用服务设置，以使用以前存在的搜索服务，并删除刚刚创建的搜索服务。

QnA Maker 的 **应用服务** 资源使用认知搜索资源。 若要更改 QnA Maker 使用的认知搜索资源，需要更改 Azure 门户中的设置。

1. 获取要 QnA Maker 使用的认知搜索资源的 **管理员密钥** 和 **名称** 。

1. 登录到 [Azure 门户](https://portal.azure.com) 并找到与 QnA Maker 资源关联的 **应用服务** 。 具有相同名称的。

1. 依次选择 " **设置**"、" **配置**"。 这将显示 QnA Maker 的应用服务的所有现有设置。

    > [!div class="mx-imgBorder"]
    > ![显示应用服务配置设置的 Azure 门户屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-app-service-configuration.png)

1. 更改以下项的值：

    * **AzureSearchAdminKey**
    * **AzureSearchName**

1. 若要使用新设置，需要重新启动应用服务。 选择 " **概述**"，然后选择 " **重新启动**"。

    > [!div class="mx-imgBorder"]
    > ![更改配置设置之后 Azure 门户重启应用服务的屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/screenshot-azure-portal-restart-app-service.png)

如果通过 Azure 资源管理器模板创建 QnA 服务，则可以创建所有资源，并控制应用服务创建，以使用现有搜索服务。

了解有关如何配置应用服务 [应用程序设置](../../../app-service/configure-common.md#configure-app-settings)的详细信息。

### <a name="get-the-latest-runtime-updates"></a>获取最新的运行时更新

QnAMaker 运行时是在 Azure 门户中 [创建 QnAMaker 服务](./set-up-qnamaker-service-azure.md) 时部署的 Azure App Service 实例的一部分。 对运行时的更新定期进行。 2019年4月版) 的站点扩展 (发布后，QnA Maker 应用服务实例处于自动更新模式。 此更新旨在在升级过程中处理零停机时间。

您可以在中检查当前版本 https://www.qnamaker.ai/UserSettings 。 如果版本低于版本1.x，则必须重启应用服务才能应用最新更新：

1. 请在 [Azure 门户](https://portal.azure.com)中 (资源组) 中转到你的 QnAMaker 服务。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker Azure 资源组](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-resourcegroup.png)

1. 选择应用服务实例，并打开 " **概述** " 部分。

    > [!div class="mx-imgBorder"]
    > ![QnAMaker 应用服务实例](../media/qnamaker-how-to-troubleshoot/qnamaker-azure-appservice.png)


1. 重新启动应用服务。 更新过程只需几秒钟即可完成。 在此重新启动期间，最终用户将无法使用此 QnAMaker 服务的所有从属应用程序或 bot。

    ![QnAMaker 应用服务实例的重新启动](../media/qnamaker-how-to-upgrade-qnamaker/qnamaker-appservice-restart.png)

### <a name="configure-app-service-idle-setting-to-avoid-timeout"></a>配置应用服务空闲设置以避免超时

用于为已发布的知识库提供 QnA Maker 预测运行时的应用服务具有空闲超时配置，默认情况下，如果服务处于空闲状态，则会自动超时。 对于 QnA Maker，这意味着预测运行时 generateAnswer API 有时会在没有流量的时段超时。

若要使预测终结点应用始终加载（即使没有流量），请将 "空闲" 设置为 "始终打开"。

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 搜索并选择 QnA Maker 资源的应用服务。 它将具有与 QnA Maker 资源相同的名称，但它将具有不同 **类型** 的应用服务。
1. 找到 " **设置** "，然后选择 " **配置**"。
1. 在 "配置" 窗格上，选择 " **常规设置**"，然后找到 " **始终打开**"，并选择 **"打开** " 作为值。

    > [!div class="mx-imgBorder"]
    > ![在 "配置" 窗格上，选择 "常规设置"，然后查找 * * Always on * *，然后选择 * * On * * 作为值。](../media/qnamaker-how-to-upgrade-qnamaker/configure-app-service-idle-timeout.png)

1. 选择“保存”  以保存配置。
1. 系统会询问你是否要重新启动应用程序以使用新设置。 选择“继续”。

详细了解如何配置应用服务 [常规设置](../../../app-service/configure-common.md#configure-general-settings)。

### <a name="business-continuity-with-traffic-manager"></a>流量管理器的业务连续性

业务连续性计划的主要目标是创建弹性知识库终结点，确保机器人或应用程序使用该终结点时不会发生停机。

> [!div class="mx-imgBorder"]
> ![QnA Maker bcp 计划](../media/qnamaker-how-to-bcp-plan/qnamaker-bcp-plan.png)

上述高级想法如下：

1. 在 [Azure 配对区域](../../../best-practices-availability-paired-regions.md)中设置两个并行 [QnA Maker 服务](set-up-qnamaker-service-azure.md)。

1. [备份](../../../app-service/manage-backup.md) 主 QnA Maker 应用服务，并在辅助安装程序中将其 [还原](../../../app-service/web-sites-restore.md) 。 这将确保两个设置使用相同的主机名和密钥。

1. 使主要和辅助 Azure 搜索索引保持同步。在 [此处](https://github.com/pchoudhari/QnAMakerBackupRestore) 使用 GitHub 示例，了解如何备份-还原 Azure 索引。

1. 使用[连续导出](../../../azure-monitor/app/export-telemetry.md)备份 Application Insights。

1. 主要和辅助堆栈设置完成后，使用[流量管理器](../../../traffic-manager/traffic-manager-overview.md)配置两个终结点并设置路由方法。

1. 需要为流量管理器终结点创建 (TLS) 之前称为安全套接字层 (SSL) 证书的传输层安全性。 绑定应用服务中[的 TLS/SSL 证书](../../../app-service/configure-ssl-bindings.md)。

1. 最后，在机器人或应用中使用流量管理器终结点。

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker 托管（预览版本）](#tab/v2)

### <a name="configure-qna-maker-managed-preview-service-to-use-different-cognitive-search-resource"></a>将 QnA Maker 托管 (预览版) 服务配置为使用不同的认知搜索资源

如果通过门户创建 QnA 服务托管 (预览版) 及其依赖项 (如搜索) ，则会为您创建一个搜索服务，并将其链接到 QnA Maker 托管 (Preview) 服务。 创建这些资源后，可以在 " **配置** " 选项卡中更新搜索服务。

1. 请在 Azure 门户中转到 QnA Maker 托管 (预览版) 服务。

1. 选择 " **配置** "，然后选择要与 QnA Maker 托管 (预览版) 服务链接的 Azure 认知搜索服务。

    ![QnA Maker managed (Preview) 配置页的屏幕截图](../media/qnamaker-how-to-upgrade-qnamaker/change-search-service-configuration.png)

1. 单击“保存”  。

> [!NOTE]
> 如果更改与 QnA Maker 关联的 Azure 搜索服务，将无法访问其中已存在的所有知识库。 请确保在更改 Azure 搜索服务之前导出现有知识库。

---
