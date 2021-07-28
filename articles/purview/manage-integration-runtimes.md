---
title: 创建和管理集成运行时
description: 本文介绍了创建和管理 Azure Purview 中的集成运行时的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: 73144611e835ac1bea20ab92212e52941af84eef
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110463734"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>创建和配置自承载集成运行时

本文介绍了如何创建和管理自承载集成运行时 (SHIR) ，以便扫描 Azure Purview 中的数据源。

> [!NOTE]
> Purview 集成运行时不能与同一台计算机上的 Azure Synapse Analytics 或 Azure 数据工厂集成运行时共享。 它需要安装在单独的计算机上。

## <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

1. 在 Purview Studio 的 "主页" ，从左侧导航窗格中选择 " **管理中心** "。

2. 在左侧窗格中的 " **源和扫描** " 下，选择 " **集成运行时**"，然后选择 " **+ 新建**"。

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="单击 IR。":::

3. 在 **集成运行时设置** 页面，选择 **“自承载”** 以创建自承载 IR，然后选择 **“继续”** 。

   :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="创建新 SHIR。":::

4. 输入 IR 的名称，然后选择“创建”。

5. 在 " **集成运行时设置** " 页上，按照 " **手动设置** " 部分中的步骤进行操作。 必须从下载站点将集成运行时下载到要运行它的虚拟机或计算机上。

   :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="获取密钥":::

   - 复制并粘贴身份验证密钥。

   - 从 [Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) 将自承载集成运行时下载到本地 Windows 计算机上。 运行安装程序。 支持自承载集成运行时版本（例如，5.4.7803.1 和 5.6.7795.1）。 

   - 在 **“注册集成运行时(自承载)”** 页上粘贴前面保存的两个密钥的其中一个密钥，然后选择 **“注册”** 。

     :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="输入密钥。":::

   - 在“新建 Integration Runtime (自承载)节点”页上，选择“完成”。 

6. 成功注册自承载集成运行时后，会看到以下窗口：

   :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="已成功注册。":::

## <a name="manage-a-self-hosted-integration-runtime"></a>管理自承载的集成运行时

可通过导航到 **管理中心** 中的 "**集成运行时**"，选择 IR，然后单击 "编辑"，编辑自承载集成运行时。 你现在可以更新说明，复制密钥，或者重新生成新密钥。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="编辑 IR。":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="编辑 IR 详细信息。":::

可通过导航到管理中心中的 "**集成运行时**"，选择 IR，然后单击 **"删除"** ，删除自承载集成运行时。 一旦删除 IR，任何依赖于它的扫描都会失败。

## <a name="next-steps"></a>后续步骤

[扫描如何检测已删除的资产](concept-detect-deleted-assets.md)
