---
title: Microsoft Threat Modeling Tool 版本 2020/07/29 - Azure
description: 阐述 Threat Modeling Tool 7.3.00729.1 版的发行说明。
author: jegeib
ms.author: jegeib
ms.service: security
ms.topic: article
ms.date: 07/29/2020
ms.openlocfilehash: 3e6fcd52ad9cb6c127c14bac2f33223fb921519e
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94516367"
---
# <a name="threat-modeling-tool-update-release-73007291---07292020"></a>Threat Modeling Tool 更新版 7.3.00729.1 - 2020/07/29

Microsoft Threat Modeling Tool (TMT) 的版本 7.3.00729.1 已于 2020 年 7 月 29 日发布，其中包含以下更改：

- Bug 修复
 
## <a name="known-issues"></a>已知问题

### <a name="errors-related-to-tmt7application-file-deserialization"></a>与 TMT7.application 文件反序列化相关的错误

#### <a name="issue"></a>问题

一些客户报告称，下载 Threat Modeling Tool 时收到以下错误消息：

```
The threat model file '$PATH\TMT7.application' could not be deserialized. File is not an actual threat model or the threat model may be corrupted.
```

导致此错误的原因是某些浏览器不本机支持 ClickOnce 安装。 在这些情况下，ClickOnce 应用程序文件将下载到用户的硬盘驱动器。

#### <a name="workaround"></a>解决方法

如果通过双击 TMT7.application 文件启动 Threat Modeling Tool，则会继续显示此错误。 但在绕过此错误后，该工具将正常运行。 用户应使用安装过程中在 Windows 菜单中创建的快捷方式来启动 Threat Modeling Tool，而不是通过双击 TMT7.application 文件来启动 Threat Modeling Tool。

## <a name="system-requirements"></a>系统要求

- 支持的操作系统
  - [Microsoft Windows 10 周年更新](https://blogs.windows.com/windowsexperience/2016/08/02/how-to-get-the-windows-10-anniversary-update/#HTkoK5Zdv0g2F2Zq.97)或更高版本
- 所需的 .NET 版本
  - [.NET 4.7.1](https://go.microsoft.com/fwlink/?LinkId=863262) 或更高版本
- 其他要求
  - 需要建立 Internet 连接才能接收工具和模板的更新。

## <a name="documentation-and-feedback"></a>文档和反馈

- Threat Modeling Tool 的文档位于 [docs.microsoft.com](./threat-modeling-tool.md)，其中包含[有关如何使用该工具](./threat-modeling-tool-getting-started.md)的信息。

## <a name="next-steps"></a>后续步骤

下载最新版本的 [Microsoft Threat Modeling Tool](https://aka.ms/threatmodelingtool)。