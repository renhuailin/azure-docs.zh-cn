---
title: 在 Windows 上使用 Azure 实验室服务设置实验室以通过 React 讲授前端开发课程
description: 了解如何设置实验室以通过 React 讲授前端开发课程。
author: emaher
ms.topic: article
ms.date: 05/16/2021
ms.author: enewman
ms.openlocfilehash: 089ec3d3d4836b4dadb5a0a60023b85ebf9950e0
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2021
ms.locfileid: "112456537"
---
# <a name="set-up-lab-for-react-on-windows"></a>在 Windows 上设置 React 实验室

[React](https://reactjs.org/) 是用于生成用户界面 (UI) 的常用 JavaScript 库。 React 是用于为网站创建可重用组件的声明性方法。  还有很多用于基于 JavaScript 的前端开发的其他常用库。  我们在创建实验室时会使用其中几个库。  [Redux](https://redux.js.org/) 是一个库，可为 JavaScript 应用提供可预测的状态容器，通常用于补充 React。 [JSX](https://reactjs.org/docs/introducing-jsx.html) 是 JavaScript 的库语法扩展，通常与 React 配合使用，用于描述 UI 应有的外观。  [NodeJS](https://nodejs.org/) 是为 React 应用程序运行 Web 服务器的一种简便方法。

本文将介绍如何安装用于开发环境的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)、工具，以及 React Web 开发课程所需的库。

## <a name="lab-configuration"></a>实验室配置

若要设置此实验室，需要有 Azure 订阅和实验室帐户才能开始。 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/)。 获取 Azure 订阅后，可以在 Azure 实验室服务中创建新的实验室帐户。 若要详细了解如何新建实验室帐户，请参阅有关[如何设置实验室帐户](./tutorial-setup-lab-account.md)的教程。 也可以使用现有实验室帐户。

### <a name="lab-account-settings"></a>实验室帐户设置

启用实验室帐户设置，如下表所述。 若要详细了解如何启用 Azure 市场映像，请参阅[指定可供实验室创建者使用的 Azure 市场映像](./specify-marketplace-images.md)。

| 实验室帐户设置 | Instructions |
| -------------------- | ----- |
| 市场映像 | 启用“Windows Server 2019 (x64) 上的 Visual Studio 2019 Community (最新版)”映像，以在实验室帐户中使用。 |

### <a name="lab-settings"></a>实验室设置

建议的虚拟机 (VM) 大小取决于学生需要完成的工作负载类型。  

| 实验室设置 | 值和描述 |
| ------------ | ------------------ |
| 虚拟机大小 | **中等** |

建议测试工作负载，以了解是否需要更大的大小。  有关各种大小的详细信息，请参阅 [VM 大小调整](administrator-guide.md#vm-sizing)。

## <a name="template-machine-configuration"></a>模板计算机配置

本部分的步骤说明了如何完成以下操作来设置模板 VM：

1. 安装开发工具。
1. 为 Web 浏览器安装调试器扩展。
1. 更新防火墙设置。

### <a name="install-development-tools"></a>安装开发工具

“Windows Server 2019 (x64) 上的 Visual Studio 2019 Community (最新版)”映像中已经安装了 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) 所需的 [Node.js 开发](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true#prerequisites)工作负载。

1. 安装首选 Web 浏览器。  默认情况下，该映像中已安装 Internet Explorer。
1. 导航到 [Node.js](https://nodejs.org) 网站，选择“下载”按钮。  可以使用最新的长期服务 (LTS) 版本、具有最新功能的当前版本，或以前的版本。  安装 NodeJS 也会安装 [Node 包管理器](https://www.npmjs.com/)，它将会用于安装 React、Redux 和 JSX。
1. 如果需要，请[将 Visual Studio 2019 更新](/visualstudio/install/update-visual-studio?view=vs-2019&preserve-view=true)到最新版本。

基于 React 的网站所需的其他组件通过 NPM 安装到特定应用程序中。  若要添加 NPM 包，请参阅[在 Visual Studio 中管理 NPM 包](/visualstudio/javascript/npm-package-management?view=vs-2019&preserve-view=true#add-npm-packages)。  

例如，如果在项目中使用 [Node.js 交互窗口](/visualstudio/javascript/nodejs-interactive-repl?view=vs-2019&preserve-view=true)，请输入以下命令来安装 React、Redux 和 JSX 库：

```bash
.npm install react
.npm install react-dom
.npm install react-redux
.npm install react-jsx
```

若要在 Visual Studio 中创建第一个使用 React 的 Node.js 应用，请参阅[教程：在 Visual Studio 中创建 Node.js 和 React 应用](/visualstudio/javascript/tutorial-nodejs-with-react-and-jsx?view=vs-2019&preserve-view=true)。

### <a name="install-debugger-extensions"></a>安装调试器扩展

为浏览器安装 React 开发人员工具扩展，这样就可以检查 React 组件并记录性能信息。  

- [React 开发人员工具 Edge 加载项](https://microsoftedge.microsoft.com/addons/detail/react-developer-tools/gpphkfbcpidddadnkolkpfckpihlkkil)
- [React 开发人员工具 Chrome 扩展](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi)
- [React 开发人员工具 FireFox 加载项](https://addons.mozilla.org/firefox/addon/react-devtools/)

### <a name="update-firewall-settings"></a>更新防火墙设置

默认会阻止发往 Node.js 服务器的入站流量。  如果你要访问正在运行的学生网站，请添加一个入站防火墙规则以允许流量。  查看“应用程序端口”项目属性，以确定调试期间将使用哪个端口。  以下示例使用 [New-NetFirewallRule](/powershell/module/netsecurity/new-netfirewallrule?view=windowsserver2019-ps&preserve-view=true) PowerShell cmdlet 来允许访问端口 1337。  

```powershell
New-NetFirewallRule -DisplayName "Allow access to Port 1337" -Direction Inbound -LocalPort 1337 -Protocol TCP -Action Allow
```

>[!IMPORTANT]
>讲师必须使用模板 VM 或其他实验室 VM 来访问学生的网站。

## <a name="cost"></a>成本

接下来介绍此课程的示例成本估算。  假设你的班上有 25 名学生。 每名学生都有 20 小时的计划课程时间。  除计划的课堂时间外，每名学生还有另外 10 小时的配额，用于完成家庭作业或分配给他们的任务。  我们所选的虚拟机大小为“中等”，包含 55 个实验室单位。

- 25 名学生 &times; (20 个计划小时 + 10 个配额小时) &times; 55 个实验室单位 &times; 0.01 美元/小时 = 412.50 美元

> [!IMPORTANT]
> 成本估算仅用于示例。  有关最新定价信息，请参阅 [Azure 实验室服务定价](https://azure.microsoft.com/pricing/details/lab-services/)。

## <a name="next-steps"></a>后续步骤

现在可以将模板映像发布到实验室。 有关进一步的说明，请参阅[发布模板 VM](how-to-create-manage-template.md#publish-the-template-vm)。

设置实验室时，请参阅以下文章：

- [添加用户](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [设置配额](how-to-configure-student-usage.md#set-quotas-for-users)
- [设置日程安排](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [通过电子邮件将注册链接发送给学生](how-to-configure-student-usage.md#send-invitations-to-users)
