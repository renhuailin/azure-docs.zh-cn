---
title: 从源代码构建 Defender 微代理（预览）
titleSuffix: Azure Defender for IoT
description: 微代理包含一个基础结构，它可用于自定义分发版。
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/18/2021
ms.topic: quickstart
ms.service: azure
ms.openlocfilehash: 782c14c6feee56c4b19d5a234cb41186d51decb2
ms.sourcegitcommit: 4784fbba18bab59b203734b6e3a4d62d1dadf031
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2021
ms.locfileid: "99810016"
---
# <a name="build-the-defender-micro-agent-from-source-code"></a>从源代码构建 Defender 微代理

微代理包含一个基础结构，它可用于自定义分发版。 若要查看可用配置参数的列表，请查看 `configs/LINUX_BASE.conf` 文件。

对于单个分发，请修改基本 `.conf` 文件。 

如果需要多个分发，可从基本配置中继承并替代其值。 

若要替代值：

1. 创建新的 `.dist` 文件。

1. 在顶端添加 `CONF_DEFINE_BASE(${g_plat_config_path} LINUX_BASE.conf)`。
 
1. 将新值定义为你需要的任何值，例如： 

    `set(ASC_LOW_PRIORITY_INTERVAL 60*60*24)` 

1. 构建时向 `.dist` 文件提供引用。 例如， 

    `cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET=UBUNTU1804 ..` 

## <a name="baseline-configuration-signing"></a>基线配置签名 

默认情况下，该代理会验证磁盘上放置的配置文件的真实性来减少篡改风险。

可定义预处理器标志 `ASC_BASELINE_CONF_SIGN_CHECK_DISABLE` 来停止此进程。

建议不要对生产环境关闭签名检查。 

如果需要对生产方案使用其他配置，请联系 Defender for IoT 团队。 

## <a name="prerequisites"></a>先决条件 

1. 请联系你的帐户管理员，请求其向你授予访问 Defender for IoT 源代码的权限。
 
1. 克隆源代码或将其提取到磁盘上的文件夹中。

1. 导航到该目录。

1. 使用以下代码拉取子模块：

    ```bash
    git submodule update --init
    ```
    
1. 接下来，使用以下代码拉取 Azure IoT SDK 的子模块： 

    ```bash
    git -C deps/azure-iot-sdk-c/ submodule update –init
    ```
 

1. 添加执行权限并运行开发人员环境设置脚本：

    ```bash
    chmod +x scripts/install_development_environment.sh && ./scripts/install_development_environment.sh 
    ```

1. 为生成输出创建一个目录： 

    ```bash
    mkdir cmake 
    ```

1. （可选）下载并安装 [VSCode](https://code.visualstudio.com/download ) 

1. （可选）安装适用于 VSCode 的 [C/C++ 扩展](https://code.visualstudio.com/docs/languages/cpp )。

## <a name="building-the-defender-iot-micro-agent"></a>构建 Defender IoT 微代理 

1. 使用 VSCode 打开目录 

1. 导航到 `cmake` 目录。 

1. 运行以下命令： 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGET<the appropriate distro configuration file name> .. 
    
    cmake --build . -- -j${env:NPROC}
    ```

    例如： 

    ```bash
    cmake -DCMAKE_BUILD_TYPE=Debug -Dlog_level=DEBUG -Dlog_level_cmdline:BOOL=ON -DIOT_SECURITY_MODULE_DIST_TARGETUBUNTU1804 ..
    
    cmake --build . -- -j${env:NPROC}
    ```

## <a name="next-steps"></a>后续步骤

[配置 Azure Defender for IoT 解决方案](quickstart-configure-your-solution.md)。
