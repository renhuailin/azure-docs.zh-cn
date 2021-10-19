---
title: Azure 计算 - Linux 诊断扩展 3.0
description: 如何配置 Azure Linux 诊断扩展 (LAD) 3.0，以收集 Azure 中运行的 Linux VM 的指标和日志事件。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: linux
ms.date: 12/13/2018
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 04264276b3419e32aa420792027502ce3d1acbb2
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129669675"
---
# <a name="use-linux-diagnostic-extension-30-to-monitor-metrics-and-logs"></a>使用 Linux 诊断扩展 3.0 监视指标和日志

本文档介绍 Linux 诊断扩展 (LAD) 3.0 和更高版本。

> [!IMPORTANT]
> 有关版本 2.3 及更早版本的信息，请参阅[监视 Linux VM 的性能和诊断数据](/previous-versions/azure/virtual-machines/linux/classic/diagnostic-extension-v2)。

## <a name="introduction"></a>简介

Linux 诊断扩展可帮助用户监视 Microsoft Azure 上运行的 Linux VM 的运行状况。 它具有以下功能：

* 从 VM 收集系统性能指标，并存储在指定存储帐户中的特定表中。
* 从 syslog 检索日志事件，并存储在指定存储帐户中的特定表中。
* 让用户能够自定义收集并上传的数据指标。
* 让用户能够自定义收集和上传的事件的 syslog 辅助参数和严重性级别。
* 让用户能够将指定日志文件上传到指定的存储表。
* 支持将指标和日志事件发送到指定存储帐户中的任意 Azure 事件中心终结点和 JSON 格式的 blob。

此扩展适用于这两种 Azure 部署模型。

## <a name="install-the-extension-on-a-vm"></a>在 VM 上安装扩展

可以使用 Azure PowerShell cmdlet、Azure CLI 脚本、Azure 资源监视器模板（ARM 模板）或 Azure 门户启用此扩展。 有关详细信息，请参阅[扩展功能](features-linux.md)。

