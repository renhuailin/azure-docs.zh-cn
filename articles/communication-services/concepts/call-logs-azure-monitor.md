---
title: Azure 通信服务 - 呼叫摘要与呼叫诊断日志
titleSuffix: An Azure Communication Services concept document
description: 了解 Azure Monitor 中的呼叫摘要与呼叫诊断日志
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 07/22/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 3844fcab3f9830bbaca60042aedbfb75bf7f09e4
ms.sourcegitcommit: 98e126b0948e6971bd1d0ace1b31c3a4d6e71703
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/26/2021
ms.locfileid: "114676083"
---
# <a name="call-summary-and-call-diagnostic-logs"></a>呼叫摘要与呼叫诊断日志

[!INCLUDE [Private Preview Notice](../includes/private-preview-include.md)]

## <a name="data-concepts"></a>数据概念

### <a name="entities-and-ids"></a>实体和 ID

“呼叫”，在涉及数据中所表示的实体时，是一个由 `correlationId` 表示的抽象概念。 每个呼叫的 `CorrelationId` 都是唯一的，并由 `callStartTime` 和 `callDuration` 确定其时限。 每个呼叫都是一个事件，其中包含来自两个或多个终结点的数据，用于表示呼叫中的不同人员、机器人或服务器参与者。

“参与者” (`participantId`) 只有当呼叫为群组呼叫时才存在，因为它表示终结点与服务器之间的连接。 

“终结点”是最具唯一性的实体，由 `endpointId` 表示。 `EndpointType` 能告诉你终结点代表的是人类用户（PSTN 或 VoIP）、机器人 (Bot)，还是在呼叫中管理多个参与者的服务器。 当 `endpointType` 为 `"Server"` 时，不会为终结点分配唯一的 ID。 通过查看 `endpointType` 和 `endpointId` 的数量，始终可以确定呼叫中有多少用户和其他非人类参与者（机器人或服务器）。 本机 SDK（例如 Android 呼叫 SDK）会对跨多个呼叫的用户重复使用同一 `endpointId`，从而使你能够理解跨会话的体验。 这不同于基于 Web 的终结点，后者会始终为每个新呼叫生成一个新的 `endpointId`。

“流”是粒度最精细的实体，因为每个方向（入站/出站）和 `mediaType`（如音频或视频）都有一个流。  

### <a name="p2p-vs-group-calls"></a>P2P 呼叫与群组呼叫的对比

呼叫类型（由 `callType` 表示）有两种：P2P 和群组。 

P2P 呼叫是仅在两个终结点之间的连接，无服务器终结点。 P2P 呼叫是作为这两个终结点之间的呼叫而启动的，而不是在连接之前作为群组呼叫事件而创建的。

:::image type="content" source="media\call-logs-images\call-logs-concepts-p2p.png" alt-text="p2p 呼叫":::

群组呼叫包括作为会议/日历事件而提前创建的任何呼叫，以及连接 2 个以上终结点的任何呼叫。 群组呼叫会包括一个服务器终结点，每个终结点和服务器之间的连接相当于一个参与者。 如果 P2P 呼叫在呼叫过程中添加了其他终结点，则该呼叫不再是 P2P 呼叫，而是成为了群组呼叫。 通过查看 `participantStartTime` 和 `participantDuration`，可以确定每个终结点何时加入呼叫的时间线。

:::image type="content" source="media\call-logs-images\call-logs-concepts-group.png" alt-text="群组呼叫":::

## <a name="log-structure"></a>日志结构
呼叫将创建两种类型的日志：呼叫摘要日志和呼叫诊断日志。 

呼叫摘要日志包含有关呼叫的基本信息，包括所有相关 ID、时间戳、终结点和 SDK 信息。 对于呼叫中的每个终结点（不包括服务器），都会创建一个单独的呼叫摘要日志。

呼叫诊断日志包含有关“流”的信息，以及一组指示呼叫体验质量度量的指标。 对于呼叫中的每个终结点（包括服务器），将为每个终结点之间的每个数据流（音频、视频等）创建一个单独的呼叫诊断日志。 在 P2P 呼叫中，每个日志都包含与各个出站流相关的数据，而各个出站流则与各个终结点关联。 在群组呼叫中，与 `endpointType`= `"Server"` 关联的每个流都将创建一个包含入站流数据的日志，而所有其他流将创建包含所有非服务器终结点出站流数据的日志。 在群组呼叫中，将使用 `participantId` 作为关键值从而将相关的入站/出站日志加入到单独的参与者连接中。

