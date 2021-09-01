---
author: probableprime
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: rifox
ms.openlocfilehash: c2d280de30997996902def6f04eb1d35c956999a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965973"
---
使用 Azure 通信服务呼叫 SDK 向应用添加一对一视频呼叫，开启 Azure 通信服务使用旅程。 你将了解如何使用适用于 iOS 的 Azure 通信服务呼叫 SDK 发起和应答视频呼叫。

## <a name="sample-code"></a>代码示例

如果你要向前跳转到末尾，可以从 [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Video%20Calling) 下载此快速入门示例。

## <a name="prerequisites"></a>必备条件
- 获取具有有效订阅的 Azure 帐户。 [免费创建帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
- 一部运行 [Xcode](https://developer.apple.com/xcode/) 的 Mac，以及一个安装到密钥链的有效开发人员证书。
- 创建活动的通信服务资源。 [创建通信服务资源](../../../create-communication-resource.md?tabs=windows&pivots=platform-azp)。
- 创建用于实例化呼叫客户端的用户访问令牌。 [了解如何创建和管理用户访问令牌](../../../access-tokens.md?pivots=programming-language-csharp)。

## <a name="setting-up"></a>设置
### <a name="creating-the-xcode-project"></a>创建 Xcode 项目
在 Xcode 中，创建新的 iOS 项目，并选择“单视图应用”模板。 本教程使用 [SwiftUI 框架](https://developer.apple.com/xcode/swiftui/)，因此应将“语言”设置为“Swift”，并将“用户界面”设置为“SwiftUI”。 在此快速入门过程中，不会创建测试。 可以取消选中“包括测试”。

:::image type="content" source="../../media/ios/xcode-new-ios-project.png" alt-text="显示 Xcode 中“新建项目”窗口的屏幕截图。":::

### <a name="installing-cocoapods"></a>安装 CocoaPods
请使用此指南在 Mac 上[安装 CocoaPods](https://guides.cocoapods.org/using/getting-started.html)。 

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>使用 CocoaPods 安装包和依赖项
1. 若要为应用程序创建 Podfile，请打开终端并导航到项目文件夹，然后运行 pod init。

2. 将以下代码添加到 Podfile 并进行保存：

```
platform :ios, '13.0'
use_frameworks!

target 'VideoCallingQuickstart' do
  pod 'AzureCommunicationCalling', '~> 1.0.0'
end
```

3. 运行 pod install。

4. 使用 Xcode 打开 .xcworkspace。


### <a name="request-access-to-the-microphone-and-camera"></a>请求访问麦克风和摄像头
若要访问设备的麦克风和摄像头，需要使用 `NSMicrophoneUsageDescription` 和 `NSCameraUsageDescription` 更新应用的信息属性列表。 将关联的值设置为将要包含在系统用于向用户请求访问权限的对话框中的字符串。

右键单击项目树的 `Info.plist` 条目，然后选择“打开为”>“源代码”。 将以下代码行添加到顶层 `<dict>` 节，然后保存文件。

```XML
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
<key>NSCameraUsageDescription</key>
<string>Need camera access for video calling</string>
```

### <a name="set-up-the-app-framework"></a>设置应用框架
打开项目的 `ContentView.swift` 文件，然后将 import 声明添加到文件顶部以导入 `AzureCommunicationCalling` 库和 `AVFoundation`。 AVFoundation 用于从代码中捕获音频权限。

```Swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>对象模型
以下类和接口处理适用于 iOS 的 Azure 通信服务呼叫 SDK 的某些主要功能。

| 名称                         | 说明                                                                                                                                                                        |
| :--------------------------- | :--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                   | CallClient 是通话 SDK 的主入口点。                                                                                                                         |
| CallAgent                    | CallAgent 用于启动和管理呼叫。                                                                                                                                   |
| CommunicationTokenCredential | CommunicationTokenCredential 用作实例化 CallAgent 的令牌凭据。                                                                                     |
| CommunicationIdentifier      | CommunicationIdentifier 用于表示用户的标识，可以是 CommunicationUserIdentifier、PhoneNumberIdentifier 或 CallingApplication。 |

## <a name="create-the-call-agent"></a>创建呼叫代理
将 ContentView 结构的实现替换为使用户可以发起和结束呼叫的一些简单 UI 控件。 我们会在本快速入门中将业务逻辑附加到这些控件。

```Swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var deviceManager: DeviceManager?
    @State var localVideoStream:[LocalVideoStream]?
    @State var incomingCall: IncomingCall?
    @State var sendingVideo:Bool = false
    @State var errorMessage:String = "Unknown"

    @State var remoteVideoStreamData:[Int32:RemoteVideoStreamData] = [:]
    @State var previewRenderer:VideoStreamRenderer? = nil
    @State var previewView:RendererView? = nil
    @State var remoteRenderer:VideoStreamRenderer? = nil
    @State var remoteViews:[RendererView] = []
    @State var remoteParticipant: RemoteParticipant?
    @State var remoteVideoSize:String = "Unknown"
    @State var isIncomingCall:Bool = false
    
    @State var callObserver:CallObserver?
    @State var remoteParticipantObserver:RemoteParticipantObserver?

    var body: some View {
        NavigationView {
            ZStack{
                Form {
                    Section {
                        TextField("Who would you like to call?", text: $callee)
                        Button(action: startCall) {
                            Text("Start Call")
                        }.disabled(callAgent == nil)
                        Button(action: endCall) {
                            Text("End Call")
                        }.disabled(call == nil)
                        Button(action: toggleLocalVideo) {
                            HStack {
                                Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                            }
                        }
                    }
                }
                // Show incoming call banner
                if (isIncomingCall) {
                    HStack() {
                        VStack {
                            Text("Incoming call")
                                .padding(10)
                                .frame(maxWidth: .infinity, alignment: .topLeading)
                        }
                        Button(action: answerIncomingCall) {
                            HStack {
                                Text("Answer")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.green))
                        }
                        Button(action: declineIncomingCall) {
                            HStack {
                                Text("Decline")
                            }
                            .frame(width:80)
                            .padding(.vertical, 10)
                            .background(Color(.red))
                        }
                    }
                    .frame(maxWidth: .infinity, alignment: .topLeading)
                    .padding(10)
                    .background(Color.gray)
                }
                ZStack{
                    VStack{
                        ForEach(remoteViews, id:\.self) { renderer in
                            ZStack{
                                VStack{
                                    RemoteVideoView(view: renderer)
                                        .frame(width: .infinity, height: .infinity)
                                        .background(Color(.lightGray))
                                }
                            }
                            Button(action: endCall) {
                                Text("End Call")
                            }.disabled(call == nil)
                            Button(action: toggleLocalVideo) {
                                HStack {
                                    Text(sendingVideo ? "Turn Off Video" : "Turn On Video")
                                }
                            }
                        }
                        
                    }.frame(maxWidth: .infinity, maxHeight: .infinity, alignment: .topLeading)
                    VStack{
                        if(sendingVideo)
                        {
                            VStack{
                                PreviewVideoStream(view: previewView!)
                                    .frame(width: 135, height: 240)
                                    .background(Color(.lightGray))
                            }
                        }
                    }.frame(maxWidth:.infinity, maxHeight:.infinity,alignment: .bottomTrailing)
                }
            }
     .navigationBarTitle("Video Calling Quickstart")
        }.onAppear{
            // Authenticate the client
            
            // Initialize the CallAgent and access Device Manager
            
            // Ask for permissions
        }
    }
}

//Functions and Observers

struct PreviewVideoStream: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct RemoteVideoView: UIViewRepresentable {
    let view:RendererView
    func makeUIView(context: Context) -> UIView {
        return view
    }
    func updateUIView(_ uiView: UIView, context: Context) {}
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}
```

### <a name="authenticate-the-client"></a>验证客户端
若要初始化 CallAgent 实例，我们需要一个可用于发起和接收呼叫的用户访问令牌。 如果还没有可用的令牌，请参阅[用户访问令牌](../../../access-tokens.md?pivots=programming-language-csharp)文档。

如果你有一个令牌，请将以下代码添加到 `ContentView.swift` 中的 `onAppear` 回调。 需要将 `<USER ACCESS TOKEN>` 替换为资源的有效用户访问令牌**：

```Swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}
```

### <a name="initialize-the-callagent-and-access-device-manager"></a>初始化 CallAgent 并访问设备管理器
若要从 CallClient 创建 CallAgent 实例，请使用 `callClient.createCallAgent` 方法，在初始化 CallAgent 对象后，该方法将以异步方式返回该对象。 借助 DeviceManager 可枚举通话中可用于传输音频/视频流的本地设备。 还可向用户请求访问麦克风/相机的权限。 

```Swift
self.callClient = CallClient()
self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    else {
        self.callAgent = agent
        print("Call agent successfully created.")
        self.callAgent!.delegate = incomingCallHandler
        self.callClient?.getDeviceManager { (deviceManager, error) in
            if (error == nil) {
                print("Got device manager instance")
                self.deviceManager = deviceManager
            } else {
                print("Failed to get device manager instance")
            }
        }
    }
}
```

### <a name="ask-for-permissions"></a>请求权限
我们需要将以下代码添加到 `onAppear` 回调，以请求音频和视频权限。 

```Swift
AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
    if granted {
        AVCaptureDevice.requestAccess(for: .video) { (videoGranted) in
            /* NO OPERATION */
        }
    }
}
```

## <a name="display-local-video"></a>显示本地视频
在开始通话之前，可以管理视频相关的设置。 本快速入门将介绍如何在通话之前或通话期间实现本地视频切换。 

首先，我们需要通过 `deviceManager` 访问本地摄像头。 选择所需的摄像头后，我们可以构造 `LocalVideoStream`，然后创建 `VideoStreamRenderer` 并将它附加到 `previewView`。 在通话期间，我们可以使用 `startVideo` 或 `stopVideo` 来开始或停止向远程参与者发送 `LocalVideoStream`。 此函数还可用于处理传入呼叫。 

```Swift
func toggleLocalVideo() {
    // toggling video before call starts
    if (call == nil)
    {
        if(!sendingVideo)
        {
            self.callClient = CallClient()
            self.callClient?.getDeviceManager { (deviceManager, error) in
                if (error == nil) {
                    print("Got device manager instance")
                    self.deviceManager = deviceManager
                } else {
                    print("Failed to get device manager instance")
                }
            }
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            self.sendingVideo = true
        }
        else{
            self.sendingVideo = false
            self.previewView = nil
            self.previewRenderer!.dispose()
            self.previewRenderer = nil
        }
    }
    // toggle local video during the call
    else{
        if (sendingVideo) {
            call!.stopVideo(stream: localVideoStream!.first!) { (error) in
                if (error != nil) {
                    print("cannot stop video")
                }
                else {
                    self.sendingVideo = false
                    self.previewView = nil
                    self.previewRenderer!.dispose()
                    self.previewRenderer = nil
                }
            }
        }
        else {
            guard let deviceManager = deviceManager else {
                return
            }
            let camera = deviceManager.cameras.first
            let scalingMode = ScalingMode.fit
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            previewRenderer = try! VideoStreamRenderer(localVideoStream: localVideoStream!.first!)
            previewView = try! previewRenderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
            call!.startVideo(stream:(localVideoStream?.first)!) { (error) in
                if (error != nil) {
                    print("cannot start video")
                }
                else {
                    self.sendingVideo = true
                }
            }
        }
    }
}
```

## <a name="place-an-outgoing-call"></a>拨出电话
`startCall` 方法设置为在点击“开始呼叫”按钮时执行的操作。 在本快速入门中，传出呼叫默认仅为音频呼叫。 若要开始视频呼叫，我们需要使用 `LocalVideoStream` 设置 `VideoOptions`，并使用 `startCallOptions` 传递它，以设置初始呼叫选项。

```Swift
func startCall() {
        let startCallOptions = StartCallOptions()
        if(sendingVideo)
        {
            if (self.localVideoStream == nil) {
                self.localVideoStream = [LocalVideoStream]()
            }
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            startCallOptions.videoOptions = videoOptions
        }
        let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(self.callee)]
        self.callAgent?.startCall(participants: callees, options: startCallOptions) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
