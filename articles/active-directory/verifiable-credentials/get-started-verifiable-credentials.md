---
title: 教程 - 通过示例应用程序开始使用 Azure Active Directory 可验证凭据（预览版）
description: 在本教程中，你将了解如何使用示例应用程序和测试租户颁发可验证凭据
ms.service: active-directory
ms.subservice: verifiable-credentials
author: barclayn
ms.author: barclayn
ms.topic: tutorial
ms.date: 04/01/2021
ROBOTS: NOINDEX
ms.openlocfilehash: 9958d12299287c7d53641ba75d917f1f18359f54
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728430"
---
#  <a name="tutorial---get-started-with-azure-active-directory-verifiable-credentials-using-a-sample-app-preview"></a>教程 - 通过示例应用程序开始使用 Azure Active Directory 可验证凭据（预览版）

在本教程中，我们将深入介绍一些步骤，帮助你颁发第一个可验证凭据：经过验证的凭据专家卡。 然后，你可以使用此卡向验证者证明你是经过验证的凭据专家，精通数字化凭据。 现在，请通过验证凭据示例应用程序颁发第一个可验证凭据，以开始使用 Azure Active Directory 可验证凭据。

![这是一个示例卡的图像](media/get-started-verifiable-credentials/verifiedcredentialexpert-card.png)

> [!IMPORTANT]
> Azure Active Directory 可验证凭据目前为公共预览版。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="prerequisites"></a>先决条件

