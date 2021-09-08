---
title: 关于服务网格
description: 大致了解服务网格、支持的方案、选择条件以及要探索的后续步骤。
author: pgibson
ms.topic: article
ms.date: 07/29/2021
ms.author: pgibson
ms.openlocfilehash: f1821bd9af9c09da1c5fb3ae80145dba3cc38b09
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123434297"
---
# <a name="about-service-meshes"></a>关于服务网格

服务网格为工作负荷提供流量管理、复原能力、策略、安全性、强标识和可观测性等功能。 应用程序与这些操作功能相分离，服务网格将这些功能移出应用层并移到基础结构层。

## <a name="scenarios"></a>方案

下面是使用服务网格时可为工作负荷实现的方案：

- **加密群集中的所有流量** - 在群集中的指定服务之间启用相互 TLS。 这可以扩展到外围网络中的入口和出口。 为应用程序代码和基础结构提供安全的默认选项，且无需进行任何更改。

- **Canary 和分阶段推出** - 指定将一部分流量路由到群集中的一组新服务所要满足的条件。 成功测试 Canary 发布后，删除条件性路由，并分阶段逐渐增加路由到新服务的所有流量的百分比。 最终，所有流量都会定向到新服务。

- **流量管理和操作** - 针对服务创建一个策略，用于限制将所有流量从特定来源路由到某个服务版本的速率。 或创建一个策略用于对指定服务之间的故障类应用重试策略。 在迁移或调试问题期间将实时流量镜像到新的服务版本。 在测试环境中的服务之间注入故障以测试复原能力。

- **可观测性** - 洞察服务如何连接到它们之间流动的流量。 获取群集中所有流量以及入口/出口的指标、日志和跟踪。 将分布式跟踪功能添加到应用程序。

## <a name="selection-criteria"></a>选择条件

在选择服务网格之前，请确保了解安装服务网格的要求和理由。 提出下列问题。

- **入口控制器是否足以满足我的需求？** - 有时，在入口位置使用 a/b 测试或流量拆分等功能就足以支持所需的方案。 不要无谓地增大环境的复杂性。

- **我的工作负荷和环境是否可容忍额外的开销？** - 用于支持服务网格的所有附加组件都需要增加 CPU 和内存等资源。 此外，所有代理及其关联的策略检查会增大流量延迟。 如果工作负荷对延迟非常敏感或者无法提供额外的资源来满足服务网格组件的需求，请再三考虑。

- **这是否会不必要地增大复杂性？** - 如果安装服务网格的理由是获取某种对于业务团队或运营团队而言不一定重要的功能，请考虑是否值得增大安装、维护和配置的复杂性。

- **是否能够以递进的方式采用此服务网格？** - 某些提供多种功能的服务网格能够以更递进的方式采用。 安装所需的组件即可确保成功。 随着使用越来越熟练或者需要更多的功能，可以探索这些功能。 避免从一开始就迫不急待地安装所有组件。

## <a name="next-steps"></a>后续步骤

下一步，在 Azure Kubernetes 服务 (AKS) 上探索 Open Service Mesh (OSM)：

> [!div class="nextstepaction"]
> [了解 OSM 详细信息...][osm-about]

此外，还可通过适用于每个服务网格的综合项目文档在 Azure Kubernetes 服务 (AKS) 上探索以下服务网格：

- [Istio][istio]
- [Linkerd][linkerd]
- [Consul 连接][consul]

若要详细了解服务网格环境、更广泛的可用服务网格、工具和符合性，请探索：

- [第 5 层服务网格环境][service-mesh-landscape]

此外，用户可能还想要探索各种服务网格的标准化工作：

- [服务网格接口 (SMI)][smi]
- [服务网格联合][smf]
- [服务网格性能 (SMP)][smp]


<!-- LINKS - external -->
[istio]: https://istio.io/latest/docs/setup/install/
[linkerd]: https://linkerd.io/getting-started/
[consul]: https://learn.hashicorp.com/tutorials/consul/service-mesh-deploy
[service-mesh-landscape]: https://layer5.io/service-mesh-landscape
[smi]: https://smi-spec.io/
[smf]: https://github.com/vmware/hamlet
[smp]: https://github.com/service-mesh-performance/service-mesh-performance

<!-- LINKS - internal -->
[osm-about]: ./open-service-mesh-about.md
