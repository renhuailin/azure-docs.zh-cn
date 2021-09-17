---
title: 排查与 Azure 中的共享映像相关的问题
description: 了解如何排查共享映像库的问题。
author: olayemio
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: troubleshooting
ms.workload: infrastructure
ms.date: 7/1/2021
ms.author: olayemio
ms.reviewer: cynthn
ms.openlocfilehash: 974dba9d3770d7d5570896f24d31fc2378472ae2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697901"
---
# <a name="troubleshoot-shared-image-galleries-in-azure"></a>排查 Azure 中共享映像库的问题

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 灵活规模集 :heavy_check_mark: 统一规模集

如果在对共享映像库、映像定义和映像版本执行任何操作时遇到问题，请在调试模式下再次运行失败的命令。 可以通过传递 `--debug` 开关（在使用 Azure CLI 时）和 `-Debug` 开关（在使用 PowerShell 时）来激活调试模式。 在找到错误后，按照本文来解决错误。


## <a name="creating-or-modifying-a-gallery"></a>创建或修改库 ##

**消息**：库名称无效。允许的字符是英文字母数字字符，允许中间包含下划线和句点，一共可包含最多 80 个字符。不允许使用所有其他特殊字符，包括短划线。  
**原因**：库的名称不符合命名要求。  
**解决方法**：选择一个满足以下条件的名称： 
- 字符数上限为 80 个字符
- 只包含英文字母、数字、下划线和句点
- 以英文字母或数字开头和结尾

**消息**：根据实体名称的验证规则，实体名称“galleryName”无效: ^[^\_\W][\w-.\_]{0,79}(?<![-.])$。  
**原因**：库名称不符合命名要求。  
**解决方法**：为库选择一个满足以下条件的名称： 
- 字符数上限为 80 个字符
- 只包含英文字母、数字、下划线和句点
- 以英文字母或数字开头和结尾

**消息**：提供的资源名称 <galleryName\> 具有以下无效的尾随字符: <character\>。名称不能以下列字符结尾: <character\>  
**原因**：库的名称以句点或下划线结尾。  
**解决方法**：为库选择一个满足以下条件的名称： 
- 字符数上限为 80 个字符
- 只包含英文字母、数字、下划线和句点
- 以英文字母或数字开头和结尾

**消息**：提供的位置 <region\> 不可用于资源类型“Microsoft.Compute/galleries”。此资源类型的可用区域列表为 …  
**原因**：为库指定的区域不正确或要求提交访问请求。  
**解决方法**：检查区域名称是否正确。 如果区域名称正确，请提交针对该区域的[访问请求](/troubleshoot/azure/general/region-access-request-process)。

**消息**：无法删除此资源，除非先删除嵌套的资源。  
**原因**：你试图删除至少包含一个现有映像定义的库。 库必须为空才能删除。  
**解决方法**：删除库中的所有映像定义，然后继续删除库。 如果映像定义包含映像版本，则必须先删除映像版本，然后才能删除映像定义。

**消息**：库名称“<galleryName\>”在订阅“<subscriptionID>”中不唯一。请选择其他库名称。  
**原因**：你有一个具有同一名称的现有库，并且试图使用同一名称创建另一个库。  
**解决方法**：为库选择一个不同的名称。

**消息**：资源 <galleryName\> 已存在于资源组 <resourceGroup\> 中的位置 <region\_1\>。不能在位置 <region\_2\> 中创建具有同一名称的资源。请选择一个新的资源名称。  
**原因**：你有一个具有同一名称的现有库，并且试图使用同一名称创建另一个库。  
**解决方法**：为库选择一个不同的名称。

## <a name="creating-or-modifying-image-definitions"></a>创建或修改映像定义 ##

**消息**：不允许更改属性“galleryImage.properties.<property\>”。  
**原因**：你试图更改 OS 类型、OS 状态、Hyper-V 代系、产品/服务、发布者或 SKU。 不允许更改这些属性中的任何一个。  
**解决方法**：改为创建新的映像定义。

**消息**：资源 <galleryName/imageDefinitionName\> 已存在于资源组 <resourceGroup\> 中的位置 <region\_1\>。不能在位置 <region\_2\> 中创建具有同一名称的资源。请选择一个新的资源名称。  
**原因**：同一个库和资源组中存在具有相同名称的现有映像定义。 你试图在同一库中（但在不同的区域中）创建具有相同名称的另一个映像定义。  
**解决方法**：为映像定义使用一个不同的名称，或将映像定义放在另一个库或资源组中。

