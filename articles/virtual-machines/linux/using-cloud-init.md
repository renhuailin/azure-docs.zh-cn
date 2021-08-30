---
title: Azure 中 Linux VM 的 cloud-init 支持概述
description: 在 Azure 中预配时用于配置 VM 的 cloud-init 功能概述。
author: srijang
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: linux
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/29/2021
ms.author: srijangupta
ms.openlocfilehash: be1bf712d91aaaff460ff2dcc4a899f8b8a089be
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741766"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Azure 中虚拟机的 cloud-init 支持
本文介绍在 Azure 中预配时用于配置虚拟机 (VM) 或虚拟机规模集的 [cloud-init](https://cloudinit.readthedocs.io) 的现有支持。 Azure 预配资源后，这些 cloud-init 配置即会在首次启动时运行。  

VM 预配是指 Azure 向下传递“VM 创建”参数值（例如主机名、用户名、密码等），并在 VM 启动时向其提供这些值的过程。 “预配代理”将使用这些值，配置 VM，并在完成后返回报告。 

Azure 支持两个预配代理：[cloud-init](https://cloudinit.readthedocs.io) 和 [Azure Linux 代理 (WALA)](../extensions/agent-linux.md)。

## <a name="cloud-init-overview"></a>cloud-init 概述
[cloud-init](https://cloudinit.readthedocs.io) 是一种广泛使用的方法，用于在首次启动 Linux VM 时对其进行自定义。 可使用 cloud-init 来安装程序包和写入文件，或者配置用户和安全性。 由于是在初始启动过程中调用 cloud-init，因此无需额外的步骤且无需代理来应用配置。  有关如何正确设置 `#cloud-config` 文件或其他输入的格式的详细信息，请参阅 [cloud-init 文档站点](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data)。  `#cloud-config` 文件是采用 base64 编码的文本文件。

cloud-init 还支持不同的发行版。 例如，不要使用 apt-get 安装或 yum 安装来安装包。 可定义要安装的程序包的列表。 cloud-init 将对你选择的发行版自动使用本机包管理工具。

我们正在积极地与我们认可的 Linux 发行版合作伙伴合作，以便在 Azure 市场中提供已启用 cloud-init 的映像。 这些映像可使 cloud-init 部署和配置无缝地应用于 VM 和虚拟机规模集。 最初，我们与认可的 Linux 发行版合作伙伴和上游协作，确保 cloud-init 可以配合 Azure 上的 OS 正常运行，然后更新了包，并在发行版包的存储库中公开提供了这些包。 

若要使 cloud-init 可供 Azure 上认可的 Linux 发行版 OS 使用，需要经历两个阶段 - 包支持，然后是映像支持：
* “Azure 上的 cloud-init 包支持”阐述了哪些 cloud-init 包即将受支持或以预览版提供，因此你可以将这些包与自定义映像中的 OS 配合使用。
* “映像 cloud-init 准备就绪”阐述了映像是否已配置为使用 cloud-init。


### <a name="canonical"></a>Canonical
| 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical 20.04 |UbuntuServer |20.04-LTS |最新 |是 | 是 |
|Canonical 18.04 |UbuntuServer |18.04-LTS |最新 |是 | 是 |


### <a name="rhel"></a>RHEL
| 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7 |RHEL |7.7、7.8、7_9 |最新 |是 | 是 |
|RedHat 8 |RHEL |8.1、8.2、8_3、8_4 |最新 |是 | 是 |

* 从 RHEL 7（版本 7.7）和 RHEL 8（版本 8.1）开始的所有其他 RedHat SKU（包括 Gen1 和 Gen2 映像）都是用 cloud-init 预配的。 RHEL 6 映像不支持 cloud-init。 


### <a name="centos"></a>CentOS
 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7 |CentOS |7.7、7.8、7.9 |最新 |是 | 是 |
|OpenLogic 8 |CentOS |8.1、8.2、8.3 |最新 |是 | 是 |

* 从 CentOS 7（版本 7.7）和 CentOS 8（版本 8.1）开始，所有其他 CentOS SKU（包括 Gen1 和 Gen2 映像）都是用 cloud-init 预配的。 CentOS 6.10、7.4、7.5 和 7.6 映像不支持 cloud-init。 

> [!NOTE]
> OpenLogic 现为 Rogue Wave Software 



### <a name="oracle"></a>Oracle

 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7 |Oracle Linux |77、78、ol79 |最新 |是 | 是 |
|Oracle 8 |Oracle Linux |81、ol82、ol83-lvm、ol84-lvm |最新 |是 | 是 |

* 从 Oracle 7 (版本 7.7) 和 Oracle 8 (版本 8.1) 开始，所有其他 Oracle SKU（包括 Gen1 和 Gen2 映像）都是用 cloud-init 预配的。


### <a name="suse-sles"></a>SUSE SLES

 发布者/版本| 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |sp1、sp2、sp3 |最新 |是 | 是 |
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sp5 |最新 |是 | 是 |

* 从 SLES 15 (sp1) 和 SLES 12 (sp5) 开始，所有其他 SUSE SKU（包括 Gen1 和 Gen2 映像）都是用 cloud-init 预配的。
* 此外，这些映像也是使用 cloud-init 预配的。


 发布者/版本| 产品/服务 | SKU/版本
|:--- |:--- |:---
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp4:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp3:2020.06.10
|SUSE 12 |SLES (SUSE Linux Enterprise Server) |sles-{byos/sap/sap-byos}:12-sp2:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-proxy-4-byosgen1:2020.06.10
| SUSE 15 |SLES (SUSE Linux Enterprise Server) |manager-server-4-byos:gen1:2020.06.10


### <a name="debian"></a>Debian
| 发布者/版本 | 产品/服务 | SKU | 版本 | 映像 cloud-init 准备就绪 | Azure 上的 cloud-init 包支持|
|:--- |:--- |:--- |:--- |:--- |:--- |
| debian (Gen1) |debian-10 | 10-cloudinit |10:0.20201013.422| 是 | 是 - 以下包版本提供支持：`20.2-2~deb10u1` |
| debian (Gen2) |debian-10 | 10-cloudinit-gen2 |0.20201013.422| 是 | 是 - 以下包版本提供支持：`20.2-2~deb10u1` |


目前 Azure Stack 将支持预配启用了 cloud-init 的映像。

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>cloud-init 和 Linux 代理 (WALA) 之间的区别是什么？
WALA 是一种特定于 Azure 平台的代理，用于预配和配置 VM 并处理 [Azure 扩展](../extensions/features-linux.md)。 

我们正在增强将 VM 配置为使用 cloud-init 而不是 Linux 代理的任务，使现有的 cloud-init 客户能够使用其当前 cloud-init 脚本，或者使新客户能够利用丰富的 cloud-init 配置功能。 如果已使用过 cloud-init 脚本来配置 Linux 系统，那么，**不需要进行额外的设置** 就能让 cloud-init 处理这些任务。 

cloud-init 无法处理 Azure 扩展，因此，仍需在映像中包含 WALA 才能处理扩展，不过，需要禁用其预配代码。要转换为由 cloud-init 预配的已认可 Linux 发行版映像将会正确安装并设置 WALA。

创建 VM 时，如果在预配时未包括 Azure CLI `--custom-data` 开关，cloud-init 或 WALA 将采用所需的最小 VM 预配参数来预配 VM 并使用默认值完成部署。  如果使用 `--custom-data` 开关引用 cloud-init 配置，在 VM 启动时，自定义数据中包含的任何内容均可用于 cloud-init。

应用于 VM 的 cloud-init 配置没有时限，也不会因为超时导致部署失败。对于 WALA，这一点并不适用。如果更改 WALA 默认值来处理自定义数据，则用于处理自定义数据的时间不能超过 VM 预配总时间限制（40 分钟），否则 VM 创建操作将会失败。

## <a name="cloud-init-vm-provisioning-without-a-udf-driver"></a>无需 UDF 驱动程序的 cloud-init VM 预配  
从 cloud-init 21.2 开始，可以使用 cloud-init 在 Azure 中预配 VM，而无需 UDF 驱动程序。 如果映像中未提供 UDF 驱动程序，cloud-init 将使用 Azure 实例元数据服务中提供的元数据来预配 VM。 请注意，此选项仅适用于 SSH 密钥和[用户数据](../user-data.md)。 若要在预配期间将密码或自定义数据传递到 VM，必须使用 UDF 驱动程序。

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>部署已启用 cloud-init 的虚拟机
部署已启用 cloud-init 的虚拟机就和在部署期间引用已启用 cloud-init 的分发一样简单。  Linux 分发 Maintainer 需要选择启用 cloud-init，并将 cloud-init 集成到其基本 Azure 已发布映像中。 确认想要部署的映像已启用 cloud-init 之后，就可以使用 AzureCLI 部署映像。 

部署此映像的第一步是使用 [az group create](/cli/azure/group) 命令创建资源组。 Azure 资源组是在其中部署和管理 Azure 资源的逻辑容器。 

以下示例在“eastus”位置创建名为“myResourceGroup”的资源组。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

下一步是在当前 shell 中创建名为 cloud-init.txt 的文件并粘贴以下配置。 对于此示例，请在不处于本地计算机上的 Cloud Shell 中创建文件。 可使用任何想要使用的编辑器。 输入 `sensible-editor cloud-init.txt` 以创建文件并查看可用编辑器的列表。 选择 #1 以使用 nano 编辑器  。 请确保已正确复制整个 cloud-init 文件，尤其是第一行：

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
> [!NOTE]
> cloud init 具有多个[输入类型](https://cloudinit.readthedocs.io/en/latest/topics/format.html)，cloud init 将使用 customData/userData 的第一行来指明它应如何处理输入，例如，`#cloud-config` 指示应将内容作为 cloud-init 配置进行处理。


按 `ctrl-X` 退出该文件，键入 `y` 以保存文件，并按 `enter` 确认退出时的文件名。

最后一步是使用 [az vm create](/cli/azure/vm) 命令创建 VM。 

以下示例创建一个名为 centos74 的 VM，并且在默认密钥位置中不存在 SSH 密钥时创建这些密钥。 若要使用特定的一组密钥，请使用 `--ssh-key-value` 选项。  使用 `--custom-data` 参数传递到 cloud-init 配置文件中。 如果未将 cloud-init.txt 配置文件保存在现有工作目录中，请提供该文件的完整路径。 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

创建 VM 后，Azure CLI 会显示部署的特定信息。 记下 `publicIpAddress`。 此地址用于访问 VM。  创建 VM、安装程序包和启动应用需要一些时间。 在 Azure CLI 向你返回提示之后，仍然存在继续运行的后台任务。 你可以使用 SSH 连接到 VM 并使用故障排除部分中所述的步骤来查看 cloud-init 日志。 

还可通过传递 [ARM 模板中的参数](../../azure-resource-manager/templates/deploy-cli.md#inline-parameters)来部署已启用 cloud-init 的 VM。

## <a name="troubleshooting-cloud-init"></a>对 cloud-init 进行故障排除
VM 预配完成后，会在 `--custom-data` 中定义的所有模块和脚本上运行 cloud-init，以便配置 VM。  若要对配置中存在的任何错误或遗漏进行故障排除，需要在位于 /var/log/cloud-init.log 的 cloud-init 日志中搜索模块名称（例如 `disk_setup` 或 `runcmd`）。

> [!NOTE]
> 并不是每个模块故障都会导致严重的 cloud-init 整体配置故障。 例如使用 `runcmd` 模块，如果脚本发生故障，cloud-init 依然会报告预配成功，因为 runcmd 模块已执行。

有关 cloud-init 日志的更多详细信息，请参阅 [cloud-init 文档](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>后续步骤

[排查 cloud-init 问题](cloud-init-troubleshooting.md)。


有关配置更改的 cloud-init 示例，请参阅以下文档：
 
- [向 VM 添加其他 Linux 用户](cloudinit-add-user.md)
- [运行包管理器以在首次启动时更新现有包](cloudinit-update-vm.md)
- [更改 VM 本地主机名](cloudinit-update-vm-hostname.md) 
- [安装应用程序包、更新配置文件和注入密钥](tutorial-automate-vm-deployment.md)
