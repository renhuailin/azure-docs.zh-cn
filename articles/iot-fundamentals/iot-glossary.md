---
title: Azure IoT 术语词汇表 | Microsoft Docs
description: 开发人员指南 - 一个词汇表，解释 Azure IoT 文章中常用的一些术语。
author: dominicbetts
ms.author: dobett
ms.service: iot-fundamentals
services: iot-fundamentals
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 91604758ad2e53b3715a5b5c746959b3fe124ba6
ms.sourcegitcommit: 8669087bcbda39e3377296c54014ce7b58909746
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/18/2021
ms.locfileid: "114403390"
---
# <a name="glossary-of-iot-terms"></a>IoT 术语词汇表

本文列出了 IoT 文章中常用的一些术语。

## <a name="a"></a>A

### <a name="advanced-message-queueing-protocol"></a>高级消息队列协议

[高级消息队列协议 (AMQP)](https://www.amqp.org/) 是 [IoT 中心](#iot-hub)支持的一种消息传送协议，适用于与设备通信。 有关 IoT 中心支持的消息传递协议的详细信息，请参阅[使用 IoT 中心发送和接收消息](../iot-hub/iot-hub-devguide-messaging.md)。

### <a name="allocation-policy"></a>分配策略

在[设备预配服务](#device-provisioning-service)中，分配策略确定服务如何将设备分配到[链接的 IoT 中心](#linked-iot-hub)。

### <a name="attestation-mechanism"></a>证明机制

在[设备预配服务](#device-provisioning-service)中，证明机制是用于确认设备标识的方法。 证明机制是在[注册](#enrollment)上配置的。

证明机制包括 X.509 证书、受信任的平台模块和对称密钥。

### <a name="automatic-deployment"></a>自动部署

在 IoT Edge 中，自动部署配置一组目标 IoT Edge 设备来运行一组 IoT Edge 模块。 每次部署持续确保符合其目标条件的所有设备运行指定的模块集，即使是在创建新设备或者为满足目标条件修改设备时。 每个 IoT Edge 设备仅接收满足其目标条件的最高优先级部署。 了解有关 [IoT Edge 自动部署](../iot-edge/module-deployment-monitoring.md)的详细信息。

### <a name="automatic-device-configuration"></a>自动设备配置

解决方案后端可以使用[自动设备配置](../iot-hub/iot-hub-automatic-device-management.md)将所需属性分配到一组[设备孪生](#device-twin)，并使用系统指标和自定义指标来报告状态。

### <a name="automatic-device-management"></a>自动设备管理

Azure IoT 中心内的自动设备管理功能可将许多复杂且重复性的任务自动化，包括在大型设备阵列的整个生命周期内对其进行管理。 使用自动设备管理，可以根据设备的属性将一组设备指定为目标、定义所需的配置，并在设备进入管理范畴时让 IoT 中心更新这些设备。  包括[自动设备配置](../iot-hub/iot-hub-automatic-device-management.md)和 [IoT Edge 自动部署](../iot-edge/how-to-deploy-at-scale.md)。

### <a name="azure-digital-twins"></a>Azure 数字孪生

Azure 数字孪生是一款平台即服务 (PaaS) 产品/服务，可用于创建现实世界物品、地点、业务流程和人员的数字表示形式。 构建代表整个环境的孪生图，并使用它们来获得见解，以促成更好的产品，优化运营和成本，以及打造突破性的客户体验。 若要了解详细信息，请参阅 [Azure 数字孪生](../digital-twins/index.yml)。

### <a name="azure-digital-twins-instance"></a>Azure 数字孪生实例

客户订阅中 Azure 数字孪生服务的单个实例。 虽然 [Azure 数字孪生](#azure-digital-twins)是指作为一个整体的 Azure 服务，但 Azure 数字孪生 **实例** 是指个体 Azure 数字孪生资源。

### <a name="azure-iot-device-sdks"></a>Azure IoT 设备 SDK

提供了多种语言的 _设备 SDK_ ，以便于用户创建与 IoT 中心交互的 [设备应用](#device-app) 。 IoT 中心教程介绍了如何使用这些设备 SDK。 可以在此 GitHub [存储库](https://github.com/Azure/azure-iot-sdks)中找到有关设备 SDK 的源代码和进一步信息。

### <a name="azure-iot-explorer"></a>Azure IoT 资源管理器

[Azure IoT 资源管理器](https://github.com/Azure/azure-iot-explorer)用于查看设备正在发送的遥测、使用设备属性和调用命令。 还可以使用资源管理器来与设备进行交互并对设备进行测试，以及管理 [IoT 即插即用设备](#iot-plug-and-play-device)。

### <a name="azure-iot-service-sdks"></a>Azure IoT 服务 SDK

提供了多种语言的 _服务 SDK_ ，以便于用户创建与 IoT 中心交互的 [后端应用](#back-end-app) 。 IoT 中心教程介绍了如何使用这些服务 SDK。 可以在此 GitHub [存储库](https://github.com/Azure/azure-iot-sdks)中找到有关服务 SDK 的源代码和进一步信息。

### <a name="azure-iot-tools"></a>Azure IoT Tools

[Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)是一个跨平台的开放源代码 Visual Studio Code 扩展，可帮助你在 VS Code 中管理 Azure IoT 中心和设备。 使用 Azure IoT 工具，IoT 开发人员可以轻松在 VS Code 中开发 IoT 项目。

## <a name="b"></a>B

### <a name="back-end-app"></a>后端应用

在 [IoT 中心](#iot-hub)环境中，后端应用是指连接到 IoT 中心的一个面向服务的终结点的应用。 例如，后端应用可能检索[设备到云](#device-to-cloud)的消息或管理[标识注册表](#identity-registry)。 通常，后端应用在云中运行，但在许多教程中，后端应用是在本地开发计算机上运行的控制台应用。

### <a name="built-in-endpoints"></a>内置终结点

内置到 IoT 中心的一种[终结点](#endpoint)。 每个 IoT 中心都包括一个与事件中心兼容的内置[终结点](../iot-hub/iot-hub-devguide-endpoints.md)。 可以使用任何适用于事件中心的机制从此终结点读取设备到云消息。

## <a name="c"></a>C

### <a name="cloud-gateway"></a>云网关

云网关使不能直接连接到 [IoT 中心](#iot-hub)的设备能建立连接。 和在设备本地运行的 [现场网关](#field-gateway) 相反，云网关在云中托管。 云网关的一个典型用例是实现设备的协议转换。

### <a name="cloud-property"></a>云属性

在 IoT Central 中，云属性是[设备模板](#device-model)的一部分，但不是[设备模型](#device-model)的一部分。 云属性使解决方案开发者可以指定要存储在 IoT Central 应用程序中的任何设备元数据。 云属性不影响设备开发者为实现设备模型而编写的代码。

### <a name="cloud-to-device"></a>云到设备

指从 IoT 中心发送到已连接设备的消息。 这些消息通常是命令，用于指示设备采取某项操作。 有关详细信息，请参阅[使用 IoT 中心发送和接收消息](../iot-hub/iot-hub-devguide-messaging.md)。

### <a name="commands"></a>命令

在 IoT 即插即用中，[接口](#interface)中定义的命令表示可对[数字孪生体](#digital-twin)执行的方法。 例如，用于重新启动设备的命令。

### <a name="component"></a>组件

在 IoT 即插即用和 Azure 数字孪生中，可以通过组件将一个模型[接口](#interface)构建为其他接口的程序集。 [设备模型](#device-model)可将多个接口组合为组件。 例如，一个模型可以包含交换机组件和恒温器组件。 模型中的多个组件也可以使用同一接口类型。 例如，一个模型可以包含两个恒温器组件。

### <a name="configuration"></a>配置

在使用[自动设备配置](../iot-hub/iot-hub-automatic-device-management.md)的情况下，可以通过 IoT 中心的配置为一组设备孪生定义所需的配置，并提供一组指标来报告状态和进度。

### <a name="connection-string"></a>连接字符串

使用应用程序代码中的连接字符串来封装连接到终结点所需的信息。 连接字符串通常包含终结点的地址和安全信息，但连接字符串的格式因服务而异。 与 IoT 中心服务关联的连接字符串有两种：

- *设备连接字符串* 使设备能够连接到 IoT 中心上面向设备的终结点。

- *IoT 中心连接字符串* 使后端应用能够连接到 IoT 中心上面向服务的终结点。

### <a name="custom-endpoints"></a>自定义终结点

可在 IoT 中心创建自定义[终结点](../iot-hub/iot-hub-devguide-endpoints.md)来传递由[路由规则](#routing-rules)调度的消息。 自定义终结点会直接连接到事件中心、服务总线队列或服务总线主题。

### <a name="custom-gateway"></a>自定义网关

网关使不能直接连接到 [IoT 中心](#iot-hub)的设备能建立连接。 可以使用 Azure IoT Edge 生成自定义网关，以便使用自定义逻辑处理消息、自定义协议转换和进行边缘上的其他处理。

## <a name="d"></a>D

### <a name="data-point-message"></a>数据点消息

数据点消息是指[设备到云](#device-to-cloud)的消息，其中包含[遥测](#telemetry)数据（例如风速或温度）。

### <a name="default-component"></a>默认组件

在 IoT 即插即用中，所有[设备模型](#device-model)都有一个默认组件。 简单设备模型只有一个默认组件 - 此类模型也称为无组件设备。 较复杂的模型在默认组件下嵌套多个组件。

### <a name="deployment-manifest"></a>部署清单

在 [IoT Edge](#iot-edge) 中，部署清单是一个 JSON 文档，它包含要在一个或多个 IoT Edge 设备的模块孪生中复制的信息，用以部署一组模块、路由和关联的模块所需属性。

### <a name="desired-configuration"></a>所需配置

在与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)相关的语境中，所需配置是指设备孪生中要与设备同步的完整的属性和元数据集。

### <a name="desired-properties"></a>所需属性

在与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)相关的语境中，所需属性是设备孪生的一部分，和[报告属性](#reported-properties)一起用于同步设备配置或条件。 所需属性只能由[后端应用](#back-end-app)设置，并由[设备应用](#device-app)遵守。

### <a name="device"></a>设备

在 IoT 上下文中，设备通常是指小型、独立的计算设备，可用于收集数据或控制其他设备。 例如，设备可以是环境监视设备，也可以是控制器，控制温室中的浇水和通风系统。 [设备目录](https://catalog.azureiotsolutions.com/)提供经过认证的可用于 [IoT 中心](#iot-hub)的硬件设备列表。

### <a name="device-app"></a>设备应用

设备应用在用户的[设备](#device)上运行，处理与 [IoT 中心](#iot-hub)的通信。 通常情况下，实现设备应用时会使用一个 [Azure IoT 设备 SDK](#azure-iot-device-sdks) 。 在许多 IoT 教程中，为方便起见使用 [模拟设备](#simulated-device) 。

### <a name="device-builder"></a>设备制造商

设备制造商在实现运行于 [IoT 即插即用设备](#iot-plug-and-play-device)上的代码时使用[设备模型](#device-model)和[接口](#interface)。 设备制造商通常使用某个 [Azure IoT 设备 SDK](#azure-iot-device-sdks) 来实现设备客户端。

### <a name="device-certification"></a>设备认证

IoT 即插即用认证计划验证设备是否符合 IoT 即插即用认证要求。 可将已验证的设备添加到公共 [Azure IoT 认证设备目录](https://aka.ms/devicecatalog)。

### <a name="device-condition"></a>设备条件

指 [设备应用](#device-app)报告的设备状态信息，例如当前正在使用的连接方法。 [设备应用](#device-app) 还可以报告其功能。 可以使用设备孪生查询条件和功能的信息。

### <a name="device-data"></a>设备数据

设备数据是指存储在 IoT 中心 [标识注册表](#identity-registry)中的每个设备数据。 可以导入和导出此数据。

### <a name="device-identity"></a>设备标识

设备标识（或设备 ID）是分配给在 IoT 中心[标识注册表](#identity-registry)中注册的每个设备的唯一标识符。

### <a name="device-management"></a>设备管理

设备管理包含在 IoT 解决方案中管理设备的完整生命周期，包括规划、预配、配置、监视和停用设备。

### <a name="device-management-patterns"></a>设备管理模式

[IoT 中心](#iot-hub) 支持常见的设备管理模式，包括重新启动、执行恢复出厂设置，以及执行设备的固件更新。

### <a name="device-model"></a>设备型号

设备模型是使用[数字孪生体定义语言](#digital-twins-definition-language-dtdl)来描述 IoT 即插即用设备功能的一种[模型](#model)。 简单设备模型使用单个接口来描述设备功能。 较复杂的设备模型包含多个组件，每个组件描述一组功能。 有关详细信息，请参阅[模型中的 IoT 即插即用组件](../iot-develop/concepts-modeling-guide.md)。

### <a name="device-modeling"></a>设备建模

[设备制造商](#device-builder)或[模块制造商](#module-builder)使用[数字孪生体定义语言](#digital-twins-definition-language-dtdl)为 [IoT 即插即用设备](#iot-plug-and-play-device)的功能建模。 [解决方案制造商](#solution-builder)可以基于模型配置 IoT 解决方案。

### <a name="device-provisioning"></a>Device Provisioning

设备预配是将初始[设备数据](#device-data)添加到解决方案中的存储的过程。 要使新设备能够连接到中心，必须将新设备 ID 和密钥添加到 IoT 中心的[标识注册表](#identity-registry)。 在预配过程中，可能需要初始化其他解决方案存储中的设备特定数据。

### <a name="device-provisioning-service"></a>设备预配服务

IoT 中心设备预配服务 (DPS) 是 [IoT 中心](#iot-hub)的一项帮助程序服务，该服务用于将零接触设备预配配置到指定 IoT 中心。 使用 DPS，可以通过安全且可缩放的方式预配数百万台设备。

### <a name="device-rest-api"></a>设备 REST API

在设备上使用[设备 REST API](/rest/api/iothub/device)，可以将设备到云消息发送到 IoT 中心，并能从 IoT 中心接收[云到设备](#cloud-to-device)消息。 通常情况下，使用 IoT 中心教程中演示的一种较高级别的 [设备 SDK](#azure-iot-device-sdks) 。

### <a name="device-template"></a>设备模板

在 IoT Central 中，[设备模板](../iot-central/core/concepts-device-templates.md)是一个蓝图，用于定义可连接到应用程序的某种设备的特征和行为。 例如，设备模板定义设备发送的遥测数据，以便 IoT Central 使用正确单位和数据类型创建可视化效果。 [设备模型](#device-model)是设备模板的一部分。

### <a name="device-twin"></a>设备孪生

设备孪生是存储设备状态信息（如元数据、配置和条件）的 JSON 文档。 IoT 中心 为在 IoT 中心预配的每台设备保留一个设备孪生。 借助设备孪生可以在设备和解决方案后端之间同步 设备条件 和配置。 可以查询设备孪生体来定位特定的设备，以及查询长时间运行的操作的状态。

### <a name="device-to-cloud"></a>设备到云

指从已连接设备发送到 [IoT 中心](#iot-hub)的消息。 这些消息可能是[数据点](#data-point-message)或[交互式](#interactive-message)消息。 有关详细信息，请参阅[使用 IoT 中心发送和接收消息](../iot-hub/iot-hub-devguide-messaging.md)。

### <a name="digital-twin"></a>数字孪生体

数字孪生体是表示物理对象的数字数据的集合。 物理对象的更改将反映在数字孪生体中。 在某些情况下，可以使用数字孪生体来操作物理对象。 [Azure 数字孪生服务](../digital-twins/index.yml)使用以[数字孪生体定义语言 (DTDL)](#digital-twins-definition-language-dtdl) 表示的[模型](#model)来表示物理设备或更高级别抽象业务概念的数字孪生体，用于实现各种基于云的数字孪生解决方案。 [IoT 即插即用](../iot-develop/index.yml)设备具有 DTDL [设备模型](#device-model)描述的数字孪生体。

### <a name="digital-twin-change-events"></a>数字孪生体更改事件

将 [IoT 即插即用设备](#iot-plug-and-play-device)连接到 IoT 中心时，该中心可以使用其路由功能发送数字孪生体更改通知。 例如，每当设备上的[属性](#properties)值发生更改时，IoT 中心都可以将通知发送到事件中心等终结点。

### <a name="digital-twin-route"></a>数字孪生体路由

在 IoT 中心设置的路由，用于将[数字孪生体更改事件](#digital-twin-change-events)传递到事件中心等终结点。

### <a name="digital-twins-definition-language-dtdl"></a>数字孪生体定义语言 (DTDL)

一种 JSON-LD 语言，用于描述 [IoT 即插即用设备](#iot-plug-and-play-device)和 [Azure 数字孪生](../digital-twins/index.yml)实体的[模型](#model)和[接口](#interface)。 使用[数字孪生体定义语言版本 2](https://github.com/Azure/opendigitaltwins-dtdl) 可以描述[数字孪生体](#digital-twin)的功能，并使 IoT 平台和 IoT 解决方案能够使用实体的语义。 数字孪生体定义语言通常缩写为 DTDL。

### <a name="direct-method"></a>直接方法

[直接方法](../iot-hub/iot-hub-devguide-direct-methods.md)可让你通过在 IoT 中心上调用 API来触发在设备上执行的方法。

### <a name="downstream-services"></a>下游服务

一个相对术语，描述从当前上下文接收数据的服务。 例如，如果是在 Azure 数字孪生的上下文中，在将数据设置为从 Azure 数字孪生流入时序见解的情况下，[时序见解](../time-series-insights/index.yml)将被视为下游服务。

## <a name="e"></a>E

### <a name="endpoint"></a>终结点

可接收来自其他服务的数据的数据路由服务的已命名表示形式。

IoT 中心公开了多个[终结点](../iot-hub/iot-hub-devguide-endpoints.md)，以便使应用能够连接到 IoT 中心。 有面向设备的终结点，通过此终结点设备可以执行一些操作，例如发送[设备到云](#device-to-cloud)的消息和接收[云到设备](#cloud-to-device)的消息。 有面向服务的管理终结点，通过此终结点[后端应用](#back-end-app)可执行一些操作，如[设备标识](#device-identity)管理和设备孪生管理。 有面向服务的 [内置终结点](#built-in-endpoints) ，用于读取设备到云消息。 可创建[自定义终结点](#custom-endpoints)，接收由[路由规则](#routing-rules)调度的设备到云消息。

### <a name="enrollment"></a>注册

在[设备预配服务](#device-provisioning-service)中，注册是可以通过自动预配注册到[链接的 IoT 中心](#linked-iot-hub)的各个设备或设备组的记录。

### <a name="enrollment-group"></a>注册组

在[设备预配服务](#device-provisioning-service)中，注册组可标识共享 X.509 或对称密钥[证明机制](#attestation-mechanism)的一组设备。

### <a name="event-handlers"></a>事件处理程序

这可以指由事件到达触发并执行某个处理操作的任何进程。 创建事件处理程序的一种方法是：向 Azure 函数添加事件处理代码，并使用[终结点](#endpoint)和[事件路由](#event-routing)通过它发送数据。

### <a name="event-hub-compatible-endpoint"></a>与事件中心兼容的终结点

如果要读取发送到 IoT 中心的 [设备到云](#device-to-cloud) 的消息，可以先连接到中心的终结点，然后使用与事件中心兼容的任何方法读取这些消息。 事件中心兼容的方法包括使用[事件中心 SDK](../event-hubs/event-hubs-programming-guide.md) 和 [Azure 流分析](../stream-analytics/stream-analytics-introduction.md)。

### <a name="event-routing"></a>事件路由

将事件及其数据从一个设备或服务发送到另一个设备或服务的[终结点](#endpoint)的过程。 

在 Iot 中心内，你可以定义[路由规则](#routing-rules)来描述应如何发送消息。 在 Azure 数字孪生中，事件路由是为此目的而创建的实体。 Azure 数字孪生事件路由可以包含筛选器，以限制发送到每个终结点的事件类型。

## <a name="f"></a>F

### <a name="field-gateway"></a>现场网关

无法直接连接到 [IoT 中心](#iot-hub) 的设备可以通过现场网关进行连接，而现场网关通常与设备一起部署在本地。 有关详细信息，请参阅[什么是 Azure IoT 中心？](../iot-hub/about-iot-hub.md)。

## <a name="g"></a>G

### <a name="gateway"></a>网关

网关使不能直接连接到 [IoT 中心](#iot-hub)的设备能建立连接。 另请参阅[现场网关](#field-gateway)、[云网关](#cloud-gateway)和[自定义网关](#custom-gateway)。

### <a name="gateway-device"></a>网关设备

设备是[现场网关](#field-gateway)的一个示例。 网关设备可以是标准 IoT [设备](#device)，也可以是 [IoT Edge 设备](#iot-edge-device)。

有了网关设备，不能直接连接到 [IoT 中心](#iot-hub)的下游设备将能够建立连接。

## <a name="h"></a>H

### <a name="hardware-security-module"></a>硬件安全模块

硬件安全模块 (HSM) 用于安全的、基于硬件的设备机密存储。 它是设备最安全的密钥存储形式。 X.509 证书和对称密钥都可以存储在 HSM 中。 在[设备预配服务](#device-provisioning-service)中，[证明机制](#attestation-mechanism)可以使用 HSM。

## <a name="i"></a>I

### <a name="id-scope"></a>ID 范围

ID 范围是在创建[设备预配服务 (DPS)](#device-provisioning-service) 实例时分配给该实例的唯一值。

IoT Central 应用程序使用 DPS 实例，并使 ID 范围在 IoT Central UI 上可用。

### <a name="identity-registry"></a>标识注册表

[标识注册表](../iot-hub/iot-hub-devguide-identity-registry.md)是 IoT 中心的内置组件，用于存储允许连接到 IoT 中心的单个设备的信息。

### <a name="individual-enrollment"></a>单独注册

在[设备配置服务](#device-provisioning-service)中，单个注册可标识使用 X.509 叶证书或对称密钥作为[证明机制](#attestation-mechanism)的单个设备。

### <a name="interactive-message"></a>交互式消息

交互式消息是 [云到设备](#cloud-to-device) 的消息，可在解决方案后端触发即时操作。 例如，设备可能会发送故障警报，而该故障会自动记录到 CRM 系统中。

### <a name="interface"></a>接口

在 IoT 即插即用中，接口描述由 [IoT 即插即用设备](#iot-plug-and-play-device)或[数字孪生体](#digital-twin)实现的相关功能。 可以跨不同的[设备模型](#device-model)重复使用接口。 在设备模型中使用接口时，该接口将定义设备的某个[组件](#component)。 简单设备只包含一个默认接口。

在 Azure 数字孪生中，接口可用于引用 [DTDL](#digital-twins-definition-language-dtdl) 模型定义中的顶级代码项。

### <a name="iot-edge"></a>IoT Edge

Azure IoT Edge 支持在本地设备进行 Azure 服务和解决方案特定代码的云驱动部署。 [IoT Edge 设备](#iot-edge-device)可以聚合其他设备的数据，以在将数据发送到云之前执行计算和分析。 有关详细信息，请参阅 [Azure IoT Edge](../iot-edge/index.yml)。

### <a name="iot-edge-agent"></a>IoT Edge 代理

IoT Edge 运行时部件负责部署和监视模块。

### <a name="iot-edge-device"></a>IoT Edge 设备

IoT Edge 设备使用容器化的 [IoT Edge 模块](#modules)运行 Azure 服务、第三方服务或你自己的代码。 在 IoT Edge 设备上，[IoT Edge 运行时](#iot-edge-runtime)将管理模块。 可以在云中远程监视和管理 IoT Edge 设备。

### <a name="iot-edge-hub"></a>IoT Edge 中心

IoT Edge 运行时部件负责模块间通信、上游（流向 IoT 中心）和下游（离开 IoT Edge）通信。

### <a name="iot-edge-runtime"></a>IoT Edge 运行时

IoT Edge 运行时包括 Microsoft 分发用于安装在 IoT Edge 设备上的所有内容。 其中包括 Edge 代理、Edge 中心和 IoT Edge 安全守护程序。

### <a name="iot-extension-for-azure-cli"></a>适用于 Azure CLI 的 IoT 扩展

[适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)是一个跨平台的命令行工具。 使用该工具可以管理 [标识注册表](#identity-registry)中的设备、向设备发送消息和文件和接收来自设备的消息和文件，以及监视 IoT 中心的操作。

### <a name="iot-hub"></a>IoT 中心

IoT 中心是一项完全托管的 Azure 服务，可在数百万个设备和一个解决方案后端之间实现安全可靠的双向通信。 有关详细信息，请参阅[什么是 Azure IoT 中心？](../iot-hub/about-iot-hub.md)。 使用 Azure 订阅可以创建 IoT 中心来处理 IoT 消息传送工作负荷。

### <a name="iot-hub-metrics"></a>IoT 中心度量值

IoT 中心指标向用户提供有关 Azure 订阅中的 IoT 中心的状态数据。 可以使用 IoT 中心指标评估服务以及连接到服务的设备的总体运行状况。 IoT 中心指标可以帮助用户了解 IoT 中心发生的情况，并调查根本原因，而无需联系 Azure 支持部门。 有关详细信息，请参阅[监视 IoT 中心](../iot-hub/monitor-iot-hub.md)。

### <a name="iot-hub-query-language"></a>IoT 中心查询语言

[IoT 中心查询语言](../iot-hub/iot-hub-devguide-query-language.md)是一种类似于 SQL 的语言，可用于查询[作业](#job)、数字孪生体和设备孪生体。

### <a name="iot-hub-resource-rest-api"></a>IoT 中心资源 REST API

使用 [IoT 中心资源 REST API](/rest/api/iothub/iothubresource)，可以管理 Azure 订阅中的 IoT 中心，包括创建、更新和删除中心等操作。

### <a name="iot-plug-and-play-bridge"></a>IoT 即插即用桥接

IoT 即插即用桥是一个开源应用程序，它使连接到 Windows 或 Linux 网关的现有传感器和外围设备能够以 [IoT 即插即用设备](#iot-plug-and-play-device)的形式进行连接。

### <a name="iot-plug-and-play-conventions"></a>IoT 即插即用约定

在与解决方案交换数据时，IoT 即插即用[设备](#iot-plug-and-play-device)预期会遵循一组约定。

### <a name="iot-plug-and-play-device"></a>IoT 即插即用设备

IoT 即插即用设备通常是一种小型的独立计算设备，可收集数据或控制其他设备，并运行实现[设备模型](#device-model)的软件或固件。  例如，IoT 即插即用设备可以是环境监视设备，也可以是智能农业灌溉系统的控制器。 可以直接实现 IoT 即插即用设备，或将其作为 IoT Edge 模块实现。 你可以编写云托管的 IoT 解决方案来命令、控制和接收 IoT 即插即用设备中的数据。

### <a name="iot-solution-accelerators"></a>IoT 解决方案加速器

Azure IoT 解决方案加速器将多个 Azure 服务一起打包到解决方案中。 利用这些解决方案，能够快速开始使用常用 IoT 方案的端到端实现。 有关详细信息，请参阅 [Azure IoT 解决方案加速器是什么？](../iot-accelerators/about-iot-accelerators.md)

## <a name="j"></a>J

### <a name="job"></a>作业

在 [IoT 中心](#iot-hub)中，通过[作业](../iot-hub/iot-hub-devguide-jobs.md)可计划和跟踪在 IoT 中心注册的一组设备上的活动。 活动包括更新设备孪生[所需的属性](#desired-properties)、更新设备孪生[标记](#tags)，以及调用[直接方法](#direct-method)。 IoT 中心还使用作业在[标识注册表](#identity-registry)中[导入和导出](../iot-hub/iot-hub-devguide-identity-registry.md#import-and-export-device-identities)。

在 IoT Central 中，借助[作业](../iot-central/core/howto-manage-devices-in-bulk.md)可通过设置属性和调用命令批量管理连接的设备。 通过 IoT Central 作业还可以批量更新[云属性](#cloud-property)。

## <a name="l"></a>L

### <a name="leaf-device"></a>叶设备

在 [IoT Edge](#iot-edge) 中，叶设备是无下游设备的设备。

### <a name="lifecycle-event"></a>生命周期事件

在 Azure 数字孪生中，当在 Azure 数字孪生实例中创建或删除数据项（例如数字孪生体、关系或事件处理程序）时，将触发此类事件。

### <a name="linked-iot-hub"></a>链接的 IoT 中心

[设备预配服务 (DPS)](#device-provisioning-service) 可以将设备预配到已链接到它的 IoT 中心。 将 IoT 中心链接到 DPS 实例将让服务注册设备 ID 并在设备孪生中设置初始配置。

## <a name="m"></a>M

### <a name="model"></a>模型

模型定义物理环境中的一种实体，包括其属性、遥测、组件，有时还包括其他信息。 模型用于创建表示此类型的特定物理对象的[数字孪生体](#digital-twin)。 模型是以[数字孪生体定义语言](#digital-twins-definition-language-dtdl)编写的。

在 [Azure 数字孪生服务](../digital-twins/index.yml)中，模型可以定义设备或更高级别的抽象业务概念。 在 [IoT 即插即用](../iot-develop/index.yml)中，[设备模型](#device-model)用于具体描述设备。

### <a name="model-id"></a>模型 ID

当 IoT 即插即用设备连接到 IoT 中心时，会发送它所实现的 [DTDL](#digital-twins-definition-language-dtdl) 模型的 **模型 ID**。 解决方案可以根据此 ID 查找设备模型。

### <a name="model-repository"></a>模型存储库

模型存储库用于存储[设备模型](#device-model)和[接口](#interface)。

### <a name="model-repository-rest-api"></a>模型存储库 REST API

用于管理模型存储库并与之交互的 API。 例如，可以使用 API 来添加和搜索[设备模型](#device-model)。

### <a name="module-builder"></a>模块制造商

模块制造商在实现运行于 [IoT 即插即用设备](#iot-plug-and-play-device)上的代码时使用[设备模型](#device-model)和[接口](#interface)。 模块制造商将代码实现为模块，或者实现为要部署到设备上的 IoT Edge 运行时中的 IoT Edge 模块。

### <a name="module-identity"></a>模块标识

模块标识是分配给属于设备的每个模块的唯一标识符。 模块标识也注册到[标识注册表](#identity-registry)中。

模块标识详细说明模块用于向 [IoT 中心](#iot-hub)进行身份验证的安全凭据；如果是 IoT Edge 模块的模块标识，则它会详细说明用于向 [IoT Edge 中心](#iot-edge-hub)进行身份验证的安全凭据。

### <a name="module-image"></a>模块映像

[IoT Edge 运行时](#iot-edge-runtime)用来实例化模块实例的 Docker 映像。

### <a name="module-twin"></a>模块孪生

与设备孪生类似，模块孪生是存储模块状态信息（如元数据、配置和条件）的 JSON 文档。 IoT 中心为在 IoT 中心的设备标识下预配的每个模块实体保留一个模块孪生。 可以借助模块孪生在模块和解决方案后端之间同步模块条件和配置。 可以通过查询模块孪生来定位特定模块和查询长时间运行的操作状态。

### <a name="modules"></a>模块

在设备端，可以使用 IoT 中心设备 SDK 创建[模块](../iot-hub/iot-hub-devguide-module-twins.md)，其中每个模块都与 IoT 中心单独建立连接。 通过此功能，可对设备上的不同组件使用不同的命名空间。

模块标识和模块孪生提供的功能与[设备标识](#device-identity)和[设备孪生](#device-twin)相同，但前者的粒度更细。 这种更高的粒度级可让有能力的设备（例如基于操作系统的设备，或管理多个组件的固件设备）隔离其中每个组件的配置和状态。

在 [IoT Edge](#iot-edge) 中，模块是可部署到 IoT Edge 设备的 Docker 容器。 它执行特定的任务，例如从设备引入消息、转换消息，或者将消息发送到 IoT 中心。 它与其他模块进行通信并将数据发送到 [IoT Edge 运行时](#iot-edge-runtime)。

### <a name="mqtt"></a>MQTT

[MQTT](https://mqtt.org/) 是 [IoT 中心](#iot-hub)支持的与设备进行通信的消息传递协议之一。 有关 IoT 中心支持的消息传递协议的详细信息，请参阅[使用 IoT 中心发送和接收消息](../iot-hub/iot-hub-devguide-messaging.md)。

## <a name="o"></a>O

### <a name="ontology"></a>本体

适用于特定领域（例如房地产、智慧城市、IoT 系统、能源网格等）的一组模型。 本体通常用作知识图的架构（例如 [Azure 数字孪生](#azure-digital-twins)中的知识图架构），因为它们提供基于行业标准和最佳实践的起点。

有关本体的详细信息，请参阅[什么是本体？](../digital-twins/concepts-ontologies.md)

### <a name="operations-monitoring"></a>操作监视

使用 IoT 中心[操作监视](../iot-hub/iot-hub-operations-monitoring.md)可实时监视其 IoT 中心上的操作状态。 [IoT 中心](#iot-hub) 可以跨多个类别的操作跟踪事件。 可以选择将一个或多个类别的事件发送到 IoT 中心终结点进行处理。 可以监视数据中是否有错误，或根据数据模式设置更复杂的处理行为。

## <a name="p"></a>P

### <a name="physical-device"></a>物理设备

物理设备是真实的设备，如连接到 IoT 中心的 Raspberry Pi。 为方便起见，许多 IoT 中心教程使用 [模拟设备](#simulated-device) ，以便在本地计算机上运行示例。

### <a name="primary-and-secondary-keys"></a>主要和次要密钥

连接到 IoT 中心的面向设备或面向服务的终结点时， [连接字符串](#connection-string) 包含密钥以授予用户访问权限。 在[标识注册表](#identity-registry)中添加设备或者在中心添加[共享访问策略](#shared-access-policy)时，服务将生成主要和次要密钥。 拥有两个密钥能够在更新密钥时从一个密钥切换到另一个密钥，而不丢失对 IoT 中心的访问。

### <a name="properties"></a>属性

属性是在[接口](#interface)中定义的数据字段，表示[数字孪生体](#digital-twin)的某种永久性状态。 你可以将属性声明为只读或可写。 只读属性（例如序列号）由 [IoT 即插即用设备](#iot-plug-and-play-device)上运行的代码设置。 可写属性（例如警报阈值）通常从基于云的 IoT 解决方案中设置。

### <a name="property-change-event"></a>属性更改事件

由[数字孪生体](#digital-twin)的属性更改引起的事件。

### <a name="protocol-gateway"></a>协议网关

协议网关通常部署在云中，为连接到 [IoT 中心](#iot-hub)的设备提供协议转换服务。 有关详细信息，请参阅[什么是 Azure IoT 中心？](../iot-hub/about-iot-hub.md)。

## <a name="r"></a>R

### <a name="registration"></a>注册

注册是设备在 IoT 中心[标识注册表](#identity-registry)中的记录。 你可以直接注册设备，也可以使用[设备预配服务](#device-provisioning-service)自动执行设备注册。

### <a name="registration-id"></a>注册 ID

注册 ID 用于在[设备预配服务](#device-provisioning-service)中唯一地标识设备[注册](#registration)。 注册 ID 可能与[设备标识](#device-identity)的值相同。

### <a name="relationship"></a>关系

在 [Azure 数字孪生](../digital-twins/index.yml)服务中，关系用于将[数字孪生体](#digital-twin)连接到知识图，该图以数字方式表示你的整个物理环境。 孪生体可以具有的关系类型定义为孪生体的[模型](#model)定义的一部分 — 一个特定类型的孪生体的 [DTDL](#digital-twins-definition-language-dtdl) 模型包括它与其他孪生体之间关系的相关信息。

### <a name="reported-configuration"></a>报告的配置

在与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)相关的上下文中，报告的配置是指设备孪生中的完整属性和元数据集，该配置应报告给解决方案后端。

### <a name="reported-properties"></a>报告的属性

在与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)相关的语境中，报告的属性是设备孪生的一部分，和[所需属性](#desired-properties)一起用于同步设备配置或条件。 报告的属性只能由[设备应用](#device-app)设置，可由[后端应用](#back-end-app)读取和查询。

### <a name="retry-policy"></a>重试策略

连接到云服务时使用重试策略来处理[暂时性错误](/azure/architecture/best-practices/transient-faults)。

### <a name="routing-rules"></a>路由规则

需要在 IoT 中心配置[路由规则](../iot-hub/iot-hub-devguide-messages-read-custom.md)，将设备到云消息路由到[内置终结点](#built-in-endpoints)或[自定义终结点](#custom-endpoints)，以供解决方案后端处理。

## <a name="s"></a>S

### <a name="sasl-plain"></a>SASL PLAIN

SASL PLAIN 是一种协议，AMQP 协议使用它来传输安全令牌。

### <a name="service-operations-endpoint"></a>服务操作终结点

由服务管理员用于管理服务设置的[终结点](#endpoint)。 例如，在[设备预配服务](#device-provisioning-service)中，你可以使用服务终结点来管理注册。

### <a name="service-rest-api"></a>服务 REST API

可以从解决方案后端使用[服务 REST API](/rest/api/iothub/service/configuration) 来管理设备。 使用 API，不仅可以检索和更新[设备孪生](#device-twin)属性，还能调用[直接方法](#direct-method)并安排[作业](#job)。 通常情况下，使用 IoT 中心教程中演示的一种较高级别的 [服务 SDK](#azure-iot-service-sdks) 。

### <a name="shared-access-policy"></a>共享访问策略

共享访问策略定义向具有有效的 [主要密钥或次要密钥](#primary-and-secondary-keys) （与该策略相关联）的任何人授予的权限。 用户可以在 门户中管理中心的共享访问策略和密钥。

### <a name="shared-access-signature"></a>共享访问签名

共享访问签名 (SAS) 是基于 SHA–256 安全哈希或 URI 的身份验证机制。 SAS 身份验证有两个组件：_共享访问策略_ 和 _共享访问签名_（通常称为令牌）。 设备使用 SAS 在 IoT 中心进行身份验证。 [后端应用](#back-end-app) 也使用 SAS 在 IoT 中心的面向服务的终结点上进行身份验证。 通常，在 [连接字符串](#connection-string) 中包含 SAS 令牌，应用使用此令牌建立与 IoT 中心的连接。

### <a name="simulated-device"></a>模拟设备

为方便起见，许多 IoT 中心教程使用模拟设备，以便在本地计算机上运行示例。 相反， [物理设备](#physical-device) 是真实的设备，如连接到 IoT 中心的 Raspberry Pi。

### <a name="solution"></a>解决方案

_解决方案_ 可以是包含一个或多个项目的 Visual Studio 解决方案。 _解决方案_ 也可能是包括诸如设备、[设备应用](#device-app)、IoT 中心、其他 Azure 服务和 [后端应用](#back-end-app)等元素的 IoT 解决方案。

### <a name="solution-builder"></a>解决方案制造商

解决方案制造商创建解决方案后端。 解决方案制造商通常使用 IoT 中心和[模型存储库](#model-repository)等 Azure 资源。

### <a name="system-properties"></a>系统属性

在与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)相关的语境中，系统属性为只读，其中包括与设备使用情况相关的信息，例如上次活动时间和连接状态。

## <a name="t"></a>T

### <a name="tags"></a>Tags

在与[设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)相关的语境中，标记是指由解决方案后端以 JSON 文档形式存储和检索的设备元数据。 标记对设备上的应用不可见。

### <a name="target-condition"></a>目标条件

在 IoT Edge 部署中，目标条件选择部署的目标设备，例如 **tag.environment = prod**。目标条件将受到持续评估，以包括满足要求的任何新设备或删除不再需要的设备。

### <a name="telemetry"></a>遥测

设备收集遥测数据，如风速或温度，并使用数据点消息将遥测数据发送到 IoT 中心。

在 IoT 即插即用和 Azure 数字孪生中，[接口](#interface)中定义的遥测字段表示度量值。 这些度量值通常是作为数据流由 [IoT 即插即用设备](#iot-plug-and-play-device)等设备发送的诸如传感器读数之类的值。

与[属性](#properties)不同，遥测数据不会存储在[数字孪生体](#digital-twin)上；它是有时间限制的数据事件流，在这些事件发生时就需要对其进行处理。

### <a name="telemetry-event"></a>遥测事件

指示遥测数据的到达的事件。

### <a name="token-service"></a>令牌服务

可以使用令牌服务对设备实施身份验证机制。 它使用包含 [DeviceConnect](#shared-access-policy) 权限的 IoT 中心 **共享访问策略** 创建 *设备范围的* 令牌。 这些令牌可让设备连接到 IoT 中心。 设备通过令牌服务使用自定义的身份验证机制进行身份验证。 如果设备成功通过身份验证，那么令牌服务向设备颁发 SAS 令牌用于访问 IoT 中心。

### <a name="twin-graph-or-digital-twin-graph"></a>孪生图（或数字孪生图）

在 [Azure 数字孪生](../digital-twins/index.yml)服务中，你可以通过[关系](#relationship)将[数字孪生体](#digital-twin)连接起来，以创建以数字方式表示你的整个物理环境的知识图。 单个 [Azure 数字孪生实例](#azure-digital-twins-instance)可以承载许多无关联的图，或承载单个包含互连的图。

### <a name="twin-queries"></a>孪生查询

[设备和模块孪生查询](../iot-hub/iot-hub-devguide-query-language.md)使用类似于 SQL 的 IoT 中心查询语言从设备孪生或模块孪生中检索信息。 可以使用相同的 IoT 中心查询语言，检索在 IoT 中心内运行的[作业](#job)的相关信息。

### <a name="twin-synchronization"></a>孪生同步

孪生同步使用设备孪生或模块孪生中的[所需属性](#desired-properties)配置设备或模块，并检索其中的[报告属性](#reported-properties)，以将这些属性存储在孪生中。

## <a name="u"></a>U

### <a name="upstream-services"></a>上游服务

一个相对术语，描述向当前上下文馈送数据的服务。 例如，如果是在 Azure 数字孪生的上下文中，则 IoT 中心将被视为上游设备，因为数据从 IoT 中心流到 Azure 数字孪生。

## <a name="x"></a>X

### <a name="x509-client-certificate"></a>X.509 客户端证书

设备可以使用 X.509 证书在 [IoT 中心](#iot-hub)进行身份验证。 使用 X.509 证书是使用 [SAS 令牌](#shared-access-signature)的替代方案。