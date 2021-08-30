---
title: 在 Azure 自动化中管理更改跟踪和库存
description: 本文介绍如何使用更改跟踪和库存在环境中跟踪软件和 Microsoft 服务的更改。
services: automation
ms.subservice: change-inventory-management
ms.date: 12/10/2020
ms.topic: conceptual
ms.openlocfilehash: d67acd025e453cee41a2461276d2f5df3066a141
ms.sourcegitcommit: 096e7972e2a1144348f8d648f7ae66154f0d4b39
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112518449"
---
# <a name="manage-change-tracking-and-inventory"></a>管理更改跟踪和清单

添加新文件或注册表项来跟踪时，Azure 自动化将会为[更改跟踪和清单](overview.md)启用新文件或注册表项。 本文介绍如何在检测到更改时配置跟踪、查看跟踪结果和处理警报。

使用本文中的步骤之前，请确保已使用以下一种方法在 VM 上启用了“更改跟踪和清单”：

* [从自动化帐户启用更改跟踪和清单](enable-from-automation-account.md)
* [通过浏览 Azure 门户启用更改跟踪和清单](enable-from-portal.md)
* [从 runbook“启用更改跟踪和清单”](enable-from-runbook.md)
* [从 Azure VM 启用更改跟踪和清单](enable-from-vm.md)

## <a name="limit-the-scope-for-the-deployment"></a><a name="scope-configuration"></a>限制部署的范围

更改跟踪和库存在工作区中使用作用域配置来限定要接收更改的计算机。 有关详细信息，请参阅[限制“更改跟踪和清单”的部署范围](manage-scope-configurations.md)。

## <a name="track-files"></a>跟踪文件

可以使用“更改跟踪和清单”来跟踪对文件和文件夹/目录的更改。 本部分介绍如何在 Windows 和 Linux 上配置文件跟踪。

### <a name="configure-file-tracking-on-windows"></a>在 Windows 上配置文件跟踪

使用以下步骤在 Windows 计算机上配置文件跟踪：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“自动化”。 当你开始键入时，列表中会根据输入筛选建议。 选择“自动化帐户”。

3. 在自动化帐户列表中，选择启用“更改跟踪和清单”时选择的帐户。

4. 在自动化帐户中，选择“配置管理”下的“更改跟踪” 。

5. 选择“编辑设置”（齿轮符号）。

6. 在“工作区配置”页上选择“Windows 文件”，然后单击“+ 添加”以添加要跟踪的新文件。 

7. 在“添加用于更改跟踪的 Windows 文件”窗格中，输入要跟踪的文件或文件夹的信息，然后单击“保存”。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 用于检查文件的路径，例如：**c:\temp\\\*.txt**。 还可以使用环境变量，例如 `%winDir%\System32\\\*.*`。       |
    |路径类型     | 路径的类型。 可能的值为“文件”和“文件夹”。        |    
    |递归     | 在查找要跟踪的项时，如果使用递归，则为 True，否则为 False。        |    
    |上传文件内容 | 若要在跟踪的更改中上传文件内容，则为 True，否则为 False。|

    如果你计划使用通配符配置文件和文件夹的监视，请考虑以下事项：

    - 跟踪多个文件需要使用通配符。
    - 只能在路径的最后一段中使用通配符，例如 C:\folder\file 或 /etc/.conf* 
    - 如果环境变量包含无效的路径，验证将成功，但运行清单时，该路径将失败。
    - 设置路径时，请避免使用 c:.* 等常规路径，因为这会导致遍历过多的文件夹。

8. 确保为“上传文件内容”指定 True。 此设置将为指示的文件路径启用文件内容跟踪。

### <a name="configure-file-tracking-on-linux"></a>在 Linux 上配置文件跟踪

使用以下步骤，在 Linux 计算机上配置要跟踪的文件：

1. 选择“编辑设置”（齿轮符号）。

2. 在“工作区配置”页上选择“Linux 文件”，然后选择“+ 添加”以添加要跟踪的新文件。 

