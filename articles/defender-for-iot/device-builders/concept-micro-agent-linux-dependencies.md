---
title: 微代理 Linux 依赖项（预览版）
description: 本文介绍 Defender for IoT 微代理的不同 Linux 操作系统依赖项。
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 102cf31dbe19c068de344f1f7f294a6f3f0a722b
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967553"
---
# <a name="micro-agent-linux-dependencies-preview"></a>微代理 Linux 依赖项（预览版）

本文介绍 Defender for IoT 微代理的不同 Linux 操作系统依赖项。 

## <a name="linux-dependencies"></a>Linux 依赖项

下表显示了每个组件的 Linux 依赖项。 

| 组件 | 依赖项 | 类型 | IoT SDK 所必需 | 说明 |
|--|--|--|--|--|
| **核心** |  |  |  |  |
|  | libcurl-openssl (libcurl) | 库 | ✔ |  |
|  | libssl | 库 | ✔ |  |
|  | uuid | 库 | ✔ |  |
|  | pthread | 一元编译标志 | ✔ |  |
|  | libuv1 | 库 |  |  |
|  | sudo | 包裹 |  |  |
|  | uuid-runtime | 包裹 |  |  |
| 系统信息收集器 |  |  |  |  |
|  | uname | 系统调用 |  |  |
| 基线收集器 |  |  |  |  |
|  | BusyBox | Linux 编译标志 |  |  |
|  | Bash | Linux 编译标志 |  |  |
| 进程收集器 |  |  |  |  |
|  | CONFIG_CONNECTOR=y | 内核配置 |  |  |
|  | CONFIG_PROC_EVENTS=y | 内核配置 |  |  |
| 网络收集器 |  |  |  |  |
|  | libpcap | 库 |  |  |
|  | CONFIG_PACKET=y | 内核配置 |  |  |
|  | CONFIG_NETFILTER =y | 内核配置 |  | 可选 - 性能提高 |
| **登录收集器** |  |  |  |  |
|  | Wtmp、btmp | 日志文件 |  | [utmp](https://en.wikipedia.org/wiki/Utmp) |

## <a name="next-steps"></a>后续步骤

[安装 Defender for IoT 微代理（预览）](quickstart-standalone-agent-binary-installation.md)