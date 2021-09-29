---
title: 使用 Defender for IoT API
description: 使用外部 REST API 访问传感器和管理控制台发现的数据，并对这些数据执行操作。
ms.date: 12/14/2020
ms.topic: reference
ms.openlocfilehash: 7b97be1e30440131045dccf1822fe6855e747768
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600620"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender for IoT 传感器和管理控制台 API

使用外部 REST API 访问传感器和管理控制台发现的数据，并对这些数据执行操作。

连接通过 SSL 受到保护。

## <a name="getting-started"></a>入门

通常，在 Azure Defender for IoT 传感器或本地管理控制台上使用外部 API 时，需要生成访问令牌。 对于在传感器和本地管理控制台上使用的身份验证 API，不需要使用令牌。

生成令牌：

1. 在“系统设置”窗口中，选择“访问令牌” 。
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="“系统设置”窗口的屏幕截图，其中突出显示了“访问令牌”按钮。":::

2. 选择“生成新令牌”。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="选择该按钮可生成新令牌。":::

3. 描述新令牌的用途，然后选择“下一步”。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="生成新令牌并输入与其关联的集成的名称。":::

4. 随即将显示访问令牌。 请复制该令牌，因为它不会再次显示。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="复制集成的访问令牌。":::

5. 选择“完成”  。 你创建的令牌将显示在“访问令牌”对话框中。
   
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="已填充令牌的“设备令牌”对话框的屏幕截图":::

   “已使用”指示上次接收此令牌的外部调用的时间。

   如果此令牌的“已使用”字段中显示“N/A”，则表示传感器与连接的服务器之间的连接不起作用 。

6. 将标题为“授权”的 HTTP 标头添加到请求中，并将其值设置为生成的令牌。

## <a name="sensor-api-specifications"></a>传感器 API 规范

本部分介绍以下传感器 API：