3. 在“添加要进行更改跟踪的 Linux 文件”页面上，输入要跟踪的文件或目录的信息，然后选择“保存” 。 下表定义了可在信息中使用的属性。

    |properties  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的文件的友好名称。        |
    |组     | 用于对文件进行逻辑分组的组名。        |
    |输入路径     | 用于检查文件的路径，例如 **/etc/*.conf**。       |
    |路径类型     | 路径的类型。 可能的值为“文件”和“目录”。        |
    |递归     | 在查找要跟踪的项时，如果使用递归，则为 True，否则为 False。        |
    |使用 Sudo     | 在检查项时，如果使用 sudo，则为 True，否则为 False。         |
    |链接     | 此设置确定在遍历目录时如何处理符号链接。 可能的值包括：<br> 忽略 - 忽略符号链接，不包括引用的文件/目录。<br>追随 - 在递归期间追随符号链接，并且包含引用的文件/目录。<br>管理 - 追随符号链接并允许更改返回的内容。<br>注意 - 不建议使用“管理”选项，因为它不支持文件内容检索。    |
    |上传文件内容 | 若要在跟踪的更改中上传文件内容，则为 True，否则为 False。 |

4. 确保为“上传文件内容”指定 True。  此设置将为指示的文件路径启用文件内容跟踪。

   ![添加 Linux 文件](./media/manage-change-tracking/add-linux-file.png)

## <a name="track-file-contents"></a>跟踪文件内容

使用文件内容跟踪可以在发生跟踪的更改之前和之后查看文件的内容。 该功能会在每次发生更改后将文件内容保存到[存储帐户](../../storage/common/storage-account-overview.md)。 下面是跟踪文件内容所要遵循的一些规则：

* 若要存储文件内容，需要有一个使用资源管理器部署模型的标准存储帐户。
* 默认情况下，存储帐户接受来自任何网络上客户端的连接。 如果你保护了存储帐户以仅允许某些流量，则需要修改配置规则以允许你的自动化帐户连接到存储帐户。 请参阅[配置 Azure 存储防火墙和虚拟网络](../../storage/common/storage-network-security.md)。
* 不要使用高级和经典部署模型存储帐户。 请参阅[关于 Azure 存储帐户](../../storage/common/storage-account-create.md)。
* 只能将存储帐户连接到一个自动化帐户。
* 必须在自动化帐户中启用“更改跟踪和清单”。

### <a name="enable-tracking-for-file-content-changes"></a>启用文件内容更改跟踪

使用以下步骤来启用对文件内容更改的跟踪：

1. 选择“编辑设置”（齿轮符号）。

2. 选择“文件内容”，然后选择“链接” 。 选择此项会打开“添加更改跟踪的内容位置”页。

   ![添加内容位置](./media/manage-change-tracking/enable.png)

3. 选择用于存储文件内容的订阅和存储帐户。

5. 如果想针对所有现有的已跟踪文件启用文件内容跟踪，请在“上传所有设置的文件内容”处选择“开启” 。 以后可以更改每个文件路径的此设置。

   ![设置存储帐户](./media/manage-change-tracking/storage-account.png)

6. 更改跟踪和库存在启用文件内容更改跟踪时，会显示存储帐户和共享访问签名 (SAS) URI。 签名在 365 天后过期；可以选择“重新生成”来重新创建签名。

   ![列出帐户密钥](./media/manage-change-tracking/account-keys.png)

### <a name="view-the-contents-of-a-tracked-file"></a>查看所跟踪文件的内容

更改跟踪和库存检测到所跟踪文件的更改后，你就可以在“更改详细信息”窗格中查看文件内容。  

![列出更改](./media/manage-change-tracking/change-list.png)

1. 在自动化帐户的“更改跟踪”页上，选择更改列表中的文件，然后选择“查看文件内容更改”以查看文件的内容。 “更改详细信息”窗格会在文件信息的前面和后面显示每个属性的标准。

   ![更改详细信息](./media/manage-change-tracking/change-details.png)

2. 可以在并排视图中查看文件内容。 可以选择“内联”以查看内联的更改视图。

## <a name="track-registry-keys"></a>跟踪注册表项

使用以下步骤，配置要在 Windows 计算机上跟踪的注册表项：

1. 在自动化帐户的“跟踪更改”页上，选择“编辑设置”（齿轮符号）。 

2. 在“工作区配置”页上，选择“Windows 注册表”。

3. 选择“+ 添加”以添加要跟踪的新注册表项。

4. 在“添加要进行更改跟踪的 Windows 注册表”页面上，输入要跟踪的项的信息，然后选择“保存” 。 下表定义了可在信息中使用的属性。 指定注册表路径时，必须是注册表项，而不是值。

    |属性  |说明  |
    |---------|---------|
    |已启用     | 如果应用了设置，则为 True，否则为 False。        |
    |项名称     | 要跟踪的注册表项的易记名称。        |
    |组     | 用于对注册表项进行逻辑分组的组名。        |
    |Windows 注册表项   | 包含路径的项名，例如 `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup`。      |

## <a name="search-logs-for-change-records"></a>在日志中搜索更改记录

可对 Azure Monitor 日志执行各种搜索以查找更改记录。 “更改跟踪”页打开后，单击“Log Analytics”打开“日志”页。 下表提供了在日志中搜索更改记录的示例。

|查询  |说明  |
|---------|---------|
|`ConfigurationData`<br>&#124; `where ConfigDataType == "WindowsServices" and SvcStartupType == "Auto"`<br>&#124; `where SvcState == "Stopped"`<br>&#124; `summarize arg_max(TimeGenerated, *) by SoftwareName, Computer`         | 显示已设置为“自动”但报告为“已停止”的 Windows 服务的最新库存记录。 结果仅限于指定软件名称和计算机的最新记录。    |
|`ConfigurationChange`<br>&#124; `where ConfigChangeType == "Software" and ChangeCategory == "Removed"`<br>&#124; `order by TimeGenerated desc`|显示已删除的软件的更改记录。|

## <a name="next-steps"></a>后续步骤

* 有关作用域配置的信息，请参阅[限制“更改跟踪和清单”的部署范围](manage-scope-configurations.md)。
* 如果需要搜索存储在 Azure Monitor Logs 中的日志，请参阅 [Azure Monitor Logs 中的日志搜索](../../azure-monitor/logs/log-query-overview.md)。
* 如果已完成部署，请参阅[删除更改跟踪和清单](remove-feature.md)。
* 要从更改跟踪和清单中删除 VM，请参阅[从更改跟踪和清单中删除 VM](remove-vms-from-change-tracking.md)。
* 若要排查功能错误，请参阅[排查更改跟踪和库存的问题](../troubleshoot/change-tracking.md)。
