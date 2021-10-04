---
title: Azure FPGFA 证明服务
description: 适用于 NP 系列 VM 的证明服务。
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: vikancha
ms.openlocfilehash: dba6962199f61eeb93dfb2f98e3e448c94ff633a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567093"
---
# <a name="fpga-attestation-for-azure-np-series-vms-preview"></a>适用于 Azure NP 系列 VM 的 FPGA 证明（预览版）

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

FPGA 证明服务对 Xilinx 工具集生成的设计检查点文件（称为“网表”）执行一系列验证，并生成一个包含已验证映像的文件（称为“位流”），该文件可加载到 NP 系列 VM 中的 Xilinx U250 FPGA 卡上。  

## <a name="prerequisites"></a>必备条件  

需要一个 Azure 订阅和一个 Azure 存储帐户。 该订阅可让你访问 Azure，该存储帐户用于保存证明服务的网表文件和输出文件。  

我们提供了 PowerShell 和 Bash 脚本用于提交证明请求。   脚本使用可在 Windows 和 Linux 上运行的 Azure CLI。 PowerShell 可在 Windows、Linux 和 macOS 上运行。  

[Azure CLI 下载（必需）](/cli/azure/install-azure-cli)

[适用于 Windows、Linux 和 macOS 的 PowerShell 下载（仅适用于 PowerShell 脚本）](/powershell/scripting/install/installing-powershell)

