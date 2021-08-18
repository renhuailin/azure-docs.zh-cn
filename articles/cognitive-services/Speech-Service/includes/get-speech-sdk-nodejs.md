---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 03/27/2020
ms.author: lajanuar
ms.custom: devx-track-js
ms.openlocfilehash: 676c621c7e5e3eafcef30be1812049a4ac5744ff
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067775"
---
:::row:::
    :::column span="3":::
        JavaScript 语音 SDK 以 npm 包的形式提供（请参阅 <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">microsoft-cognitiveservices-speech-sdk </a> 及其配套的 GitHub 存储库库 <a href="https://github.com/Microsoft/cognitive-services-speech-sdk-js" target="_blank">cognitive-services-speech-sdk-js </a>。
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Node.js" src="https://docs.microsoft.com/media/logos/logo_nodejs.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

> [!TIP]
> 尽管适用于 JavaScript 的语音 SDK 以 npm 包的形式提供，但是 Node.js 和客户端 Web 浏览器都可以使用它 - 请考虑每种环境的各种体系结构含义。 例如，<a href="https://en.wikipedia.org/wiki/Document_Object_Model" target="_blank">文档对象模型 (DOM) </a> 不可用于服务器端应用程序，就像<a href="https://nodejs.org/api/fs.html" target="_blank">文件系统 </a> 不可用于客户端应用程序一样。

### <a name="nodejs-package-manager-npm"></a>Node.js 包管理器 (NPM)

若要安装 JavaScript 语音 SDK，请运行以下 `npm install` 命令。

```nodejs
npm install microsoft-cognitiveservices-speech-sdk
```

有关详细信息，请参阅 <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/node" target="_blank">Node.js 语音 SDK 快速入门</a>。
