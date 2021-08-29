---
title: 如何新增更新 | Microsoft Docs
description: 向 Device Update for IoT Hub 新增更新的操作指南。
author: andrewbrownmsft
ms.author: andbrown
ms.date: 4/19/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 790d363a3bd0e961b184cc2511c39833f0eac3d7
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122253977"
---
# <a name="add-an-update-to-device-update-for-iot-hub"></a>向 Device Update for IoT Hub 新增更新
了解如何将新的更新添加到 Device Update for IoT Hub。

## <a name="prerequisites"></a>先决条件

* [访问启用了 IoT 中心设备更新的 IoT 中心](create-device-update-account.md)。 
* 为 IoT 中心内的[设备更新预配](device-update-agent-provisioning.md)的 IoT 设备（或模拟器）。
* [PowerShell 5](/powershell/scripting/install/installing-powershell) 或更高版本（包括 Linux、macOS 和 Windows 安装）
* 支持的浏览器：
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

> [!NOTE]
> 提交给此服务的某些数据可能在创建此实例的区域之外的区域进行处理。

## <a name="obtain-an-update-for-your-devices"></a>获取设备的更新

现在，你已设置了设备更新并预配了设备，接下来需要的是打算部署到这些设备的更新文件。

如果是通过 OEM 或解决方案集成商购买的设备，该组织很可能会为你提供更新文件，不需要你自行创建更新。 请联系 OEM 或解决方案集成商，了解他们如何提供更新。

