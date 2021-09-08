---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 07/29/2021
ms.author: mimart
ms.openlocfilehash: 720b464c02b929590df46123e0caf9a9e83fe2f4
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220275"
---
## <a name="configure-logging"></a>配置日志记录

MSAL 库生成可帮助诊断问题的日志消息。 应用可配置日志记录。 应用还可以让你对详细程度以及是否记录个人和组织数据进行自定义控制。 

建议创建 MSAL 日志记录回叫，并提供一种方式来让用户在遇到身份验证问题时提交日志。 MSAL 提供以下级别的日志记录详细信息：

- **错误**：出现问题并已生成错误。 此级别用于调试并确定问题。
- **警告**：不一定会出现错误或故障，但该信息用于诊断和找到问题。
- **信息**：MSAL 记录用于参考但不一定用于调试的事件。
- **详细**：这是默认级别。 MSAL 将记录库行为的完整详细信息。

默认情况下，MSAL 记录器不捕获任何个人或组织数据。 该库提供相关选项，允许你自行决定是否记录个人和组织数据。
