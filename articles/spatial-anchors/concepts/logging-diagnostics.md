---
title: 日志记录和诊断
description: 详细说明如何在 Azure 空间定位点中生成并检索日志记录和诊断。
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.custom: devx-track-csharp
ms.openlocfilehash: da8ffd7ff0b8473ce558943bb420b36f26c3fc32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "95494635"
---
# <a name="logging-and-diagnostics-in-azure-spatial-anchors"></a>Azure 空间定位点中的日志记录和诊断

Azure 空间定位点提供了一种适用于应用开发的标准日志记录机制。 在需要更多信息进行调试时，空间定位点诊断日志记录模式很有用。 诊断日志记录存储环境的图像。

## <a name="standard-logging"></a>标准日志记录
在空间定位点 API 中，可以订阅日志记录机制，以获取适用于应用程序开发和调试的日志。 标准日志记录 API 不会在设备磁盘上存储环境的图片。 SDK 将这些日志作为事件回叫提供。 由你将这些日志集成到应用程序的日志记录机制。

### <a name="configuration-of-log-messages"></a>日志消息的配置
有两个用户感兴趣的回叫。 以下示例显示了如何配置会话。

```csharp
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .
    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callback for the debug log
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;

    // configure the callback for the error log
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;
```

### <a name="events-and-properties"></a>事件和属性

提供这些事件回叫是为了处理会话中的日志和错误：

- [LogLevel](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.loglevel)：指定要从运行时接收的事件的详细程度。
- [OnLogDebug](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.onlogdebug)：提供标准调试日志事件。
- [Error](/dotnet/api/microsoft.azure.spatialanchors.cloudspatialanchorsession.error)：提供运行时会将其视为错误的日志事件。

## <a name="diagnostics-logging"></a>诊断日志记录

除了用于日志记录的标准操作模式之外，空间定位点还有诊断模式。 诊断模式会捕获环境的图像，并将其记录到磁盘。 可以使用此模式来调试某些类型的问题，如未能有预见性地找到某个定位点。 只是为了重现某个特定问题才需要启用诊断日志记录。 请随后将其禁用。 请不要在正常运行应用时启用诊断。

在与 Microsoft 进行支持交互时，Microsoft 代表可能会询问你是否愿意提交诊断捆绑包以便进一步进行调查。 在这种情况下，你可能会决定要启用诊断并重现该问题，以便你可以提交诊断捆绑包。

如果在 Microsoft 代表未事先认可的情况下将诊断日志提交到 Microsoft，该提交不会获得应答。

以下部分介绍了如何启用诊断模式，以及如何将诊断日志提交到 Microsoft。

### <a name="enable-diagnostics-logging"></a>启用诊断日志记录

在启用诊断日志记录会话时，该会话中的所有操作在本地文件系统中都会有相应的诊断日志记录。 在日志记录过程中，环境的图像会保存到磁盘。

```csharp
private void ConfigureSession()
{
    cloudSpatialAnchorSession = new CloudSpatialAnchorSession();
    . . .

    // set up the log level for the runtime session
    cloudSpatialAnchorSession.LogLevel = SessionLogLevel.Information;

    // configure the callbacks for logging and errors
    cloudSpatialAnchorSession.OnLogDebug += CloudSpatialAnchorSession_OnLogDebug;
    cloudSpatialAnchorSession.Error += CloudSpatialAnchorSession_Error;

    // opt in to diagnostics logging of environment images
    // if this is enabled, the diagnostics bundle includes images of the environment captured by the session
    cloudSpatialAnchorSession.Diagnostics.ImagesEnabled = true;

    // set the level of detail to be collected in the diagnostics log by the session
    cloudSpatialAnchorSession.Diagnostics.LogLevel = SessionLogLevel.All;

    // set the max bundle size to capture the bug information
    cloudSpatialAnchorSession.Diagnostics.MaxDiskSizeInMB = 200;
    . . .
}
```

### <a name="submit-the-diagnostics-bundle"></a>提交诊断捆绑包

以下代码片段演示了如何将诊断捆绑包提交到 Microsoft。 此捆绑包会包括在启用诊断后由会话捕获的环境的图像。

```csharp
// method to handle the diagnostics bundle submission
private async Task CreateAndSubmitBundle()
{
    // create the diagnostics bundle manifest to collect the session information
    string path = await cloudSpatialAnchorSession
                              .Diagnostics
                              .CreateManifestAsync("Description of the issue");

    // submit the manifest and data to send feedback to Microsoft
    await cloudSpatialAnchorSession.Diagnostics.SubmitManifestAsync(path);
}
```

### <a name="parts-of-a-diagnostics-bundle"></a>诊断捆绑包的组成部分
该诊断捆绑包可能会包含以下信息：

- 关键帧图像：启用诊断后在会话过程中捕获的环境图像。
- 日志：由运行时记录的日志事件。
- 会话元数据：标识该会话的元数据。