### <a name="example-1-p2p-call"></a>示例 1：P2P 呼叫

以下关系图表示的是 P2P 呼叫中两个直接连接的终结点。 在此示例中，将创建 2 个呼叫摘要日志（每个 `endpointId` 各有 1 个日志），还将创建 4 个呼叫诊断日志（每个媒体流各有 1 个日志）。 每个日志都包含与 `endpointId` 的出站流相关的数据。

:::image type="content" source="media\call-logs-images\call-logs-p2p-streams.png" alt-text="p2p 呼叫流":::


### <a name="example-2-group-call"></a>示例 2：群组呼叫

以下关系图表示的是一个具有三个 `particpantIds` 的群组呼叫示例，这意味着有三个 `endpointIds`（`endpointIds` 可能出现在多个参与者中，例如从同一设备重新加入呼叫时）和一个服务器终结点。 对于 `participantId` 1，将创建两个呼叫摘要日志：一个为 `endpointId` 创建，另一个为服务器创建。 此外，还将创建四个与 `participantId` 1 相关的呼叫诊断日志，每个媒体流各有一个日志。 其中，具有 `endpointId` 1 的三个日志将包含与出站媒体流相关的数据，另一个具有 `endpointId = null, endpointType = "Server"` 的日志将包含与入站流相关的数据。

:::image type="content" source="media\call-logs-images\call-logs-concepts-group-call-endpoint-detail.png" alt-text="群组呼叫详细信息":::

## <a name="data-definitions"></a>数据定义

### <a name="call-summary-log"></a>呼叫摘要日志
呼叫摘要日志包含有助于标识所有呼叫关键值属性的数据。 对于呼叫中的每个 `participantId`（如果是 P2P 呼叫则为 `endpointId`），都会创建一个不同的呼叫摘要日志。

|     属性                  |     说明                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|-------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     time                      |     生成日志的时间戳 (UTC)。                                                                                                                                                                                                                                                                                                                                                                                                                       |
|     operationName             |     与日志记录相关联的操作。                                                                                                                                                                                                                                                                                                                                                                                                                                |
|     operationVersion          |     与该操作关联的 API 版本（如果使用 API 执行 `operationName`）。 如果没有与此操作相对应的 API，则该版本表示该操作的版本，以防与操作相关联的属性在将来发生更改。                                                                                                                                                                           |
|     category                  |     事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 出现在事件的 `properties` blob 中的属性与特定日志类别和资源类型中的属性相同。                                                                                                                                                                                                    |
|     correlationIdentifier     |     `correlationIdentifier` 可从单个呼叫期间连接的所有参与者和终结点中标识相关事件。 `correlationIdentifier` 是呼叫的唯一 ID。 如果需要通过 Microsoft 打开支持案例，`correlationID` 可用于轻松标识你要排查故障的呼叫。                                                                                                                                                                      |
|     标识符                |     这是用户的唯一 ID，与身份验证服务 (MRI) 分配的标识相匹配。                                                                                                                                                                                                                                                                                                                                                              |
|     callStartTime             |     呼叫开始的时间戳，获取依据为任何终结点第一次尝试连接的时间。                                                                                                                                                                                                                                                                                                                                                                   |
|     callDuration              |     呼叫的持续时间，以秒为单位，获取依据为两个终结点之间第一次尝试连接的时间和最后一次连接结束的时间。                                                                                                                                                                                                                                                                                                                         |
|     callType                  |     将包含 `"P2P"` 或 `"Group"`。 `"P2P"` 呼叫是仅在两个非服务器终结点之间的直接 1:1 连接。 `"Group"` 呼叫是具有两个以上终结点的呼叫，或是在连接之前作为 `"Group"` 而创建的呼叫。                                                                                                                                                                                                                                 |
|     teamsThreadId             |     此 ID 仅在将呼叫组织为 Microsoft Teams 会议时才相关，表示 Microsoft Teams - Azure 通信服务互操作性用例。 此 ID 在操作日志中是公开的。 你还可以通过聊天 API 获取此 ID。                                                                                                                                                                                              |
|     participantID             |     生成此 ID 是用于表示 `"Participant"` 终结点 (`endpointType` = `"Server"`) 与服务器之间的双向连接。 当 `callType` = `"P2P"` 时，两个终结点之间为直接连接，不会生成 `participantId`。                                                                                                                                                                                                               |
|     participantStartTime      |     参与者第一次连接尝试开始的时间戳。                                                                                                                                                                                                                                                                                                                                                                                            |
|     participantDuration       |     每个参与者连接的持续时间，以秒为单位，从 `participantStartTime` 到连接结束的时间戳为止。                                                                                                                                                                                                                                                                                                                                             |
|     participantEndReason      |     包含由 SDK 发出的呼叫 SDK 错误代码（当与各个 `participantId` 相关时）。 请参阅下面的呼叫 SDK 错误代码。                                                                                                                                                                                                                                                                                                                          |
|     endpointId                |     表示连接到呼叫的各个终结点的唯一 ID，呼叫中的终结点类型由 `endpointType` 定义。 当该值为 `null` 时，连接的实体是通信服务服务器 (`endpointType`= `"Server"`)。 对于本机客户端，有时可为跨多个呼叫 (`correlationIdentifier`) 的同一用户保留 `EndpointId`。 `endpointId` 的数量将决定呼叫摘要日志的数量。 对于每一个 `endpointId`，都会创建一个单独的摘要日志。    |
|     endpointType              |     该值用于描述连接到呼叫的各个终结点的属性。 可包含 `"Server"`、`"VOIP"`、`"PSTN"`、`"BOT"` 或 `"Unknown"`。                                                                                                                                                                                                                                                                                                                               |
|     sdkVersion                |     各个相关终结点使用的通信服务呼叫 SDK 版本的版本字符串。 （示例：`"1.1.00.20212500"`）                                                                                                                                                                                                                                                                                                                                                          |
|     osVersion                 |     表示各个终结点设备的操作系统和版本的字符串。                                                                                                                                                                                                                                                                                                                                                                                       |

