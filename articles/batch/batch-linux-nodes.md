---
title: 在虚拟机计算节点上运行 Linux
description: 了解如何在 Azure Batch 中处理 Linux 虚拟机池中的并行计算工作负荷。
ms.topic: how-to
ms.date: 01/21/2021
ms.custom: H1Hack27Feb2017, devx-track-python, devx-track-csharp
ms.openlocfilehash: c711ec0d035b9b59ec7628a51fe3cff26de358bc
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98683694"
---
# <a name="provision-linux-compute-nodes-in-batch-pools"></a>在 Batch 池中预配 Linux 计算节点

可以使用 Azure Batch 在 Linux 和 Windows 虚拟机上运行并行计算工作负荷。 本文详细介绍如何使用 [Batch Python](https://pypi.python.org/pypi/azure-batch) 和 [Batch .NET](/dotnet/api/microsoft.azure.batch) 客户端库在 Batch 服务中创建 Linux 计算节点池。 

## <a name="virtual-machine-configuration"></a>虚拟机配置

在 Batch 中创建计算节点池时，可以使用两个选项来选择节点大小和操作系统：“云服务配置”和“虚拟机配置”。 [虚拟机配置](nodes-and-pools.md#virtual-machine-configuration) 池由 Azure vm 构成，这些 vm 可以从 Linux 或 Windows 映像创建。 使用虚拟机配置创建池时，需要指定 [可用的计算节点大小](../virtual-machines/sizes.md)、要安装在节点上的虚拟机映像引用，以及 Batch 节点代理 SKU (在每个节点上运行的程序，并在节点与 Batch 服务) 之间提供接口。

### <a name="virtual-machine-image-reference"></a>虚拟机映像引用

Batch 服务使用[虚拟机规模集](../virtual-machine-scale-sets/overview.md)提供虚拟机配置中的计算节点。 可以从 [Azure 市场](https://azuremarketplace.microsoft.com/marketplace/apps/category/compute?filters=virtual-machine-images&page=1)中指定映像，也可以[使用共享映像库来准备自定义映像](batch-sig-images.md)。

在创建虚拟机映像引用时，必须指定以下属性：

| **映像引用属性** | **示例** |
| --- | --- |
| Publisher |Canonical |
| 产品/服务 |UbuntuServer |
| SKU |18.04-LTS |
| 版本 |最新 |

> [!TIP]
> 可以在[使用 Azure CLI 在 Azure 市场中查找 Linux VM 映像](../virtual-machines/linux/cli-ps-findimage.md)中详细了解这些属性以及如何指定市场映像。 请注意，某些 Marketplace 映像当前不与 Batch 兼容。

### <a name="list-of-virtual-machine-images"></a>虚拟机映像列表

并非所有市场映像都与当前可用的 Batch 节点代理兼容。 若要列出 Batch 服务及其相应节点代理 SKU 支持的所有市场虚拟机映像，请使用 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) (Python)、[ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) (Batch .NET) 或其他语言 SDK 的相应 API。

### <a name="node-agent-sku"></a>节点代理 SKU

[Batch 节点代理](https://github.com/Azure/Batch/blob/master/changelogs/nodeagent/CHANGELOG.md)是一个程序，它在池中的每个节点上运行，并在节点与 Batch 服务之间提供命令和控制接口。 节点代理对于不同操作系统有不同的实现（称为 SKU）。 从根本上讲，在创建虚拟机配置时，需要先指定虚拟机映像引用，然后指定要在其上安装映像的代理节点。 通常，每个节点代理 SKU 与多个虚拟机映像兼容。 下面是节点代理 SKU 的几个示例：

- batch.node.ubuntu 18.04
- batch.node.centos 7
- batch.node.windows amd64

## <a name="create-a-linux-pool-batch-python"></a>创建 Linux 池：批处理 Python

下面的代码片段示范如何使用[用于 Python 的 Microsoft Azure Batch 客户端库](https://pypi.python.org/pypi/azure-batch)创建 Ubuntu Server 计算节点池。 有关 Batch Python 模块的更多详细信息，请查看[参考文档](/python/api/overview/azure/batch)。

此代码片段显式创建 [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference)，并指定它的每个属性（publisher、offer、SKU、version）。 但是，我们建议在生产代码中使用 [list_node_agent_skus](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) 方法在运行时从可用映像和节点代理 SKU 组合中做出选择。

```python
# Import the required modules from the
# Azure Batch Client Library for Python
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify Batch account credentials
account = "<batch-account-name>"
key = "<batch-account-key>"
batch_url = "<batch-account-url>"

# Pool settings
pool_id = "LinuxNodesSamplePoolPython"
vm_size = "STANDARD_D2_V3"
node_count = 1

# Initialize the Batch client
creds = batchauth.SharedKeyCredentials(account, key)
config = batch.BatchServiceClientConfiguration(creds, batch_url)
client = batch.BatchServiceClient(creds, batch_url)

# Create the unbound pool
new_pool = batchmodels.PoolAddParameter(id=pool_id, vm_size=vm_size)
new_pool.target_dedicated = node_count

# Configure the start task for the pool
start_task = batchmodels.StartTask()
start_task.run_elevated = True
start_task.command_line = "printenv AZ_BATCH_NODE_STARTUP_DIR"
new_pool.start_task = start_task

# Create an ImageReference which specifies the Marketplace
# virtual machine image to install on the nodes
ir = batchmodels.ImageReference(
    publisher="Canonical",
    offer="UbuntuServer",
    sku="18.04-LTS",
    version="latest")

# Create the VirtualMachineConfiguration, specifying
# the VM image reference and the Batch node agent
# to install on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=ir,
    node_agent_sku_id="batch.node.ubuntu 18.04")

# Assign the virtual machine configuration to the pool
new_pool.virtual_machine_configuration = vmc

# Create pool in the Batch service
client.pool.add(new_pool)
```

如上所述，建议使用 [list_supported_images](/python/api/azure-batch/azure.batch.operations.AccountOperations#list-supported-images-account-list-supported-images-options-none--custom-headers-none--raw-false----operation-config-) 方法从当前支持的节点代理/市场映像组合中进行动态选择（而不是显式创建 [ImageReference](/python/api/azure-mgmt-batch/azure.mgmt.batch.models.imagereference)）。 以下 Python 代码片段演示如何使用此方法。

```python
# Get the list of supported images from the Batch service
images = client.account.list_supported_images()

# Obtain the desired image reference
image = None
for img in images:
  if (img.image_reference.publisher.lower() == "canonical" and
        img.image_reference.offer.lower() == "ubuntuserver" and
        img.image_reference.sku.lower() == "18.04-lts"):
    image = img
    break

if image is None:
  raise RuntimeError('invalid image reference for desired configuration')

# Create the VirtualMachineConfiguration, specifying the VM image
# reference and the Batch node agent to be installed on the node
vmc = batchmodels.VirtualMachineConfiguration(
    image_reference=image.image_reference,
    node_agent_sku_id=image.node_agent_sku_id)
```

## <a name="create-a-linux-pool-batch-net"></a>创建 Linux 池：批处理 .NET

以下代码片段示范如何使用 [Batch .NET](https://www.nuget.org/packages/Microsoft.Azure.Batch/) 客户端库创建 Ubuntu Server 计算节点池。 有关 Batch .NET 的更多详细信息，请查看[参考文档](/dotnet/api/microsoft.azure.batch)。

以下代码片段使用 [PoolOperations.ListSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) 方法从当前支持的市场映像和节点代理 SKU 组合列表中进行选择。 建议使用这种方法，因为受支持组合的列表有时会发生变化。 通常情况下，添加支持的组合。

```csharp
// Pool settings
const string poolId = "LinuxNodesSamplePoolDotNet";
const string vmSize = "STANDARD_D2_V3";
const int nodeCount = 1;

// Obtain a collection of all available node agent SKUs.
// This allows us to select from a list of supported
// VM image/node agent combinations.
List<ImageInformation> images =
    batchClient.PoolOperations.ListSupportedImages().ToList();

// Find the appropriate image information
ImageInformation image = null;
foreach (var img in images)
{
    if (img.ImageReference.Publisher == "Canonical" &&
        img.ImageReference.Offer == "UbuntuServer" &&
        img.ImageReference.Sku == "18.04-LTS")
    {
        image = img;
        break;
    }
}

// Create the VirtualMachineConfiguration for use when actually
// creating the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(image.ImageReference, image.NodeAgentSkuId);

// Create the unbound pool object using the VirtualMachineConfiguration
// created above
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    virtualMachineSize: vmSize,
    virtualMachineConfiguration: virtualMachineConfiguration,
    targetDedicatedComputeNodes: nodeCount);

// Commit the pool to the Batch service
await pool.CommitAsync();
```

尽管上述代码片段是使用 [PoolOperations.istSupportedImages](/dotnet/api/microsoft.azure.batch.pooloperations.listsupportedimages) 方法来动态列出支持的映像和节点代理 SKU 组合并从中做出选择（建议的做法），但也可以显式配置 [ImageReference](/dotnet/api/microsoft.azure.batch.imagereference)：

```csharp
ImageReference imageReference = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "18.04-LTS",
    version: "latest");
```

## <a name="connect-to-linux-nodes-using-ssh"></a>使用 SSH 连接到 Linux 节点

在开发期间或进行故障排除时，可能会发现需要登录到池中的节点。 与 Windows 计算节点不同，你无法使用远程桌面协议 (RDP) 来连接到 Linux 节点。 相反，Batch 服务在每个节点上启用 SSH 访问以建立远程连接。

以下 Python 代码片段会在池中的每个节点上创建一个用户（远程连接时需要）。 然后列显每个节点的安全外壳 (SSH) 连接信息。

```python
import datetime
import getpass
import azure.batch.batch_service_client as batch
import azure.batch.batch_auth as batchauth
import azure.batch.models as batchmodels

# Specify your own account credentials
batch_account_name = ''
batch_account_key = ''
batch_account_url = ''

# Specify the ID of an existing pool containing Linux nodes
# currently in the 'idle' state
pool_id = ''

# Specify the username and prompt for a password
username = 'linuxuser'
password = getpass.getpass()

# Create a BatchClient
credentials = batchauth.SharedKeyCredentials(
    batch_account_name,
    batch_account_key
)
batch_client = batch.BatchServiceClient(
    credentials,
    base_url=batch_account_url
)

# Create the user that will be added to each node in the pool
user = batchmodels.ComputeNodeUser(username)
user.password = password
user.is_admin = True
user.expiry_time = \
    (datetime.datetime.today() + datetime.timedelta(days=30)).isoformat()

# Get the list of nodes in the pool
nodes = batch_client.compute_node.list(pool_id)

# Add the user to each node in the pool and print
# the connection information for the node
for node in nodes:
    # Add the user to the node
    batch_client.compute_node.add_user(pool_id, node.id, user)

    # Obtain SSH login information for the node
    login = batch_client.compute_node.get_remote_login_settings(pool_id,
                                                                node.id)

    # Print the connection info for the node
    print("{0} | {1} | {2} | {3}".format(node.id,
                                         node.state,
                                         login.remote_login_ip_address,
                                         login.remote_login_port))
```

此代码将会有类似于以下示例的输出。 在本例中，该池包含四个 Linux 节点。

```
Password:
tvm-1219235766_1-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50000
tvm-1219235766_2-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50003
tvm-1219235766_3-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50002
tvm-1219235766_4-20160414t192511z | ComputeNodeState.idle | 13.91.7.57 | 50001
```

在节点上创建用户时不需要指定密码，而可以指定 SSH 公钥。 在 Python SDK 中，请在 [ComputeNodeUser](/python/api/azure-batch/azure.batch.models.computenodeuser) 上使用 ssh_public_key 参数。 在 .NET 中，请使用 [ComputeNodeUser.SshPublicKey](/dotnet/api/microsoft.azure.batch.computenodeuser.sshpublickey#Microsoft_Azure_Batch_ComputeNodeUser_SshPublicKey) 属性。

## <a name="pricing"></a>定价

Azure Batch 构建在 Azure 云服务和 Azure 虚拟机技术基础之上。 Batch 服务本身是免费提供的，这意味着，只需支付 Batch 解决方案使用的计算资源费用（以及产生的相关费用）。 如果选择“虚拟机配置”，系统会根据[虚拟机定价](https://azure.microsoft.com/pricing/details/virtual-machines/)结构收费。

如果使用[应用程序包](batch-application-packages.md)将应用程序部署到 Batch 节点，系统还会对应用程序包使用的 Azure 存储资源收费。

## <a name="next-steps"></a>后续步骤

- 探索 [azure-batch-samples GitHub 存储库](https://github.com/Azure/azure-batch-samples)中的 [Python 代码示例](https://github.com/Azure/azure-batch-samples/tree/master/Python/Batch)，以了解如何执行常见 Batch 操作，如创建池、作业和任务。 Python 示例随附的 [README](https://github.com/Azure/azure-batch-samples/blob/master/Python/Batch/README.md) 文件包含有关如何安装所需包的详细信息。
- 了解如何使用 Batch 的 [低优先级 vm](batch-low-pri-vms.md) 。
