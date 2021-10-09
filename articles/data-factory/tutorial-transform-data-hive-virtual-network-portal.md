---
title: 通过 Azure 门户在 Azure 虚拟网络中使用 Hive 转换数据
description: 本教程提供有关在 Azure 数据工厂中使用 Hive 活动转换数据的分步说明。
ms.service: data-factory
ms.subservice: tutorials
author: nabhishek
ms.author: abnarain
ms.topic: tutorial
ms.date: 06/07/2021
ms.openlocfilehash: f937d03691c21430f0055916b81e3a8963cbceba
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124769838"
---
# <a name="transform-data-in-azure-virtual-network-using-hive-activity-in-azure-data-factory-using-the-azure-portal"></a>通过 Azure 门户在 Azure 数据工厂中使用 Hive 活动转换 Azure 虚拟网络中的数据

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

本教程使用 Azure 门户创建一个数据工厂管道，该管道可以使用 HDInsight 群集上的 Hive 活动转换 Azure 虚拟网络 (VNet) 中的数据。 在本教程中执行以下步骤：

> [!div class="checklist"]
> * 创建数据工厂。 
> * 创建自承载 Integration Runtime
> * 创建 Azure 存储和 Azure HDInsight 链接服务
> * 使用 Hive 活动创建管道。
> * 触发管道运行。
> * 监视管道运行 
> * 验证输出

