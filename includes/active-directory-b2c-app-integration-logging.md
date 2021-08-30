---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 991883785974648597141f0333679b39477a6ff9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121777819"
---
## <a name="configure-logging"></a>配置日志记录

MSAL 库会生成可帮助诊断问题的日志消息。 应用可以配置日志记录，并可对详细程度以及是否记录个人和组织数据进行自定义控制。 

建议创建 MSAL 日志记录回调，并提供一种方式来让用户在遇到身份验证问题时提交日志。 MSAL 提供多个日志记录详细级别：

- 错误：指示出现问题并已生成错误。 用于调试并确定问题。
- 警告：不一定会出现错误或故障，只是为了诊断和指出问题。
- 信息：MSAL 将要记录的事件可为用户提供信息，不一定用于调试。
- 详细：默认。 MSAL 将记录库行为的完整详细信息。

默认情况下，MSAL 记录器不捕获任何个人或组织数据。 该库提供相关选项，允许你自行决定是否记录个人和组织数据。