如果组织已为你使用的设备创建软件，也将这些设备创建该软件的更新。 创建要使用 Device Update for IoT Hub 部署的更新时，请从[基于映像或基于包的方法](understand-device-update.md#support-for-a-wide-range-of-update-artifacts)开始，具体取决于你的方案。 注意：如果你是刚刚开始创建自己的更新，GitHub 是用于管理开发的最佳选项。 你可以存储和管理你的源代码，并使用 [GitHub Actions](https://docs.github.com/en/actions/guides/about-continuous-integration) 来执行持续集成 (CI) 和持续部署 (CD)。

## <a name="create-a-device-update-import-manifest"></a>创建设备更新导入清单

如果尚未这样做，请务必熟悉基本的[导入概念](import-concepts.md)。

1. 确保更新文件位于可从 PowerShell 访问的目录中。

2. 在更新映像文件或 APT 清单文件所在的目录中创建一个名为 **AduUpdate.psm1** 的文本文件。 然后打开 [AduUpdate.psm1](https://github.com/Azure/iot-hub-device-update/tree/main/tools/AduCmdlets) PowerShell cmdlet，将内容复制到文本文件中，然后保存该文本文件。

3. 在 PowerShell 中，导航到步骤 2 中创建了 PowerShell cmdlet 的目录。 使用下面的复制选项，然后粘贴到 PowerShell 中来运行以下命令：

    ```powershell
    Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope Process
    Import-Module .\AduUpdate.psm1
    ```

4. 通过替换示例参数值来运行以下命令，以生成导入清单（描述更新的 JSON 文件）：
    ```powershell
    $compat = New-AduUpdateCompatibility -DeviceManufacturer 'deviceManufacturer' -DeviceModel 'deviceModel'

    $importManifest = New-AduImportManifest -Provider 'updateProvider' -Name 'updateName' -Version 'updateVersion' `
                                            -UpdateType 'updateType' -InstalledCriteria 'installedCriteria' `
                                            -Compatibility $compat -Files 'updateFilePath(s)'

    $importManifest | Out-File '.\importManifest.json' -Encoding UTF8
    ```

    下表是有关如何填充以上参数的快速参考。 如需更多信息，还可查看完整的[导入清单架构](import-schema.md)。

    | 参数 | 说明 |
    | --------- | ----------- |
    | deviceManufacturer | 与该更新兼容的设备制造商，例如 Contoso。 必须与 _制造商_ [设备属性](./device-update-plug-and-play.md#device-properties)匹配。
    | deviceModel | 与更新兼容的设备模型，例如 Toaster。 必须与 _模型_ [设备属性](./device-update-plug-and-play.md#device-properties)匹配。
    | updateProvider | 正在创建或直接负责更新的实体。 它通常是公司名称。
    | updateName | 更新类的标识符。 类可以是你选择的任何内容。 它通常是一个设备或模型名称。
    | updateVersion | 将此更新与具有相同“提供程序”和“名称”的其他更新区分开来的版本号。 不会与设备上单个软件组件的版本相匹配 （但你可以选择匹配）。
    | updateType | <ul><li>指定 `microsoft/swupdate:1` 用于映像更新</li><li>指定 `microsoft/apt:1`用于包更新</li></ul>
    | installedCriteria | 在部署过程中用于将设备上已有的版本与更新的版本进行比较。 如果 installedCriteria 值与设备上的版本不匹配，则在将更新部署到该设备时，会返回“失败”的结果。<ul><li>对于 `microsoft/swupdate:1` 更新类型，请指定 SWVersion 的值 </li><li>对于 `microsoft/apt:1` 更新类型，请指定 name-version，其中的 name 是 APT 清单的名称，version 是 APT 清单的版本 。 例如 contoso-iot-edge-1.0.0.0。
    | updateFilePath(s) | 计算机上更新文件的路径。


## <a name="review-the-generated-import-manifest"></a>查看生成的导入清单

示例清单输出如下。 如果对其中任何项有疑问，请查看完整的[导入清单架构](import-schema.md)。 
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

## <a name="import-an-update"></a>导入更新

> [!NOTE]
> 以下说明演示了如何通过 Azure 门户 UI 导入更新。 还可以改用 [Device Update for IoT Hub API](#if-youre-importing-via-apis-instead) 来导入更新。

1. 登录 [Azure 门户](https://portal.azure.com)并导航到具有设备更新的 IoT 中心。

2. 在页面的左侧，选择“自动设备管理”下的“设备更新”。

   :::image type="content" source="media/import-update/import-updates-3.png" alt-text="导入更新" lightbox="media/import-update/import-updates-3.png":::

3. 屏幕顶部将显示几个选项卡。 选择“更新”选项卡。

   :::image type="content" source="media/import-update/updates-tab.png" alt-text="更新" lightbox="media/import-update/updates-tab.png":::

4. 在“准备部署”标头下方选择“+ 导入新的更新”。

   :::image type="content" source="media/import-update/import-new-update-2.png" alt-text="导入新的更新" lightbox="media/import-update/import-new-update-2.png":::

5. 在“选择导入清单文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择以前使用 PowerShell cmdlet 创建的导入清单。 接下来，在“选择一个或多个更新文件”下选择文件夹图标或文本框。 你将看到文件选取器对话框。 选择在创建导入清单时已包含的更新文件。

   :::image type="content" source="media/import-update/select-update-files.png" alt-text="选择更新文件" lightbox="media/import-update/select-update-files.png":::

6. 在“选择存储容器”下选择文件夹图标或文本框。 然后选择适当的存储帐户。 存储容器用于临时暂存更新文件。

   :::image type="content" source="media/import-update/storage-account.png" alt-text="存储帐户" lightbox="media/import-update/storage-account.png":::

7. 如果已创建容器，可以重复使用它。 （否则，请选择“+ 容器”，为更新创建新的存储容器。）  选择要使用的容器，然后单击“选择”。

   :::image type="content" source="media/import-update/container.png" alt-text="选择容器" lightbox="media/import-update/container.png":::

8. 选择“提交”以开始导入过程。

   :::image type="content" source="media/import-update/publish-update.png" alt-text="发布更新" lightbox="media/import-update/publish-update.png":::

9. 导入过程开始，屏幕切换到“导入历史记录”部分。 选择“刷新”以查看进度，直到导入过程完成 （具体取决于更新大小，这可能会在几分钟内完成，但可能需要更长时间）。

   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="更新导入顺序" lightbox="media/import-update/update-publishing-sequence-2.png":::

10. 当“状态”列指示导入已成功时，请选择“部署准备就绪”标头。 现在应会在列表中看到导入的更新。

   :::image type="content" source="media/import-update/update-ready.png" alt-text="作业状态" lightbox="media/import-update/update-ready.png":::

## <a name="if-youre-importing-via-apis-instead"></a>如果是改为通过 API 进行导入

如果刚完成了使用以上步骤通过 Azure 门户进行导入的操作，请跳转到下面的“后续步骤”。

如果要使用 [Device Update for IoT Hub Update API](/rest/api/deviceupdate/updates) 来导入更新，而不是通过 Azure 门户进行导入，请注意以下事项：
  - 在调用更新 API 之前，需要将更新文件上传到 Azure Blob 存储位置。
  - 可以参考此[示例 API 调用](import-schema.md#example-import-request-body)，该调用使用上面创建的导入清单。
  - 如果在测试时重复使用相同的 SAS URL，那么，在令牌过期时，就可能会遇到错误。 这是在提交导入清单以及更新内容本身时的情况。


## <a name="next-steps"></a>后续步骤

[创建组](create-update-group.md)

[了解导入概念](import-concepts.md)