```

`CallObserver` 和 `RemotePariticipantObserver` 用于管理呼叫中事件和远程参与者。 我们将在 `setCallAndOberserver` 函数中设置观察程序。 

```Swift
func setCallAndObersever(call:Call!, error:Error?) {
    if (error == nil) {
        self.call = call
        self.callObserver = CallObserver(self)
        self.call!.delegate = self.callObserver
        self.remoteParticipantObserver = RemoteParticipantObserver(self)
    } else {
        print("Failed to get call object")
    }
}
```

## <a name="answer-an-incoming-call"></a>应答传入呼叫
若要应答传入呼叫，请实现 `IncomingCallHandler` 来显示传入呼叫横幅，以便应答或拒绝呼叫。 在 `IncomingCallHandler.swift` 中添加以下实现。 

```Swift
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate {
    public var contentView: ContentView?
    private var incomingCall: IncomingCall?

    private static var instance: IncomingCallHandler?
    static func getOrCreateInstance() -> IncomingCallHandler {
        if let c = instance {
            return c
        }
        instance = IncomingCallHandler()
        return instance!
    }

    private override init() {}
    
    public func callAgent(_ callAgent: CallAgent, didRecieveIncomingCall incomingCall: IncomingCall) {
        self.incomingCall = incomingCall
        self.incomingCall?.delegate = self
        contentView?.showIncomingCallBanner(self.incomingCall!)
    }
    
    public func callAgent(_ callAgent: CallAgent, didUpdateCalls args: CallsUpdatedEventArgs) {
        if let removedCall = args.removedCalls.first {
            contentView?.callRemoved(removedCall)
            self.incomingCall = nil
        }
    }
}
```

需要通过将以下代码添加到 `ContentView.swift` 中的 `onAppear` 回调来创建 `IncomingCallHandler` 的实例：

```Swift
let incomingCallHandler = IncomingCallHandler.getOrCreateInstance()
incomingCallHandler.contentView = self
```

成功创建 CallAgent 后，设置 CallAgent 的委托：

```Swift
self.callAgent!.delegate = incomingCallHandler
```

收到传入呼叫后，`IncomingCallHandler` 将调用函数 `showIncomingCallBanner` 来显示 `answer` 和 `decline` 按钮。 

```Swift
func showIncomingCallBanner(_ incomingCall: IncomingCall?) {
    isIncomingCall = true
    self.incomingCall = incomingCall
}
```

附加到 `answer` 和 `decline` 的操作按如下所示实现。 若要应答视频呼叫，我们需要打开本地视频，然后使用 `localVideoStream` 设置 `AcceptCallOptions` 的选项。 

```Swift
func answerIncomingCall() {
    isIncomingCall = false
    let options = AcceptCallOptions()
    if (self.incomingCall != nil) {
        guard let deviceManager = deviceManager else {
            return
        }        
        if (self.localVideoStream == nil) {
            self.localVideoStream = [LocalVideoStream]()
        }
        if(sendingVideo)
        {
            let camera = deviceManager.cameras.first
            localVideoStream!.append(LocalVideoStream(camera: camera!))
            let videoOptions = VideoOptions(localVideoStreams: localVideoStream!)
            options.videoOptions = videoOptions
        }
        self.incomingCall!.accept(options: options) { (call, error) in
            setCallAndObersever(call: call, error: error)
        }
    }
}

