---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 09/08/2021
ms.author: rifox
ms.openlocfilehash: df7a63c57674417bbee5bf04a154cf64bcdc474f
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909157"
---
[!INCLUDE [Install SDK](../install-sdk/install-sdk-android.md)]

使用我们的 Android SDK，可以订阅大多数属性和集合，以便在值更改时收到通知。

## <a name="properties"></a>属性
若要订阅 `property changed` 事件，请采取以下操作：

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnStateChangedListener(callStateChangeListener);
```
使用在同一类中定义的事件侦听器时，请将侦听器绑定到变量。 将变量作为参数传入以添加和删除侦听器方法。

如果尝试直接将侦听器作为参数传入，你将丢失对该侦听器的引用。 Java 将创建这些侦听器的新实例，而不是引用以前创建的实例。 这些侦听器仍会正常触发，但无法删除，因为你将不再有对它们的引用。


## <a name="collections"></a>集合
若要订阅 `collection updated` 事件，请采取以下操作：

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
