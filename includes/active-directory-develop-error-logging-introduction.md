---
author: mmacy
ms.author: marsma
ms.date: 12/16/2020
ms.service: active-directory
ms.subservice: develop
ms.topic: include
ms.openlocfilehash: 53198c663d318a2eb47bcb3207939bbcb1fdd59c
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763336"
---
Microsoft (MSAL) 应用程序的 Microsoft 身份验证库将生成日志消息，这些消息可帮助诊断问题。 应用可以通过数行代码配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 建议创建 MSAL 日志记录回调，并提供一种方式来让用户在遇到身份验证问题时提交日志。

## <a name="logging-levels"></a>日志记录级别

MSAL 提供多个日志记录详细级别：

- 错误：指示出现问题并已生成错误。 用于调试和确定问题。
- 警告：不一定会出现错误或故障，只是为了诊断和指出问题。
- 信息：MSAL 将要记录的事件可为用户提供信息，不一定用于调试。
- 详细：默认。 MSAL 将记录库行为的完整详细信息。

## <a name="personal-and-organizational-data"></a>个人和组织数据

默认情况下，MSAL 记录器不捕获任何高度敏感的个人或组织数据。 该库提供相关选项，允许你自行决定是否记录个人和组织数据。

以下各节提供了有关应用程序的 MSAL 错误日志记录的更多详细信息。