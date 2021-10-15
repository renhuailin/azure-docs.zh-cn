---
title: Azure IoT Edge 安全管理器/模块运行时 - Azure IoT Edge
description: 管理 IoT Edge 设备安全性情况和安全服务的完整性。
services: iot-edge
keywords: 安全性, 安全元素, enclave, TEE, IoT Edge
author: kgremban
ms.author: kgremban
ms.reviewer: eustacea
ms.date: 09/17/2021
ms.topic: conceptual
ms.service: iot-edge
ms.openlocfilehash: fedb06a23a71dbe30954154daa6d314267268551
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659671"
---
# <a name="azure-iot-edge-security-manager"></a>Azure IoT Edge 安全管理器

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge 安全管理器是一种界限明确的安全内核，用于通过抽象化安全硅硬件来保护 IoT Edge 设备及其所有组件。 安全管理器是增强安全性的重点，为原始设备制造商 (OEM) 提供技术集成点。

<!--1.1-->
:::moniker range="iotedge-2018-06"
安全管理器在 IoT Edge 设备上抽象化安全硅硬件。

![Azure IoT Edge 安全管理器](media/edge-security-manager/iot-edge-security-manager.png)
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"
安全管理器在 IoT Edge 设备上抽象化安全硅硬件，并提供适用于其他安全服务的扩展性框架。

<!-- add new graphic here -->
:::moniker-end

IoT Edge 安全管理器旨在保护 IoT Edge 设备和所有固有软件操作的完整性。 安全管理器会转换来自基础硬件信任根（如果可用）的信任，以便启动 IoT Edge 运行时并监视持续进行的操作。  IoT Edge 安全管理器是与安全硅硬件（如果可用）结合使用的软件，以帮助提供最高安全保证。  

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
此外，IoT Edge安全管理器通过主机级别模块提供适用于安全服务扩展的安全框架。 这些服务包括安全监视和更新，要求设备内的代理具有对设备某些组件的特权访问权限。 扩展性框架可确保此类集成一致地维护整体系统安全性。
:::moniker-end

IoT Edge 安全管理器的职责包括但不限于：

<!--1.1-->
:::moniker range="iotedge-2018-06"

* 启动 Azure IoT Edge 设备。
* 通过公证服务控制对设备硬件信任根的访问。
* 监视运行时 IoT Edge 操作的完整性。
* 从硬件安全模块 (HSM) 接收信任委派
* 预配设备标识和管理信任转换（如果适用）。
* 托管和保护云服务（如设备预配服务）的设备组件。
* 使用唯一标识预配 IoT Edge 模块。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* 启动 Azure IoT Edge 设备。
* 通过公证服务控制对设备硬件信任根的访问。
* 监视运行时 IoT Edge 操作的完整性。
* 预配设备标识和管理信任转换（如果适用）。
* 确保服务的客户端代理的安全操作（包括 IoT 中心和 Azure Defender for IoT 的设备更新）。
:::moniker-end

IoT Edge 安全管理器包含三个组件：

<!--1.1-->
:::moniker range="iotedge-2018-06"

* IoT Edge 安全守护程序
* 硬件安全模块平台抽象层 (HSM PAL)
* 硬件硅信任根或 HSM（可选但强烈推荐）
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

* IoT Edge 模块运行时
* 通过标准实现（如 PKCS#11 和受信任的平台模块 (TPM)）的硬件安全模块 (HSM) 抽象
* 硬件硅信任根或 HSM（可选但强烈推荐）

## <a name="changes-in-version-12"></a>版本 1.2 中的更改