### <a name="call-diagnostic-log"></a>呼叫诊断日志
呼叫诊断日志提供有关终结点的重要信息和各个参与者的媒体传输信息，以及有助于理解呼叫质量问题的度量。 

|     属性              |     说明                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|---------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     operationName         |     与日志记录相关联的操作。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     operationVersion      |     如果使用 API 执行 `operationName`，则 `api-version` 与该操作关联。 如果没有与该操作相对应的 API，则此版本表示该操作的版本，以防与该操作关联的属性将来发生更改。                                                                                                                                                                                                                                                                                                                                                                  |
|     category              |     事件的日志类别。 类别是可以在特定资源上启用或禁用日志的粒度。 出现在事件的 `properties` blob 中的属性与特定日志类别和资源类型中的属性相同。                                                                                                                                                                                                                                                                                                                                                                                         |
|     correlationIdentifier     |     `correlationIdentifier` 可从单个呼叫期间连接的所有参与者和终结点中标识相关事件。 `correlationIdentifier` 是呼叫的唯一 ID。 如果需要通过 Microsoft 打开支持案例，`correlationID` 可用于轻松标识你要排查故障的呼叫。                                                                                                                                                                                                                                                                                                                                                                         |
|     participantID         |     生成此 ID 是用于表示“Participant”终结点 (`endpointType` =  `“Server”`) 与服务器之间的双向连接。 当 `callType`   = `"P2P"` 时，两个终结点之间为直接连接，不会生成 `participantId`。                                                                                                                                                                                                                                                                                                                                                                                                |
|     标识符            |     此 ID 表示由身份验证服务定义的用户标识。 使用此 ID 可关联跨多个呼叫和服务的不同事件。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     endpointId            |     表示连接到呼叫的各个终结点的唯一 ID，终结点类型由 `endpointType` 定义。   当该值为 `null` 时，意味着连接的实体是通信服务服务器。  对于本机客户端，可在跨多个呼叫 (`correlationIdentifier`) 中为同一用户保留 `EndpointId`，但当客户端是 Web 浏览器时，该属性值对于每个呼叫都是唯一的。                                                                                                                                                                                                                                                                                  |
|     endpointType          |     该值用于描述各个 `endpointId` 的属性。 可包含 `“Server”`、`“VOIP”`、`“PSTN”`、`“BOT”` 或 `“Unknown”`。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
|     mediaType             |     此字符串值用于描述在各个流内多个终结点之间传输的媒体类型。 可能的值包括 `“Audio”`、`“Video”`、`“VBSS”`（基于视频的屏幕共享）和 `“AppSharing”`。                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|     streamId              |     非唯一的整数，与 `mediaType` 一起使用，可用于对同一 `participantId` 的多个流进行唯一标识。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
|     transportType         |     字符串值，用于描述各个 `participantId` 的网络传输协议。 可包含 `"UDP”`、`“TCP”` 或 `“Unrecognized”`。 `"Unrecognized"` 指示系统无法确定 `transportType` 是 TCP 还是 UDP。                                                                                                                                                                                                                                                                                                                                                                                                                                   |
|     roundTripTimeAvg      |     表示在一个 `participantDuration` 中获取从一个终结点到另一个终结点的 IP 数据包所花费的平均时间。 这种网络传播延迟本质上与两点之间的物理距离和光速相关，还包括在此过程中不同路由器花费的其他时间。 延迟以单向时间或往返时间 (RTT) 进行度量。  其值以毫秒 (ms) 为单位表示，如果 RTT 大于 500ms，应视为对呼叫质量产生负面影响。                                                                                                                                                                      |
|     roundTripTimeMax      |     在群组呼叫的 `participantDuration` 期间或 P2P 呼叫的 `callDuration` 期间，对各个媒体流测得的最大 RTT (ms)。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|     jitterAvg             |     这是连续数据包之间的延迟的平均变化。 通过缓冲，Azure 通信服务可以适应某些级别的抖动。 仅当抖动值超出缓冲值（大约当 `jitterAvg` > 30 ms）时，才可能对呼叫质量产生负面影响。 以不同速度传入的数据包会导致说话者的声音听起来像机器人。 该值是在群组呼叫的 `participantDuration` 期间或 P2P 呼叫的 `callDuration` 期间对各个媒体流进行测量而获得的。                                                                                                                                              |
|     jitterMax             |     在各个媒体流的数据包之间测得的最大抖动值。 网络突发情况会导致音频/视频通信流出现问题。                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
|     packetLossRateAvg     |     丢失的数据包的平均百分比。 数据包丢失会直接影响音频质量：少量个别的数据包丢失几乎没有影响，但连续的数据包突发丢失会导致音频完全中断。 丢弃和未到达预期目标的数据包会造成媒体中出现间隙，导致音节和单词丢失，以及视频和共享不连贯。 如果数据包丢失率超过 10% (0.1)，应将其视为可能产生负面影响的速率。 该值是在群组呼叫的 `participantDuration` 期间或 P2P 呼叫的 `callDuration` 期间对各个媒体流进行测量而获得的。    |
|     packetLossRateMax     |     该值表示在群组呼叫的 `participantDuration` 期间或 P2P 呼叫的 `callDuration` 期间各个媒体流的最大数据包丢失率 (%)。 网络突发情况会导致音频/视频通信流出现问题。                                                                                                                                                                                                                                                                                                                                                                                                                  |
|                           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                   |

