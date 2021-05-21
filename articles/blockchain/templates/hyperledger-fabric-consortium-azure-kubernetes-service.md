---
title: 在 Azure Kubernetes 服务上部署 Hyperledger Fabric 联盟
description: 如何在 Azure Kubernetes 服务上部署和配置 Hyperledger Fabric 联盟网络
ms.date: 03/01/2021
ms.topic: how-to
ms.reviewer: ravastra
ms.custom: contperf-fy21q3
ms.openlocfilehash: 42d16adbc5e6396c8d5d38176ac7681c712f4555
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102101097"
---
# <a name="deploy-hyperledger-fabric-consortium-on-azure-kubernetes-service"></a>在 Azure Kubernetes 服务上部署 Hyperledger Fabric 联盟

可以使用“Azure Kubernetes 服务 (AKS) 上的 Hyperledger Fabric”模板在 Azure 上部署和配置 Hyperledger Fabric 联盟网络。

阅读本文后，可以：

- 对 Hyperledger Fabric 以及构成 Hyperledger Fabric 区块链网络构建基块的组件有一定的了解。
- 知道如何在 Azure Kubernetes 服务上为生产方案部署和配置 Hyperledger Fabric 联盟网络。

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="choose-an-azure-blockchain-solution"></a>选择 Azure 区块链解决方案

在选择使用解决方案模板之前，请将你的应用场景与可用 Azure 区块链选项的常见用例进行比较：

选项 | 服务模型 | 常见用例
-------|---------------|-----------------
解决方案模板 | IaaS | 解决方案模板是一种 Azure 资源管理器模板，可用于预配完全配置的区块链网络拓扑。 此类模板可为区块链网络类型部署和配置 Microsoft Azure 计算、网络和存储服务。 解决方案模板在提供时不附带服务级别协议。 使用 [Microsoft Q&A 页面](/answers/topics/azure-blockchain-workbench.html)获取支持。
[Azure 区块链服务](../service/overview.md) | PaaS | Azure 区块链服务预览版简化了联盟区块链网络的构成、管理和监管。 将 Azure 区块链服务用于需要 PaaS、联盟管理或合同和事务隐私的解决方案。
[Azure Blockchain Workbench](../workbench/overview.md) | IaaS 和 PaaS | Azure Blockchain Workbench 预览版是 Azure 服务和功能的集合，旨在帮助创建和部署区块链应用程序，以便与其他组织共享业务流程和数据。 Azure Blockchain Workbench 用于为区块链解决方案或区块链应用程序概念证明设计原型。 Azure Blockchain Workbench 在提供时不附带服务级别协议。 使用 [Microsoft Q&A 页面](/answers/topics/azure-blockchain-workbench.html)获取支持。

## <a name="deploy-the-orderer-and-peer-organization"></a>部署 Orderer 和对等组织

