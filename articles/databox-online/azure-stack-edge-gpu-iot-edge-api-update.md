---
title: Azure Stack Edge 2021 年 1 月更新影响 | Microsoft Docs
description: 本文介绍在安装 2021 年 1 月更新后，对 Azure Stack Edge 设备上 IoT Edge 角色管理的影响。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578733"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>Azure Stack Edge 设备的 IoT Edge 角色管理更改

对于 Azure Stack Edge 设备的 Azure IoT Edge 角色管理，请使用 API、SDK 和 Azure PowerShell 的更新版本，该版本计划于 2021 年 1 月发布。 

本文详细介绍了使用此最新版本时需要进行的更改。

2021 年 1 月更新将仅适用于 Azure Stack Edge Pro - GPU、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备。 本文中的信息仅适用于这些设备。

> [!NOTE]
> 不需要升级到 2021 年 1 月版本。 如果选择继续使用当前版本，则不会对 IoT Edge 角色管理产生任何影响。 但是，为了利用新功能并帮助降低安全风险，建议安装较新版本。 

## <a name="iot-edge-role-management-changes"></a>IoT Edge 角色管理更改

在 Azure Stack Edge 设备上安装可选的 2021 年 1 月更新后，需要使用最新版本的 API、SDK 和 PowerShell cmdlet 来进行 IoT Edge 角色管理。

仅当应用 2021 年 1 月更新时，才需要进行以下更改：

- 如果当前使用角色管理 API 版本&nbsp;2019-08-01，请升级到将于 2021 年 1 月发布的 API 版本。 
- 如果当前通过 SDK 版本&nbsp;1.0.0 使用角色管理，请升级到将于 2021 年 1 月发布的版本。
- 如果将角色管理用于 Azure PowerShell cmdlet（预览版）（例如 `Get-AzStackEdgeRole`、`New-AzStackEdgeRole`、`Set-AzStackEdgeRole` 或 `Remove-AzStackEdgeRole`），请等待将于 2021 年 2 月发布的新 cmdlet。

## <a name="api-usage"></a>API 用法

如果当前通过 API 执行 IoT Edge 角色管理，应使用新的 API 版本 2020-12-01，该版本稍后将发布。 如果你使用的是当前的角色 API，在安装即将推出的设备软件版本后，必须转到后接 PUT IoT 加载项 API 的 PUT、GET 或 DELETE 这三种 Kubernetes 角色。

### <a name="for-the-put-method"></a>对于 PUT 方法

#### <a name="the-current-http-request"></a>当前 HTTP 请求 

- 在此 URI 上执行 API 调用：“https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01”

- 请求正文如下所示：

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="the-upcoming-http-request"></a>即将发出的 HTTP 请求 

- 在以下 URI 上执行针对 Kubernetes 角色的 API 调用： 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    请求正文如下所示：

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- 在以下 URI 上执行针对 IoT Edge 加载项的 API 调用： 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    请求正文如下所示：

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-the-get-method"></a>对于 GET 方法

#### <a name="the-current-http-response"></a>当前 HTTP 响应

- 在以下 URI 上执行 API 调用：

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

- 响应正文如下所示：

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="the-upcoming-http-response"></a>即将发出的 HTTP 响应

- 在以下 URI 上执行 API 调用： 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

- 响应正文如下所示： 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-the-delete-method"></a>对于 DELETE 方法

### <a name="the-current-api-calls"></a>当前的 API 调用

在以下 URI 上执行 API 调用： 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>即将发出的 API 调用

在以下 URI 上执行 API 调用： 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK 用法

如果你使用的是 SDK，在安装 2021 年 1 月更新后，需要更改设置 IoT Edge 角色的方式，如以下示例所示。 然后，你将下载并安装即将推出的 NuGet 包，以便移动到新的 SDK，如下所示。

### <a name="the-current-sdk-sample"></a>当前 SDK 示例

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="the-new-sdk-sample"></a>新的 SDK 示例

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Cmdlet 用法

如果你当前使用 `Get-AzStackEdgeRole`、`New-AzStackEdgeRole`、`Set-AzStackEdgeRole` 或 `Remove-AzStackEdgeRole` cmdlet，则需要等待计划于 2021 年 2 月发行的新版本。

## <a name="frequently-asked-questions"></a>常见问题

**我使用的是 Azure Stack Edge Pro - FPGA。2021 年 1 月更新是否会影响 FPGA 模型？**

不是。 2021 年 1 月更新仅适用于 Azure Stack Edge Pro - FPGA、Azure Stack Edge Pro R 和 Azure Stack Edge Mini R 设备。 Azure Stack Edge Pro - FPGA 不受此更新的影响，而且无需对 IoT Edge 角色管理进行任何更改。

**将 Azure Stack Edge Pro - GPU 更新为 2021 年 1 月的新设备软件后，是否会影响任何现有服务？**

不是。 安装 2021 年 1 月的设备更新后，你配置的服务不会受到影响。

**对 IoT Edge 管理 API、SDK 或 cmdlet 所作的大致更改有哪些？**

IoT Edge 是 Kubernetes 角色下的加载项，这意味着首先需要确保已配置 Kubernetes，然后再执行 IoT Edge 配置。


## <a name="next-steps"></a>后续步骤

- [了解如何应用更新](azure-stack-edge-gpu-install-update.md)