**消息**：提供的资源名称 <galleryName\>/<imageDefinitionName\> 具有以下无效的尾随字符: <character\>。名称不能以下列字符结尾: <character\>  
**原因**：<imageDefinitionName\> 名称以句点或下划线结尾。  
**解决方法**：为映像定义选择一个满足以下条件的名称： 
- 字符数上限为 80 个字符
- 只包含英文字母、数字、下划线、连字符和句点
- 以英文字母或数字开头和结尾。

**消息**：根据实体名称的验证规则，实体名称 <imageDefinitionName\> 无效: ^[^\_\\W][\\w-.\_]{0,79}(?<![-.])$"。  
**原因**：<imageDefinitionName\> 名称以句点或下划线结尾。  
**解决方法**：为映像定义选择一个满足以下条件的名称： 
- 字符数上限为 80 个字符
- 只包含英文字母、数字、下划线、连字符和句点
- 以英文字母或数字开头和结尾

**消息**：资产名称 galleryImage.properties.identifier.<property\> 无效。它不能为空。允许使用的字符为大写或小写字母、数字、连字符(-)、句点(.)、下划线 (\_)。名称不能以句点(.)结尾。名称的长度不能超过 <number\> 个字符。  
**原因**：发布者、产品/服务或 SKU 值不符合命名要求。  
**解决方法**：选择一个满足以下条件的值： 
- 对于发布者，字符数上限为 128 个字符；对于产品/服务和 SKU，字符数上限为 64 个字符
- 只包含英文字母、数字、连字符、下划线和句点
- 不得以句点结尾

**消息**：无法对嵌套的资源执行所请求的操作。找不到父资源 <galleryName\>。  
**原因**：当前订阅和资源组中没有名为 <galleryName\> 的库。  
**解决方法**：检查库、订阅和资源组的名称是否正确。 如果不正确，请创建名为 <galleryName\> 的新库。

**消息**：提供的位置 <region\> 不可用于资源类型“Microsoft.Compute/galleries”。此资源类型的可用区域列表为 …  
**原因**：<region\> 名称不正确或要求提交访问请求。  
**解决方法**：检查区域名称拼写是否正确。 你可以运行此命令来查看你有权访问哪些区域。 如果该区域不在列表中，请提交[访问请求](/troubleshoot/azure/general/region-access-request-process)。

**消息**：无法将值 <value\> 串行化为类型: 'iso-8601'，ISO8601Error: ISO 8601 时间指示符 'T' 缺失。无法分析日期/时间字符串 <value\>  
**原因**：为属性提供的值未正确设置为日期格式。  
**解决方法**：采用 yyyy-MM-dd、yyyy-MM-dd'T'HH:mm:sszzz 或 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 有效格式提供一个日期。

**消息**：无法将字符串转换为 DateTimeOffset: <value\>。路径为“properties.<property\>”  
**原因**：为属性提供的值未正确设置为日期格式。  
**解决方法**：采用 yyyy-MM-dd、yyyy-MM-dd'T'HH:mm:sszzz 或 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 有效格式提供一个日期。

**消息**：EndOfLifeDate 必须设置为将来的日期。  
**原因**：生命周期结束日期属性的格式不正确，未设置为当天日期之后的日期。  
**解决方法**：采用 yyyy-MM-dd、yyyy-MM-dd'T'HH:mm:sszzz 或 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 有效格式提供一个日期。

**消息**：参数 --<property\>: 无效的 int 值: <value\>  
**原因**：<property\> 仅接受整数值，但 <value\> 不是整数。  
**解决方法**：选择一个整数值。

**消息**：<property\> 的最小值不得大于 <property\> 的最大值。  
**原因**：为 <property\> 提供的最小值大于为 <property\> 提供的最大值。  
**解决方法**：更改值，使最小值小于或等于最大值。

**消息**：由(发布者 <Publisher\>、产品/服务 <Offer\>、sku <SKU\>)标识的库映像 <imageDefinitionName\> 已存在。请选择一个不同的发布者、产品/服务、sku 组合。  
**原因**：你试图在同一个库中使用与现有映像定义相同的发布者、产品/服务和 SKU 三元组创建新的映像定义。  
**解决方法**：在库中，所有映像定义必须具有发布者、产品/服务和 SKU 的唯一组合。 请选择一个唯一组合，或者选择一个新库并再次创建映像定义。

