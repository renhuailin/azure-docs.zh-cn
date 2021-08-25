---
title: 快速入门 - 从 Azure 门户中创建适用于 Apache Cassandra 群集的 Azure 托管实例
description: 本快速入门展示了如何使用 Azure 门户创建适用于 Apache Cassandra 群集的 Azure 托管实例。
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: quickstart
ms.date: 03/02/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 67f317594b257776a3a8ac8c257281352516bfee
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749472"
---
# <a name="quickstart-create-an-azure-managed-instance-for-apache-cassandra-cluster-from-the-azure-portal-preview"></a>快速入门：从 Azure 门户中创建适用于 Apache Cassandra 群集的 Azure 托管实例（预览版）
 
适用于 Apache Cassandra 的 Azure 托管实例为托管的开源 Apache Cassandra 数据中心提供自动部署和缩放操作，可以加速混合方案的部署并减少日常维护。

> [!IMPORTANT]
> Azure Managed Instance for Apache Cassandra 目前以公共预览版形式提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

本快速入门演示了如何使用 Azure 门户创建适用于 Apache Cassandra 群集的 Azure 托管实例。

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-managed-instance-cluster"></a><a id="create-account"></a>创建托管实例群集

1. 登录 [Azure 门户](https://portal.azure.com/)。

1. 从搜索栏中搜索“适用于 Apache Cassandra 的托管实例”，然后选择结果。

   :::image type="content" source="./media/create-cluster-portal/search-portal.png" alt-text="搜索“适用于 Apache Cassandra 的托管实例”。" lightbox="./media/create-cluster-portal/search-portal.png" border="true":::

1. 选择“创建适用于 Apache Cassandra 群集的托管实例”按钮。

   :::image type="content" source="./media/create-cluster-portal/create-cluster.png" alt-text="创建群集。" lightbox="./media/create-cluster-portal/create-cluster.png" border="true":::

1. 在“创建适用于 Apache Cassandra 的托管实例”窗格中，输入以下详细信息：

   * **订阅** - 从下拉列表中选择你的 Azure 订阅。
   * **资源组** - 指定是要创建新的资源组还是使用现有的资源组。 资源组是用于保存 Azure 解决方案相关资源的容器。 有关详细信息，请参阅 [Azure 资源组](../azure-resource-manager/management/overview.md)概述文章。
   * **群集名称** - 为群集输入名称。
   * **位置** - 要将群集部署到的位置。
   * **SKU** - 群集的 SKU 类型。
   * **节点数** - 群集中节点的数量。 这些节点充当数据的副本。
   * **初始 Cassandra 管理员密码** - 用于创建群集的密码。
   * **确认 Cassandra 管理员密码** - 重新输入你的密码。

    > [!NOTE]
    > 在公共预览版中，可以在美国东部、美国西部、美国东部 2、美国西部 2、美国中部、美国中南部、北欧、西欧、东南亚和澳大利亚东部区域中创建托管实例群集。

   :::image type="content" source="./media/create-cluster-portal/create-cluster-page.png" alt-text="填写“创建群集”窗体。" lightbox="./media/create-cluster-portal/create-cluster-page.png" border="true":::

1. 接下来，选择“网络”选项卡。

1. 在“网络”窗格中，选择虚拟网络名称和子网。 你可以选择现有的虚拟网络或创建一个新的虚拟网络。

   :::image type="content" source="./media/create-cluster-portal/networking.png" alt-text="配置网络详细信息。" lightbox="./media/create-cluster-portal/networking.png" border="true":::

    > [!NOTE]
    > 部署 Azure Managed Instance for Apache Cassandra 需要 Internet 访问权限。 在限制 Internet 访问权限的环境中，部署将失败。 请确保未在 VNet 中阻止对以下关键 Azure 服务（托管 Cassandra 需要这些服务才能正常工作）的访问权限：
    > - Azure 存储
    > - Azure KeyVault
    > - Azure 虚拟机规模集
    > - Azure 监视
    > - Azure Active Directory
    > - Azure 安全性

1. 如果你在上一步中创建了新的 VNet，请跳到步骤 8。 如果你选择了现有 VNet，则在创建群集之前，你需要对虚拟网络和子网应用某些特殊权限。 为此，请使用 `az role assignment create` 命令，将 `<subscription ID>`、`<resource group name>` 和 `<VNet name>` 替换为适当的值：

   ```azurecli-interactive
   az role assignment create --assignee a232010e-820c-4083-83bb-3ace5fc29d0b --role 4d97b98b-1d4f-4787-a291-c67834d212e7 --scope /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.Network/virtualNetworks/<VNet name>
   ```

   > [!NOTE]
   > 上一命令中的 `assignee` 和 `role` 值是固定值，请完全按照命令中的说明输入这些值。 如果不这样做，则会在创建群集时出错。 如果在执行此命令时遇到任何错误，则你可能没有运行此命令的权限，请与管理员联系以获取权限。

1. 现在，你已完成网络设置，请单击“查看 + 创建” > “创建” 

    > [!NOTE]
    > 创建群集最多可能需要 15 分钟。

   :::image type="content" source="./media/create-cluster-portal/review-create.png" alt-text="查看摘要以创建群集。" lightbox="./media/create-cluster-portal/review-create.png" border="true":::


1. 部署完成后，请检查你的资源组，看看新创建的托管实例群集：

   :::image type="content" source="./media/create-cluster-portal/managed-instance.png" alt-text="创建群集后的“概述”页。" lightbox="./media/create-cluster-portal/managed-instance.png" border="true":::

1. 若要浏览群集节点，请导航到你用来创建群集的“虚拟网络”窗格，并打开“概述”窗格以查看它们：

   :::image type="content" source="./media/create-cluster-portal/resources.png" alt-text="查看群集资源。" lightbox="./media/create-cluster-portal/resources.png" border="true":::


## <a name="connecting-to-your-cluster"></a>连接到你的群集

适用于 Apache Cassandra 的 Azure 托管实例不会创建具有公共 IP 地址的节点，因此，若要连接到新创建的 Cassandra 群集，你需要在 VNet 中创建另一个资源。 这可以是应用程序，也可以是安装了 Apache 的开源查询工具 [CQLSH](https://cassandra.apache.org/doc/latest/cassandra/tools/cqlsh.html) 的虚拟机。 你可以使用[模板](https://azure.microsoft.com/resources/templates/vm-simple-linux/)部署 Ubuntu 虚拟机。 部署后，请使用 SSH 连接到计算机，并使用以下命令安装 CQLSH：

```bash
# Install default-jre and default-jdk
sudo apt update
sudo apt install openjdk-8-jdk openjdk-8-jre

# Install the Cassandra libraries in order to get CQLSH:
echo "deb http://www.apache.org/dist/cassandra/debian 311x main" | sudo tee -a /etc/apt/sources.list.d/cassandra.sources.list
curl https://downloads.apache.org/cassandra/KEYS | sudo apt-key add -
sudo apt-get update
sudo apt-get install cassandra

# Export the SSL variables:
export SSL_VERSION=TLSv1_2
export SSL_VALIDATE=false

# Connect to CQLSH (replace <IP> with the private IP addresses of the nodes in your Datacenter):
host=("<IP>" "<IP>" "<IP>")
cqlsh $host 9042 -u cassandra -p cassandra --ssl
```

## <a name="troubleshooting"></a>疑难解答

如果在将权限应用到虚拟网络时遇到错误，如 *无法在e5007d2c-4b13-4a74-9b6a-605d99f03501”的图形数据库中找到用户或服务主体*，则可以在 Azure 门户中手动应用相同的权限。 要在门户中应用权限，请访问现有虚拟网络的“访问控制 (IAM)”窗格，并将“Azure Cosmos DB”的角色分配添加到“网络管理员”角色。 如果搜索“Azure Cosmos DB”时出现两个条目，请同时添加这两个条目，如下图所示： 

   :::image type="content" source="./media/create-cluster-cli/apply-permissions.png" alt-text="应用权限" lightbox="./media/create-cluster-cli/apply-permissions.png" border="true":::

> [!NOTE] 
> Azure Cosmos DB 角色分配仅用于部署目的。 Azure Managed Instanced for Apache Cassandra 对于 Azure Cosmos DB 不存在后端依赖关系。   

## <a name="clean-up-resources"></a>清理资源

如果不打算继续使用此托管实例群集，请按照以下步骤删除它：

1. 从 Azure 门户的左侧菜单中选择“资源组”。
1. 从列表中选择为本快速入门创建的资源组。
1. 在资源组的“概述”窗格上，选择“删除资源组” 。
1. 在下一窗口中输入要删除的资源组的名称，然后选择“删除”  。

## <a name="next-steps"></a>后续步骤

在本快速入门中，你已学习了如何使用 Azure 门户创建适用于 Apache Cassandra 群集的 Azure 托管实例。 你现在可以开始使用群集了：

> [!div class="nextstepaction"]
> [使用 Azure Databricks 部署托管的 Apache Spark 群集](deploy-cluster-databricks.md)