需要为你的租户和订阅 ID 授权，使其可以向证明服务提交请求。 请访问 [https://aka.ms/AzureFPGAAttestationPreview](https://aka.ms/AzureFPGAAttestationPreview) 来请求访问权限。 

## <a name="building-your-design-for-attestation"></a>生成证明设计  

用于生成设计的首选 Xilinx 工具集是 Vitis 2020.2。 可以使用通过该工具集的早期版本创建的，但仍与 2020.2 兼容的网表文件。 请确保加载了要用于进行生成的正确 shell。 目前支持的版本为 xilinx_u250_gen3x16_xdma_2_1_202010_1。 可以从 Xilinx Alveo Lounge 下载支持文件。 

必须将以下参数包含在 Vitis（v++ 命令行）中，以生成包含网表（而不是位流）的 xclbin 文件。   

`--advanced.param compiler.acceleratorBinaryContent=dcp`

## <a name="logging-into-azure"></a>登录到 Azure  

在 Azure 中执行任何操作之前，必须先登录到 Azure，设置有权调用该服务的订阅。 为此，请使用 `az login` 和 `az account set –s <Sub ID or Name>` 命令。 若要进一步了解此过程，可查看此处文章：[使用 Azure CLI 登录](/cli/azure/authenticate-azure-cli)。 在命令行中使用“以交互方式登录”或“使用凭据登录”选项 。  

## <a name="creating-a-storage-account-and-blob-container"></a>创建存储帐户和 Blob 容器  

必须将网表文件上传到 Azure 存储 Blob 容器供证明服务访问。  

有关创建帐户和容器，以及将网表作为 Blob 上传到该容器的详细信息，请参阅以下页面：[https://docs.microsoft.com/en-us/azure/storage/blobs/storage-quickstart-blobs-cli](../storage/blobs/storage-quickstart-blobs-cli.md)。  

也可以使用 Azure 门户执行此操作。  

## <a name="upload-your-netlist-file-to-azure-blob-storage"></a>将网表文件上传到 Azure Blob 存储  

可通过多种方式复制文件；下面演示了使用 az storage upload cmdlet 的示例。 az 命令在 Linux 和 Windows 上均可运行。 可以选择任何名称作为“Blob”名称，但请确保保留 `xclbin` 扩展名。

`az storage blob upload --account-name <storage account to receive netlist> --container-name <blob container name> --name <blob filename> --file <local file with netlist>`

## <a name="download-the-attestation-scripts"></a>下载证明脚本  

可从以下 Azure 存储 Blob 容器下载验证脚本：  

[https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip](https://fpgaattestation.blob.core.windows.net/validationscripts/validate.zip)

zip 文件包含两个 PowerShell 脚本，其中一个脚本用于提交，另一个脚本用于监视；第三个文件是一个 bash 脚本，可执行这两项功能。  

## <a name="running-the-attestation-scripts"></a>运行证明脚本  

若要运行脚本，需要提供你的存储帐户名称、存储了网表文件的 Blob 容器的名称，以及网表文件的名称。 此外，还需要创建一个服务共享访问签名 (SAS)，用于授予对容器（不是网表）的读/写访问权限。 证明服务使用此 SAS 创建网表文件的本地副本，并将验证过程生成的输出文件写回到容器。  

此处提供了共享访问签名的概述，以及有关服务 SAS 的具体信息。 服务 SAS 页面包含有关保护生成的 SAS 的重要注意事项。  请阅读这些注意事项，了解为何有必要防范 SAS 遭到恶意使用或意外使用。  

可以使用 az storage container generate-sas cmdlet 生成容器的 SAS。 以 UTC 格式指定过期时间，该时间比提交时间至少晚几个小时；6 小时左右应该就绰绰有余了。  

如果你要使用虚拟目录，必须将目录层次结构包含为容器参数的一部分。 例如，如果你有一个名为“netlists”的容器，并有一个包含网表 Blob 的名为“image1”的虚拟目录，请指定“netlists/image1”作为容器名称。 追加任何其他目录名称可指定更深的层次结构。 

### <a name="powershell"></a>PowerShell   

```powershell
$sas=$(az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <e.g., 2021-01-07T17:00Z> --output tsv)

.\Validate-FPGAImage.ps1 -StorageAccountName <storage acct name> -Container <blob container name> -BlobContainerSAS $sas -NetlistName <netlist blob filename>
```

### <a name="bash"></a>Bash  

```bash
sas=az storage container generate-sas --account-name <storage acct name> --name <blob container name> --https-only --permissions rwc --expiry <2021-01-07T17:00Z> --output tsv  

validate-fpgaimage.sh --storage-account <storage acct name> --container <blob container name> --netlist-name <netlist blob filename> --blob-container-sas $sas
``` 

## <a name="checking-on-the-status-of-your-submission"></a>检查提交状态  

证明服务将返回提交项的业务流程 ID。 提交脚本会自动开始通过轮询完成状态来监视提交项。 业务流程 ID 是我们评审提交项所发生的情况的主要方式，因此在遇到问题时请保留该 ID。 作为参考点，证明服务大约需要花费 30 分钟才能处理完一个较小的网表文件（300MB 大小）；处理完一个 1.6GB 文件则需要一小时。 

可以随时调用 Monitor-Validation.ps1 脚本并提供业务流程 ID 作为参数，来获取证明的状态和结果：  

`.\Monitor-Validation.ps1 -OrchestrationId <orchestration ID>`

或者，可将 HTTP POST 请求提交到证明服务终结点：  

`https://fpga-attestation.azurewebsites.net/api/ComputeFPGA_HttpGetStatus`

请求正文应包含你的订阅 ID、租户 ID，以及证明请求的业务流程 ID：  

```json
{  
  "OrchestrationId": "<orchestration ID>",  
  "ClientSubscriptionId": "<your subscription ID>",  
  "ClientTenantId": "<your tenant ID>"
}
```

## <a name="post-validation-steps"></a>验证后的步骤

服务会将其输出写回到容器。 如果成功通过了验证，则容器将具有原始网表文件 (abc.xclbin)、包含位流的文件 (abc.xclbin)、用于标识所存储位流的专用位置的文件 (abc.azure.xclbin) 和四个日志文件：启动进程有一个日志文件 (abc-log.txt)，执行验证的三个并行阶段各有一个日志文件。 这些文件命名为 *logPhaseX.txt，其中 X 是阶段的编号。 将在 VM 上使用 azure.xclbin，以发出将已验证的映像上传到 U250 的信号。 

如果通不过验证，则会写入一个 error-*.txt 文件，指出哪个步骤失败。 另外，还可在日志文件中检查是否有错误日志指出证明已失败。 联系我们请求支持时，请务必连同业务流程 ID 一起，将所有这些文件包含为支持请求的一部分。  

可以使用 Azure 门户创建容器，以及上传网表和下载位流与日志文件。 目前不支持通过门户提交证明请求及监视其进度，必须如前所述通过脚本实现此目的。
