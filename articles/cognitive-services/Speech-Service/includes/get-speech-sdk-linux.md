---
author: laujan
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: lajanuar
ms.openlocfilehash: 342cf69a7ef50b38a50d9d95af50ea60e5c19258
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067770"
---
:::row:::
    :::column span="3":::
        在 Linux 上使用时，语音 SDK 仅支持以下目标体系结构上的 Ubuntu 16.04（截至 9 月）、Ubuntu 18.04/20.04、Debian 9/10、Red Hat Enterprise Linux (RHEL) 7/8 和 CentOS 7/8：    
    :::column-end:::
    :::column:::
        <br>
        <div class="icon is-large">
            <img alt="Linux" src="/media/logos/logo_linux-color.svg" width="60px">
        </div>
    :::column-end:::
:::row-end:::

- 用于 C++ 开发的 x86 (Debian/Ubuntu)、x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu)
- 用于 Java 的 x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu)
- 用于 .NET Core 的 x64、ARM32 (Debian/Ubuntu) 和 ARM64 (Debian/Ubuntu)
- 适用于 Python 的 x64

> [!IMPORTANT]
> 对于 Linux ARM64 上的 C#，需要 .NET Core 3.x（dotnet-sdk-3.x 包）。

> [!NOTE]
> 若要在 Alpine Linux 中使用语音 SDK，请按照 Alpine Linux Wiki 中的[运行 glibc 程序](https://wiki.alpinelinux.org/wiki/Running_glibc_programs)所述创建 Debian chroot 环境，然后按照此处的 Debian 说明操作。

### <a name="system-requirements"></a>系统要求

对于本机应用程序，语音 SDK 依赖于 `libMicrosoft.CognitiveServices.Speech.core.so`。 请确保目标体系结构（x86、x64）与应用程序匹配。 可能需要其他依赖项，具体取决于 Linux 版本。

- GNU C 库的共享库（包括 POSIX 线程编程库 `libpthreads`）
- OpenSSL 库（`libssl.so.1.0.0` 或 `libssl.so.1.0.2`）
- ALSA 应用程序的共享库 (`libasound.so.2`)

# <a name="ubuntu-160418042004"></a>[Ubuntu 16.04/18.04/20.04](#tab/ubuntu)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.0 libasound2 wget
```

> [!NOTE]
> 如果 libssl1.0.x 不可用，请改为安装 libssl1.1。

# <a name="debian-910"></a>[Debian 9/10](#tab/debian)

```Bash
sudo apt-get update
sudo apt-get install build-essential libssl1.0.2 libasound2 wget
```

> [!NOTE]
> 如果 libssl1.0.x 不可用，请改为安装 libssl1.1。

# <a name="rhel-78-and-centos-78"></a>[RHEL 7/8 和 CentOS 7/8](#tab/rhel-centos)

```Bash
sudo yum update
sudo yum groupinstall "Development tools"
sudo yum install alsa-lib openssl wget
```

> [!IMPORTANT]
> - 在 RHEL/CentOS 7 上，按照[如何为语音 SDK 配置 RHEL/CentOS 7](~/articles/cognitive-services/speech-service/how-to-configure-rhel-centos-7.md) 上的说明进行操作。
> - 在 RHEL/CentOS 8 上，按照[如何配置 OpenSSL for Linux](~/articles/cognitive-services/speech-service/how-to-configure-openssl-linux.md) 上的说明进行操作。

---

### <a name="c"></a>C#

[!INCLUDE [Get .NET Speech SDK](get-speech-sdk-dotnet.md)]

### <a name="c"></a>C++

[!INCLUDE [Get C++ Speech SDK](get-speech-sdk-cpp.md)]

### <a name="python"></a>Python

[!INCLUDE [Get Python Speech SDK](get-speech-sdk-python.md)]

### <a name="java"></a>Java

[!INCLUDE [Get Java Speech SDK](get-speech-sdk-java.md)]