- 测试系统需安装 [NodeJS](https://nodejs.org/en/download/) 10.14 或更高版本。
- 如果要克隆托管示例应用程序的存储库，则需要安装 [GIT](https://git-scm.com/downloads)。
- [Visual Studio Code](https://code.visualstudio.com/Download)
- 用于托管示例站点的系统。
- 安装了 Microsoft Authenticator 6.2005.3599 或更高版本的移动设备。
- [NGROK](https://ngrok.com/) 免费版。

## <a name="download-the-sample-code"></a>下载示例代码

若要自行发出经验证的凭据专家卡，则需要在本地计算机上运行网站。 该网站用于启动可验证凭据的颁发过程。 本教程中使用的网站较为简单，由 NodeJS 编写。

首先，点击[此处](https://github.com/Azure-Samples/active-directory-verifiable-credentials)从 GitHub 下载我们的示例代码，或将存储库克隆到本地计算机：

```terminal
git clone https://github.com/Azure-Samples/active-directory-verifiable-credentials.git
```

你可能需要熟悉示例网站中的代码。 `issuer` 文件夹包含用于颁发可验证凭据的所有代码。 如需了解更多详情，请参阅示例的[自述文件](https://github.com/Azure-Samples/active-directory-verifiable-credentials)。

## <a name="run-the-issuer-website"></a>运行颁发者网站

可以通过 Visual Studio Code 或操作系统中提供的任何命令行运行步骤。 

1. 导航到 `issuer` 文件夹。 

    ```terminal
    cd issuer
    ```

2. 随后，需要安装所有必需的程序包并启动站点。

   ```terminal
    npm install
    node app.js
    ```

3. 终端现在会显示颁发者应用程序正在侦听端口 8081。 现在，让我们使用 Ngrok 设置反向代理，以便 Authenticator 能够与你的应用程序进行通信。 

## <a name="creating-a-reverse-proxy-with-ngrok"></a>使用 Ngrok 创建反向代理

在运行示例网站时，你的设备需要与本地计算机上运行的节点服务器进行通信。 建议使用 [ngrok](https://ngrok.com/) 这种简单的方法，这样便可通过互联网使用你的本地开发服务器。

1.  下载并提取 ngrok 后，需要运行：

    ```terminal
     ngrok http 8081
    ```

默认情况下，示例网站在端口 `8081` 上运行。 Ngrok 输出服务器的两个转接 URL。 复制带有前缀 `https://` 的 URL。

![ngrok 可帮助你通过互联网使用应用程序终结点](media/get-started-verifiable-credentials/ngrok.png)

>[!NOTE] 
> 如果使用的是 PowerShell，则可能需要输入 `./ngrok` 才能识别该命令。

现在，在使用 ngrok 向互联网开放本地端口后，示例站点会自动使用 ngrok 生成的主机名。 打开浏览器并导航到 ngrok https 转接 URL。 这样应该能够成功访问示例站点的主页。 如果页面打开，则你的设备便可与本地服务器上运行的示例应用程序进行通信。 现在，你可以颁发已验证凭据专家卡。

## <a name="issue-a-credential"></a>颁发凭据

1. 在移动设备上安装 Authenticator。 Microsoft Authenticator 用于接收、存储并向感兴趣的参与方提供可验证凭据。

2. 接下来，请自行颁发一个可验证凭据。 点击“获取凭据”按钮。  点击“获取凭据”按钮后，示例网站会显示一个 QR 码，你可以使用 Authenticator 对其进行扫描。 如果是通过移动设备上的浏览器查看站点，则点击“获取凭据”按钮会触发一个深层链接，该链接会直接打开 Authenticator 应用程序，不需要你扫描 QR 码。

   ![“获取凭据”按钮](media/get-started-verifiable-credentials/credential-expert-get.png)

3. 使用 Authenticator 扫描网站的 QR 码，或者如果要通过移动设备访问网站，请点击“获取凭据”按钮触发深层链接。 

   ![QR 码 ](media/get-started-verifiable-credentials/credential-expert-issue.png)

4. 请注意，“添加”按钮此时为灰显状态。 在卡图像下方选择“登录你的帐户”。

   ![登录 ](media/get-started-verifiable-credentials/add-verified-credential-expert.png)

5. 在获取凭据专家卡之前，我们使用的租户要求你提供身份验证信息。 如果这是你第一次学习教程，由于没有凭据专家帐户，你需要在 B2C 租户中创建新的用户帐户。

   ![在继续之前进行身份验证](media/get-started-verifiable-credentials/authenticate-credential-expert.png)

6. 登录后，“添加”按钮将不再灰显。选择“添加”以接受新的 VC。

   ![在进行身份验证后选择“添加”](media/get-started-verifiable-credentials/add-verified-credential-expert-after-auth.png) 


7. 恭喜！ 你现在已经拥有经验证凭据专家 VC。

   ![添加了凭据专家 VC](media/get-started-verifiable-credentials/credential-expert-add-card.png) 
 
接下来，可以验证凭据。

## <a name="validate-credentials"></a>验证凭据

完成本教程的颁发部分，并且在 Authenticator 中有一个可验证凭据后，现在你可以在自己的验证应用程序中对其进行验证。

1.  停止运行颁发者 ngrok 服务。

    ```terminal
     control+c
    ```


2. 在另一个终端窗口中，打开 Verifier 应用程序文件夹，并按与颁发者应用程序相同的运行方式运行。

    ```terminal
     cd verifier
     npm install
     node app.js
    ```

3. 现在，使用验证程序端口 8082 运行 ngrok。

    ```terminal
    ngrok http 8082
    ```

4. 在浏览器中打开 ngrok https 转接 url，然后点击“验证凭据”按钮。  

   ![“验证凭据专家”按钮](media/get-started-verifiable-credentials/prove-credential-expert.png)

5. 打开 Authenticator 并扫描 QR 码。

   ![扫描 QR 码以验证凭据](media/get-started-verifiable-credentials/scan-verify.png)

  > [!IMPORTANT]
  > 它位于 iOS 平台的右上方，Android 平台的右下方。 扫描 QR 码。

6. 在 Authenticator 的“新权限请求”界面上选择“允许”。 通过按“允许”，你将 DID（分散式身份识别）对可验证演示进行签名，以证明你确实掌握了此可验证凭据。

   ![新权限请求](media/get-started-verifiable-credentials/new-permission-request.png)

    成功演示后，三项内容应当更新完毕：

   1. 网页现在应显示“恭喜，<你的姓名>已成为已验证凭据专家！”
   
    ![恭喜，再次验证](media/get-started-verifiable-credentials/congratulations.png)


   2. 验证应用程序终端还应显示来自日志的相同消息。
   
    ![命令提示符中的应用程序活动](media/get-started-verifiable-credentials/cmd-verified-expert.png)

   3. 在 Authenticator 中，此演示的最近活动应有一个条目。

    ![Authenticator 中的活动](media/get-started-verifiable-credentials/recent-activity.png)

   
>[!NOTE]
> 运行验证应用程序时，ngrok 可能会停止工作并显示连接太多的错误。 我们发现，可通过将帐户注册到 ngrok 避免这种情况。 

## <a name="next-steps"></a>后续步骤

现在，你已成功完成快速入门指南，接下来可以在 Azure AD 的可验证凭据服务中创建自己的分散标识符，并颁发你自己的可验证凭据。

>[!div class="nextstepaction"] 
>[使用可验证凭据示例应用程序配置你自己的颁发者](./enable-your-tenant-verifiable-credentials.md)