func declineIncomingCall(){
    self.incomingCall!.reject { (error) in }
    isIncomingCall = false
}
```

## <a name="remote-participant-video-streams"></a>远程参与者视频流
我们可以创建一个 `RemoteVideoStreamData` 类来处理远程参与者视频流的呈现。 

```Swift
public class RemoteVideoStreamData : NSObject, RendererDelegate {
    public func videoStreamRenderer(didFailToStart renderer: VideoStreamRenderer) {
        owner.errorMessage = "Renderer failed to start"
    }
    
    private var owner:ContentView
    let stream:RemoteVideoStream
    var renderer:VideoStreamRenderer? {
        didSet {
            if renderer != nil {
                renderer!.delegate = self
            }
        }
    }
    
    var views:[RendererView] = []
    init(view:ContentView, stream:RemoteVideoStream) {
        owner = view
        self.stream = stream
    }
    
    public func videoStreamRenderer(didRenderFirstFrame renderer: VideoStreamRenderer) {
        let size:StreamSize = renderer.size
        owner.remoteVideoSize = String(size.width) + " X " + String(size.height)
    }
}
```

## <a name="subscribe-to-events"></a>订阅事件
我们可以实现一个 `CallObserver` 类来订阅事件集合，以便值在通话期间更改时收到通知。 

```Swift
public class CallObserver: NSObject, CallDelegate, IncomingCallDelegate {
    private var owner: ContentView
    init(_ view:ContentView) {
            owner = view
    }
        
    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        if(call.state == CallState.connected) {
            initialCallParticipant()
        }
    }

    // render remote video streams when remote participant changes
    public func call(_ call: Call, didUpdateRemoteParticipant args: ParticipantsUpdatedEventArgs) {
        for participant in args.addedParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                if !owner.remoteVideoStreamData.isEmpty {
                    return
                }
                let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
                let scalingMode = ScalingMode.fit
                data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
                let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
                data.views.append(view)
                self.owner.remoteViews.append(view)
                owner.remoteVideoStreamData[stream.id] = data
            }
            owner.remoteParticipant = participant
        }
    }

    // Handle remote video streams when the call is connected
    public func initialCallParticipant() {
        for participant in owner.call!.remoteParticipants {
            participant.delegate = owner.remoteParticipantObserver
            for stream in participant.videoStreams {
                renderRemoteStream(stream)
            }
            owner.remoteParticipant = participant
        }
    }
    
    //create render for RemoteVideoStream and attach it to view
    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        if !owner.remoteVideoStreamData.isEmpty {
            return
        }
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
}
```

## <a name="remote-participant-management"></a>远程参与者管理
所有远程参与者均以 `RemoteParticipant` 类型表示，可通过通话实例上的 `remoteParticipants` 集合获得。 我们可以实现一个 `RemoteParticipantObserver` 类来订阅有关远程参与者远程视频流的更新。 

```Swift
public class RemoteParticipantObserver : NSObject, RemoteParticipantDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func renderRemoteStream(_ stream: RemoteVideoStream!) {
        let data:RemoteVideoStreamData = RemoteVideoStreamData(view: owner, stream: stream)
        let scalingMode = ScalingMode.fit
        data.renderer = try! VideoStreamRenderer(remoteVideoStream: stream)
        let view:RendererView = try! data.renderer!.createView(withOptions: CreateViewOptions(scalingMode:scalingMode))
        self.owner.remoteViews.append(view)
        owner.remoteVideoStreamData[stream.id] = data
    }
    
    // render RemoteVideoStream when remote participant turns on the video, dispose the renderer when remote video is off
    public func remoteParticipant(_ remoteParticipant: RemoteParticipant, didUpdateVideoStreams args: RemoteVideoStreamsEventArgs) {
        for stream in args.addedRemoteVideoStreams {
            renderRemoteStream(stream)
        }
        for stream in args.removedRemoteVideoStreams {
            for data in owner.remoteVideoStreamData.values {
                data.renderer?.dispose()
            }
            owner.remoteViews.removeAll()
        }
    }
}
```

## <a name="run-the-code"></a>运行代码
可以通过选择“产品”>“运行”或使用 (⌘-R) 键盘快捷方式，在 iOS 模拟器上生成并运行应用。