**消息**：无法删除此资源，除非先删除嵌套的资源。  
**原因**：你试图删除包含映像版本的映像定义。 映像定义必须为空才能删除。  
**解决方法**：删除映像定义中的所有映像版本，然后继续删除映像定义。

**消息**：无法绑定参数 <property\>。无法将值 <value\> 转换为类型 <propertyType\>。无法将标识符名称 <value\> 与有效的枚举器名称匹配。请指定以下枚举器名称之一并重试：<choice\_1\>、<choice\_2\> …  
**原因**：该属性有一组有限的可能值，并且 <value\> 不是其中的值之一。  
**解决方法**：选择可能的 <choice\> 值之一。

**消息**：无法绑定参数 <property\>。无法将值 <value\> 转换为 &quot;System.DateTime&quot; 类型  
**原因**：为属性提供的值未正确设置为日期格式。  
**解决方法**：采用 yyyy-MM-dd、yyyy-MM-dd'T'HH:mm:sszzz 或 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 有效格式提供一个日期。

**消息**：无法绑定参数 <property\>。无法将值 <value\> 转换为 &quot;System.Int32&quot; 类型  
**原因**：<property\> 仅接受整数值，但 <value\> 不是整数。  
**解决方法**：选择一个整数值。

**消息**：ZRS 存储帐户类型在此区域中不受支持。  
**原因**：你在尚不支持标准区域冗余存储 (ZRS) 的区域中选择了它。  
**解决方法**：将存储帐户类型更改为 **Premium\_LRS** 或 **Standard\_LRS**。 查看我们的文档来了解已启用 ZRS 预览版的最新[区域列表](../storage/common/storage-redundancy.md#zone-redundant-storage)。

## <a name="creating-or-updating-image-versions"></a>创建或更新映像版本 ##

**消息**：提供的位置 <region\> 不可用于资源类型“Microsoft.Compute/galleries”。此资源类型的可用区域列表为 …  
**原因**：<region\> 名称不正确或要求提交访问请求。  
**解决方法**：检查区域名称拼写是否正确。 你可以运行此命令来查看你有权访问哪些区域。 如果该区域不在列表中，请提交[访问请求](/troubleshoot/azure/general/region-access-request-process)。

**消息**：无法对嵌套的资源执行所请求的操作。找不到父资源 <galleryName/imageDefinitionName\>。  
**原因**：当前订阅和资源组中没有名为 <galleryName/imageDefinitionName\> 的库。  
**解决方法**：检查库、订阅和资源组的名称是否正确。 如果不正确，请在指定的资源组中创建一个名为 <galleryName\> 的库和/或一个名为 <imageDefinitionName\> 的映像定义。

**消息**：无法绑定参数 <property\>。无法将值 <value\> 转换为 &quot;System.DateTime&quot; 类型  
**原因**：为属性提供的值未正确设置为日期格式。  
**解决方法**：采用 yyyy-MM-dd、yyyy-MM-dd'T'HH:mm:sszzz 或 [ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) 有效格式提供一个日期。

**消息**：无法绑定参数 <property\>。无法将值 <value\> 转换为 &quot;System.Int32&quot; 类型  
**原因**：<property\> 仅接受整数值，但 <value\> 不是整数。  
**解决方法**：选择一个整数值。

**消息**：库映像版本发布配置文件区域 <publishingRegions\> 必须包含映像版本的位置 <sourceRegion\>  
**原因**：源映像的位置(<sourceRegion\>)必须包括在 <publishingRegions\> 列表中。  
**解决方法**：将 <sourceRegion\> 包括在 <publishingRegions\> 列表中。

**消息**：参数 <property\> 的值 <value\> 超出范围。该值必须介于 <minValue\> 和 <maxValue\> 之间(包括这两者)。  
**原因**：<value\> 不在 <property\> 的可能值范围内。  
**解决方法**：选择一个位于 <minValue\> 到 <maxValue\> 范围（包括这两者）中的值。

**消息**：找不到源 <resourceID\>。请检查源是否存在，是否与正在创建的库映像版本位于同一区域中。  
**原因**：<resourceID\> 处没有源，或者 <resourceID\> 处的源与正在创建的库映像不在同一区域中。  
**解决方法**：检查 <resourceID\> 值是否正确，以及库映像版本的源区域是否与 <resourceID\> 值的区域相同。

**消息**：不允许更改属性“galleryImageVersion.properties.storageProfile.<diskImage\>.source.id”。  
**原因**：在创建库映像版本后，无法更改其源 ID。  
**解决方法**：确保源 ID 与现有源 ID 相同，更改映像版本的版本号，或删除当前映像版本，然后重试。

**消息**：在输入数据磁盘中检测到重复的 lun 编号。Lun 编号在每个数据磁盘中必须唯一。  
**原因**：使用磁盘和/或磁盘快照列表创建映像版本时，有两个或更多磁盘或磁盘快照具有相同的 LUN。  
**解决方法**：删除或更改任何重复的 LUN。

**消息**：在输入磁盘中发现重复的源 id。源 id 在每个磁盘中应当唯一。  
**原因**：使用磁盘和/或磁盘快照列表创建映像版本时，有两个或更多磁盘或磁盘快照具有相同的资源 ID。  
**解决方法**：删除或更改任何重复的磁盘源 ID。

**消息**：“properties.storageProfile.<diskImages\>.source.id”路径上的属性 ID <resourceID\> 无效。需要以“/subscriptions/<subscriptionID>”或“/providers/<resourceProviderNamespace>/”开头的完全限定的资源 ID。  
**原因**：<resourceID\> 值的格式不正确。  
**解决方法**：检查资源 ID 是否正确。

**消息**：源 id: <resourceID\> 必须是托管映像、虚拟机或其他库映像版本  
**原因**：<resourceID\> 值的格式不正确。  
**解决方法**：如果你使用 VM、托管映像或库映像版本作为源映像，请检查 VM、托管映像或库映像版本的资源 ID 是否正确。

**消息**：源 id: <resourceID\> 必须是一个托管磁盘或快照。  
**原因**：<resourceID\> 值的格式不正确。  
**解决方法**：如果你使用磁盘和/或磁盘快照作为映像版本的源，请检查磁盘和/或磁盘快照的资源 ID 是否正确。

**消息**：无法基于 <resourceID\> 创建库映像版本，因为父级库映像中的 OS 状态(<OsState\_1\>)不是 <OsState\_2\>。  
**原因**：操作系统状态（“通用”或“专用”）与映像定义中指定的操作系统状态不匹配。  
**解决方法**：基于操作系统状态为 <OsState\_1\> 的某个 VM 选择一个源，或者基于 <OsState\_2\> 为 VM 创建一个新的映像定义。

**消息**：id 为“<resourceID\>”的资源具有与父级库映像虚拟机监控程序代系 [“<V#\_2\>”] 不同的虚拟机监控程序代系 [“<V#\_1\>”]。  
**原因**：映像版本的虚拟机监控程序代系与映像定义中指定的虚拟机监控程序代系不匹配。 映像定义操作系统为 <V#\_1\>，而映像版本操作系统为 <V#\_2\>。  
**解决方法**：选择与映像定义具有相同的虚拟机监控程序代系的源，或创建/选择与映像版本具有相同的虚拟机监控程序代系的新映像定义。

**消息**：id 为 <resourceID\> 的资源具有与父级库映像 OS 类型代系 [“<OsType \_2\>”] 不同的 OS 类型 [“<OsType\_1\>”]  
**原因**：映像版本的虚拟机监控程序代系与映像定义中指定的虚拟机监控程序代系不匹配。 映像定义操作系统为 <OsType\_1\>，而映像版本操作系统为 <OsType\_2\>。  
**解决方法**：选择与映像定义具有相同的操作系统(Linux/Windows)的源，或创建/选择与映像版本具有相同的操作系统代系的新映像定义。

**消息**：源虚拟机 <resourceID\> 不能包含临时 OS 磁盘。  
**原因**：位于 <resourceID\> 处的源包含一个临时 OS 磁盘。 共享映像库当前不支持临时 OS 磁盘。  
**解决方法**：基于不使用临时 OS 磁盘的 VM 选择一个不同的源。

**消息**：源虚拟机 <resourceID\> 不能包含 UltraSSD 帐户类型中存储的磁盘 [“<diskID\>”]。  
**原因**：磁盘 <diskID\> 是一个超级 SSD 磁盘。 共享映像库当前不支持超级 SSD 磁盘。  
**解决方法**：使用只包含高级 SSD、标准 SSD 和/或标准 HDD 托管磁盘的源。

**消息**：源虚拟机 <resourceID\> 必须基于托管磁盘创建。  
**原因**：<resourceID\> 中的虚拟机使用非托管磁盘。  
**解决方法**：使用基于只包含高级 SSD、标准 SSD 和/或标准 HDD 托管磁盘的 VM 的源。

**消息**：源“<resourceID\>”上的请求太多。请减少该源上的请求数或者等待一段时间后重试。  
**原因**：由于请求过多，此映像版本的源当前被限制。  
**解决方法**：稍后再尝试创建映像版本。

**消息**：磁盘加密集“<diskEncryptionSetID\>”必须与库资源位于同一订阅“<subscriptionID\>”中。  
**原因**：磁盘加密集只能在创建它们的同一订阅和区域中使用。  
**解决方法**：在与映像版本相同的订阅和区域中创建或使用加密集。

**消息**：加密的源“<resourceID\>”位于与当前库映像版本订阅“<subscriptionID\_1\>”不同的订阅 ID 中。请使用未加密的源重试，或者使用源的订阅“<subcriptionID\_2\>”来创建库映像版本。  
**原因**：如果源映像是加密的，则共享映像库当前不支持在其他订阅中从其他源映像创建映像版本。  
**解决方法**：使用未加密的源，或在与源相同的订阅中创建映像版本。

**消息**：找不到磁盘加密集 <diskEncryptionSetID\>。  
**原因**：磁盘加密可能不正确。  
**解决方法**：检查磁盘加密集的资源 ID 是否正确。

**消息**：映像版本名称无效。映像版本名称应遵循 Major(int).Minor(int).Patch(int)格式。例如: 1.0.0、2018.12.1，等等。  
**原因**：映像版本的有效格式是以句点分隔的三个整数。 映像版本名称不符合有效的格式。  
**解决方法**：使用遵循格式 Major(int).Minor(int).Patch(int) 的映像版本名称。 例如：1.0.0 或 2018.12.1。

**消息**：参数 galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.dataDiskImages.diskEncryptionSetId 的值无效  
**原因**：数据磁盘映像上使用的磁盘加密集的资源 ID 使用了无效的格式。  
**解决方法**：确保磁盘加密集的资源 ID 遵循格式 /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroupName\>/providers/Microsoft.Compute/<diskEncryptionSetName\>。

**消息**：参数 galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId 的值无效。  
**原因**：OS 磁盘映像上使用的磁盘加密集的资源 ID 使用了无效的格式。  
**解决方法**：确保磁盘加密集的资源 ID 遵循格式 /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroupName\>/providers/Microsoft.Compute/<diskEncryptionSetName\>。

**消息**：不能为更新库映像版本请求指定磁盘加密集位于区域 [<region\>] 中的新数据磁盘映像加密 lun [<number\>]。若要更新此版本，请删除新的 lun。如果需要更改数据磁盘映像加密设置，必须使用正确的设置创建一个新的库映像版本。  
**原因**：你向现有映像版本的数据磁盘添加了加密。 不能向现有映像版本添加加密。  
**解决方法**：创建一个新的库映像版本或删除添加的加密设置。

**消息**：库项目版本源只能直接在 storageProfile 下指定或者在各个 OS 或数据磁盘中指定。能且只能提供一个源类型（用户映像、快照、磁盘、虚拟机）。  
**原因**：缺少源 ID。  
**解决方法**：确保源的源 ID 存在。

**消息**：找不到源: <resourceID\>。请确保该源存在。  
**原因**：源的资源 ID 可能不正确。  
**解决方法**：确保源的资源 ID 正确。

**消息**：目标区域“<region\_1\>”中的磁盘“galleryArtifactVersion.properties.publishingProfile.targetRegions.encryption.osDiskImage.diskEncryptionSetId”需要一个磁盘加密集，因为区域“<region\_2\>”中的相应磁盘使用了磁盘加密集“<diskEncryptionSetID\>”  
**原因**：<region\_2\> 中的 OS 磁盘使用了加密，但 <region\_1\> 中未使用。  
**解决方法**：如果在 OS 磁盘上使用加密，请在所有区域中使用加密。

**消息**：目标区域“<region\_1\>”中的磁盘“LUN <number\>”需要一个磁盘加密集，因为区域“<region\_2\>”中的相应磁盘使用了磁盘加密集“<diskEncryptionSetID\>”  
**原因**：<region\_2\> 中的 LUN <number\> 处的数据磁盘使用了加密，但 <region\_1\> 中未使用。  
**解决方法**：如果在数据磁盘上使用加密，请在所有区域中使用加密。

**消息**：在 encryption.dataDiskImages 中指定了无效的 lun [<number\>]。Lun 必须为下列值之一: [0,9]。  
**原因**：为加密指定的 LUN 与附加到 VM 的磁盘的任何 LUN 都不匹配。  
**解决方法**：将加密中的 LUN 更改为 VM 中存在的某个数据磁盘的 LUN。

**消息**：在目标区域“<region\>”的 encryption.dataDiskImages 中指定了重复的 lun“<number\>”。  
**原因**：<region\> 中使用的加密设置至少将某个 LUN 指定了两次。  
**解决方法**：更改 <region\> 中的 LUN 以确保所有 LUN 在 <region\> 中都是唯一的。

**消息**：OSDiskImage 和 DataDiskImage 不能指向同一 blob <sourceID\>  
**原因**：OS 磁盘的源和至少一个数据磁盘的源不唯一。  
**解决方法**：更改 OS 磁盘和/或数据磁盘的源，以确保 OS 磁盘以及每个数据磁盘都是唯一的。

**消息**：目标发布区域中不允许出现重复的区域。  
**原因**：某个区域在发布区域中列出了多次。  
**解决方法**：删除重复的区域。

**消息**：不允许在现有映像中添加新的数据磁盘或更改数据磁盘的 LUN。  
**原因**：对映像版本的更新调用中包含新的数据磁盘或用于某个磁盘的新 LUN。  
**解决方法**：使用现有映像版本的 LUN 和数据磁盘。

**消息**：磁盘加密集 <diskEncryptionSetID\> 必须与库资源位于同一订阅 <subscriptionID\> 中。  
**原因**：共享映像库当前不支持使用其他订阅中的磁盘加密集。  
**解决方法**：在同一订阅中创建映像版本和磁盘加密集。

**消息**：由于“GalleryImageVersion 源资源大小 2048 超出支持的最大大小 1024”，此区域中的复制失败。  
**原因**：源中的某个数据磁盘大于 1TB。  
**解决方法**：将数据磁盘的大小调整为 1 TB。

消息：不允许对 <versionNumber> 进行“更新库映像版本”操作，因为它是已标记的需删除的对象。你只能重试“删除”操作（或等待正在进行的操作完成）。  
原因：你尝试更新正要被删除的库映像版本。  
解决方法：等待删除事件完成再重新创建映像版本。

消息：源资源“<sourceID>”不支持加密。请使用其他支持加密的源资源类型或删除加密属性。  
原因：目前，共享映像库仅支持针对 VM、磁盘、快照和托管映像的加密。 为映像版本提供的源之一不在以前的支持加密的源的列表中。  
解决方法：从映像版本中删除磁盘加密集，并与支持团队联系。

## <a name="creating-or-updating-a-vm-or-scale-sets-from-an-image-version"></a>从某个映像版本创建或更新 VM 或规模集 ##

**消息**：不存在“<imageDefinitionResourceID\>”的最新映像版本  
**原因**：用来部署虚拟机的映像定义未包含最新版本中包括的任何映像版本。  
**解决方法**：请确保至少有一个映像版本将“从最新版本中排除”设置为 False。 

**消息**：库映像 /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroup\>/providers/Microsoft.Compute/galleries/<galleryName\>/images/<imageName\>/versions/<versionNumber\> 在 <region\> 区域不可用。请联系映像所有者以将其复制到此区域，或者更改你请求的区域。  
**原因**：选择用于部署的版本不存在或在指定的区域中没有副本。  
**解决方法**：请确保映像资源的名称正确，并且在指定的区域中至少有一个副本。 

**消息**：库映像 /subscriptions/<subscriptionID\>/resourceGroups/<resourceGroup\>/providers/Microsoft.Compute/galleries/<galleryName\>/images/<imageName\> 在 <region\> 区域不可用。请联系映像所有者以将其复制到此区域，或者更改你请求的区域。  
**原因**：选择用于部署的映像定义没有包含在最新版本和指定区域中的任何映像版本。  
**解决方法**：请确保区域中至少有一个映像版本将“从最新版本中排除”设置为 False。 

**消息**：客户端有权在范围 <resourceID\> 中执行操作“Microsoft.Compute/galleries/images/versions/read”，但当前租户 <tenantID\> 无权访问链接的订阅 <subscriptionID\>。  
**原因**：虚拟机或规模集是通过另一租户中的 SIG 映像创建的。 你尝试对虚拟机或规模集进行更改，但无权访问拥有该映像的订阅。  
**解决方法**：联系该映像版本的订阅的所有者，请求其授予对该映像版本的读取访问权限。

**消息**：库映像 <resourceID\> 在 <region\> 区域中不可用。请联系映像所有者以将其复制到此区域，或者更改你请求的区域。  
**原因**：正在其中创建 VM 的区域不在库映像的已发布区域列表中。  
**解决方法**：将映像复制到该区域中，或在库映像的其中一个发布区域中创建 VM。

**消息**：不允许使用参数“osProfile”。  
**原因**：为基于专用映像版本创建的 VM 提供了管理员用户名、密码或 SSH 密钥。  
**解决方法**：如果要基于该映像创建 VM，请勿包含管理员用户名、密码或 SSH 密钥。 否则，请使用通用映像版本，并提供管理员用户名、密码或 SSH 密钥。

**消息**：必需的参数“osProfile”缺失或为 null。  
**原因**：VM 是基于通用映像创建的，并且缺少管理员用户名、密码或 SSH 密钥。 因为通用映像不会保留管理员用户名、密码或 SSH 密钥，因此，在创建 VM 或规模集的过程中，必须指定这些字段。  
**解决方法**：指定管理员用户名、密码或 SSH 密钥，或使用专用映像版本。

**消息**：无法更新虚拟机规模集 <vmssName\>，因为 VM 规模集的当前 OS 状态为“通用”，不同于更新后的库映像 OS 状态“专用”。  
**原因**：规模集的当前源映像是一个通用源映像，但使用专用的源映像进行更新。 规模集的当前源映像和新的源映像必须处于相同状态。  
**解决方法**：若要更新规模集，请使用一个通用的映像版本。

**消息**：共享映像库 <versionID\> 中的磁盘加密集 <diskEncryptionSetID\> 属于订阅 <subscriptionID\_1\>，不能用于订阅 <subscriptionID\_2\> 中的资源  
**原因**：用于对映像版本进行加密的磁盘加密集所在的订阅不是用于承载映像版本的订阅。  
**解决方法**：为映像版本和磁盘加密集使用同一订阅。

**消息**：创建 VM 或虚拟机规模集需要很长时间。  
**解决方法**：验证你要尝试从中创建 VM 或虚拟机规模集的映像版本的 **OSType** 与用于创建映像版本的源的 **OSType** 是否相同。 

**消息**：ID 为 <vmID\> 的资源的计划 ['{\"name\":\"<name>\",\"publisher\":\"<publisher>\",\"product\":\"<product>\",\"promotionCode\":\"<promotionCode>\"}'] 与父级库映像计划 ['null'] 不同。  
**原因**：正在部署的映像版本的父映像定义没有购买计划信息。  
**解决方法**：根据错误消息创建具有相同购买计划详细信息的映像定义，并在该映像定义内创建映像版本。


## <a name="creating-a-disk-from-an-image-version"></a>基于某个映像版本创建磁盘 ##

**消息**：参数 imageReference 的值无效。  
**原因**：你尝试从某个 SIG 映像版本导出到磁盘，但使用了该映像中不存在的 LUN 位置。    
**解决方法**：检查映像版本，查看正在使用的 LUN 位置。

## <a name="sharing-resources"></a>共享资源

可以通过 [Azure 基于角色的访问控制 (Azure RBAC)](../role-based-access-control/rbac-and-directory-admin-roles.md) 启用对映像库、映像定义、映像版本资源的跨订阅共享。 

## <a name="replication-speed"></a>复制速度

请使用 **--expand ReplicationStatus** 标志来检查是否已完成到所有指定目标区域的复制。 如果尚未完成，请等待作业完成，最长等待时间为 6 小时。 如果失败，请再次触发用于创建并复制映像版本的命令。 如果需要将映像版本复制到许多目标区域，可考虑分阶段进行复制。

## <a name="azure-limits-and-quotas"></a>Azure 限制和配额 

[Azure 限制和配额](../azure-resource-manager/management/azure-subscription-service-limits.md)适用于所有共享映像库、映像定义和映像版本资源。 请确保未超出订阅限制。 


## <a name="next-steps"></a>后续步骤

详细了解[共享映像库](./shared-image-galleries.md)。