---
title: 创建和管理集成运行时
description: 本文介绍创建和管理 Azure 监控范围中的集成运行时的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/16/2020
ms.openlocfilehash: 0d365787ea3603ef0adb8ad0b48bef9792ffb003
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2020
ms.locfileid: "96551651"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>创建和管理自承载集成运行时

本文介绍如何创建和管理自承载集成运行时， (SHIR) 帮助扫描数据源。

## <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

1. 在监控范围 Studio 的 "主页" 页上，从左侧导航窗格中选择 " **管理中心** "。

2. 在左侧窗格中的 " **源和扫描** " 下，选择 " **集成运行时**"，然后选择 " **+ 新建**"。

    :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="单击 &quot;IR&quot;。":::

3. 在 " **集成运行时安装** " 页上，选择 " **自承载** " 创建 Self-Hosted IR，然后选择 " **继续**"。

    :::image type="content" source="media/manage-integration-runtimes/select-self-hosted-ir.png" alt-text="创建新 SHIR。":::

4. 输入 IR 的名称，然后选择“创建”。

5. 在 " **Integration Runtime 设置** " 页上，按照 " **手动设置** " 部分中的步骤进行操作。 必须从下载站点将集成运行时下载到要运行它的 VM 或计算机上。

    :::image type="content" source="media/manage-integration-runtimes/integration-runtime-settings.png" alt-text="获取密钥":::

    a. 复制并粘贴身份验证密钥。
        
    b. 从本地 Windows 计算机上的 [Azure 数据工厂 Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717) 下载自承载集成运行时。 运行安装程序。
        
    c. 在 " **注册 Integration Runtime (自承载)** " 页上，粘贴之前保存的2个密钥之一，然后选择 " **注册**"。

    :::image type="content" source="media/manage-integration-runtimes/register-integration-runtime.png" alt-text="输入密钥。":::

    d. 在“新建 Integration Runtime (自承载)节点”页上，选择“完成”。 

6. 成功注册自承载集成运行时后，会看到以下窗口：

    :::image type="content" source="media/manage-integration-runtimes/successfully-registered.png" alt-text="注册成功。":::

## <a name="manage-a-self-hosted-integration-runtime"></a>管理自承载集成运行时

通过导航到 **管理中心** 中的 "**集成** 运行时"，选择 IR，然后单击 "编辑"，可以编辑自承载集成运行时。 你现在可以更新说明，复制密钥或重新生成新密钥。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="编辑 IR。":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="编辑 IR 详细信息。":::

可以通过导航到管理中心中的 " **集成** 运行时"，选择 IR，然后单击 " **删除**"，来删除自承载集成运行时。 一旦删除 IR，任何依赖于它的扫描都会失败。

## <a name="next-steps"></a>后续步骤

* [扫描如何检测已删除的资产](concept-detect-deleted-assets.md)
