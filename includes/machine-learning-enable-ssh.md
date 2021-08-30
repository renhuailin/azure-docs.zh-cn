---
author: sdgilley
ms.service: machine-learning
ms.topic: include
ms.date: 07/30/2021
ms.author: sgilley
ms.openlocfilehash: bd6760d152edeb3f8e50fa50a5b72476fc46150f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736585"
---
选择“下一步: 高级设置”后：

1. 打开“启用 SSH 访问”。
1. 在“SSH 公钥源”中，从下拉列表中选择一个选项：
    * 如果选择的是“生成新的密钥对”：
        1. 请在“密钥对名称”中输入密钥的名称。
        1. 选择“创建”。
        1. 选择“下载私钥并创建计算”。  密钥通常下载到 Downloads 文件夹中。  
    * 如果选择的是“使用存储在 Azure 中的现有公钥”，请在“存储的密钥”中搜索并选择该密钥 。
    * 如果选择的是“使用现有公钥”，请提供单行格式（以“ssh-rsa”开头）或多行 PEM 格式的 RSA 公钥。 可以在 Linux 和 OS X 上使用 ssh-keygen 生成 SSH 密钥，或在 Windows 上使用 PuTTYGen 生成这些密钥。