开始之前，需要一个能够支持部署多个虚拟机和标准存储帐户的 Azure 订阅。 如果没有 Azure 订阅，可以[创建一个免费 Azure 帐户](https://azure.microsoft.com/free/)。

若要开始部署 Hyperledger Fabric 网络组件，请转到 [Azure 门户](https://portal.azure.com)。

1. 选择“创建资源” > “区块链”，然后搜索“Azure Kubernetes 服务上的 Hyperledger Fabric (预览版)”。

2. 在“基本信息”选项卡上输入项目详细信息。

    ![该屏幕截图显示了“基本信息”选项卡。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-basics.png)

3. 输入以下详细信息：
    - **订阅**：选择要在其中部署 Hyperledger Fabric 网络组件的订阅名称。
    - **资源组**：创建新的资源组或选择现有的空资源组。 资源组将保存作为模板的一部分部署的所有资源。
    - **区域**：选择要为 Hyperledger Fabric 组件部署 Azure Kubernetes 服务群集的 Azure 区域。 该模板在所有提供 AKS 的区域均可用。 请选择订阅未达到虚拟机 (VM) 配额限制的区域。
    - **资源前缀**：输入用于为已部署资源命名的前缀。 它必须少于六个字符，并且字符组合必须同时包含数字和字母。
4. 选择“Fabric 设置”选项卡以定义将要部署的 Hyperledger Fabric 网络组件。

    ![该屏幕截图显示了“Fabric 设置”选项卡。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-settings.png)

5. 输入以下详细信息：
    - **组织名称**：输入 Hyperledger Fabric 组织的名称，该名称是执行各种数据平面操作所必需的。 每个部署的组织名称必须唯一。
    - **Fabric 网络组件**：根据要设置的区块链网络组件，选择“排序服务”或“对等节点”。
    - **节点数**：下面列出了两种类型的节点：
        - **排序服务**：负责分类帐中的事务排序的节点。 选择要为网络提供容错的节点数。 支持的排序节点计数为 3、5 和 7。
        - **对等节点**：托管分类帐和智能合同的节点。 可以根据需要选择 1 到 10 个节点。
    - **对等节点 World State 数据库**：对等节点的 World State 数据库。 LevelDB 是对等节点中嵌入的默认状态数据库。 它将 chaincode 数据存储为简单的键/值对，并且仅支持键、键范围和组合键查询。 CouchDB 是可选的备用状态数据库，支持在将 chaincode 数据值建模为 JSON 时进行丰富的查询。 在“Fabric 网络组件”下拉列表中选择“对等节点”时，将显示此字段。
    - **Fabric CA 用户名**：Fabric 证书颁发机构允许你初始化并启动托管证书颁发机构的服务器进程。 它可用于管理标识和证书。 默认情况下，作为模板一部分部署的每个 AKS 群集都有一个 Fabric CA Pod。 输入用于 Fabric CA 身份验证的用户名。
    - **Fabric CA 密码**：输入用于 Fabric CA 身份验证的密码。
    - **确认密码**：确认 Fabric CA 密码。
    - **证书**：如果要使用自己的根证书来初始化 Fabric CA，请选择“为 Fabric CA 上传根证书”选项。 否则，Fabric CA 会默认创建自签名证书。
    - **根证书**：上传需要用来初始化 Fabric CA 的根证书（公钥）。 支持 .pem 格式的证书。 证书应该有效并且在 UTC 时区中。
    - **根证书私钥**：上传根证书的私钥。 如果拥有 .pem 证书（包含组合的公钥和私钥），也请在此处上传。


6. 选择“AKS 群集设置”选项卡以定义 Azure Kubernetes 服务群集配置。 AKS 群集配置了各种 Pod，用于运行 Hyperledger Fabric 网络组件。 部署的 Azure 资源包括：

    - **Fabric 工具**：负责配置 Hyperledger Fabric 组件的工具。
    - **Orderer/对等 Pod**：Hyperledger Fabric 网络的节点。
    - **代理**：一种 NGNIX 代理 Pod，客户端应用程序可以通过它与 AKS 群集通信。
    - **Fabric CA**：运行 Fabric CA 的 Pod。
    - **PostgreSQL**：维护 Fabric CA 标识的数据库实例。
    - **Key Vault**：Azure Key Vault 服务的实例，部署该实例是为了保存 Fabric CA 凭据和客户提供的根证书。 尝试重新部署模板时，可以使用该 Vault 来处理模板的各种机制。
    - **托管磁盘**：Azure 托管磁盘服务的实例，用于为分类帐和对等节点的 World State 数据库提供持久存储。
    - **公共 IP**：部署的 AKS 群集的终结点，用于与群集通信。

    输入以下详细信息： 

    ![该屏幕截图显示了 AKS 群集设置选项卡。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/create-for-hyperledger-fabric-aks-cluster-settings-1.png)

    - **Kubernetes 群集名称**：必要时，可更改 AKS 群集的名称。 此字段根据提供的资源前缀预先填充。
    - **Kubernetes 版本**：选择将在群集上部署的 Kubernetes 版本。 根据你在“基本信息”选项卡上选择的区域，可用的受支持版本可能会发生变化。
    - **DNS 前缀**：输入 AKS 群集的域名系统 (DNS) 名称前缀。 创建群集后，在管理容器时将使用 DNS 连接到 Kubernetes API。
    - **节点大小**：对于 Kubernetes 节点的大小，可以从 Azure 上可用的 VM 库存单位 (SKU) 列表中进行选择。 为了获得最佳性能，建议使用标准 DS3 v2。
    - **节点计数**：输入要在群集中部署的 Kubernetes 节点数。 建议将此节点计数保持为等于或大于“Fabric 设置”选项卡上指定的 Hyperledger Fabric 节点数量。
    - **服务主体客户端 ID**：输入现有服务主体的客户端 ID 或创建一个新的服务主体。 AKS 身份验证需要服务主体。 请参阅[创建服务主体的步骤](/powershell/azure/create-azure-service-principal-azureps#create-a-service-principal)。
    - **服务主体客户端密码**：输入在服务主体的客户端 ID 中提供的服务主体的客户端密码。
    - **确认客户端密码**：确认服务主体的客户端密码。
    - **启用容器监视**：选择启用 AKS 监视，使 AKS 日志推送到指定的 Log Analytics 工作区。
    - **Log Analytics 工作区**：如果启用了监视，系统将使用创建的默认工作区填充 Log Analytics 工作区。

8. 选择“查看并创建”选项卡。此步骤将触发对你提供的值的验证。
9. 通过验证后，选择“创建”。

    部署通常需要 10 到 12 分钟。 根据指定的 AKS 节点大小和数量，时间可能会有所不同。
10. 成功部署后，系统会通过右上角的 Azure 通知通知你。
11. 选择“转到资源组”，以检查在模板部署过程中创建的所有资源。 所有资源名称都将以“基本信息”选项卡上提供的前缀开头。

## <a name="build-the-consortium"></a>建立联盟

若要在部署排序服务和对等节点后建立区块链联盟，请依次执行以下步骤。 Azure Hyperledger Fabric 脚本 (azhlf) 可帮助你设置联盟、创建通道和执行 chaincode 操作。

> [!NOTE]
> Azure Hyperledger Fabric (azhlf) 脚本已更新，可提供更多功能。 如果要引用旧脚本，请参阅 [GitHub 上的自述文件](https://github.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/blob/master/consortiumScripts/README.md)。 此脚本与“Azure Kubernetes 服务上的 Hyperledger Fabric”模板 2.0.0 及更高版本兼容。 若要检查部署的版本，请遵循[故障排除](#troubleshoot)中的步骤。

> [!NOTE]
> 此脚本仅用于帮助演示、开发和测试方案。 此脚本创建的通道和联盟具有基本的 Hyperledger Fabric 策略，以简化演示、开发和测试方案。 对于生产设置，建议根据组织的合规性需求，使用本机 Hyperledger Fabric API 来更新通道/联盟 Hyperledger Fabric 策略。


可以通过 Azure Bash 命令行接口 (CLI) 执行所有用于运行 Azure Hyperledger Fabric 脚本的命令。 你可以通过 Azure 门户右上角的![“Azure Kubernetes 服务上的 Hyperledger Fabric”模板](./media/hyperledger-fabric-consortium-azure-kubernetes-service/arrow.png) 选项登录到 Azure Cloud Shell。 在命令提示符下键入 `bash`，并选择 Enter 键切换到 Bash CLI，或从 Cloud Shell 工具栏中选择“Bash”。

有关详细信息，请参阅 [Azure Cloud Shell](../../cloud-shell/overview.md)。

![该屏幕截图显示了 Azure Cloud Shell 中的命令。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/hyperledger-powershell.png)


下图显示了在 Orderer 组织和对等组织之间建立联盟的分步过程。 以下部分介绍了用于完成这些步骤的详细命令。

![建立联盟的过程图。](./media/hyperledger-fabric-consortium-azure-kubernetes-service/process-to-build-consortium-flow-chart.png)

完成初始设置后，使用客户端应用程序执行以下操作：  

- 通道管理
- 联盟管理
- chaincode 管理

### <a name="download-client-application-files"></a>下载客户端应用程序文件

第一个设置是下载客户端应用程序文件。 运行以下命令以下载所有必需的文件和包：

```bash-interactive
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/azhlfToolSetup.sh | bash
cd azhlfTool
npm install
npm run setup
```

这些命令将从公共 GitHub 存储库中克隆 Azure Hyperledger Fabric 客户端应用程序代码，然后加载所有相关的 npm 包。 成功执行命令后，你可以在当前目录中看到一个 node_modules 文件夹。 所有必需的包都已加载到 node_modules 文件夹中。

### <a name="set-up-environment-variables"></a>设置环境变量

所有环境变量都遵循 Azure 资源命名约定。

#### <a name="set-environment-variables-for-the-orderer-organizations-client"></a>为 Orderer 组织的客户端设置环境变量

```bash
ORDERER_ORG_SUBSCRIPTION=<ordererOrgSubscription>
ORDERER_ORG_RESOURCE_GROUP=<ordererOrgResourceGroup>
ORDERER_ORG_NAME=<ordererOrgName>
ORDERER_ADMIN_IDENTITY="admin.$ORDERER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

#### <a name="set-environment-variables-for-the-peer-organizations-client"></a>为对等组织的客户端设置环境变量

```bash
PEER_ORG_SUBSCRIPTION=<peerOrgSubscritpion>
PEER_ORG_RESOURCE_GROUP=<peerOrgResourceGroup>
PEER_ORG_NAME=<peerOrgName>
PEER_ADMIN_IDENTITY="admin.$PEER_ORG_NAME"
CHANNEL_NAME=<channelName>
```

根据联盟中对等组织的数量，你可能需要重复执行对等命令并相应地设置环境变量。

#### <a name="set-environment-variables-for-an-azure-storage-account"></a>为 Azure 存储帐户设置环境变量

```bash
STORAGE_SUBSCRIPTION=<subscriptionId>
STORAGE_RESOURCE_GROUP=<azureFileShareResourceGroup>
STORAGE_ACCOUNT=<azureStorageAccountName>
STORAGE_LOCATION=<azureStorageAccountLocation>
STORAGE_FILE_SHARE=<azureFileShareName>
```

使用以下命令创建一个 Azure 存储帐户。 如果已有 Azure 存储帐户，请跳过此步骤。

```bash
az account set --subscription $STORAGE_SUBSCRIPTION
az group create -l $STORAGE_LOCATION -n $STORAGE_RESOURCE_GROUP
az storage account create -n $STORAGE_ACCOUNT -g  $STORAGE_RESOURCE_GROUP -l $STORAGE_LOCATION --sku Standard_LRS
```

使用以下命令在 Azure 存储帐户中创建文件共享。 如果已有文件共享，请跳过此步骤。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
az storage share create  --account-name $STORAGE_ACCOUNT  --account-key $STORAGE_KEY  --name $STORAGE_FILE_SHARE
```

使用以下命令为 Azure 文件共享生成连接字符串。

```bash
STORAGE_KEY=$(az storage account keys list --resource-group $STORAGE_RESOURCE_GROUP  --account-name $STORAGE_ACCOUNT --query "[0].value" | tr -d '"')
SAS_TOKEN=$(az storage account generate-sas --account-key $STORAGE_KEY --account-name $STORAGE_ACCOUNT --expiry `date -u -d "1 day" '+%Y-%m-%dT%H:%MZ'` --https-only --permissions lruwd --resource-types sco --services f | tr -d '"')
AZURE_FILE_CONNECTION_STRING=https://$STORAGE_ACCOUNT.file.core.windows.net/$STORAGE_FILE_SHARE?$SAS_TOKEN

```

### <a name="import-an-organization-connection-profile-admin-user-identity-and-msp"></a>导入组织连接配置文件、管理员用户标识和 MSP

使用以下命令从 Azure Kubernetes 服务群集中提取组织的连接配置文件、管理员用户标识和托管服务提供商 (MSP)，并将这些标识存储在客户端应用程序的本地存储中。 azhlfTool/stores 目录是本地存储的一个示例。

对于 Orderer 组织：

```bash
./azhlf adminProfile import fromAzure -o $ORDERER_ORG_NAME -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION
./azhlf connectionProfile import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME   
./azhlf msp import fromAzure -g $ORDERER_ORG_RESOURCE_GROUP -s $ORDERER_ORG_SUBSCRIPTION -o $ORDERER_ORG_NAME
```

对于对等组织：

```bash
./azhlf adminProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf connectionProfile import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
./azhlf msp import fromAzure -g $PEER_ORG_RESOURCE_GROUP -s $PEER_ORG_SUBSCRIPTION -o $PEER_ORG_NAME
```

### <a name="create-a-channel"></a>创建频道

在 Orderer 组织的客户端中，使用以下命令创建仅包含 Orderer 组织的通道。  

```bash
./azhlf channel create -c $CHANNEL_NAME -u $ORDERER_ADMIN_IDENTITY -o $ORDERER_ORG_NAME
```

### <a name="add-a-peer-organization-for-consortium-management"></a>添加对等组织以进行联盟管理

>[!NOTE]
> 在开始进行任何联盟操作之前，请确保已完成客户端应用程序的初始设置。  

按给定顺序运行以下命令，以在通道和联盟中添加对等组织： 

1.  在对等组织的客户端中，将对等组织的 MSP 上传到 Azure 存储。

      ```bash
      ./azhlf msp export toAzureStorage -f  $AZURE_FILE_CONNECTION_STRING -o $PEER_ORG_NAME
      ```
2.  在 Orderer 组织的客户端中，从 Azure 存储下载对等组织的 MSP。 然后发出命令，将对等组织添加到通道和联盟中。

      ```bash
      ./azhlf msp import fromAzureStorage -o $PEER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel join -c  $CHANNEL_NAME -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ./azhlf consortium join -o $ORDERER_ORG_NAME  -u $ORDERER_ADMIN_IDENTITY -p $PEER_ORG_NAME
      ```

3.  在 Orderer 组织的客户端中，将 Orderer 的连接配置文件上传到 Azure 存储，以便对等组织可以使用此连接配置文件连接到 Orderer 节点。

      ```bash
      ./azhlf connectionProfile  export toAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ```

4.  在对等组织的客户端中，从 Azure 存储下载 Orderer 的连接配置文件。 然后运行命令，将对等节点添加到通道中。

      ```bash
      ./azhlf connectionProfile  import fromAzureStorage -o $ORDERER_ORG_NAME -f $AZURE_FILE_CONNECTION_STRING
      ./azhlf channel joinPeerNodes -o $PEER_ORG_NAME  -u $PEER_ADMIN_IDENTITY -c $CHANNEL_NAME --ordererOrg $ORDERER_ORG_NAME
      ```

同样，若要在通道中添加更多对等组织，请根据所需的对等组织更新对等环境变量，然后重做步骤 1 至 4。

### <a name="set-anchor-peers"></a>设置定位对等节点

在对等组织的客户端中运行以下命令，以在指定通道中为对等组织设置定位对等节点。

>[!NOTE]
> 在运行此命令之前，请确保使用联盟管理命令在通道中添加对等组织。

```bash
./azhlf channel setAnchorPeers -c $CHANNEL_NAME -p <anchorPeersList> -o $PEER_ORG_NAME -u $PEER_ADMIN_IDENTITY --ordererOrg $ORDERER_ORG_NAME
```

`<anchorPeersList>` 是要设置为定位对等节点的以空格分隔的对等节点列表。 例如：

  - 如果只想将 peer1 节点设置为定位对等节点，则将 `<anchorPeersList>` 设置为 `"peer1"`。
  - 如果要将 peer1 和 peer3 节点都设置为定位对等节点，则将 `<anchorPeersList>` 设置为 `"peer1" "peer3"`。

## <a name="chaincode-management-commands"></a>chaincode 管理命令

>[!NOTE]
> 在开始进行任何 chaincode 操作之前，请确保已完成客户端应用程序的初始设置。  

### <a name="set-the-chaincode-specific-environment-variables"></a>设置特定于 chaincode 的环境变量

```bash
# Peer organization name where the chaincode operation will be performed
ORGNAME=<PeerOrgName>
USER_IDENTITY="admin.$ORGNAME"  
# If you are using chaincode_example02 then set CC_NAME=“chaincode_example02”
CC_NAME=<chaincodeName>  
# If you are using chaincode_example02 then set CC_VERSION=“1” for validation
CC_VERSION=<chaincodeVersion>
# Language in which chaincode is written. Supported languages are 'node', 'golang', and 'java'  
# Default value is 'golang'  
CC_LANG=<chaincodeLanguage>  
# CC_PATH contains the path where your chaincode is placed. This is the absolute path to the chaincode project root directory.
# If you are using chaincode_example02 to validate then CC_PATH=“/home/<username>/azhlfTool/samples/chaincode/src/chaincode_example02/go”
CC_PATH=<chaincodePath>  
# Channel on which chaincode will be instantiated/invoked/queried  
CHANNEL_NAME=<channelName>  
```

### <a name="install-chaincode"></a>安装 chaincode  

运行以下命令以在对等组织上安装 chaincode。  

```bash
./azhlf chaincode install -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -p $CC_PATH -l $CC_LANG -v $CC_VERSION  

```
该命令将在 `ORGNAME` 环境变量中设置的对等组织的所有对等节点上安装 chaincode。 如果通道中有两个或更多对等组织，并且你想在所有对等组织上安装 chaincode，请为每个对等组织单独运行此命令。  

按照以下步骤操作：  

1.  根据 `peerOrg1` 设置 `ORGNAME` 和 `USER_IDENTITY`，并运行 `./azhlf chaincode install` 命令。  
2.  根据 `peerOrg2` 设置 `ORGNAME` 和 `USER_IDENTITY`，并运行 `./azhlf chaincode install` 命令。  

### <a name="instantiate-chaincode"></a>实例化 chaincode  

在对等客户端应用程序中，运行以下命令以实例化通道上的 chaincode。  

```bash
./azhlf chaincode instantiate -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -v $CC_VERSION -c $CHANNEL_NAME -f <instantiateFunc> --args <instantiateFuncArgs>
```

分别在 `<instantiateFunc>` 和 `<instantiateFuncArgs>` 中传递实例化函数名称和以空格分隔的参数列表。 例如，若要实例化 chaincode_example02.go chaincode，请将 `<instantiateFunc>` 设置为 `init`，将 `<instantiateFuncArgs>` 设置为 `"a" "2000" "b" "1000"`。

你还可以使用 `--collections-config` 标志传递集合的配置 JSON 文件。 或者，在实例化用于专用事务的 chaincode 时，使用 `-t` 标志来设置暂时性参数。

例如：

```bash
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath>
./azhlf chaincode instantiate -c $CHANNEL_NAME -n $CC_NAME -v $CC_VERSION -o $ORGNAME -u $USER_IDENTITY --collections-config <collectionsConfigJSONFilePath> -t <transientArgs>
```

`<collectionConfigJSONFilePath>` 部分是 JSON 文件的路径，该文件包含为实例化专用数据 chaincode 而定义的集合。 你可以在相对于 azhlfTool 目录的以下路径中找到示例集合的配置 JSON 文件：`./samples/chaincode/src/private_marbles/collections_config.json`。
以字符串格式将 `<transientArgs>` 作为有效 JSON 传递。 对所有特殊字符进行转义。 例如：`'{\\\"asset\":{\\\"name\\\":\\\"asset1\\\",\\\"price\\\":99}}'`

> [!NOTE]
> 从通道中的任一对等组织运行一次命令。 将事务成功提交到 Orderer 后，Orderer 会将该事务分发给通道中的所有对等组织。 然后，在通道中所有对等组织的所有对等节点上实例化 chaincode。  

### <a name="invoke-chaincode"></a>调用 chaincode  

在对等组织的客户端中，运行以下命令以调用 chaincode 函数：  

```bash
./azhlf chaincode invoke -o $ORGNAME -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <invokeFunc> -a <invokeFuncArgs>  
```

分别在 `<invokeFunction>` 和 `<invokeFuncArgs>` 中传递调用函数名称和以空格分隔的参数列表。 继续以 chaincode_example02.go chaincode 为例，若要执行调用操作，请将 `<invokeFunction>` 设置为 `invoke` ，将 `<invokeFuncArgs>` 设置为 `"a" "b" "10"`。  

>[!NOTE]
> 从通道中的任一对等组织运行一次命令。 将事务成功提交到 Orderer 后，Orderer 会将该事务分发给通道中的所有对等组织。 然后，在通道中所有对等组织的所有对等节点上更新 World State。  


### <a name="query-chaincode"></a>查询 chaincode  

运行以下命令以查询 chaincode：  

```bash
./azhlf chaincode query -o $ORGNAME -p <endorsingPeers> -u $USER_IDENTITY -n $CC_NAME -c $CHANNEL_NAME -f <queryFunction> -a <queryFuncArgs> 
```
认可对等节点是安装了 chaincode 的对等节点，在执行事务时调用。 你必须将 `<endorsingPeers>` 设置为包含当前对等组织中的对等节点名称。 列出给定 chaincode 和通道组合的认可对等节点，并用空格分隔。 例如：`-p "peer1" "peer3"`。

如果使用 azhlfTool 安装 chaincode，请将任意对等节点名称作为值传递给认可对等节点参数。 chaincode 将安装在该组织的每个对等节点上。 

分别在 `<queryFunction>` 和 `<queryFuncArgs>` 中传递查询函数名称和以空格分隔的参数列表。 再次以 chaincode_example02.go chaincode 作为参考，若要查询 World State 中“a”的值，请将 `<queryFunction>` 设置为  `query`，将  `<queryArgs>` 设置为 `"a"`。  

## <a name="troubleshoot"></a>故障排除

### <a name="find-deployed-version"></a>查找部署的版本

运行以下命令以查找模板部署的版本。 根据部署了模板的资源组设置环境变量。

```bash
SWITCH_TO_AKS_CLUSTER $AKS_CLUSTER_RESOURCE_GROUP $AKS_CLUSTER_NAME $AKS_CLUSTER_SUBSCRIPTION
kubectl describe pod fabric-tools -n tools | grep "Image:" | cut -d ":" -f 3
```

### <a name="patch-previous-version"></a>修补先前版本

如果在 v3.0.0 以下的任何模板部署版本上运行 chaincode 时遇到问题，请按照以下步骤使用修补程序修补对等节点。

下载对等部署脚本。

```bash
curl https://raw.githubusercontent.com/Azure/Hyperledger-Fabric-on-Azure-Kubernetes-Service/master/scripts/patchPeerDeployment.sh -o patchPeerDeployment.sh; chmod 777 patchPeerDeployment.sh
```

使用以下命令运行脚本，并替换对等节点的参数。

```bash
source patchPeerDeployment.sh <peerOrgSubscription> <peerOrgResourceGroup> <peerOrgAKSClusterName>
```

等待所有对等节点完成修补。 始终可以使用以下命令在 shell 的不同实例中检查对等节点的状态。

```bash
kubectl get pods -n hlf
```

## <a name="support-and-feedback"></a>支持和反馈

如需了解区块链服务产品的最新情况和 Azure 区块链工程团队的信息，请访问 [Azure 区块链博客](https://azure.microsoft.com/blog/topics/blockchain/)。

若要提供产品反馈或请求新功能，请通过[区块链的 Azure 反馈论坛](https://aka.ms/blockchainuservoice)来发布想法或进行投票。

### <a name="community-support"></a>社区支持

与 Microsoft 工程师和 Azure 区块链社区专家交流：

- [Microsoft Q&A 页](/answers/topics/azure-blockchain-workbench.html) 
   
  针对区块链模板的工程支持仅限于部署问题。
- [Microsoft 技术社区](https://techcommunity.microsoft.com/t5/Blockchain/bd-p/AzureBlockchain)
- [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-blockchain-workbench)
