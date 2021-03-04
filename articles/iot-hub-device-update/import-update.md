---
title: 如何导入新的更新 |Microsoft Docs
description: How-To 指南，用于将新的更新导入到 IoT 中心的 IoT 中心设备更新。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: c83221743e0566d783c38c40aaf92111a0cd80f7
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030726"
---
# <a name="import-new-update"></a>导入新更新
了解如何将新的更新导入到 IoT 中心的设备更新。

## <a name="prerequisites"></a>先决条件

* [使用启用 Iot 中心的设备更新访问 Iot 中心](create-device-update-account.md)。 建议为 IoT 中心使用 S1 (标准) 层或更高版本。 
* 为 IoT 中心内的设备更新设置的 IoT 设备 (或模拟器) 。
   * 如果使用的是真实设备，则需要用于更新映像的更新映像文件，或用于更新包的 [APT 清单文件](device-update-apt-manifest.md) 。
* [PowerShell 5](https://docs.microsoft.com/powershell/scripting/install/installing-powershell) 或更高版本。
* 支持的浏览器：
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> 提交给此服务的某些数据可能在创建此实例的区域之外的区域进行处理。

## <a name="create-device-update-import-manifest"></a>创建设备更新导入清单

1. 确保更新映像文件或 APT 清单文件位于可从 PowerShell 访问的目录中。

2. 为[IoT 中心存储库克隆设备更新](https://github.com/azure/iot-hub-device-update)，或将其作为 .zip 文件下载到可从 PowerShell 中访问的位置 (一旦下载了 zip 文件，请右键单击 `Properties`  >  `General` "选项卡 `Unblock` "，以在该部分中 > 检查 `Security` 以避免 PowerShell 安全警告提示) 。

3. 在 PowerShell 中，导航到 " `tools/AduCmdlets` 目录" 并运行：

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. 通过替换示例参数值来运行以下命令，以生成导入清单，这是描述更新的 JSON 文件：
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    有关快速参考，请参阅上述参数的一些示例值。 您还可以查看完整的 [导入清单架构](import-schema.md) 以了解更多详细信息。

    | 参数 | 说明 |
    | --------- | ----------- |
    | deviceManufacturer | 与该更新兼容的设备制造商，例如 Contoso
    | deviceModel | 与更新兼容的设备的模型，例如，Toaster
    | updateProvider | 提供程序的更新标识部分，例如 Fabrikam
    | e | 命名更新标识的一部分，例如，ImageUpdate
    | updateVersion | 更新版本，例如2。0
    | updateType | <ul><li>指定 `microsoft/swupdate:1` 映像更新</li><li>`microsoft/apt:1`为包更新指定</li></ul>
    | installedCriteria | <ul><li>为更新类型指定 SWVersion 值 `microsoft/swupdate:1`</li><li>指定更新类型的推荐值 `microsoft/apt:1` 。
    | updateFilePath (s)  | 计算机上) 的更新文件的路径 (


## <a name="review-generated-import-manifest"></a>查看生成的导入清单

示例：
```json
{
  "updateId": {
    "provider": "Microsoft",
    "name": "Toaster",
    "version": "2.0"
  },
  "updateType": "microsoft/swupdate:1",
  "installedCriteria": "5",
  "compatibility": [
    {
      "deviceManufacturer": "Fabrikam",
      "deviceModel": "Toaster"
    },
    {
      "deviceManufacturer": "Contoso",
      "deviceModel": "Toaster"
    }
  ],
  "files": [
    {
      "filename": "file1.json",
      "sizeInBytes": 7,
      "hashes": {
        "sha256": "K2mn97qWmKSaSaM9SFdhC0QIEJ/wluXV7CoTlM8zMUo="
      }
    },
    {
      "filename": "file2.zip",
      "sizeInBytes": 11,
      "hashes": {
        "sha256": "gbG9pxCr9RMH2Pv57vBxKjm89uhUstD06wvQSioLMgU="
      }
    }
  ],
  "createdDateTime": "2020-10-08T03:32:52.477Z",
  "manifestVersion": "2.0"
}
```

## <a name="import-update"></a>导入更新

1. 登录到 [Azure 门户](https://portal.azure.com) ，并通过设备更新导航到 IoT 中心。

2. 在页面的左侧，选择 "自动设备管理" 下的 "设备更新"。

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="导入更新" lightbox="media/import-update/import-updates-3.png":::

3. 屏幕顶部将显示几个选项卡。 选择 "更新" 选项卡。

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="更新" lightbox="media/import-update/updates-tab.png":::

4. 在 "准备部署" 标头下方选择 "+ 导入新更新"。

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="导入新更新" lightbox="media/import-update/import-new-update-2.png":::

5. 选择 "选择导入清单文件" 下的文件夹图标或文本框。 你将看到文件选取器对话框。 选择以前使用 PowerShell cmdlet 创建的导入清单。 接下来，在 "选择一个或多个更新文件" 下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择 () 的更新文件。

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="选择更新文件" lightbox="media/import-update/select-update-files.png":::

6. 选择 "选择存储容器" 下的文件夹图标或文本框。 然后选择适当的存储帐户。 存储容器用于临时暂存更新文件。

   :::image type="content" source="media/import-update/storage-account.png" alt-text="存储帐户" lightbox="media/import-update/storage-account.png":::

7. 如果已创建容器，可以重复使用它。 否则，请选择 "+ 容器"，为更新创建新的存储容器 ) 。。 (  选择要使用的容器，然后单击 "选择"。

   :::image type="content" source="media/import-update/container.png" alt-text="选择容器" lightbox="media/import-update/container.png":::

8. 选择 "提交" 以开始导入过程。

   :::image type="content" source="media/import-update/publish-update.png" alt-text="发布更新" lightbox="media/import-update/publish-update.png":::

9. 导入过程开始，屏幕切换到 "导入历史记录" 部分。 选择 "刷新" 以查看进度，直到导入过程完成 (，这可能会在几分钟内完成，但可能需要更长时间) 。

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="更新导入顺序" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 当 "状态" 列指示导入已成功时，请选择 "部署准备就绪" 标头。 现在应会在列表中看到导入的更新。

   :::image type="content" source="media/import-update/update-ready.png" alt-text="作业状态" lightbox="media/import-update/update-ready.png":::

## <a name="next-steps"></a>后续步骤

[创建组](create-update-group.md)

[了解导入概念](import-concepts.md)
