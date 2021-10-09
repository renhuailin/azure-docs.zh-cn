---
title: 创建和管理集成运行时
description: 本文介绍了创建和管理 Azure Purview 中的集成运行时的步骤。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 1a51af8fd34516ca87d7ab98332221a308480193
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217122"
---
# <a name="create-and-manage-a-self-hosted-integration-runtime"></a>创建和配置自承载集成运行时

本文介绍了如何创建和管理自承载集成运行时 (SHIR) ，以便扫描 Azure Purview 中的数据源。

> [!NOTE]
> Purview 集成运行时不能与同一台计算机上的 Azure Synapse Analytics 或 Azure 数据工厂集成运行时共享。 它需要安装在单独的计算机上。

> [!IMPORTANT]
> 如果在 2021 年 8 月 18 日之后创建了 Azure Purview 帐户，请确保从 [Microsoft 下载中心](https://www.microsoft.com/download/details.aspx?id=39717)下载并安装最新版本的自承载集成运行时。

## <a name="prerequisites"></a>先决条件

- 支持的 Windows 版本为：
  - Windows 8.1
  - Windows 10
  - Windows Server 2012
  - Windows Server 2012 R2
  - Windows Server 2016
  - Windows Server 2019

不支持在域控制器上安装自承载集成运行时。

- 自承载集成运行时需要带有 .NET Framework 4.7.2 或更高版本的 64 位操作系统。 有关详细信息，请参阅 [.NET Framework 系统需求](/dotnet/framework/get-started/system-requirements)。
- 对于自承载集成运行时计算机，建议的最低配置为 4 核 2 GHz 处理器，8 GB RAM，80 GB 可用硬盘空间。 有关系统要求的详细信息，请参阅[下载](https://www.microsoft.com/download/details.aspx?id=39717)。
- 如果主机计算机进入休眠状态，则自承载集成运行时不会响应数据请求。 安装自承载集成运行时之前，请在计算机上配置相应的电源计划。 如果计算机配置为休眠，则自承载集成运行时安装程序会通过消息发出提示。
- 只有计算机管理员才能成功安装和配置自承载集成运行时。
- 扫描运行按照你设置的计划的特定频率发生。 计算机上的处理器和 RAM 使用率遵循相同的高峰期和空闲期模式。 此外，资源使用率在很大程度上取决于扫描的数据量。 进行多个扫描作业时，会看到资源使用率在高峰期上升。
- 在提取 Parquet、ORC 或 Avro 格式的数据时，任务可能会失败。

## <a name="setting-up-a-self-hosted-integration-runtime"></a>设置自承载集成运行时

若要创建和设置自承载集成运行时，请使用以下过程。

## <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime

1. 在 [Purview Studio](https://web.purview.azure.com/resource/) 的“主页”上，从左侧导航窗格中选择“数据映射”。

2. 在左侧窗格中的 " **源和扫描** " 下，选择 " **集成运行时**"，然后选择 " **+ 新建**"。

   :::image type="content" source="media/manage-integration-runtimes/select-integration-runtimes.png" alt-text="选择 IR。":::

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

## <a name="networking-requirements"></a>网络要求

自承载集成运行时计算机需要连接到多个资源才能正常工作：

* 希望使用自承载集成运行时扫描的源。
* 用于存储 Purview 资源的凭据的任何 Azure Key Vault。
* Purview 创建的托管存储和事件中心资源。

托管存储和事件中心资源可以在包含 Purview 资源名称的资源组下的订阅中找到。 Azure Purview 使用这些资源来引入扫描结果，包括许多其他内容，因此自承载集成运行时需要能够直接连接这些资源。

下面是需要通过企业防火墙和计算机防火墙允许的域和端口。

> [!NOTE]
> 对于使用“\<managed Purview storage account>”列出的域，你将添加与 Purview 资源关联的托管存储帐户的名称。 可以在门户中查找此资源。 在资源组中搜索名为 managed-rg-\<your Purview Resource name> 的组。 例如：managed-rg-contosoPurview。 你将使用此资源组中存储帐户的名称。
> 
> 对于使用“\<managed Event Hub resource>”列出的域，你将添加与 Purview 资源关联的托管事件中心的名称。 可以在托管存储帐户所在的资源组中找到此名称。

| 域名                  | 出站端口 | 说明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net` | 443            | 全局基础结构 Purview 使用此域来运行其扫描。 由于没有专用资源，因此需要通配符。 |
| `<managed Event Hub resource>.servicebus.windows.net` | 443            | Purview 使用此域连接到关联的服务总线。 如果允许上面的域，该域将被涵盖，但如果使用的是专用终结点，则需要测试对这一个域的访问权限。|
| `*.frontend.clouddatahub.net` | 443            | 全局基础结构 Purview 使用此域来运行其扫描。 由于没有专用资源，因此需要通配符。 |
| `<managed Purview storage account>.core.windows.net`          | 443            | 自承载集成运行时使用此域连接到托管 Azure 存储帐户。|
| `<managed Purview storage account>.queue.core.windows.net` | 443            | Purview 用于运行扫描过程的队列。 |
| `download.microsoft.com` | 443           | 对于 SHIR 更新是可选的。 |

根据你的源，可能还需要允许其他 Azure 或外部源的域。 下面提供了一些示例，以及用于连接到存储在 Key Vault 中的任何凭据的 Azure Key Vault 域。

| 域名                  | 出站端口 | 说明                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `<storage account>.core.windows.net`          | 443            | 可选，用于连接到 Azure 存储帐户。 |
| `*.database.windows.net`      | 1433           | 可选，用于连接到 Azure SQL 数据库或 Azure Synapse Analytics。 |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | 可选，用于连接到 Azure Data Lake Store Gen 1。 |
| `<datastoragename>.dfs.core.windows.net`    | 443            | 可选，用于连接到 Azure Data Lake Store Gen 2。 |
| `<your Key Vault Name>.vault.azure.net` | 443           | 如果所有凭据都存储在 Azure Key Vault 中，则需要此域。 |
| 各种域 | 从属          | SHIR 将连接到的任何其他源的域。 |
  
> [!IMPORTANT]
> 在大多数环境中，还需要确认 DNS 的配置是否正确。 若要确认，可以使用 SHIR 计算机中的 nslookup 检查与上述每个域的连接。 每个 nslookup 应返回资源的 IP。 如果使用的是[专用终结点](catalog-private-link.md)，应返回专用 IP，而不是公共 IP。 如果未返回 IP，或者在使用专用终结点时返回了公共 IP，则需要解决 DNS/VNET 关联或专用终结点/VNET 对等互连。

## <a name="manage-a-self-hosted-integration-runtime"></a>管理自承载的集成运行时

可通过导航到“管理中心”的“集成运行时”，选择 IR，然后选择“编辑”，编辑自承载集成运行时。 你现在可以更新说明，复制密钥，或者重新生成新密钥。

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime.png" alt-text="编辑 IR。":::

:::image type="content" source="media/manage-integration-runtimes/edit-integration-runtime-settings.png" alt-text="编辑 IR 详细信息。":::

可通过导航到“管理中心”的“集成运行时”，选择“IR”，然后选择“删除”，删除自承载集成运行时。 一旦删除 IR，任何依赖于它的扫描都会失败。

## <a name="next-steps"></a>后续步骤

- [扫描如何检测已删除的资产](concept-scans-and-ingestion.md#how-scans-detect-deleted-assets)

- [对 Purview 使用专用终结点](catalog-private-link.md)
