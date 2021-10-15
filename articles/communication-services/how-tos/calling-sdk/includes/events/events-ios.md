---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: 0ba2baf8d6517a4719f63bf4cec37541e223540a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "128698914"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-ios.md)]

使用我们的 iOS SDK，可以订阅大多数属性和集合，以便在值更改时收到通知。

## <a name="properties"></a>属性
若要订阅 `property changed` 事件，请使用以下代码。

```swift
call.delegate = self
// Get the property of the call state by getting on the call's state member
public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

// to unsubscribe
self.call.delegate = nil
```

## <a name="collections"></a>集合
若要订阅 `collection updated` 事件，请使用以下代码。

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func call(_ call: Call, didUpdateLocalVideoStreams args: LocalVideoStreamsUpdatedEventArgs) {
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```