如果没有 Azure 订阅，请在开始之前创建一个[免费](https://azure.microsoft.com/free/)帐户。

## <a name="prerequisites"></a>先决条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure 存储帐户**。 创建 Hive 脚本并将其上传到 Azure 存储。 Hive 脚本的输出存储在此存储帐户中。 在本示例中，HDInsight 群集使用此 Azure 存储帐户作为主存储。 
- **Azure 虚拟网络**。 如果没有 Azure 虚拟网络，请遵照[这些说明](../virtual-network/quick-create-portal.md)创建虚拟网络。 在本示例中，HDInsight 位于 Azure 虚拟网络中。 下面是 Azure 虚拟网络的示例配置。 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/create-virtual-network.png" alt-text="创建虚拟网络":::
- **HDInsight 群集**。 创建一个 HDInsight 群集，并按照以下文章中所述，将该群集加入到在前一步骤中创建的虚拟网络：[使用 Azure 虚拟网络扩展 Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md)。 下面是虚拟网络中 HDInsight 的示例配置。 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/hdinsight-virtual-network-settings.png" alt-text="虚拟网络中的 HDInsight":::
- **Azure PowerShell**。 遵循[如何安装和配置 Azure PowerShell](/powershell/azure/install-Az-ps) 中的说明。
- **一个虚拟机**。 创建一个 Azure 虚拟机 (VM)，并将其加入到 HDInsight 群集所在的同一个虚拟网络。 有关详细信息，请参阅[如何创建虚拟机](../virtual-network/quick-create-portal.md#create-virtual-machines)。 

### <a name="upload-hive-script-to-your-blob-storage-account"></a>将 Hive 脚本上传到 Blob 存储帐户

1. 创建包含以下内容的名为 **hivescript.hql** 的 Hive SQL 文件：

   ```sql
   DROP TABLE IF EXISTS HiveSampleOut; 
   CREATE EXTERNAL TABLE HiveSampleOut (clientid string, market string, devicemodel string, state string)
   ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
   STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

   INSERT OVERWRITE TABLE HiveSampleOut
   Select 
       clientid,
       market,
       devicemodel,
       state
   FROM hivesampletable
   ```
2. 在 Azure Blob 存储中，创建名为 **adftutorial** 的容器（如果尚不存在）。
3. 创建名为 **hivescripts** 的文件夹。
4. 将 **hivescript.hql** 文件上传到 **hivescripts** 子文件夹。

## <a name="create-a-data-factory"></a>创建数据工厂

1. 启动 **Microsoft Edge** 或 **Google Chrome** Web 浏览器。 目前，仅 Microsoft Edge 和 Google Chrome Web 浏览器支持数据工厂 UI。
1. 登录到 [Azure 门户](https://portal.azure.com/)。    
2. 在左侧菜单中单击“新建”，并依次单击“数据 + 分析”、“数据工厂”。    
   
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-data-factory-menu.png" alt-text="“新建”->“数据工厂”":::
3. 在“新建数据工厂”页中，输入 **ADFTutorialHiveFactory** 作为 **名称**。 
      
     :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-azure-data-factory.png" alt-text="“新建数据工厂”页":::
 
   Azure 数据工厂的名称必须 **全局唯一**。 如果收到错误，请更改数据工厂的名称（例如改为 yournameMyAzureSsisDataFactory），并重新尝试创建。 有关数据工厂项目命名规则，请参阅[数据工厂 - 命名规则](naming-rules.md)一文。
  
    数据工厂名“MyAzureSsisDataFactory”不可用
3. 选择要在其中创建数据工厂的 Azure **订阅**。 
4. 对于 **资源组**，请执行以下步骤之一：
     
   - 选择“使用现有资源组”，并从下拉列表选择现有的资源组。  
   - 选择“新建”，并输入资源组的名称。   
         
     若要了解有关资源组的详细信息，请参阅 [使用资源组管理 Azure 资源](../azure-resource-manager/management/overview.md)。  
4. 选择“V2”作为“版本”。
5. 选择数据工厂的 **位置**。 列表中只会显示支持创建数据工厂的位置。
6. 选择“固定到仪表板”。     
7. 单击“创建”。
8. 在仪表板上，你会看状态如下的以下磁贴：“正在部署数据工厂”。 

     :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/deploying-data-factory.png" alt-text="“正在部署数据工厂”磁贴":::
9. 创建完成后，可以看到图中所示的“数据工厂”页。
   
    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/data-factory-home-page.png" alt-text="数据工厂主页":::
10. 单击“创作和监视”，在单独的选项卡中启动数据工厂用户界面 (UI)。
11. 在主页中，切换到左侧面板中的“管理”选项卡，如下图所示： 

    :::image type="content" source="media/doc-common-process/get-started-page-manage-button.png" alt-text="此屏幕截图显示了“管理”选项卡。":::

## <a name="create-a-self-hosted-integration-runtime"></a>创建自承载 Integration Runtime
由于 Hadoop 群集位于虚拟网络中，因此需要在同一个虚拟网络中安装自承载集成运行时 (IR)。 在本部分，我们将创建一个新的 VM、将其加入到同一个虚拟网络，然后在其上安装自承载 IR。 数据工厂服务可以使用自承载 IR 将处理请求分发到虚拟网络中的某个计算服务，例如 HDInsight。 自承载 IR 还可用于将数据移入/移出虚拟网络中的数据存储，以及移到 Azure。 当数据存储或计算资源位于本地环境中时，也可以使用自承载 IR。 

1. 在 Azure 数据工厂 UI 中，单击窗口底部的“链接”，切换到“集成运行时”选项卡，然后单击工具栏上的“+ 新建”按钮。   

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-integration-runtime-menu.png" alt-text="新建集成运行时菜单":::
2. 在“集成运行时安装”窗口中，选择“执行数据移动并将活动分发到外部计算”选项，然后单击“下一步”。   

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-perform-data-movement-compute-option.png" alt-text="选择执行数据移动并分发活动选项":::
3. 选择“专用网络”，单击“下一步”。 
    
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-private-network.png" alt-text="选择专用网络":::
4. 在“名称”中输入 **MySelfHostedIR**，然后单击“下一步”。  

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/integration-runtime-name.png" alt-text="指定集成运行时名称"::: 
5. 单击复制按钮复制集成运行时的 **身份验证密钥**，并将其保存。 使窗口保持打开。 稍后将要使用此密钥注册虚拟机中安装的 IR。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/copy-key.png" alt-text="复制身份验证密钥":::

### <a name="install-ir-on-a-virtual-machine"></a>在虚拟机上安装 IR

1. 在该 Azure VM 上，下载[自我托管的集成运行时](https://www.microsoft.com/download/details.aspx?id=39717)。 使用上一步骤中获取的 **身份验证密钥** 手动注册自承载集成运行时。 

    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/register-integration-runtime.png" alt-text="注册集成运行时":::

2. 成功注册自承载集成运行时后，会看到以下消息： 
   
    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/registered-successfully.png" alt-text="已成功注册":::
3. 单击“启动配置管理器”。 将节点连接到云服务后，会看到以下页： 
   
    :::image type="content" source="media/tutorial-transform-data-using-hive-in-vnet-portal/node-is-connected.png" alt-text="节点已连接":::

### <a name="self-hosted-ir-in-the-azure-data-factory-ui"></a>Azure 数据工厂 UI 中的自承载 IR

1. 在 **Azure 数据工厂 UI** 中，应会看到自承载 VM 的名称及其状态。

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/existing-self-hosted-nodes.png" alt-text="现有的自承载节点":::
2. 单击“完成”关闭“集成运行时安装”窗口。  集成运行时列表中会显示该自承载 IR。

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/self-hosted-ir-in-list.png" alt-text="列表中的自承载 IR":::


## <a name="create-linked-services"></a>创建链接服务

在本部分中创作并部署两个链接服务：
- 一个用于将 Azure 存储帐户链接到数据工厂的 **Azure 存储链接服务**。 此存储是 HDInsight 群集使用的主存储。 在本例中，我们将使用此 Azure 存储帐户来存储 Hive 脚本以及该脚本的输出。
- 一个 **HDInsight 链接服务**。 Azure 数据工厂将 Hive 脚本提交到此 HDInsight 群集以供执行。

### <a name="create-azure-storage-linked-service"></a>创建 Azure 存储链接服务

1. 切换到“链接的服务”选项卡，单击“新建”。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png" alt-text="“新建链接服务”按钮":::    
2. 在“新建链接服务”窗口中，选择“Azure Blob 存储”，然后单击“继续”。 

   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-azure-storage.png" alt-text="选择“Azure Blob 存储”":::
3. 在“新建链接服务”窗口中执行以下步骤：

    1. 输入 **AzureStorageLinkedService** 作为 **名称**。
    2. 为“通过集成运行时连接”选择“MySelfHostedIR”。 
    3. 对于“存储帐户名称”，请选择自己的 Azure 存储帐户。 
    4. 若要测试与存储帐户的连接，请单击“测试连接”。
    5. 单击“保存”  。
   
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-storage-account.png" alt-text="指定 Azure Blob 存储帐户":::

### <a name="create-hdinsight-linked-service"></a>创建 HDInsight 链接的服务

1. 再次单击“新建”以创建另一个链接服务。 
    
   :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-linked-service.png" alt-text="“新建链接服务”按钮":::    
2. 切换到“计算”选项卡，选择“Azure HDInsight”，然后单击“继续”。  

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight.png" alt-text="选择“Azure HDInsight”":::
3. 在“新建链接服务”窗口中执行以下步骤：

    1. 在“名称”中输入 **AzureHDInsightLinkedService**。
    2. 选择“自带 HDInsight”。 
    3. 对于“HDI 群集”，请选择自己的 HDInsight 群集。 
    4. 输入 HDInsight 群集的 **用户名**。
    5. 输入该用户的 **密码**。 
    
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/specify-azure-hdinsight.png" alt-text="Azure HDInsight 设置":::

本文假设你有权通过 Internet 访问该群集。 例如，可以通过 `https://clustername.azurehdinsight.net` 连接到该群集。 此地址使用公共网关。如果已使用网络安全组 (NSG) 或用户定义的路由 (UDR) 限制了从 Internet 的访问，则该网关不可用。 要使数据工厂能够将作业提交到 Azure 虚拟网络中的 HDInsight 群集，需要相应地配置 Azure 虚拟网络，使 URL 可解析成 HDInsight 所用的网关的专用 IP 地址。

1. 在 Azure 门户中，打开 HDInsight 所在的虚拟网络。 打开名称以 `nic-gateway-0` 开头的网络接口。 记下其专用 IP 地址。 例如 10.6.0.15。 
2. 如果 Azure 虚拟网络包含 DNS 服务器，请更新 DNS 记录，使 HDInsight 群集 URL `https://<clustername>.azurehdinsight.net` 可解析成 `10.6.0.15`。 如果 Azure 虚拟网络中没有 DNS 服务器，可以通过编辑已注册为自承载集成运行时节点的所有 VM 的 hosts 文件 (C:\Windows\System32\drivers\etc) 并添加如下所示的条目，来暂时解决此问题： 

    `10.6.0.15 myHDIClusterName.azurehdinsight.net`

## <a name="create-a-pipeline"></a>创建管道 
本步骤创建包含 Hive 活动的新管道。 该活动执行 Hive 脚本来返回示例表中的数据，并将其保存到定义的路径。

请注意以下几点：

- **scriptPath** 指向用于 MyStorageLinkedService 的 Azure 存储帐户中的 Hive 脚本路径。 此路径区分大小写。
- **Output** 是 Hive 脚本中使用的参数。 使用 `wasbs://<Container>@<StorageAccount>.blob.core.windows.net/outputfolder/` 格式指向 Azure 存储中的现有文件夹。 此路径区分大小写。 

1. 在数据工厂 UI 中，单击左窗格中的“+”（加号），然后单击“管道”。  

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/new-pipeline-menu.png" alt-text="“新建管道”菜单":::
2. 在“活动”工具箱中展开“HDInsight”，将“Hive”活动拖放到管道设计器图面。   

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/drag-drop-hive-activity.png" alt-text="拖放 Hive 活动":::
3. 在属性窗口中切换到“HDI 群集”选项卡，然后为“HDInsight 链接服务”选择“AzureHDInsightLinkedService”。  

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/select-hdinsight-linked-service.png" alt-text="选择“HDInsight 链接服务”":::
4. 切换到“脚本”选项卡，然后执行以下步骤： 

    1. 为“脚本链接服务”选择“AzureStorageLinkedService”。  
    2. 对于“文件路径”，请单击“浏览存储”。  
 
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/browse-storage-hive-script.png" alt-text="浏览存储":::
    3. 在“选择文件或文件夹”窗口中导航到 **adftutorial** 容器中的 **hivescripts** 文件夹，选择 **hivescript.hql**，然后单击“完成”。   
        
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/choose-file-folder.png" alt-text="选择文件或文件夹"::: 
    4. 确认“文件路径”中显示了 **adftutorial/hivescripts/hivescript.hql**。

        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/confirm-hive-script-settings.png" alt-text="脚本设置":::
    5. 在“脚本”选项卡中，展开“高级”部分。  
    6. 单击“参数”对应的“从脚本自动填充”。  
    7. 使用以下格式输入“输出”参数的值：`wasbs://<Blob Container>@<StorageAccount>.blob.core.windows.net/outputfolder/`。 例如：`wasbs://adftutorial@mystorageaccount.blob.core.windows.net/outputfolder/`。
 
        :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/script-arguments.png" alt-text="脚本参数":::
1. 若要将项目发布到数据工厂，请单击“发布”。

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/publish.png" alt-text="屏幕截图显示了用于发布到数据工厂的选项。":::

## <a name="trigger-a-pipeline-run"></a>触发管道运行

1. 首先，请单击工具栏中的“验证”按钮来验证管道。 单击 **右箭头 (>>)** 关闭“管道验证输出”窗口。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/validate-pipeline.png" alt-text="验证管道"::: 
2. 若要触发某个管道运行，请在工具栏中单击“触发器”，然后单击“立即触发”。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/trigger-now-menu.png" alt-text="立即触发":::

## <a name="monitor-the-pipeline-run"></a>监视管道运行

1. 在左侧切换到“监视”选项卡。 “管道运行”列表中会显示一个管道运行。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/monitor-pipeline-runs.png" alt-text="监视管道运行":::
2. 若要刷新列表，请单击“刷新”。
4. 若要查看与管道运行相关联的活动运行，请单击“操作”列中的“查看活动运行”。  其他操作链接用于停止/重新运行管道。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs-link.png" alt-text="查看活动运行":::
5. 只能看到一个活动运行，因为该管道中只包含一个 **HDInsightHive** 类型的活动。 若要切换回到上一视图，请单击顶部的“管道”链接。

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/view-activity-runs.png" alt-text="活动运行":::
6. 确认可以在 **adftutorial** 容器的 **outputfolder** 中看到输出文件。 

    :::image type="content" source="./media/tutorial-transform-data-using-hive-in-vnet-portal/output-file.png" alt-text="输出文件":::

## <a name="next-steps"></a>后续步骤
已在本教程中执行了以下步骤： 

> [!div class="checklist"]
> * 创建数据工厂。 
> * 创建自承载 Integration Runtime
> * 创建 Azure 存储和 Azure HDInsight 链接服务
> * 使用 Hive 活动创建管道。
> * 触发管道运行。
> * 监视管道运行 
> * 验证输出

请转到下一篇教程，了解如何在 Azure 上使用 Spark 群集转换数据：

> [!div class="nextstepaction"]
>[分支和链接数据工厂控制流](tutorial-control-flow-portal.md)