### <a name="error-codes"></a>错误代码
`participantEndReason` 包含的值将从一组呼叫 SDK 的错误代码中获取。 你可以参考这些代码，以便针对每个终结点排除呼叫过程中的故障。

|     错误代码                 |     说明                                                                                                       |     采取的操作                                                                                                                                                                                                                                                             |
|--------------------------------|-----------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     0                          |     Success                                                                                                           |     呼叫 (P2P) 或参与者（群组）正确终止。                                                                                                                                                                                                                    |
|     403                        |     被禁止/身份验证失败。                                                                               |     请确保通信服务令牌有效且未过期。 若要使用 Teams 互操作性，请确保已将 Teams 租户添加到预览访问允许列表。 若要启用/禁用 Teams 租户互操作性，请填写此表单。    |
|     404                        |     找不到呼叫。                                                                                                   |     请确保要呼叫的号码（或要加入的呼叫）确实存在。                                                                                                                                                                                                   |
|     408                        |     呼叫控制器超时。                                                                                        |     等待来自用户终结点的协议消息时呼叫控制器超时。 确保客户端已连接且可用。                                                                                                                                                 |
|     410                        |     本地媒体堆栈或媒体基础结构错误。                                                                  |     请确保在受支持的环境中使用最新的 SDK。                                                                                                                                                                                                      |
|     430                        |     无法将消息传递到客户端应用程序。                                                                  |     请确保客户端应用程序正在运行而且可用。                                                                                                                                                                                                             |
|     480                        |     未注册远程客户端终结点。                                                                            |     请确保远程终结点可用。                                                                                                                                                                                                                              |
|     481                        |     无法处理传入呼叫。                                                                                   |     通过 Azure 门户提交支持请求。                                                                                                                                                                                                                           |
|     487                        |     呼叫被取消，呼叫被本地拒绝，因终结点不匹配问题而结束呼叫，或无法生成媒体产品/服务。    |     预期行为。                                                                                                                                                                                                                                                         |
|     490、491、496、487、498    |     本地终结点网络问题。                                                                                    |     检查网络。                                                                                                                                                                                                                                                        |
|     500、503、504              |     通信服务基础结构错误。                                                                      |     通过 Azure 门户提交支持请求。                                                                                                                                                                                                                           |
|     603                        |     全局呼叫被远程通信服务参与者拒绝。                                             |     预期行为。                                                                                                                                                                                                                                                         |
|     Unknown                    |     非标准的结束原因（不属于标准 SIP 代码的一部分）。                                                      |                                                                                                                                                                                                                                                                                |

