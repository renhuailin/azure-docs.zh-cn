---
title: Azure 虚拟桌面屏幕捕获保护
titleSuffix: Azure
description: 如何为 Azure 虚拟桌面设置屏幕捕获保护。
author: gundarev
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: denisgun
ms.service: virtual-desktop
ms.openlocfilehash: ddf70a56d3a787387d1364c88f26cd0faa5df6b3
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123219899"
---
# <a name="screen-capture-protection"></a>屏幕捕获保护

屏幕捕获保护功能可防止在客户端终结点上捕获敏感信息。 启用此功能后，将在屏幕截图和屏幕共享中自动阻止或隐藏远程内容。 此外，远程桌面客户端将对可能正在捕获屏幕的恶意软件隐藏内容。

## <a name="prerequisites"></a>先决条件

屏幕捕获保护功能在会话主机级别进行配置，在客户端上强制实施。 只有支持此功能的客户端才能连接到远程会话。 目前，只有 Windows 桌面客户端支持屏幕捕获保护。 仅支持完整桌面。 假设用户尝试使用不受支持的客户端连接到受保护的会话主机，或访问受保护的会话主机上发布的 RemoteApp。 在这种情况下，连接将会失败并出现错误 0x1151。 

## <a name="configure-screen-capture-protection"></a>配置屏幕捕获保护

1. 若要配置屏幕捕获保护，需要安装可为 Azure 虚拟桌面添加规则和设置的管理模板。 
2. 下载 [Azure 虚拟桌面策略模板文件](https://aka.ms/avdgpo) (AVDGPTemplate.cab) 并提取 cab 文件和 zip 存档的内容。
3. 将 terminalserver-avd.admx 文件复制到 %windir%\PolicyDefinitions 文件夹 
4. 将 en-us\terminalserver-avd.adml 文件复制到 %windir%\PolicyDefinitions\en-us 文件夹 
5. 若要确认是否正确复制了这些文件，请打开本地组策略编辑器，并导航到“计算机配置” -> “管理模板” -> “Windows 组件” -> “远程桌面服务” -> “远程桌面会话主机” -> “Azure 虚拟桌面”     
6. 应会看到一个或多个 Azure 虚拟桌面策略，如下所示。

   :::image type="content" source="media/azure-virtual-desktop-gpo.png" alt-text="组策略编辑器的屏幕截图" lightbox="media/azure-virtual-desktop-gpo.png":::

   > [!TIP]
   > 还可以将管理模板安装到 Active Directory 域中的组策略“中心存储”。
   > 有关组策略管理模板的中心存储的详细信息，请参阅[如何在 Windows 中创建和管理组策略管理模板的中心存储](/troubleshoot/windows-client/group-policy/create-and-manage-central-store)。

7. 打开“启用屏幕捕获保护”策略并将其设置为“已启用” 。

## <a name="limitations-and-known-issues"></a>限制和已知问题

- 此功能可以防止通过一组特定的公共操作系统功能和 API 捕获远程桌面窗口。 但是，无法保证此功能会严格保护内容，例如，在某人截屏的情况下。
- 客户应在禁用剪贴板、驱动器和打印机重定向的情况下结合使用该功能。 禁用重定向有助于防止用户从远程会话复制捕获的屏幕内容。
- 启用该功能后，用户无法使用本地协作软件（例如 Microsoft Teams）共享远程桌面窗口。 如果使用了 Microsoft Teams，则本地 Teams 应用以及正在运行的启用了媒体优化的 Teams 都无法共享受保护内容。
- 当用户尝试使用 Web 客户端 1.0.24.14 连接到受保护的资源时，他们会收到身份验证提示而不是错误消息。

## <a name="next-steps"></a>后续步骤

* 若要了解 Azure 虚拟桌面安全最佳做法，请参阅 [Azure 虚拟桌面安全最佳做法](security-guide.md)。