>[!NOTE]
>[Log Analytics VM 扩展](./oms-linux.md)中还随附了 LAD VM 扩展的某些组件。 由于这种体系结构，如果在同一 ARM 模板中对两个扩展进行实例化，则可能会发生冲突。 
>
>为避免安装时冲突，请使用 [`dependsOn` 指令](../../azure-resource-manager/templates/resource-dependency.md#dependson)，确保按顺序安装扩展。 可按任一顺序安装扩展。

这些安装说明和[可下载的示例配置](https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json)会将 LAD 3.0 配置为：

* 捕获并存储与 LAD 2.3 中相同的指标。
* 捕获一组有用的文件系统指标。 此功能是 LAD 3.0 中的新功能。
* 捕获 LAD 2.3 启用的默认 syslog 收集。
* 启用 Azure 门户体验，以便对 VM 指标进行制图以及就其发送警报。

可下载配置只是一个示例。 可对其进行修改来满足自己的需要。

### <a name="supported-linux-distributions"></a>支持的 Linux 分发

LAD 支持以下分发和版本。 分发和版本的列表仅适用于 Azure 认可的 Linux 供应商映像。 扩展通常不支持第三方 BYOL 和 BYOS 映像（例如设备）。

仅列出主要版本（如 Debian 7）的分发对于所有次要版本也均受支持。 如果指定了一个次要版本，则仅支持该版本。 如果追加了加号 (+)，则支持指定版本及之后的次要版本。

支持的分发和版本：

- Ubuntu 20.04、18.04、16.04、14.04
- CentOS 7、6.5+
- Oracle Linux 7、6.4+
- OpenSUSE 13.1+
- SUSE Linux Enterprise Server 12
- Debian 9、8、7
- Red Hat Enterprise Linux (RHEL) 7，6.7+

### <a name="prerequisites"></a>先决条件

* Azure Linux 代理 2.2.0 版或更高版本。 大部分 Azure VM Linux 库映像包含 2.2.7 或更高版本。 运行 `/usr/sbin/waagent -version` 以确认 VM 上安装的版本。 如果 VM 正在运行较早的版本，请[更新来宾代理](./update-linux-agent.md)。
* **Azure CLI**。 如果有需要，在计算机上[设置 Azure CLI](/cli/azure/install-azure-cli) 环境。
* Wget 命令。 如果尚未有该命令，请运行 `sudo apt-get install wget`。
* 现有的 Azure订阅。 
* 用于存储数据的现有常规用途存储帐户。 常规用途存储帐户必须支持表存储。 Blob 存储帐户不起作用。
* Python 2。

### <a name="python-requirement"></a>Python 要求

Linux 诊断扩展需要 Python 2。 如果虚拟机使用的分发在默认情况下不包括 Python 2，则必须安装它。 以下示例命令在各种分发上安装 Python 2：   

- Red Hat、CentOS、Oracle：`yum install -y python2`
- Ubuntu、Debian：`apt-get install -y python2`
- SUSE: `zypper install -y python2`

`python2` 可执行文件必须将别名设置为 python。 下面是设置此别名的一种方法：

1. 运行以下命令以删除所有现有别名。
 
    ```
    sudo update-alternatives --remove-all python
    ```

2. 运行以下命令以创建别名。

    ```
    sudo update-alternatives --install /usr/bin/python python /usr/bin/python2 1
    ```

### <a name="sample-installation"></a>示例安装

下列示例中下载的示例配置会收集一组标准数据，并将其发送到表存储。 示例配置的 URL 及其内容可能会更改。 

在大多数情况下，应下载门户设置 JSON 文件的副本，并根据需要进行自定义。 随后使用模板或自己的自动化来使用自定义版本的配置文件，而不是每次都从 URL 下载。

> [!NOTE]
> 对于以下示例，在运行代码前，为第一部分中的变量填写正确的值。 
#### <a name="azure-cli-sample"></a>Azure CLI 示例

```azurecli
# Set your Azure VM diagnostic variables.
my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
my_linux_vm=<your_azure_linux_vm_name>
my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the VM resource ID. Replace the storage account name and resource ID in the public settings.
my_vm_resource_id=$(az vm show -g $my_resource_group -n $my_linux_vm --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vm_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vm-name $my_linux_vm --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```
#### <a name="azure-cli-sample-to-install-lad-30-on-the-virtual-machine-scale-set-instance"></a>在虚拟机规模集实例上安装 LAD 3.0 的 Azure CLI 示例

```azurecli
#Set your Azure Virtual Machine Scale Sets diagnostic variables.
$my_resource_group=<your_azure_resource_group_name_containing_your_azure_linux_vm>
$my_linux_vmss=<your_azure_linux_vmss_name>
$my_diagnostic_storage_account=<your_azure_storage_account_for_storing_vm_diagnostic_data>

# Login to Azure before you do anything else.
az login

# Select the subscription that contains the storage account.
az account set --subscription <your_azure_subscription_id>

# Download the sample public settings. (You could also use curl or any web browser.)
wget https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json -O portal_public_settings.json

# Build the virtual machine scale set resource ID. Replace the storage account name and resource ID in the public settings.
$my_vmss_resource_id=$(az vmss show -g $my_resource_group -n $my_linux_vmss --query "id" -o tsv)
sed -i "s#__DIAGNOSTIC_STORAGE_ACCOUNT__#$my_diagnostic_storage_account#g" portal_public_settings.json
sed -i "s#__VM_RESOURCE_ID__#$my_vmss_resource_id#g" portal_public_settings.json

# Build the protected settings (storage account SAS token).
$my_diagnostic_storage_account_sastoken=$(az storage account generate-sas --account-name $my_diagnostic_storage_account --expiry 2037-12-31T23:59:00Z --permissions wlacu --resource-types co --services bt -o tsv)
$my_lad_protected_settings="{'storageAccountName': '$my_diagnostic_storage_account', 'storageAccountSasToken': '$my_diagnostic_storage_account_sastoken'}"

# Finally, tell Azure to install and enable the extension.
az vmss extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group $my_resource_group --vmss-name $my_linux_vmss --protected-settings "${my_lad_protected_settings}" --settings portal_public_settings.json
```

#### <a name="powershell-sample"></a>PowerShell 示例

```powershell
$storageAccountName = "yourStorageAccountName"
$storageAccountResourceGroup = "yourStorageAccountResourceGroupName"
$vmName = "yourVMName"
$VMresourceGroup = "yourVMResourceGroupName"

# Get the VM object
$vm = Get-AzVM -Name $vmName -ResourceGroupName $VMresourceGroup

# Get the public settings template from GitHub and update the templated values for storage account and resource ID
$publicSettings = (Invoke-WebRequest -Uri https://raw.githubusercontent.com/Azure/azure-linux-extensions/master/Diagnostic/tests/lad_2_3_compatible_portal_pub_settings.json).Content
$publicSettings = $publicSettings.Replace('__DIAGNOSTIC_STORAGE_ACCOUNT__', $storageAccountName)
$publicSettings = $publicSettings.Replace('__VM_RESOURCE_ID__', $vm.Id)

# If you have customized public settings, you can inline those rather than using the preceding template: $publicSettings = '{"ladCfg":  { ... },}'

# Generate a SAS token for the agent to use to authenticate with the storage account
$sasToken = New-AzStorageAccountSASToken -Service Blob,Table -ResourceType Service,Container,Object -Permission "racwdlup" -Context (Get-AzStorageAccount -ResourceGroupName $storageAccountResourceGroup -AccountName $storageAccountName).Context -ExpiryTime $([System.DateTime]::Now.AddYears(10))

# Build the protected settings (storage account SAS token)
$protectedSettings="{'storageAccountName': '$storageAccountName', 'storageAccountSasToken': '$sasToken'}"

# Finally, install the extension with the settings you built
Set-AzVMExtension -ResourceGroupName $VMresourceGroup -VMName $vmName -Location $vm.Location -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0 
```

### <a name="update-the-extension-settings"></a>更新扩展设置

更改受保护或公开设置后，通过运行相同的命令将其部署到 VM。 如果更改了设置，则更新会发送到扩展。 LAD 将重载配置并自行重启。

### <a name="migrate-from-previous-versions-of-the-extension"></a>从以前版本的扩展迁移

扩展的最新版本是 4.0。 

> [!IMPORTANT]
> 此扩展引入了对扩展配置的中断性变更。 其中一项更改提升了扩展的安全性，因此无法维持与 2.x 版本的向后兼容性。 此外，此扩展的扩展发布服务器与 2.x 版本的发布服务器不同。
>
> 若要从 2.x 迁移到新版本，请先卸载旧扩展（在旧发布服务器名称下）。 然后安装版本 3。

建议：

* 启用自动次要版本升级后再安装扩展。
  * 在经典部署模型 VM 上，如果要通过 Azure XPLAT CLI 或 PowerShell 安装该扩展，请指定版本 `3.*`。
  * 在 Azure 资源管理器部署模型 VM 上，在 VM 部署模板中包含 `"autoUpgradeMinorVersion": true`。
* 为 LAD 3.0 使用新的或不同的存储帐户。 LAD 2.3 和 LAD 3.0 具有几个小的不兼容性，使得共享帐户变得麻烦：
  * LAD 3.0 将 syslog 事件存储在不同名称的表中。
  * `builtin` 指标的 `counterSpecifier` 字符串在 LAD 3.0 中有所不同。

## <a name="protected-settings"></a>受保护的设置

此组配置信息包含不应公开的敏感信息。 例如，其中包含存储凭据。 扩展以加密形式传输和存储这些设置。

```json
{
    "storageAccountName" : "the storage account to receive data",
    "storageAccountEndPoint": "the hostname suffix for the cloud for this account",
    "storageAccountSasToken": "SAS access token",
    "mdsdHttpProxy": "HTTP proxy settings",
    "sinksConfig": { ... }
}
```

名称 | 值
---- | -----
storageAccountName | 扩展写入数据的存储帐户的名称。
storageAccountEndPoint | （可选）标识存储帐户所在云的终结点。 如果缺少此设置，则 LAD 默认为 Azure 公有云 `https://core.windows.net`。 若要使用 Azure 德国、Azure 政府或 Azure 中国世纪互联中的存储帐户，请根据需要设置此值。
storageAccountSasToken | blob 和表服务 (`ss='bt'`) 的[帐户 SAS 令牌](https://azure.microsoft.com/blog/sas-update-account-sas-now-supports-all-storage-services/)。 适用于容器和对象 (`srt='co'`)。 可授予添加、创建、列出、更新和写入权限 (`sp='acluw'`)。 请勿使用前导问号 (?)。
mdsdHttpProxy | （可选）扩展连接到指定存储帐户和终结点所需的 HTTP 代理信息。
sinksConfig | （可选）可将指标和事件传递到的替换目标的详细信息。 以下各部分介绍有关扩展支持的每个数据接收器的详细信息。

若要在 ARM 模板中获取 SAS 令牌，请使用 `listAccountSas` 函数。 有关示例模板，请参阅 [List 函数示例](../../azure-resource-manager/templates/template-functions-resource.md#list-example)。

可通过 Azure 门户构造所需的 SAS 令牌：

1. 选择希望扩展写入的常规用途存储帐户。
1. 在左侧菜单中的“设置”下，选择“共享访问签名” 。
1. 按照前面所述进行选择。
1. 选择“生成 SAS”。

:::image type="content" source="./media/diagnostics-linux/make_sas.png" alt-text="屏幕截图显示了带有“生成 SAS”按钮的“共享访问签名”页。":::

将生成的 SAS 复制到 `storageAccountSasToken` 字段中。 删除前导问号 (?)。

### <a name="sinksconfig"></a>sinksConfig

```json
"sinksConfig": {
    "sink": [
        {
            "name": "sinkname",
            "type": "sinktype",
            ...
        },
        ...
    ]
},
```

`sinksConfig` 可选部分用于定义扩展要将其收集到的信息发送到的更多的目的地。 `sink` 数组包含每个附加数据接收器的对象。 `type` 特性确定对象中的其他特性。

元素 | 值
------- | -----
name | 在扩展配置中其他位置引用此接收器的字符串。
type | 要定义的接收器的类型。 确定此类型实例中的其他值（如果有）。

LAD 版本3.0 支持两种接收器类型：`EventHub` 和 `JsonBlob`。

#### <a name="eventhub-sink"></a>EventHub 接收器

```json
"sink": [
    {
        "name": "sinkname",
        "type": "EventHub",
        "sasURL": "https SAS URL"
    },
    ...
]
```

`"sasURL"` 条目包含应将数据发布到的事件中心的完整 URL，包括 SAS 令牌。 LAD 要求 SAS 命名启用发送声明的策略。 

例如：

* 创建名为 `contosohub` 的 Azure 事件中心命名空间。
* 在名为 `syslogmsgs` 的命名空间中创建事件中心。
* 在启用发送声明的事件中心上创建共享访问策略。 将策略命名为 `writer`。

如果 SAS 在 UTC 2018 年 1 月 1 日午夜之前都有效，则 sasURL 的值可能类似于以下示例：

```https
https://contosohub.servicebus.windows.net/syslogmsgs?sr=contosohub.servicebus.windows.net%2fsyslogmsgs&sig=xxxxxxxxxxxxxxxxxxxxxxxxx&se=1514764800&skn=writer
```

有关为事件中心生成和检索 SAS 令牌信息的详细情况，请参阅[生成 SAS 令牌](/rest/api/eventhub/generate-sas-token#powershell)。

#### <a name="jsonblob-sink"></a>JsonBlob 接收器

```json
"sink": [
    {
        "name": "sinkname",
        "type": "JsonBlob"
    },
    ...
]
```

定向到 `JsonBlob` 接收器的数据将存储在 Azure 存储中的 blob 内。 每个 LAD 实例每小时为每个接收器名称创建一个 blob。 每个 blob 总是包含一个语法上有效的 JSON 对象数组。 新条目以基元形式添加到数组。 

Blob 存储在与接收器同名的容器中。 用于 blob 容器名称的 Azure 存储规则适用于 `JsonBlob` 接收器的名称。 该名称必须包含 3 到 63 个小写字母数字 ASCII 字符或短划线。

## <a name="public-settings"></a>公用设置

此公用设置结构包含多个设置块，这些块控制扩展收集的信息。 每个设置都是可选的。 如果指定 `ladCfg`，则还必须指定 `StorageAccount`。

```json
{
    "ladCfg":  { ... },
    "perfCfg": { ... },
    "fileLogs": { ... },
    "StorageAccount": "the storage account to receive data",
    "mdsdHttpProxy" : ""
}
```

元素 | Value
------- | -----
StorageAccount | 扩展写入数据的存储帐户的名称。 必须是[受保护的设置](#protected-settings)中指定的名称。
mdsdHttpProxy | （可选）与[受保护的设置](#protected-settings)中的相同。 如果设置，则专用值将替代公用值。 将包含机密（如密码）的代理设置放在[受保护的设置](#protected-settings)中。

以下各部分提供了有关其余元素的详细信息。

### <a name="ladcfg"></a>ladCfg

```json
"ladCfg": {
    "diagnosticMonitorConfiguration": {
        "eventVolume": "Medium",
        "metrics": { ... },
        "performanceCounters": { ... },
        "syslogEvents": { ... }
    },
    "sampleRateInSeconds": 15
}
```

`ladCfg` 结构是可选的。 该结构可控制要传送到 Azure Monitor 指标服务和其他数据接收器的指标和日志的收集。 必须指定以下内容：

* `performanceCounters` 或 `syslogEvents` 或两者兼备。 
* `metrics` 结构。

元素 | 值
------- | -----
eventVolume | （可选）控制在存储表中创建的分区数。 必须为 `"Large"`、`"Medium"` 或 `"Small"`。 如果未指定值，则默值认为 `"Medium"`。
sampleRateInSeconds | （可选）两次收集原始（未聚合）指标之间的默认时间间隔。 支持的最小采样率为 15 秒。 如果未指定值，则默认值为 `15`。

#### <a name="metrics"></a>指标

```json
"metrics": {
    "resourceId": "/subscriptions/...",
    "metricAggregation" : [
        { "scheduledTransferPeriod" : "PT1H" },
        { "scheduledTransferPeriod" : "PT5M" }
    ]
}
```

元素 | Value
------- | -----
ResourceId | VM 或 VM 所属规模集的 Azure 资源管理器资源 ID。 如果配置中使用了任何 `JsonBlob` 接收器，也必须指定此设置。
scheduledTransferPeriod | 计算聚合指标并传输到 Azure Monitor 指标的频率。 该频率以 IS 8601 时间间隔形式表示。 最小传输周期为 60 秒，即 PT1M。 请至少指定一个 `scheduledTransferPeriod`。

`performanceCounters` 部分中指定的指标样本每 15 秒收集一次，或者按计数器明确定义的采样率进行收集。 如果出现多个 `scheduledTransferPeriod` 频率（如示例所示），则每个聚合都将独立计算。

#### <a name="performancecounters"></a>performanceCounters

```json
"performanceCounters": {
    "sinks": "",
    "performanceCounterConfiguration": [
        {
            "type": "builtin",
            "class": "Processor",
            "counter": "PercentIdleTime",
            "counterSpecifier": "/builtin/Processor/PercentIdleTime",
            "condition": "IsAggregate=TRUE",
            "sampleRate": "PT15S",
            "unit": "Percent",
            "annotation": [
                {
                    "displayName" : "Aggregate CPU %idle time",
                    "locale" : "en-us"
                }
            ]
        }
    ]
}
```

`performanceCounters` 可选部分控制指标的收集。 每个 [`scheduledTransferPeriod`](#metrics) 的原始样本聚合产生以下值：

* 平均值
* 最小值
* 最大值
* 上一次收集的值
* 用于计算聚合的原始样本数

元素 | Value
------- | -----
sinks | （可选）LAD 将聚合指标结果发送到的接收器的名称的逗号分隔列表。 所有聚合指标都将发布到列出的每个接收器。 示例：`"EHsink1, myjsonsink"`。 有关详细信息，请参阅 [`sinksConfig`](#sinksconfig)。
type | 标识指标的实际提供程序。
class | 与 `"counter"` 一起标识提供程序的命名空间中的特定指标。
counter | 与 `"class"` 一起标识提供程序的命名空间中的特定指标。
counterSpecifier | 标识 Azure Monitor 指标命名空间中的特定指标。
condition | （可选）选择指标适用的对象的特定实例。 或选择该对象的所有实例的聚合。 
sampleRate | IS 8601 时间间隔，用于设置收集此指标原始样本的速率。 如果未设置该值，则收集时间间隔由 [`sampleRateInSeconds`](#ladcfg) 的值设置。 支持的最短采样率为 15 秒 (PT15S)。
unit | 定义指标的单位。 应为以下字符串之一：`"Count"`、`"Bytes"`、`"Seconds"`、`"Percent"`、`"CountPerSecond"`、`"BytesPerSecond"` 和 `"Millisecond"`。 所收集数据的使用者会预期收集到的数据值与此单位匹配。 LAD 会忽略此字段。
displayName | 要附加到 Azure Monitor 指标中的数据的标签。 此标签采用关联区域设置指定的语言。 LAD 会忽略此字段。

`counterSpecifier` 是任意标识符。 Azure 门户绘图和警报功能等指标使用者使用 `counterSpecifier` 作为标识指标或指标实例的“关键字”。 

对于 `builtin` 指标，建议使用以 `/builtin/` 开头的 `counterSpecifier` 值。 如果要收集指标的特定实例，建议将该实例的标识符附加到 `counterSpecifier` 值。 

下面是一些示例：

* `/builtin/Processor/PercentIdleTime` - 所有 vCPU 的平均空闲时间
* `/builtin/Disk/FreeSpace(/mnt)` - `/mnt` 文件系统的可用空间
* `/builtin/Disk/FreeSpace` - 所有已装载文件系统的平均可用空间

LAD 和 Azure 门户不要求 `counterSpecifier` 值与任何模式匹配。 请以相同的模式构造各个 `counterSpecifier` 值。

指定 `performanceCounters` 时，LAD 始终将数据写入 Azure 存储中的表。 可以将相同的数据写入 JSON blob 或事件中心，或两者都写入。 但不能禁止将数据存储到表。 

使用相同存储帐户名称和终结点的所有 LAD 实例会将其指标和日志添加到同一个表中。 如果有过多 VM 写入同一表分区，则 Azure 可能限制写入该分区。 

`eventVolume` 设置会使条目分散在 1 个（小型）、10 个（中型）或 100 个（大型）分区中。 通常，中型分区足以避免流量限制。 

Azure 门户的 Azure Monitor 指标功能使用此表中的数据来生成图形或触发警报。 表名是这些字符串的串联：

* `WADMetrics`
* 表中存储的聚合值的 `"scheduledTransferPeriod"`
* `P10DV2S`
* 日期格式为“YYYYMMDD”，每 10 天更改一次

示例包括 `WADMetricsPT1HP10DV2S20170410` 和 `WADMetricsPT1MP10DV2S20170609`。

#### <a name="syslogevents"></a>syslogEvents

```json
"syslogEvents": {
    "sinks": "",
    "syslogEventConfiguration": {
        "facilityName1": "minSeverity",
        "facilityName2": "minSeverity",
        ...
    }
}
```

`syslogEvents` 可选部分控制 syslog 中日志事件的收集。 如果省略此部分，则完全不会捕获 syslog 事件。

`syslogEventConfiguration` 收集将为相关的每个 syslog 辅助参数创建一个条目。 如果特定辅助参数的 `minSeverity` 为 `"NONE"`，或者该辅助参数并未出现在元素中，则不会捕获该辅助参数下的任何事件。

元素 | Value
------- | -----
sinks | 一个逗号分隔列表，包含要将单个日志事件发布到其中的接收器的名称。 与 `syslogEventConfiguration` 中的限制匹配的所有日志事件都会发布到列出的每个接收器。 示例： `"EHforsyslog"`
facilityName | Syslog 辅助参数名称，例如 `"LOG_USER"` 或 `"LOG\LOCAL0"`。 有关详细信息，请参阅 [syslog 手册页](http://man7.org/linux/man-pages/man3/syslog.3.html)的“辅助参数”部分。
minSeverity | Syslog 严重性级别，例如 `"LOG_ERR"` 或 `"LOG_INFO"`。 有关详细信息，请参阅 [syslog 手册页](http://man7.org/linux/man-pages/man3/syslog.3.html)的“级别”部分。 扩展将捕获发送到该辅助参数的等于或高于指定级别的事件。

指定 `syslogEvents` 时，LAD 始终将数据写入 Azure 存储中的表。 可以将相同的数据写入 JSON blob 或事件中心，或两者都写入。 但不能禁止将数据存储到表。 

此表的分区行为与 `performanceCounters` 的描述相同。 表名是这些字符串的串联：

* `LinuxSyslog`
* 日期格式为“YYYYMMDD”，每 10 天更改一次

示例包括 `LinuxSyslog20170410` 和 `LinuxSyslog20170609`。

### <a name="perfcfg"></a>perfCfg

`perfCfg` 部分是可选的。 可控制任意[开放式管理基础结构 (OMI)](https://github.com/Microsoft/omi) 查询的运行。

```json
"perfCfg": [
    {
        "namespace": "root/scx",
        "query": "SELECT PercentAvailableMemory, PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
        "table": "LinuxOldMemory",
        "frequency": 300,
        "sinks": ""
    }
]
```

元素 | 值
------- | -----
命名空间 | （可选）应在其中运行查询的 OMI 命名空间。 如果未指定，则默认值为 `"root/scx"`。 由 [System Center 跨平台提供程序](https://github.com/Microsoft/SCXcore)实现。
query | 要运行的 OMI 查询。
表 | （可选）指定存储帐户中的 Azure 存储表。 有关详细信息，请参阅[受保护的设置](#protected-settings)。
频率 | （可选）两次查询运行之间的秒数。 默认值为 300（5 分钟）。 最小值为 15 秒。
sinks | （可选）应将原始样本指标结果发布到的更多接收器的名称的逗号分隔列表。 扩展或 Azure Monitor 指标不计算这些原始样本的聚合。

必须指定 `"table"` 或 `"sinks"` 或两者都指定。

### <a name="filelogs"></a>fileLogs

`fileLogs` 部分控制日志文件的捕获。 LAD 在新文本行写入文件时捕获它们。 它会将这些文本行写入表行和/或任何指定接收器，如 `JsonBlob` 或 `EventHub`。

> [!NOTE]
> `fileLogs` 由名为 `omsagent` 的 LAD 的子组件捕获。 若要收集 `fileLogs`，请确保 `omsagent` 用户对指定的文件具有读取权限。 用户还必须对该文件的路径中的所有目录具有执行权限。 安装 LAD 后，可以通过运行 `sudo su omsagent -c 'cat /path/to/file'` 来检查权限。

```json
"fileLogs": [
    {
        "file": "/var/log/mydaemonlog",
        "table": "MyDaemonEvents",
        "sinks": ""
    }
]
```

元素 | Value
------- | -----
文件 | 要监视和捕获的日志文件的完整路径名。 路径名必须命名单个文件。 它不能命名目录，也不能包含通配符。 `omsagent` 用户帐户必须具有文件路径的读取访问权限。
表 | （可选）新行从文件“结尾”写入其中的 Azure 存储表。 该表必须位于指定存储帐户中，如受保护配置中所指定。 
sinks | （可选）日志行发送到的更多接收器的名称的逗号分隔列表。

必须指定 `"table"` 或 `"sinks"`，或二者都指定。

## <a name="metrics-supported-by-the-builtin-provider"></a>内置提供程序支持的指标

`builtin` 指标提供程序可提供大量用户最感兴趣的指标。 这些指标分为五个大类：

* 处理器
* 内存
* 网络
* 文件系统
* 磁盘

### <a name="builtin-metrics-for-the-processor-class"></a>处理器类的内置指标

处理器类指标提供有关 VM 中处理器使用情况的信息。 聚合百分比时，结果是所有 CPU 的平均值。 

在双 vCPU VM 中，如果一个 vCPU 是 100% 忙碌，另一个是 100% 空闲，则报告的 `PercentIdleTime` 是 50。 如果在相同时段内每个 vCPU 都是 50% 忙碌，则报告的结果也是 50。 在四 vCPU VM 中，如果一个 vCPU 是 100% 忙碌，其他几个是空闲，则报告的 `PercentIdleTime` 是 75。

计数器 | 含义
------- | -------
PercentIdleTime | 聚合窗口期内，处理器运行内核空闲循环的时间所占百分比
PercentProcessorTime | 运行非空闲线程的时间所占百分比
PercentIOWaitTime | 等待 IO 操作完成的时间所占百分比
PercentInterruptTime | 运行硬件或软件中断和 DPC（延迟过程调用）的时间所占百分比
PercentUserTime | 聚合窗口期非空闲时间内，处于常规优先级的用户模式中所花费的时间所占百分比
PercentNiceTime | 非空闲时间内，处于降低（良好）优先级的时间所占百分比
PercentPrivilegedTime | 非空闲时间内，处于特权（内核）模式的时间所占百分比

前四个计数器结果之和应为 100%。 后三个计数器结果之和应为 100%。 这三个计数器将总和细分为 `PercentProcessorTime`、`PercentIOWaitTime` 和 `PercentInterruptTime`。

要在所有处理器上聚合单个指标，请设置 `"condition": "IsAggregate=TRUE"`。 若要获取特定处理器的指标，如四 vCPU VM 的第二个逻辑处理器，请设置 `"condition": "Name=\\"1\\""`。 逻辑处理器编号处于 `[0..n-1]` 范围内。

### <a name="builtin-metrics-for-the-memory-class"></a>内存类的内置指标

内存类指标提供有关内存使用率、分页和交换的信息。

计数器 | 含义
------- | -------
AvailableMemory | 可用物理内存 (MB)
PercentAvailableMemory | 可用物理内存占内存总量的百分比
UsedMemory | 已用物理内存 (MB)
PercentUsedMemory | 已用物理内存占内存总量的百分比
PagesPerSec | 总分页（读取/写入）
PagesReadPerSec | 从后备存储（如交换文件、程序文件和映射文件）读取的页面数
PagesWrittenPerSec | 向后备存储（如交换文件和映射文件）写入的页面数
AvailableSwap | 未使用的交换空间 (MB)
PercentAvailableSwap | 未使用的交换空间占交换空间总量的百分比
UsedSwap | 已使用的交换空间 (MB)
PercentUsedSwap | 已使用的交换空间占交换空间总量的百分比

此类指标仅有一个实例。 `"condition"` 特性没有有用的设置，应当省略。

### <a name="builtin-metrics-for-the-network-class"></a>网络类的内置指标

网络类指标提供有关自启动以来各个网络接口上网络活动的信息。 

LAD 不会公开带宽指标。 可以从主机指标获取这些指标。

计数器 | 含义
------- | -------
BytesTransmitted | 自启动以来发送的字节数总计
BytesReceived | 自启动以来接收的字节数总计
BytesTotal | 自启动以来发送或接收的字节数总计
PacketsTransmitted | 自启动以来发送的包数总计
PacketsReceived | 自启动以来接收的包数总计
TotalRxErrors | 自启动以来接收的错误数
TotalTxErrors | 自启动以来发送的错误数
TotalCollisions | 自启动以来网络端口报告的冲突数

虽然网络类拥有实例，但是 LAD 不支持捕获跨所有网络设备聚合的网络指标。 若要获取特定接口（如 eth0）的指标，请设置 `"condition": "InstanceID=\\"eth0\\""`。

### <a name="builtin-metrics-for-the-file-system-class"></a>文件系统类的内置指标

文件系统类指标提供有关文件系统使用情况的信息。 将报告绝对值和百分比值，因为这些指标会向普通用户（而不是 root 用户）显示。

计数器 | 含义
------- | -------
FreeSpace | 可用磁盘空间（字节）
UsedSpace | 已用磁盘空间（字节）
PercentFreeSpace | 可用空间百分比
PercentUsedSpace | 已用空间百分比
PercentFreeInodes | 未使用的索引节点 (Inode) 的百分比
PercentUsedInodes | 所有文件系统中已分配的（使用中）Inode 的百分比之和
BytesReadPerSecond | 每秒读取的字节数
BytesWrittenPerSecond | 每秒写入的字节数
每秒字节数 | 每秒读取或写入的字节数
ReadsPerSecond | 每秒读取操作数
WritesPerSecond | 每秒写入操作数
TransfersPerSecond | 每秒读取或写入操作数

可通过设置 `"condition": "IsAggregate=True"`，获取跨所有文件系统的聚合值。 通过设置 `"condition": 'Name="/mnt"'`，获取特定的装载式文件系统的值，例如 `"/mnt"`。 

> [!NOTE]
> 如果使用的是 Azure 门户而不是 JSON，则条件字段格式为 `Name='/mnt'`。

### <a name="builtin-metrics-for-the-disk-class"></a>磁盘类的内置指标

磁盘类指标提供有关磁盘设备使用情况的信息。 这些统计信息适用于整个驱动器。 

如果设备上有多个文件系统，则针对该设备的计数器会有效地跨所有文件系统聚合。

计数器 | 含义
------- | -------
ReadsPerSecond | 每秒读取操作数
WritesPerSecond | 每秒写入操作数
TransfersPerSecond | 每秒总操作数
AverageReadTime | 每个读取操作的平均秒数
AverageWriteTime | 每个写入操作的平均秒数
AverageTransferTime | 每个操作的平均秒数
AverageDiskQueueLength | 队列中磁盘操作的平均数
ReadBytesPerSecond | 每秒读取的字节数
WriteBytesPerSecond | 每秒写入的字节数
每秒字节数 | 每秒读取或写入的字节数

可通过设置 `"condition": "IsAggregate=True"`，获取跨所有磁盘的聚合值。 若要获取特定设备（例如 `/dev/sdf1`）的信息，请设置 `"condition": "Name=\\"/dev/sdf1\\""`。

## <a name="install-and-configure-lad-30"></a>安装和配置 LAD 3.0

### <a name="azure-cli"></a>Azure CLI

如果受保护的设置位于 ProtectedSettings.json 文件中，而公用配置信息位于 PublicSettings.json 中，请运行以下命令 。

```azurecli
az vm extension set --publisher Microsoft.Azure.Diagnostics --name LinuxDiagnostic --version 3.0 --resource-group <resource_group_name> --vm-name <vm_name> --protected-settings ProtectedSettings.json --settings PublicSettings.json
```

该命令假定你使用 Azure CLI 的 Azure 资源管理模式。 若要为经典部署模型 VM 配置 LAD，请切换到“asm”模式 (`azure config mode asm`)，并在命令中省略资源组名称。 

有关详细信息，请参阅[跨平台 CLI 文档](/cli/azure/authenticate-azure-cli)。

### <a name="powershell"></a>PowerShell

如果受保护的设置位于 `$protectedSettings` 变量中，而公共配置信息位于 `$publicSettings` 变量中，请运行以下命令：

```powershell
Set-AzVMExtension -ResourceGroupName <resource_group_name> -VMName <vm_name> -Location <vm_location> -ExtensionType LinuxDiagnostic -Publisher Microsoft.Azure.Diagnostics -Name LinuxDiagnostic -SettingString $publicSettings -ProtectedSettingString $protectedSettings -TypeHandlerVersion 3.0
```

## <a name="example-lad-30-configuration"></a>LAD 3.0 配置示例

根据前述定义，此部分提供了一个 LAD 3.0 扩展配置示例和一些说明。 要将此示例应用于具体情况，请使用自己的存储帐户名称、帐户 SAS 令牌和事件中心 SAS 令牌。

> [!NOTE]
> 提供公共和受保护设置的方法会有所不同，具体取决于是否使用 Azure CLI 或 PowerShell 安装 LAD： 
>
> * 如果使用 Azure CLI，请将以下设置保存到 ProtectedSettings.json 和 PublicSettings.json，以使用上面的示例命令 。 
> * 如果使用 PowerShell，请通过运行 `$protectedSettings = '{ ... }'` 将以下设置保存到 `$protectedSettings` 和 `$publicSettings`。

### <a name="protected-settings"></a>受保护的设置

受保护的设置配置：

* 存储帐户。
* 匹配的帐户 SAS 令牌。
* 几个接收器（带有 SAS 令牌的 `JsonBlob` 或 `EventHub`）。

```json
{
  "storageAccountName": "yourdiagstgacct",
  "storageAccountSasToken": "sv=xxxx-xx-xx&ss=bt&srt=co&sp=wlacu&st=yyyy-yy-yyT21%3A22%3A00Z&se=zzzz-zz-zzT21%3A22%3A00Z&sig=fake_signature",
  "sinksConfig": {
    "sink": [
      {
        "name": "SyslogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "FilelogJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuJsonBlob",
        "type": "JsonBlob"
      },
      {
        "name": "MyJsonMetricsBlob",
        "type": "JsonBlob"
      },
      {
        "name": "LinuxCpuEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=fake_signature&se=1808096361&skn=yourehpolicy"
      },
      {
        "name": "MyMetricEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&skn=yourehpolicy"
      },
      {
        "name": "LoggingEventHub",
        "type": "EventHub",
        "sasURL": "https://youreventhubnamespace.servicebus.windows.net/youreventhubpublisher?sr=https%3a%2f%2fyoureventhubnamespace.servicebus.windows.net%2fyoureventhubpublisher%2f&sig=yourehpolicy&se=1808096361&skn=yourehpolicy"
      }
    ]
  }
}
```

### <a name="public-settings"></a>公用设置

公用设置会使 LAD 执行以下操作：

* 将 percent-processor-time 和 used-disk-space 上传到 `WADMetrics*` 表。
* 将 syslog 辅助参数 `"info"` 和严重性 `LinuxSyslog*` 之下的消息上传到 `"user"` 表。
* 将原始 OMI 查询结果（`PercentProcessorTime` 和 `PercentIdleTime`）上传到命名的 `LinuxCPU` 表。
* 将文件 `/var/log/myladtestlog` 中的追加行上传到 `MyLadTestLog` 表。

无论如何，数据都还会上传到以下位置：

* Azure Blob 存储。 容器名称在接收器 `JsonBlob` 中定义。
* 事件中心终结点，在 `EventHub` 接收器中指定。

```json
{
  "StorageAccount": "yourdiagstgacct",
  "ladCfg": {
    "sampleRateInSeconds": 15,
    "diagnosticMonitorConfiguration": {
      "performanceCounters": {
        "sinks": "MyMetricEventHub,MyJsonMetricsBlob",
        "performanceCounterConfiguration": [
          {
            "unit": "Percent",
            "type": "builtin",
            "counter": "PercentProcessorTime",
            "counterSpecifier": "/builtin/Processor/PercentProcessorTime",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Aggregate CPU %utilization"
              }
            ],
            "condition": "IsAggregate=TRUE",
            "class": "Processor"
          },
          {
            "unit": "Bytes",
            "type": "builtin",
            "counter": "UsedSpace",
            "counterSpecifier": "/builtin/FileSystem/UsedSpace",
            "annotation": [
              {
                "locale": "en-us",
                "displayName": "Used disk space on /"
              }
            ],
            "condition": "Name=\"/\"",
            "class": "Filesystem"
          }
        ]
      },
      "metrics": {
        "metricAggregation": [
          {
            "scheduledTransferPeriod": "PT1H"
          },
          {
            "scheduledTransferPeriod": "PT1M"
          }
        ],
        "resourceId": "/subscriptions/your_azure_subscription_id/resourceGroups/your_resource_group_name/providers/Microsoft.Compute/virtualMachines/your_vm_name"
      },
      "eventVolume": "Large",
      "syslogEvents": {
        "sinks": "SyslogJsonBlob,LoggingEventHub",
        "syslogEventConfiguration": {
          "LOG_USER": "LOG_INFO"
        }
      }
    }
  },
  "perfCfg": [
    {
      "query": "SELECT PercentProcessorTime, PercentIdleTime FROM SCX_ProcessorStatisticalInformation WHERE Name='_TOTAL'",
      "table": "LinuxCpu",
      "frequency": 60,
      "sinks": "LinuxCpuJsonBlob,LinuxCpuEventHub"
    }
  ],
  "fileLogs": [
    {
      "file": "/var/log/myladtestlog",
      "table": "MyLadTestLog",
      "sinks": "FilelogJsonBlob,LoggingEventHub"
    }
  ]
}
```

配置中的 `resourceId` 必须与 VM 或虚拟机规模集中的相匹配。

* Azure 平台指标绘图和警报了解你所用 VM 的 `resourceId`。 它会预期能够使用 `resourceId` 查找关键字找到该 VM 的数据。
* 如果使用 Azure 自动缩放，则自动缩放配置中的 `resourceId` 必须与 LAD 使用的 `resourceId` 相匹配。
* LAD 所编写的 JSON blob 名称包含 `resourceId`。

## <a name="view-your-data"></a>查看数据

使用 Azure 门户查看性能数据或设置警报：

:::image type="content" source="./media/diagnostics-linux/graph_metrics.png" alt-text="屏幕截图显示 Azure 门户。其中选中了已用磁盘空间指标。同时显示了生成的图表。":::

`performanceCounters` 数据始终存储在 Azure 存储表中。 Azure 存储 API 适用于多种语言和平台。

发送到 `JsonBlob` 接收器的数据存储在[受保护的设置](#protected-settings)中指定的存储帐户中的 blob 内。 可使用任意 Azure Blob 存储 API 来使用这些 blob 数据。

还可使用这些 UI 工具访问 Azure 存储中的数据：

* Visual Studio 服务器资源管理器
* [Azure 存储资源管理器](https://azurestorageexplorer.codeplex.com/)

Azure 存储资源管理器会话的以下屏幕截图显示了测试 VM 上正确配置的 LAD 3.0 扩展生成的 Azure 存储表和容器。 此图与[示例 LAD 3.0 配置](#example-lad-30-configuration)不完全匹配。

:::image type="content" source="./media/diagnostics-linux/stg_explorer.png" alt-text="屏幕截图显示了 Azure 存储资源管理器。":::


有关如何使用发布到事件中心终结点的消息的详细信息，请参阅相关[事件中心文档](../../event-hubs/event-hubs-about.md)。

## <a name="next-steps"></a>后续步骤

* 在 [Azure Monitor](../../azure-monitor/alerts/alerts-classic-portal.md) 中，为收集的指标创建警报。
* 为指标创建[监控图表](../../azure-monitor/data-platform.md)。
* 使用指标[创建虚拟机规模集](../linux/tutorial-create-vmss.md)以控制自动缩放。