## <a name="call-examples-and-sample-data"></a>呼叫示例与示例数据

### <a name="p2p-call"></a>P2P 呼叫

:::image type="content" source="media\call-logs-images\call-logs-concepts-p2p-sample.png" alt-text="P2P 示例的日志示例":::

呼叫中所有日志的共享字段：

```
"time":                     "2021-07-19T18:46:50.188Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "8d1a8374-344d-4502-b54b-ba2d6daaf0ae",
```

#### <a name="call-summary-logs"></a>呼叫摘要日志
呼叫摘要日志包含共享的操作和类别信息：

```
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",

```
VoIP 用户 1 的呼叫摘要
```
"properties": {
    "identifier":               "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "callStartTime":            "2021-07-19T17:54:05.113Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",    
    "participantStartTime":     "2021-07-19T17:54:06.758Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.1.0",
    "osVersion":                "Windows 10.0.17763 Arch: x64"
}
```

VoIP 用户 2 的呼叫摘要
```
"properties": {
    "identifier":               "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "callStartTime":            "2021-07-19T17:54:05.335Z",
    "callDuration":             6,
    "callType":                 "P2P",
    "teamsThreadId":            "null",
    "participantId":            "null",
    "participantStartTime":     "2021-07-19T17:54:06.335Z",
    "participantDuration":      "5",
    "participantEndReason":     "0",
    "endpointId":               "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.1.0.0",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>呼叫诊断日志
呼叫诊断日志共享操作信息：
```
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
从 VoIP 终结点 1 到 VoIP 终结点 2 的音频流诊断日志：
```
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "82",
    "roundTripTimeMax":     "88",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
从 VoIP 终结点 2 到 VoIP 终结点 1 的音频流诊断日志：
```
"properties": {
    "identifier":           "acs:7af14122-9ac7-4b81-80a8-4bf3582b42d0_06f9276d-8efe-4bdd-8c22-ebc5434903f0",
    "participantId":        "null",
    "endpointId":           "a5bd82f9-ac38-4f4a-a0fa-bb3467cdcc64",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "1363841599",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "78",
    "roundTripTimeMax":     "84",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
从 VoIP 终结点 1 到 VoIP 终结点 2 的视频流诊断日志：
```
"properties": {
    "identifier":           "acs:61fddbe3-0003-4066-97bc-6aaf143bbb84_0000000b-4fee-66cf-ac00-343a0d003158",
    "participantId":        "null",
    "endpointId":           "570ea078-74e9-4430-9c67-464ba1fa5859",
    "endpointType":         "VoIP",
    "mediaType":            "Video",
    "streamId":             "2804",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "103",
    "roundTripTimeMax":     "143",
    "jitterAvg":            "0",
    "jitterMax":            "4",
    "packetLossRateAvg":    "3.146336E-05",
    "packetLossRateMax":    "0.001769911"
}
```
### <a name="group-call"></a>群组呼叫
在以下示例中，群组呼叫中有三个用户，其中两个用户通过 VOIP 连接，另一个通过 PSTN 连接。 所有用户都只使用音频。 

:::image type="content" source="media\call-logs-images\call-logs-concepts-group-sample.png" alt-text="群组呼叫示例的日志示例":::

呼叫数据将生成 3 个呼叫摘要日志和 6 个呼叫诊断日志。

呼叫中所有日志的共享字段：
```
"time":                     "2021-07-05T06:30:06.402Z",
"resourceId":               "SUBSCRIPTIONS/XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX/RESOURCEGROUPS/ACS-PROD-CCTS-TESTS/PROVIDERS/MICROSOFT.COMMUNICATION/COMMUNICATIONSERVICES/ACS-PROD-CCTS-TESTS",
"correlationId":            "341acde7-8aa5-445b-a3da-2ddadca47d22",
```

#### <a name="call-summary-logs"></a>呼叫摘要日志
呼叫摘要日志包含共享的操作和类别信息：
```
"operationName":            "CallSummary",
"operationVersion":         "1.0",
"category":                 "CallSummaryPRIVATEPREVIEW",
```

VoIP 终结点 1 的呼叫摘要：
```
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "participantStartTime":     "2021-07-05T06:16:44.235Z",
    "participantDuration":      "82",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Darwin Kernel Version 18.7.0: Mon Nov 9 15:07:15 PST 2020; root:xnu-4903.272.3~3/RELEASE_ARM64_S5L8960X"
}
```
VoIP 终结点 3 的呼叫摘要：
```
"properties": {
    "identifier":               "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "participantStartTime":     "2021-07-05T06:16:40.240Z",
    "participantDuration":      "87",
    "participantEndReason":     "0",
    "endpointId":               "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":             "VoIP",
    "sdkVersion":               "1.0.0.3",
    "osVersion":                "Android 11.0; Manufacturer: Google; Product: redfin; Model: Pixel 5; Hardware: redfin"
}
```
PSTN 终结点 2 的呼叫摘要：
```
"properties": {
    "identifier":               "null",
    "callStartTime":            "2021-07-05T06:16:40.240Z",
    "callDuration":             87,
    "callType":                 "Group",
    "teamsThreadId":            "19:meeting_MjZiOTAyN2YtZWU1Yi00ZTZiLTk2ZDUtYTZlM2I2ZjgxOTkw@thread.v2",
    "participantId":            "515650f7-8204-4079-ac9d-d8f4bf07b04c",
    "participantStartTime":     "2021-07-05T06:17:10.447Z",
    "participantDuration":      "52",
    "participantEndReason":     "0",
    "endpointId":               "46387150-692a-47be-8c9d-1237efe6c48b",
    "endpointType":             "PSTN",
    "sdkVersion":               "null",
    "osVersion":                "null"
}
```
#### <a name="call-diagnostic-logs"></a>呼叫诊断日志
呼叫诊断日志共享操作信息：
```
"operationName":            "CallDiagnostics",
"operationVersion":         "1.0",
"category":                 "CallDiagnosticsPRIVATEPREVIEW",
```
从 VoIP 终结点 1 到服务器终结点的音频流诊断日志：
```
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-729f-ac00-343a0d00d975",
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           "5ebd55df-ffff-ffff-89e6-4f3f0453b1a6",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "14884",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "46",
    "roundTripTimeMax":     "48",
    "jitterAvg":            "0",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
从服务器终结点到 VoIP 终结点 1 的音频流诊断日志：
```
"properties": {
    "identifier":           null,
    "participantId":        "04cc26f5-a86d-481c-b9f9-7a40be4d6fba",
    "endpointId":           null,
    "endpointType":         "Server",
    "mediaType":            "Audio",
    "streamId":             "2001",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "42",
    "roundTripTimeMax":     "44",
    "jitterAvg":            "1",
    "jitterMax":            "1",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
从 VoIP 终结点 3 到服务器终结点的音频流诊断日志：
```
"properties": {
    "identifier":           "acs:1797dbb3-f982-47b0-b98e-6a76084454f1_0000000b-1531-57c6-ac00-343a0d00d972",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           "5ebd55df-ffff-ffff-ab89-19ff584890b7",
    "endpointType":         "VoIP",
    "mediaType":            "Audio",
    "streamId":             "13783",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "2",
    "packetLossRateAvg":    "0",
    "packetLossRateMax":    "0"
}
```
从服务器终结点到 VoIP 终结点 3 的音频流诊断日志：
```
"properties": {
    "identifier":           "null",
    "participantId":        "1a9cb3d1-7898-4063-b3d2-26c1630ecf03",
    "endpointId":           null,
    "endpointType":         "Server"    
    "mediaType":            "Audio",
    "streamId":             "1000",
    "transportType":        "UDP",
    "roundTripTimeAvg":     "45",
    "roundTripTimeMax":     "46",
    "jitterAvg":            "1",
    "jitterMax":            "4",
    "packetLossRateAvg":    "0",
```

## <a name="next-steps"></a>后续步骤

- 了解有关[日志记录和诊断](./logging-and-diagnostics.md)的详细信息