在 IoT Edge 版本 1.0 和 1.1 中，名为“安全守护程序”的组件负责安全管理器的逻辑安全操作。 更新到版本 1.2 时，向 [Azure IoT 标识服务](https://azure.github.io/iot-identity-service/)安全子系统委派了多个主要职责。 从安全守护程序中删除这些基于安全的任务后，其名称不再有意义。 为了更好地反映此组件在版本 1.2 及更高版本中进行的工作，我们将其重命名为“模块运行时”。
:::moniker-end

<!--1.1-->
:::moniker range="iotedge-2018-06"
## <a name="the-iot-edge-security-daemon"></a>IoT Edge 安全守护程序

IoT Edge 安全守护程序负责安全管理器的逻辑安全操作。 它表示 IoT Edge 设备的受信任计算基础的重要部分。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
## <a name="the-iot-edge-module-runtime"></a>IoT Edge 模块运行时

IoT Edge 模块运行时委派 [Azure IoT 标识服务](https://azure.github.io/iot-identity-service/)安全子系统的信任，以保护 IoT Edge 容器运行时环境。 模块运行时负责安全管理器的逻辑安全操作。 它表示 IoT Edge 设备的受信任计算基础的重要部分。 模块运行时使用 IoT 标识服务中的安全服务，反过来被设备制造商选择的硬件安全模块 (HSM) 强化。 我们强烈建议使用 HSM 进行设备强化。
:::moniker-end

### <a name="design-principles"></a>设计原理

IoT Edge 遵循两个核心原则：最大程度地提高操作完整性，并最大程度地减小膨胀和改动。

#### <a name="maximize-operational-integrity"></a>最大程度地提高操作完整性

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 安全守护程序会在任何给定硬件信任根的防御功能内尽可能以最高完整性运行。 通过适当集成，硬件信任根以静态方式度量并监视安全守护程序，并在运行时抵御篡改。
:::moniker-end

<!--1.1-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 模块运行时会在任何给定硬件信任根的防御功能内尽可能以最高完整性运行。 通过适当集成，硬件信任根以静态方式度量并监视安全守护程序，并在运行时抵御篡改。
:::moniker-end

恶意物理访问设备始终是 IoT 中的一种威胁。 硬件信任根对于保护 IoT Edge 设备的完整性起重要作用。  硬件信任根分为两类：

* 用于保护敏感信息（如机密和加密密钥）的安全元素。
* 用于保护机密（如密钥）和敏感工作负载（如机密机器学习模型和计量操作）的安全 enclave。

存在两种类型的可以使用硬件信任根的执行环境：

* 标准或丰富执行环境 (REE)，依赖于使用安全元素来保护敏感信息。
* 受信任的执行环境 (TEE)，依赖于使用安全 enclave 技术来保护敏感信息并对软件执行提供保护。

<!--1.1-->
:::moniker range="iotedge-2018-06"
对于使用安全 enclave 作为硬件信任根的设备，IoT Edge 安全守护程序中的敏感逻辑应该位于 enclave 内。  安全守护程序的非敏感部分可以位于 TEE 外部。  在所有情况下，我们强烈建议原始设计制造商 (ODM) 和原始设备制造商 (OEM) 都从其 HSM 扩展信任，以在启动和运行时度量和保护 IoT Edge 安全守护程序的完整性。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
对于使用安全 enclave 作为硬件信任根的设备，IoT Edge 模块运行时中的敏感逻辑应该位于 enclave 内。  模块运行时的非敏感部分可以位于 TEE 外部。  在所有情况下，我们强烈建议原始设计制造商 (ODM) 和原始设备制造商 (OEM) 都从其 HSM 扩展信任，以在启动和运行时度量和保护 IoT Edge 模块运行时的完整性。
:::moniker-end

#### <a name="minimize-bloat-and-churn"></a>最大程度地减小膨胀和改动

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 安全守护程序的另一个核心原则是最大程度地减小改动。  对于最高的信任级别，IoT Edge 安全守护程序可与设备硬件信任根紧密结合，并作为本机代码运行。  在这些情况下，通常可通过硬件信任根的安全更新路径（而不是操作系统的更新机制，这可能比较困难）来更新 IoT Edge 软件。  虽然建议为 IoT 设备进行安全续订，但更新需求过多或更新有效负载过大会以多种方式扩展威胁面。 例如，你可能想要跳过某些更新，从而使设备可用性达到最高。 因此，IoT Edge 安全守护程序的设计简洁明了，保持独立的受信任计算基础较小，以鼓励频繁更新。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 模块运行时的另一个核心原则是最大程度地减小改动。  对于最高的信任级别，IoT Edge 模块运行时可与设备硬件信任根紧密结合，并作为本机代码运行。  在这些情况下，通常可通过硬件信任根的安全更新路径（而不是操作系统的更新机制，这可能比较困难）来更新 IoT Edge 软件。  虽然建议为 IoT 设备进行安全续订，但更新需求过多或更新有效负载过大会以多种方式扩展威胁面。 例如，你可能想要跳过某些更新，从而使设备可用性达到最高。 因此，IoT Edge 模块运行时的设计简洁明了，保持独立的受信任计算基础较小，以鼓励频繁更新。
:::moniker-end

### <a name="architecture"></a>体系结构

<!--1.1-->
:::moniker range="iotedge-2018-06"

IoT Edge 安全守护程序可充分利用任何可用硬件信任根技术来增强安全性。  此外，在硬件技术提供受信任的执行环境的情况下，它还允许在标准/丰富执行环境 (REE) 和受信任的执行环境 (TEE) 之间进行分字运算。 特定于角色的接口可启用 IoT Edge 的主要组件，确保 IoT Edge 设备及其操作的完整性。

![Azure IoT Edge 安全守护程序体系结构](media/edge-security-manager/iot-edge-security-daemon.png)
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

IoT Edge 模块运行时充分利用任何可用硬件信任根技术来增强安全性。  此外，在硬件技术提供受信任的执行环境的情况下，它还允许在标准/丰富执行环境 (REE) 和受信任的执行环境 (TEE) 之间进行分字运算。 特定于角色的接口可启用 IoT Edge 的主要组件，确保 IoT Edge 设备及其操作的完整性。

![Azure IoT Edge 模块运行时体系结构](media/edge-security-manager/iot-edge-module-runtime.png)
:::moniker-end

#### <a name="cloud-interface"></a>云接口

云接口允许访问补充设备安全性的云服务。  例如，此接口允许访问[设备预配服务](../iot-dps/index.yml)，以便进行设备标识生命周期管理。  

#### <a name="management-api"></a>管理 API

<!--1.1-->
:::moniker range="iotedge-2018-06"
在创建/启动/停止/删除 IoT Edge 模块时，IoT Edge 代理会调用管理 API。 安全守护程序存储所有活动模块的“注册”。 这些注册将模块的标识映射到该模块的某些属性。 例如，这些模块属性包括容器中运行的进程的进程标识符 (pid) 和 docker 容器内容的哈希值。

工作负荷 API 使用这些属性（如下所述）来验证调用方是否有权执行某一操作。

管理 API 是一个特权 API，仅可从 IoT Edge 代理进行调用。  IoT Edge 安全守护程序启动后，它会启动 IoT Edge 代理并验证 IoT Edge 代理是否未被篡改，然后就可以为 IoT Edge 代理创建隐式注册。 工作负载 API 使用的相同证明过程也会将管理 API 的访问权限限制为仅供 IoT Edge 代理使用。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
在创建/启动/停止/删除 IoT Edge 模块时，IoT Edge 代理会调用管理 API。 模块运行时存储所有活动模块的“注册”。 这些注册将模块的标识映射到该模块的某些属性。 例如，这些模块属性包括容器中运行的进程的进程标识符 (pid) 和 docker 容器内容的哈希值。

工作负荷 API 使用这些属性（如下所述）来验证调用方是否有权执行某一操作。

管理 API 是一个特权 API，仅可从 IoT Edge 代理进行调用。  IoT Edge 模块运行时启动后，它会启动 IoT Edge 代理并验证 IoT Edge 代理是否未被篡改，然后就可以为 IoT Edge 代理创建隐式注册。 工作负载 API 使用的相同证明过程也会将管理 API 的访问权限限制为仅供 IoT Edge 代理使用。
:::moniker-end

#### <a name="container-api"></a>容器 API

容器 API 会与正在使用的容器系统（例如 Moby 或 Docker）交互，以便进行模块管理。

#### <a name="workload-api"></a>工作负载 API

工作负荷 API 可供所有模块访问。 它向模块提供标识证明（以 HSM 根签名令牌或 X509 证书的形式）和相应的信任捆绑包。 信任捆绑包包含模块应信任的所有其他服务器的 CA 证书。

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 安全守护程序使用证明过程来保护此 API。 当模块调用此 API 时，安全守护程序尝试查找标识的注册。 如果成功，则其使用注册的属性来度量该模块。 如果度量过程的结果与注册匹配，则生成新的标识证明。 相应 CA 证书（信任捆绑包）也返回到模块。  该模块使用此证书来连接到 IoT 中心、其他模块或启动服务器。 当签名令牌或证书即将到期时，模块有责任请求新的证书。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 模块运行时使用证明过程来保护此 API。 当模块调用此 API 时，模块运行时尝试查找标识的注册。 如果成功，则其使用注册的属性来度量该模块。 如果度量过程的结果与注册匹配，则生成新的标识证明。 相应 CA 证书（信任捆绑包）也返回到模块。  该模块使用此证书来连接到 IoT 中心、其他模块或启动服务器。 当签名令牌或证书即将到期时，模块有责任请求新的证书。
:::moniker-end

### <a name="integration-and-maintenance"></a>集成和维护

<!--1.1-->
:::moniker range="iotedge-2018-06"
Microsoft 维护 [GitHub 上的 IoT Edge 安全守护程序](https://github.com/Azure/iotedge/tree/release/1.1/edgelet)的主要代码库。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Microsoft 维护 GitHub 上的 [IoT Edge 模块运行时](https://github.com/Azure/iotedge/tree/master/edgelet)和 [Azure IoT 标识服务](https://github.com/Azure/iot-identity-service)的主要代码库。

读取 IoT Edge 代码库时，请记住模块运行时是从安全守护程序演变而来的。 代码库可能仍包含对安全守护程序的引用。
:::moniker-end

#### <a name="installation-and-updates"></a>安装和更新

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 安全守护程序的安装和更新通过操作系统的程序包管理系统进行管理。 具有硬件信任根的 IoT Edge 设备应通过安全启动和更新管理系统来管理守护程序的生命周期，从而进一步增强其完整性。 设备制造商应该根据各自的设备功能来探索这些方法。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 模块运行时的安装和更新通过操作系统的程序包管理系统进行管理。 具有硬件信任根的 IoT Edge 设备应通过安全启动和更新管理系统来管理模块运行时的生命周期，从而进一步增强其完整性。 设备制造商应该根据各自的设备功能来探索这些方法。
:::moniker-end

#### <a name="versioning"></a>版本控制

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge 运行时可跟踪和报告 IoT Edge 安全守护程序的版本。 该版本报告为 IoT Edge 代理模块报告的属性的 runtime.platform.version 特性。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 运行时可跟踪和报告 IoT Edge 模块运行时的版本。 该版本报告为 IoT Edge 代理模块报告的属性的 runtime.platform.version 特性。
:::moniker-end

## <a name="hardware-security-module"></a>硬件安全模块

<!--1.1-->
:::moniker range="iotedge-2018-06"
硬件安全模块平台抽象层 (HSM PAL) 将所有硬件信任根抽象化，从而使 IoT Edge 开发者或用户从其复杂性中解脱出来。  它包括应用程序编程接口 (API) 和跨域通信过程的结合，例如标准执行环境和安全 enclave 之间的通信。  HSM PAL 的实际实现取决于使用的特定安全硬件。 它的存在允许使用几乎任何安全硅硬件。
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
IoT Edge 安全管理器实现了受信任的平台模块和 PKCS#11 接口标准，以用于集成硬件安全模块 (HSM)。 借助这些标准，几乎任何 HSM （包括具有专有接口的 HSM）都可以集成。 我们强烈建议使用 HSM 进行安全性强化。
:::moniker-end

## <a name="secure-silicon-root-of-trust-hardware"></a>安全硅硬件信任根

安全硅必须在 IoT Edge 设备硬件内定位标记信任。  安全硅种类繁多，包括受信任的平台模块 (TPM)、嵌入式安全元素 (eSE)、ARM TrustZone、Intel SGX 和自定义安全硅技术。  考虑到与 IoT 设备物理访问相关联的威胁，建议在设备中使用安全硅信任根。

IoT Edge 安全管理器旨在标识并隔离保护 Azure IoT Edge 平台的安全性和完整性的组件，以增强自定义。 第三方（如设备制造商）应使用其设备硬件提供可用的自定义安全功能。  

了解如何使用软件或虚拟 TPM 通过受信任的平台模块 (TPM) 强化 Azure IoT 安全管理器：  

[使用 Linux 虚拟机上的虚拟 TPM](how-to-provision-devices-at-scale-linux-tpm.md) 创建和预配 IoT Edge 设备。

<!--1.1-->
:::moniker range="iotedge-2018-06"
在 Windows 上使用模拟的 TPM 创建和预配 [IoT Edge 设备](how-to-provision-devices-at-scale-windows-tpm.md)。
:::moniker-end

## <a name="next-steps"></a>后续步骤

若要详细了解如何保护 IoT Edge 设备，请阅读以下博客文章：

* [保护智能边缘](https://azure.microsoft.com/blog/securing-the-intelligent-edge/)。
* [大规模安全地解决 IoT 设备中难以发现的零接触预配的蓝图](https://azure.microsoft.com/blog/the-blueprint-to-securely-solve-the-elusive-zerotouch-provisioning-of-iot-devices-at-scale/)
* [通过标准大规模解决 IoT 设备安全](https://azure.microsoft.com/blog/solving-iot-device-security-at-scale-through-standards/)