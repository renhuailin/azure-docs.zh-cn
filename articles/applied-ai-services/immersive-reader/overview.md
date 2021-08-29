---
title: 什么是 Azure 沉浸式阅读器？
titleSuffix: Azure Applied AI Services
description: 沉浸式阅读器是一种工具，旨在帮助具有学习差异的用户，或者帮助新读者和语言学习者提高阅读理解能力。
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: applied-ai-services
ms.subservice: immersive-reader
ms.topic: overview
ms.date: 01/4/2020
ms.author: metang
ms.custom: cog-serv-seo-aug-2020
keywords: 读者, 语言学习者, 显示图片, 提高阅读能力, 阅读内容, 翻译
ms.openlocfilehash: 12d6f926934cf7b7c18d079d8d483824918057b1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121782176"
---
# <a name="what-is-azure-immersive-reader"></a>什么是 Azure 沉浸式阅读器？

[沉浸式阅读器](https://www.onenote.com/learningtools)是 [Azure 应用 AI 服务](../../applied-ai-services/what-are-applied-ai-services.md)的部件，它是一款具备包容性设计的工具，通过应用可靠的技术，帮助提高新读者、语言学习者和有学习差异（如阅读障碍）的用户的阅读理解能力。 通过沉浸式阅读器客户端库，你可利用 Microsoft Word 和 Microsoft OneNote 中所用的相同技术来改进 Web 应用程序。 

本文档包含以下类型的文章：  

* **[快速入门](quickstarts/client-libraries.md)** 介绍了入门说明，指导您完成向服务发出请求。
* **[操作指南](how-to-create-immersive-reader.md)** 包含以更具体的方式或自定义方式使用服务的说明。

## <a name="use-immersive-reader-to-improve-reading-accessibility"></a>使用沉浸式阅读器提升阅读辅助功能 

沉浸式阅读器旨在让每个人都能更轻松、更方便地阅读。 让我们看一下沉浸式阅读器的一些核心功能。

### <a name="isolate-content-for-improved-readability"></a>将内容划分开来提高可读性

沉浸式阅读器可将内容划分开来提高可读性。 

  ![使用沉浸式阅读器将内容划分开来提高可读性](./media/immersive-reader.png)

### <a name="display-pictures-for-common-words"></a>显示常用字词的图片

对于常用字词，沉浸式阅读器将显示图片。

  ![使用沉浸式阅读器绘制字典](./media/picture-dictionary.png)

### <a name="highlight-parts-of-speech"></a>突出显示语音的各个部分

沉浸式阅读器可突出显示动词、名词、代词等内容，用于帮助学习者了解语音和语法的各个部分。

  ![使用沉浸式阅读器显示语音的各个部分](./media/parts-of-speech.png)

### <a name="read-content-aloud"></a>朗读内容

语音合成（或文本转语音）已嵌入到沉浸式阅读器服务中，可让读者选择要朗读的文本。 

  ![使用沉浸式阅读器朗读文本](./media/read-aloud.png)

### <a name="translate-content-in-real-time"></a>实时翻译内容

沉浸式阅读器可将文本实时翻译成多种语言。 这有助于提高读者学习新语言时的理解力。

  ![使用沉浸式阅读器翻译文本](./media/translation.png)

### <a name="split-words-into-syllables"></a>将单词拆分为音节

通过沉浸式阅读器，可将单词拆分为音节，以提高可读性或读出新单词。

  ![使用沉浸式阅读器将单词拆分为音节](./media/syllabification.png)

## <a name="how-does-immersive-reader-work"></a>沉浸式阅读器如何工作？

沉浸式阅读器是一款独立的 Web 应用程序。 使用沉浸式阅读器调用客户端库时，将显示在 `iframe` 中的现有 Web 应用程序的顶部。 当 Web 应用程序调用沉浸式阅读器服务时，由你指定要显示阅读器的内容。 沉浸式阅读器客户端库会处理 `iframe` 的创建和样式设定，以及与沉浸式阅读器后端服务的通信。 沉浸式阅读器服务会处理语音各部分、文本转语音、翻译等的内容。

## <a name="get-started-with-immersive-reader"></a>开始使用沉浸式阅读器

可在 C#、JavaScript、Java (Android)、Kotlin (Android) 和 Swift (iOS) 中使用沉浸式阅读器客户端库。 通过以下方式入门：

* [快速入门：使用沉浸式阅读器客户端库](quickstarts/client-libraries.md)