- [检索设备信息 - /api/v1/devices](#retrieve-device-information---apiv1devices)

- [检索设备连接信息 - /api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [检索有关 CVE 的信息 - /api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [检索警报信息 - /api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [检索时间线事件 - /api/v1/events](#retrieve-timeline-events---apiv1events)

- [检索漏洞信息 - /api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [检索安全漏洞 - /api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [检索操作漏洞 - /api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

- [验证用户凭据 - /api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [更改密码 - /external/authentication/set_password](#change-password---externalauthenticationset_password)

- [由系统管理员执行的用户密码更新 - /external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="retrieve-device-information---apiv1devices"></a>检索设备信息 - /api/v1/devices

使用此 API 可请求已检测到 Defender for IoT 传感器的所有设备的列表。

#### <a name="method"></a>方法

**GET**

请求已检测到 Defender for IoT 传感器的所有设备的列表。

#### <a name="query-parameters"></a>查询参数

- **authorized**：仅筛选已授权和未授权的设备。

  示例：

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示设备的 JSON 对象数组。

#### <a name="device-fields"></a>设备字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **id** | Numeric | 否 | - |
| **ipAddresses** | JSON 数组 | 是 | IP 地址（使用 Internet 地址或设备具有双 NIC 时，可以有多个地址） |
| **name** | 字符串 | 否 | - |
| type | 字符串 | 否 | 未知、工程站、PLC、HMI、Historian、域控制器、数据库服务器、无线接入点、路由器、交换机、服务器、工作站、IP 摄像头、打印机、防火墙、终端站、VPN 网关、Internet 或多播和广播 |
| **macAddresses** | JSON 数组 | 是 | MAC 地址（设备具有双 NIC 时可以有多个地址） |
| **OperatingSystem** | 字符串 | 是 | - |
| **engineeringStation** | 布尔 | 否 | True 或 False |
| **scanner** | 布尔 | 否 | True 或 False |
| **已授权** | 布尔 | 否 | True 或 False |
| **vendor** | 字符串 | 是 | - |
| **protocols** | JSON 数组 | 是 | 协议对象 |
| **firmware** | JSON 数组 | 是 | 固件对象 |

#### <a name="protocol-fields"></a>协议字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **名称** | 字符串 | 否 |  |
| **地址** | JSON 数组 | 是 | 主地址或数值 |

#### <a name="firmware-fields"></a>固件字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **serial** | 字符串 | 否 | N/A 或实际值 |
| **model** | 字符串 | 否 | N/A 或实际值 |
| **firmwareVersion** | Double | 否 | N/A 或实际值 |
| **additionalData** | 字符串 | 否 | N/A 或实际值 |
| **moduleAddress** | 字符串 | 否 | N/A 或实际值 |
| **rack** | 字符串 | 否 | N/A 或实际值 |
| **slot** | 字符串 | 否 | N/A 或实际值 |
| **address** | 字符串 | 否 | N/A 或实际值 |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices?authorized=true` |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>检索设备连接信息 - /api/v1/devices/connections

使用此 API 可请求每个设备的所有连接的列表。

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查询参数

如果未设置查询参数，则返回所有设备连接。

**示例**：

`/api/v1/devices/connections`

- **deviceId**：按特定设备 ID 进行筛选，以查看其连接。

  **示例**：

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**：现在之前连接处于活动状态的时长（以分钟为单位）。

  **示例**：

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**：只筛选在特定时间（以毫秒为单位，UTC）之前检测到的连接。

  **示例**：

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**：只筛选在特定时间（以毫秒为单位，UTC）之后检测到的连接。

  **示例**：

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示设备连接的 JSON 对象数组。

#### <a name="fields"></a>字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **firstDeviceId** | Numeric | 否 | - |
| **secondDeviceId** | Numeric | 否 | - |
| **lastSeen** | Numeric | 否 | Epoch (UTC) |
| **discovered** | Numeric | 否 | Epoch (UTC) |
| **ports** | 数字数组 | 否 | - |
| **protocols** | JSON 数组 | 否 | 协议字段 |

#### <a name="protocol-field"></a>协议字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **name** | 字符串 | 否 | - |
| **commands** | 字符串数组 | 否 | - |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl 命令

> [!div class="mx-tdBreakAll"]
> | 类型 | API | 示例 |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/connections` |
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/<deviceId>/connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000'` |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>检索有关 CVE 的信息 - /api/v1/devices/cves

使用此 API 可请求在网络中的设备上发现的所有已知 CVE 的列表。

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查询参数

默认情况下，此 API 提供具有 CVE 的所有设备 IP 的列表，每个 IP 地址最多前 100 个高分 CVE。

**示例**：

`/api/v1/devices/cves`

- **deviceId**：按特定设备 IP 地址筛选，最多可在该特定设备上识别前 100 个高分 CVE。

  **示例**：

  `/api/v1/devices/<ipAddress>/cves`

- **top**：要为每个设备 IP 地址检索多少个得分最高的 CVE。

  **示例**：

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

JSON 对象数组，用于表示在 IP 地址上识别到的 CVE。

#### <a name="fields"></a>字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **cveId** | 字符串 | 否 | - |
| **ipAddress** | 字符串 | 否 | IP 地址 |
| **score** | 字符串 | 否 | 0.0 - 10.0 |
| **attackVector** | 字符串 | 否 | 网络、相邻网络、本地或物理 |
| description | 字符串 | 否 | - |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/cves` |
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/<deviceIpAddress>/cves?top=` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50` |

### <a name="retrieve-alert-information---apiv1alerts"></a>检索警报信息 - /api/v1/alerts

使用此 API 可请求已检测到 IoT 传感器的所有警报的列表。

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查询参数

- **state**：仅筛选已处理或未处理的警报。

  **示例**：

  `/api/v1/alerts?state=handled`

- **fromTime**：筛选从特定时间开始创建的警报（以毫秒为单位，UTC）。

  **示例**：

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**：仅筛选在特定时间（以毫秒为单位，UTC）之前创建的警报。

  **示例**：

  `/api/v1/alerts?toTime=<epoch>`

- **type**：按特定类型筛选警报。 可作为筛选依据的现有类型：意外的新设备、断开连接。

  **示例**：

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示警报的 JSON 对象数组。

#### <a name="alert-fields"></a>警报字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **ID** | Numeric | 否 | - |
| **time** | Numeric | 否 | Epoch (UTC) |
| **title** | 字符串 | 否 | - |
| **message** | String | 否 | - |
| severity  | 字符串 | 否 | 警告、次要、主要或严重 |
| **engine** | 字符串 | 否 | 协议冲突、策略冲突、恶意软件、异常或操作 |
| **sourceDevice** | Numeric | 是 | 设备 ID |
| **destinationDevice** | Numeric | 是 | 设备 ID |
| **sourceDeviceAddress** | Numeric | 是 | IP、MAC |
| **destinationDeviceAddress** | Numeric | 是 | IP、MAC |
| **remediationSteps** | 字符串 | 是 | 警报中所述的修正步骤 |
| **additionalInformation** | 其他信息对象 | 是 | - |

请注意，以下信息需要 /api/v2/：

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps

#### <a name="additional-information-fields"></a>其他信息字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| description | 字符串 | 否 | - |
| **information** | JSON 数组 | 否 | 字符串 |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl 命令

> [!div class="mx-tdBreakAll"]
> | 类型 | API | 示例 |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections'` |

### <a name="retrieve-timeline-events---apiv1events"></a>检索时间线事件 - /api/v1/events

使用此 API 可请求向事件时间线报告的事件列表。

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查询参数

- **minutesTimeFrame**：从开始报告事件到现在的时长（以分钟为单位）。

  **示例**：

  `/api/v1/events?minutesTimeFrame=20`

- **type**：按特定类型筛选事件列表。

  示例：

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示警报的 JSON 对象数组。

#### <a name="event-fields"></a>事件字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|--|
| **timestamp** | Numeric | 否 | Epoch (UTC) |
| **title** | 字符串 | 否 | - |
| severity  | 字符串 | 否 | INFO、NOTICE 或 ALERT |
| **所有者** | 字符串 | 是 | 如果事件是手动创建的，则此字段将包含创建该事件的用户名 |
| **content** | 字符串 | 否 | - |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED' |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>检索漏洞信息 - /api/v1/reports/vulnerabilities/devices

使用此 API 可请求每个设备的漏洞评估结果。

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示已评估的设备的 JSON 对象数组。

设备对象包含：

- 常规数据

- 评估分数

- 漏洞

#### <a name="device-fields"></a>设备字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **name** | 字符串 | 否 | - |
| **ipAddresses** | JSON 数组 | 否 | - |
| **securityScore** | Numeric | 否 | - |
| **vendor** | 字符串 | 是 |  |
| **firmwareVersion** | 字符串 | 是 | - |
| **model** | 字符串 | 是 | - |
| **isWirelessAccessPoint** | 布尔 | 否 | True 或 False |
| **OperatingSystem** | 操作系统对象 | 是 | - |
| **vulnerabilities** | 漏洞对象 | 是 | - |

#### <a name="operating-system-fields"></a>操作系统字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **名称** | 字符串 | 是 | - |
| **类型** | 字符串 | 是 | - |
| **Version** | 字符串 | 是 | - |
| **latestVersion** | 字符串 | 是 | - |

#### <a name="vulnerabilities-fields"></a>漏洞字段
 
| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **antiViruses** | JSON 数组 | 是 | 防病毒软件名称 |
| **plainTextPasswords** | JSON 数组 | 是 | 密码对象 |
| **remoteAccess** | JSON 数组 | 是 | 远程访问对象 |
| **isBackupServer** | 布尔 | 否 | True 或 False |
| **openedPorts** | JSON 数组 | 是 | 打开的端口对象 |
| **isEngineeringStation** | 布尔 | 否 | True 或 False |
| **isKnownScanner** | 布尔 | 否 | True 或 False |
| **cves** | JSON 数组 | 是 | CVE 对象 |
| **isUnauthorized** | 布尔 | 否 | True 或 False |
| **malwareIndicationsDetected** | 布尔 | 否 | True 或 False |
| **weakAuthentication** | JSON 数组 | 是 | 检测到使用弱身份验证的应用程序 |

#### <a name="password-fields"></a>密码字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **password** | 字符串 | 否 | - |
| **protocol** | 字符串 | 否 | - |
| **strength** | 字符串 | 否 | 非常弱、弱、中等或强 |

#### <a name="remote-access-fields"></a>远程访问字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **port** | Numeric | 否 | - |
| **transport** | 字符串 | 否 | TCP 或 UDP |
| **客户端** | 字符串 | 否 | IP 地址 |
| **clientSoftware** | 字符串 | 否 | SSH、VNC、Remote desktop 或 Team viewer |

#### <a name="open-port-fields"></a>打开端口字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **port** | Numeric | 否 | - |
| **transport** | 字符串 | 否 | TCP 或 UDP |
| **protocol** | 字符串 | 是 | - |
| **isConflictingWithFirewall** | 布尔 | 否 | True 或 False |

#### <a name="cve-fields"></a>CVE 字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **ID** | 字符串 | 否 | - |
| **score** | Numeric | 否 | Double |
| description | 字符串 | 否 | - |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]

```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/devices |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>检索安全漏洞 - /api/v1/reports/vulnerabilities/security

使用此 API 可请求一般漏洞评估结果。 此评估可提供有关系统安全级别的深入见解。

此评估基于常规网络和系统信息，而不是基于特定的设备评估。

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示评估结果的 JSON 对象。 每个键都可为空。 否则，它将包含一个键不可为 null 的 JSON 对象。

### <a name="result-fields"></a>结果字段

**“键”**

**unauthorizedDevices**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| **address** | 字符串 | IP 地址 |
| **name** | 字符串 | - |
| **firstDetectionTime** | Numeric | Epoch (UTC) |
| lastSeen | Numeric | Epoch (UTC) |

**illegalTrafficByFirewallRules**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| 服务器  | 字符串 | IP 地址 |
| **客户端** | 字符串 | IP 地址 |
| **port** | Numeric | - |
| **transport** | 字符串 | TCP、UDP 或 ICMP |

**weakFirewallRules**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| **sources** | 源的 JSON 数组。 每个源都可采用四种格式中的任意一种。 | “Any”、“ip address (Host)”、“from ip-to ip (RANGE)”、“ip address, subnet mask (NETWORK)” |
| **destinations** | 目标的 JSON 数组。 每个目标都可以采用四种格式中的任意一种。 | “Any”、“ip address (Host)”、“from ip-to ip (RANGE)”、“ip address, subnet mask (NETWORK)” |
| **ports** | 采用以下三种格式之一的端口的 JSON 数组 | “Any”、“port (protocol, if detected)”、“from port-to port (protocol, if detected)” |

**accessPoints**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| **macAddress** | 字符串 | MAC 地址 |
| **vendor** | 字符串 | 供应商名称 |
| **ipAddress** | 字符串 | IP 地址或 N/A |
| **name** | 字符串 | 设备名称或 N/A |
| **wireless** | 字符串 | 否、怀疑或是 |

**connectionsBetweenSubnets**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| 服务器  | 字符串 | IP 地址 |
| **客户端** | 字符串 | IP 地址 |

**industrialMalwareIndicators**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| **detectionTime** | Numeric | Epoch (UTC) |
| **alertMessage** | 字符串 | - |
| description | 字符串 | - |
| **devices** | JSON 数组 | 设备名称 | 

**internetConnections**

| 字段名称 | 类型 | 值列表 |
| ---------- | ---- | -------------- |
| **internalAddress** | 字符串 | IP 地址 |
| **已授权** | 布尔 | 是或否 | 
| **externalAddresses** | JSON 数组 | IP 地址 |

#### <a name="response-example"></a>响应示例

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/security |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>检索操作漏洞 - /api/v1/reports/vulnerabilities/operational

使用此 API 可请求一般漏洞评估结果。 此评估可提供有关网络操作状态的见解。 它基于常规网络和系统信息，而不是基于特定的设备评估。

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示评估结果的 JSON 对象。 每个键都包含结果的 JSON 数组。

#### <a name="result-fields"></a>结果字段

**“键”**

**backupServer**

| 字段名称 | 类型 | 值列表 |
|--|--|--|
| **source** | 字符串 | IP 地址 |
| **destination** | 字符串 | IP 地址 |
| **port** | Numeric | - |
| **transport** | 字符串 | TCP 或 UDP |
| **backupMaximalInterval** | 字符串 | - |
| **lastSeenBackup** | Numeric | Epoch (UTC) |

**ipNetworks**

| 字段名称 | 类型 | 值列表 |
|--|--|--|
| **addresse** s | Numeric | - |
| **network** | 字符串 | IP 地址 |
| **mask** | 字符串 | 子网掩码 |

**protocolProblems**

| 字段名称 | 类型 | 值列表 |
|--|--|--|
| **protocol** | 字符串 | - |
| **addresses** | JSON 数组 | IP 地址 |
| **alert** | 字符串 | - |
| **reportTime** | Numeric | Epoch (UTC) |

**protocolDataVolumes**

| 字段名称 | 类型 | 值列表 |
|--|--|--|
| protocol | 字符串 | - |
| 卷 | 字符串 | “数据量值 MB” |

**disconnections**

| 字段名称 | 类型 | 值列表 |
|--|--|--|
| **assetAddress** | 字符串 | IP 地址 |
| **assetName** | 字符串 | - |
| **lastDetectionTime** | Numeric | Epoch (UTC) |
| **backToNormalTime** | Numeric | Epoch (UTC) |     

#### <a name="response-example"></a>响应示例

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}

```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/v1/reports/vulnerabilities/operational |

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>验证用户凭据 - /api/external/authentication/validation

使用此 API 可验证 Defender for IoT 用户名和密码。 所有 Defender for IoT 用户角色都可以使用此 API。

使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="query-parameters"></a>查询参数

| **名称** | **类型** | **可以为 Null** |
|--|--|--|
| **username** | 字符串 | 否 |
| **password** | 字符串 | 否 |

#### <a name="request-example"></a>请求示例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

包含操作状态详细信息的消息字符串：

- **成功 - msg**：身份验证成功

- **失败 - error**：凭据验证失败

#### <a name="response-example"></a>响应示例

```rest
response:

{

    "msg": "Authentication succeeded."

}

```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/api/external/authentication/validation |

### <a name="change-password---externalauthenticationset_password"></a>更改密码 - /external/authentication/set_password

使用此 API 可让用户更改其自己的密码。 所有 Defender for IoT 用户角色都可以使用此 API。 使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="request-example"></a>请求示例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

包含操作状态详细信息的消息字符串：

- **成功 - msg**：已替换密码

- **失败 - error**：用户身份验证失败

- **失败 - error**：密码与安全策略不匹配

#### <a name="response-example"></a>响应示例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>设备字段

| **名称** | **类型** | **可以为 Null** |
|--|--|--|
| **username** | 字符串 | 否 |
| **password** | 字符串 | 否 |
| **new_password** | 字符串 | 否 |

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| POST | curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password | curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/api/external/authentication/set_password |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>由系统管理员执行的用户密码更新 - /external/authentication/set_password_by_admin

使用此 API 可让系统管理员更改指定用户的密码。 Defender for IoT 管理员用户角色可使用 API。 使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="request-example"></a>请求示例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

包含操作状态详细信息的消息字符串：

- **成功 - msg**：已替换密码

- **失败 - error**：用户身份验证失败

- **失败 - error**：用户不存在

- **失败 - error**：密码与安全策略不匹配

- **失败 - error**：用户没有更改密码的权限

#### <a name="response-example"></a>响应示例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>设备字段

| **名称** | **类型** | **可以为 Null** |
|--|--|--|
| **admin_username** | 字符串 | 否 |
| **admin_password** | 字符串 | 否 |
| **username** | 字符串 | 否 |
| **new_password** | 字符串 | 否 |

#### <a name="curl-command"></a>Curl 命令

> [!div class="mx-tdBreakAll"]
> | 类型 | API | 示例 |
> |--|--|--|
> | POST | curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin | curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/api/external/authentication/set_password_by_admin |

## <a name="on-premises-management-console-api-specifications"></a>本地管理控制台 API 规范 ##

本部分介绍适用于以下内容的本地管理控制台 API：
- 警报排除项
- 设备信息
- 警报信息

### <a name="alert-exclusions"></a>警报排除项 ###

定义不会根据其发送警报的条件。 例如，定义和更新停止和开始时间、触发警报时应排除的设备或子网，或者应排除的 Defender for IoT 引擎。 例如，在维护时段最好停止发送所有警报，但关键设备上的恶意软件警报除外。 此处定义的项在本地管理控制台的“警报排除项”窗口中显示为只读排除规则。

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **响应示例**

- **响应：**

```rest
{
    "msg": "Authentication succeeded."
}

```

#### <a name="change-password---externalauthenticationset_password"></a>更改密码 - /external/authentication/set_password 

使用此 API 可让用户更改其自己的密码。 所有 Defender for IoT 用户角色都可以使用此 API。 使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>由系统管理员执行的用户密码更新 - /external/authentication/set_password_by_admin 

使用此 API 可让系统管理员更改指定用户的密码。 Defender for IoT 管理员用户角色可以使用此 API。 使用此 API 无需 Defender for IoT 访问令牌。

### <a name="retrieve-device-information---externalv1devices"></a>检索设备信息 - /external/v1/devices ###

此 API 可请求由 Defender for IoT 传感器检测到的连接到本地管理控制台的所有设备的列表。

#### <a name="method"></a>方法

**GET**

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示设备的 JSON 对象数组。

#### <a name="query-parameters"></a>查询参数

- **authorized**：仅筛选已授权和未授权的设备。

- **siteId**：仅筛选与特定站点相关的设备。

- **zoneId**：仅筛选与特定区域相关的设备。 [1](#1)

- **sensorId**：仅筛选特定传感器检测到的设备。 [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> 你可能没有站点和区域 ID。如果是这种情况，请查询所有设备以检索站点和区域 ID。

#### <a name="query-parameters-example"></a>查询参数示例

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>设备字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **sensorId** | Numeric | 否 | - |
| **zoneId** | Numeric | 是 | - |
| **siteId** | Numeric | 是 | - |
| **ipAddresses** | JSON 数组 | 是 | IP 地址（使用 Internet 地址或设备具有双 NIC 时，可以有多个地址） |
| **name** | 字符串 | 否 | - |
| type | 字符串 | 否 | 未知、工程站、PLC、HMI、Historian、域控制器、数据库服务器、无线接入点、路由器、交换机、服务器、工作站、IP 摄像头、打印机、防火墙、终端站、VPN 网关、Internet 或多播和广播 |
| **macAddresses** | JSON 数组 | 是 | MAC 地址（设备具有双 NIC 时可以有多个地址） |
| **OperatingSystem** | 字符串 | 是 | - |
| **engineeringStation** | 布尔 | 否 | True 或 False |
| **scanner** | 布尔 | 否 | True 或 False |
| **已授权** | 布尔 | 否 | True 或 False |
| **vendor** | 字符串 | 是 | - |
| **协议** | JSON 数组 | 是 | 协议对象 |
| **firmware** | JSON 数组 | 是 | 固件对象 |

#### <a name="protocol-fields"></a>协议字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| 名称 | String | 否 | - |
| 地址 | JSON 数组 | 是 | 主地址或数值 |

#### <a name="firmware-fields"></a>固件字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **serial** | 字符串 | 否 | N/A 或实际值 |
| **model** | 字符串 | 否 | N/A 或实际值 |
| **firmwareVersion** | Double | 否 | N/A 或实际值 |
| **additionalData** | 字符串 | 否 | N/A 或实际值 |
| **moduleAddress** | 字符串 | 否 | N/A 或实际值 |
| **rack** | 字符串 | 否 | N/A 或实际值 |
| **slot** | 字符串 | 否 | N/A 或实际值 |
| **address** | 字符串 | 否 | N/A 或实际值 |

#### <a name="response-example"></a>响应示例

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| GET | curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized=' | curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true' |

### <a name="retrieve-alert-information---externalv1alerts"></a>检索警报信息 - /external/v1/alerts

使用此 API 可从本地管理控制台检索所有/经过筛选的警报。

#### <a name="method"></a>方法

**GET**

#### <a name="query-parameters"></a>查询参数

- **state**：仅筛选已处理和未处理的警报。

  **示例**：

  `/api/v1/alerts?state=handled`

- **fromTime**：筛选从特定时间开始创建的警报（以毫秒为单位，UTC）。

  **示例**：

  `/api/v1/alerts?fromTime=<epoch>`

- **toTime**：仅筛选在特定时间（以毫秒为单位，UTC）之前创建的警报。

  **示例**：

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**：在其中发现警报的站点。
- **zoneId**：在其中发现警报的区域。
- **传感器**：在其中发现警报的传感器。

你可能没有站点和区域 ID。如果是这种情况，请查询所有设备以检索站点和区域 ID。

#### <a name="alert-fields"></a>警报字段 

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **ID** | Numeric | 否 | - |
| **time** | Numeric | 否 | Epoch (UTC) |
| **title** | 字符串 | 否 | - |
| **message** | String | 否 | - |
| severity  | 字符串 | 否 | 警告、次要、主要或严重 |
| **engine** | 字符串 | 否 | 协议冲突、策略冲突、恶意软件、异常或操作 |
| **sourceDevice** | Numeric | 是 | 设备 ID |
| **destinationDevice** | Numeric | 是 | 设备 ID |
| **sourceDeviceAddress** | Numeric | 是 | IP、MAC |
| **destinationDeviceAddress** | Numeric | 是 | IP、MAC |
| **remediationSteps** | 字符串 | 是 | 警报中显示的修正步骤|
| **sensorName** | 字符串 | 是 | 用户定义的传感器的名称 |
|**zoneName** | 字符串 | 是 | 与传感器关联的区域的名称|
| **siteName** | 字符串 | 是 | 与传感器关联的站点的名称 |
| **additionalInformation** | 其他信息对象 | 是 | - |

请注意，以下信息需要 /api/v2/：

- sourceDeviceAddress 
- destinationDeviceAddress
- remediationSteps
- sensorName
- zoneName
- siteName

#### <a name="additional-information-fields"></a>其他信息字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| description | 字符串 | 否 | - |
| **information** | JSON 数组 | 否 | 字符串 |

#### <a name="response-example"></a>响应示例

```rest
[
    {
        "engine": "Operational",
        "handled": false,
        "title": "Traffic Detected on sensor Interface",
        "additionalInformation": null,
        "sourceDevice": 0,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808245000,
        "sensorId": 1,
        "message": "The sensor resumed detecting network traffic on ens224.",
        "destinationDevice": 0,
        "id": 1,
        "severity": "Warning"
    },
    {
        "engine": "Anomaly",
        "handled": false,
        "title": "Address Scan Detected",
        "additionalInformation": null,
        "sourceDevice": 4,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808260000,
        "sensorId": 1,
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        "destinationDevice": 0,
        "id": 2,
        "severity": "Critical"
    },
    {
        "engine": "Operational",
        "handled": false,
        "title": "Suspicion of Unresponsive MODBUS Device",
        "additionalInformation": null,
        "sourceDevice": 194,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808285000,
        "sensorId": 1,
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        "destinationDevice": 0,
        "id": 3,
        "severity": "Minor"
    }
]
```

#### <a name="curl-command"></a>Curl 命令

> [!div class="mx-tdBreakAll"]
> | 类型 | API | 示例 |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1'` |

### <a name="qradar-alerts"></a>QRadar 警报

QRadar 与 Defender for IoT 的集成可帮助识别 Defender for IoT 生成的警报，并根据这些警报执行操作。 QRadar 会接收来自 Defender for IoT 的数据，然后通知公共 API 本地管理控制台组件。

若要将 Defender for IoT 发现的数据发送到 QRadar，请在 Defender for IoT 中定义一个转发规则，然后选择“远程支持警报处理”选项。

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="根据自己的需求编辑转发规则。":::

在配置转发规则的过程中选择此选项时，QRadar 中将显示以下附加字段：

- **UUID**：唯一警报标识符，如 1-1555245116250。

- **站点**：发现警报的站点。

- **区域**：发现警报的区域。

发送到 QRadar 的有效负载示例：

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/&lt;UUID&gt;

#### <a name="method"></a>方法

**PUT**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="request-content"></a>请求内容

JSON 对象，表示要对包含 UUID 的警报执行的操作。

#### <a name="action-fields"></a>操作字段

| 名称 | 类型 | Nullable | 值列表 |
|--|--|--|--|
| **action** | 字符串 | 否 | handle 或 handleAndLearn |

#### <a name="request-example"></a>请求示例

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示设备的 JSON 对象数组。

#### <a name="response-fields"></a>响应字段


| 名称 | 类型 | Nullable | 说明 |
|--|--|--|--|
| **content / error** | 字符串 | 否 | 如果请求成功，则显示内容属性。 否则，显示错误属性。 |

#### <a name="possible-content-values"></a>可能的内容值

| 状态代码 | 内容值 | 说明 |
|--|--|--|
| 200 | 警报更新请求已成功完成。 | 更新请求已成功完成。 无注释。 |
| 200 | 已处理警报 (handle)。 | 在收到警报处理请求时已处理了警报。<br />警报将保持 handled 状态。 |
| 200 | 已处理并了解了警报 (handleAndLearn)。 | 在收到 handleAndLearn 请求时已处理并了解了警报。<br />警报将保持 handledAndLearn 状态。 |
| 200 | 已处理警报 (handled)。<br />已处理并了解了警报 (handleAndLearn)。 | 在收到 handleAndLearn 请求时已处理了警报。<br />警报变为 handleAndLearn 状态。 |
| 200 | 已处理并了解了警报 (handleAndLearn)。 已忽略处理请求。 | 在收到警报处理请求时，此警报已处于 handleAndLearn 状态。 警报保持 handleAndLearn 状态。 |
| 500 | 无效操作。 | 发送的操作不是要对警报执行的有效操作。 |
| 500 | 出现了意外错误。 | 发生了意外错误。 若要解决此问题，请与技术支持联系。 |
| 500 | 无法执行请求，因为找不到此 UUID 的警报。 | 在系统中找不到指定的警报 UUID。 |

#### <a name="response-example"></a>响应示例

成功

```rest
{
    "content": "Alert update request finished successfully"
}
```

不成功

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| PUT | `curl -k -X PUT -d '{"action": "<ACTION>"}' -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID>` | `curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/alerts/1-1594550943000` |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>警报排除（维护时段）- /external/v1/maintenanceWindow

定义不会根据其发送警报的条件。 例如，定义和更新停止和开始时间、触发警报时应排除的设备或子网，或者应排除的 Defender for IoT 引擎。 例如，在维护时段最好停止发送所有警报，但关键设备上的恶意软件警报除外。

此处定义的 API 在本地管理控制台的“警报排除”窗口中显示为只读排除规则。

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="警报排除”窗口，其中显示了所有排除规则的列表。":::

#### <a name="method---post"></a>方法 - POST

#### <a name="query-parameters"></a>查询参数

- **ticketId**：定义用户系统中的维护票证 ID。

- **ttl**：定义 TTL（生存时间），这是维护时段的持续时间（以分钟为单位）。 此参数定义的时间段结束之后，系统会自动开始发送警报。

- **engines**：定义在维护过程中不得显示警报的安全引擎：

   - ANOMALY
   - MALWARE
   - OPERATIONAL
   - POLICY_VIOLATION
   - PROTOCOL_VIOLATION

- **sensorIds**：定义要在维护过程中不得显示警报的 Defender for IoT 传感器。 它与从 /api/v1/appliances (GET) 检索到的 ID 相同。

- **subnets**：定义在维护过程中不得显示警报的子网。 子网的发送格式如下：192.168.0.0/16。

#### <a name="error-codes"></a>错误代码

- **201（已创建）** ：操作已成功完成。

- **400（错误的请求）** ：在以下情况下会出现此错误代码：

   - ttl 参数不是数字或不是正数。
   - subnets 参数是使用错误格式定义的。
   - ticketId 参数缺失。
   - engine 参数与现有安全引擎不匹配。

- **404（未找到）** ：其中一个传感器不存在。

- **409（冲突）** ：票证 ID 已链接到另一个打开的维护时段。

- **500（内部服务器错误）** ：任何其他意外错误。

> [!NOTE]
> 确保票证 ID 未链接到打开的现有时段。 将生成以下排除规则：Maintenance-{令牌名称}-{令牌 ID}。

#### <a name="method---put"></a>方法 - PUT

允许通过更改 ttl 参数，在启动维护过程后更新维护时段持续时间。 新的持续时间定义将替代之前的定义。

如果要设置比当前配置的持续时间更长的持续时间，此方法就非常有用。

#### <a name="query-parameters"></a>查询参数

- **ticketId**：定义用户系统中的维护票证 ID。

- **ttl**：定义时段的持续时间（以分钟为单位）。

#### <a name="error-code"></a>错误代码

- **200（正常）** ：操作已成功完成。

- **400（错误的请求）** ：在以下情况下会出现此错误代码：

   - ttl 参数不是数字或不是正数。
   - ticketId 参数缺失。
   - ttl 参数缺失。

- **404（未找到）** ：票证 ID 未链接到打开的维护时段。

- **500（内部服务器错误）** ：任何其他意外错误。

> [!NOTE]
> 请确保票证 ID 链接到打开的现有时段。

#### <a name="method---delete"></a>方法 - DELETE

关闭现有的维护时段。

#### <a name="query-parameters"></a>查询参数

- **ticketId**：记录用户系统中的维护票证 ID。

#### <a name="error-code"></a>错误代码

- **200（正常）** ：操作已成功完成。

- **400（错误的请求）** ：ticketId 参数缺失。

- **404（未找到）** ：票证 ID 未链接到打开的维护时段。

- **500（内部服务器错误）** ：任何其他意外错误。

> [!NOTE]
> 请确保票证 ID 链接到打开的现有时段。

#### <a name="method---get"></a>方法 - GET

检索维护期间系统中执行的所有打开、关闭和更新操作的日志。 只能检索过去处于活动状态且已关闭的维护时段的日志。

#### <a name="query-parameters"></a>查询参数

- **fromDate**：筛选预定义日期及以后的日志。 格式为 2019-12-30。

- **toDate**：筛选截至预定义日期的日志。 格式为 2019-12-30。

- **ticketId**：筛选与特定票证 ID 相关的日志。

- **tokenName**：筛选与特定令牌名称相关的日志。

#### <a name="error-code"></a>错误代码 

- **200（正常）** ：操作已成功完成。

- **400（错误的请求）** ：日期格式错误。

- **204（无内容）** ：没有要显示的数据。

- **500（内部服务器错误）** ：任何其他意外错误。

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

表示维护时段操作的 JSON 对象数组。

#### <a name="response-structure"></a>响应结构

| 名称 | 类型 | 评论 | Nullable |
|--|--|--|--|
| **dateTime** | 字符串 | 示例：“2012-04-23T18:25:43.511Z” | 否 |
| **ticketId** | 字符串 | 例如：“9a5fe99c-d914-4bda-9332-307384fe40bf” | 否 |
| **tokenName** | String | - | 否 |
| **engines** | 字符串数组 | - | 是 |
| **sensorIds** | 字符串数组 | - | 是 |
| **subnets** | 字符串数组 | - | 是 |
| **ttl** | Numeric | - | 是 |
| **operationType** | 字符串 | 值为“OPEN”、“UPDATE”和“CLOSE” | 否 |

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| POST | `curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2...ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2...SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2....SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","3"],"subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow` |
| PUT | `curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow` |
| DELETE | `curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https:/<span>/127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https:/<span>/127.0.0.1/external/v1/maintenanceWindow` |
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https:/<span>/127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-01-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a'` |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>验证用户凭据 - /external/authentication/validation

使用此 API 可验证用户凭据。 所有 Defender for IoT 用户角色都可以使用此 API。 使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="request-example"></a>请求示例

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

包含操作状态详细信息的消息字符串：

- **成功 - msg**：身份验证成功

- **失败 - error**：凭据验证失败

#### <a name="device-fields"></a>设备字段

| **名称** | **类型** | **可以为 Null** |
|--|--|--|
| **username** | 字符串 | 否 |
| **password** | 字符串 | 否 |

#### <a name="response-example"></a>响应示例

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| POST | `curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation'` | `curl -k -d '{"username":"myUser","password":"1234@abcd"}' 'https:/<span>/127.0.0.1/external/authentication/validation'` |

### <a name="change-password---externalauthenticationset_password"></a>更改密码 - /external/authentication/set_password

使用此 API 可让用户更改其自己的密码。 所有 Defender for IoT 用户角色都可以使用此 API。 使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="request-example"></a>请求示例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

包含操作状态详细信息的消息字符串：

- **成功 - msg**：已替换密码

- **失败 - error**：用户身份验证失败

- **失败 - error**：密码与安全策略不匹配

#### <a name="response-example"></a>响应示例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>设备字段

| **名称** | **类型** | **可以为 Null** |
|--|--|--|
| **username** | 字符串 | 否 |
| **password** | 字符串 | 否 |
| **new_password** | 字符串 | 否 |

#### <a name="curl-command"></a>Curl 命令

| 类型 | API | 示例 |
|--|--|--|
| POST | `curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password` | `curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/external/authentication/set_password` |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>由系统管理员执行的用户密码更新 - /external/authentication/set_password_by_admin

使用此 API 可让系统管理员更改指定用户的密码。 Defender for IoT 管理员用户角色可以使用此 API。 使用此 API 无需 Defender for IoT 访问令牌。

#### <a name="method"></a>方法

**POST**

#### <a name="request-type"></a>请求类型

**JSON**

#### <a name="request-example"></a>请求示例

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>响应类型

**JSON**

#### <a name="response-content"></a>响应内容

包含操作状态详细信息的消息字符串：

- **成功 - msg**：已替换密码

- **失败 - error**：用户身份验证失败

- **失败 - error**：用户不存在

- **失败 - error**：密码与安全策略不匹配

- **失败 - error**：用户没有更改密码的权限

#### <a name="response-example"></a>响应示例

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>设备字段

| **名称** | **类型** | **可以为 Null** |
|--|--|--|
| **admin_username** | 字符串 | 否 |
| **admin_password** | 字符串 | 否 |
| **username** | 字符串 | 否 |
| **new_password** | 字符串 | 否 |

#### <a name="curl-command"></a>Curl 命令

> [!div class="mx-tdBreakAll"]
> | 类型 | API | 示例 |
> |--|--|--|
> | POST | `curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password_by_admin` | `curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https:/<span>/127.0.0.1/external/authentication/set_password_by_admin` |

## <a name="next-steps"></a>后续步骤

- [调查设备清单中的传感器检测](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [调查设备清单中的所有企业传感器检测](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)
