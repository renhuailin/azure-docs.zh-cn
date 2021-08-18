---
title: 面向 Azure Spring Cloud 微服务应用的 Java 和基本 OS
description: 维护面向 Azure Spring Cloud 微服务应用的 Java 和基本操作系统正常运行的原则
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 05/27/2020
ms.custom: devx-track-java
ms.openlocfilehash: 24ca2cce27169c2973f16ca7b28599d5e0f01079
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014876"
---
# <a name="java-and-base-os-for-spring-microservice-apps"></a>面向 Spring 微服务应用的 Java 和基础操作系统

本文适用于：✔️ Java

下面是维护面向 Spring 微服务应用的 Java 和基本操作系统正常运行的原则。

## <a name="principles-for-healthy-java-and-base-os"></a>Java 和基本 OS 正常运行的原则

* 各层的基本操作系统应相同 - 基本 | 标准 | 高级。

    * 目前，Azure Spring Cloud 上的应用混用 Debian 10 和 Ubuntu 18.04。
    * VMware 生成服务使用 Ubuntu 18.04。

* 无论部署起点如何，都应使用相同基本操作系统 - 源 | JAR

    * 目前，Azure Spring Cloud 上的应用混用 Debian 10 和 Ubuntu 18.04。

* 基本操作系统应没有安全漏洞。

    * Debian 10 基本操作系统有 147 个开放 CVE。
    * Ubuntu 18.04 基本操作系统有 132 个开放 CVE。

* 应使用无外设 JRE。

    * 目前，Azure Spring Cloud 上的应用使用 JDK。 无外设 JRE 是一个较小的映像。

* 应使用最新的 Java 版本。

    * 目前，Azure Spring Cloud 上的应用使用 Java 8 版本 242。 这是一种已过时的版本。

Azul 系统将持续扫描基本操作系统的更改，并使最后生成的映像保持最新。 Azure Spring Cloud 会查找对映像的更改，并跨部署持续更新它们。

## <a name="faq-for-azure-spring-cloud"></a>Azure Spring Cloud 常见问题解答

* 支持哪些版本的 Java？ 主版本和生成号。

    * 支持 LTS 版本 - Java 8 和 11。
    * 使用最新的版本，例如现在的 Java 8 版本 252 和 Java 11 版本 7。

* 谁构建了这些 Java 运行时？

    * Azul 系统。

* 什么是映像的基本操作系统？

    * Ubuntu 20.04 LTS (Focal Fossa)。 应用将继续保持在最新的 LTS 版 Ubuntu 上。
    * 请参阅 [Ubuntu 20.04 LTS (Focal Fossa)](http://releases.ubuntu.com/focal/)

* 如何针对本地开发下载支持的 Java 运行时？

    * 请参阅[安装适用于 Azure 和 Azure Stack 的 JDK](/azure/developer/java/fundamentals/java-jdk-install)

* 如何获取对 Java 运行时级别的问题的支持？

    * 通过 Azure 支持开具支持票证。

## <a name="default-deployment-on-azure-spring-cloud"></a>Azure Spring Cloud 中的默认部署

> ![默认部署](media/spring-cloud-principles/spring-cloud-default-deployment.png)

## <a name="next-steps"></a>后续步骤

* [快速入门：部署第一个 Azure Spring Cloud 应用程序](./quickstart.md)
* [针对 Azure 和 Azure Stack 的 Java 长期支持](/azure/developer/java/fundamentals/java-support-on-azure)
