---
title: 快速入门 - 从 iOS 应用加入 Teams 会议
description: 本教程介绍如何使用适用于 iOS 的 Azure 通信服务通话 SDK 加入 Teams 会议
author: chpalm
ms.author: rifox
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 1b277dcca381cd621fac6011ad47f30e2744ee68
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078572"
---
本快速入门介绍如何使用适用于 iOS 的 Azure 通信服务通话 SDK 加入 Teams 会议。

## <a name="prerequisites"></a>先决条件

- 正常工作的[通信服务通话 iOS 应用](../../getting-started-with-calling.md)。
- [Teams 部署](/deployoffice/teams-install)。

对于本快速入门，我们将使用 AzureCommunicationCalling SDK 的 beta.12，因此需要更新 Pod 文件并再次安装 Pod。 

将 Podfile 替换为以下代码并保存（确保“目标对象”与项目名称匹配）：

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '1.0.0-beta.12'
   end
   ```
删除您的 Pod 文件夹、Podfile.lock 和 `.xcworkspace.` 文件。

运行 `pod install` 并使用 Xcode 打开 `.xcworkspace`。

## <a name="add-the-teams-ui-controls-and-enable-the-teams-ui-controls"></a>添加 Teams UI 控件并启用 Teams UI 控件

将 ContentView.swift 中的代码替换为以下代码片段。 文本框用于输入 Teams 会议上下文，按钮用于加入指定的会议：

```swift

import SwiftUI
import AzureCommunicationCalling
import AVFoundation

struct ContentView: View {
    @State var meetingLink: String = ""
    @State var callStatus: String = ""
    @State var message: String = ""
    @State var recordingStatus: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?
    @State var callObserver: CallObserver?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Teams meeting link", text: $meetingLink)
                    Button(action: joinTeamsMeeting) {
                        Text("Join Teams Meeting")
                    }.disabled(callAgent == nil)
                    Button(action: leaveMeeting) {
                        Text("Leave Meeting")
                    }.disabled(call == nil)
                    Text(callStatus)
                    Text(message)
                    Text(recordingStatus)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
            var userCredential: CommunicationTokenCredential?
            do {
                userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
            } catch {
                print("ERROR: It was not possible to create user credential.")
                self.message = "Please enter your token in source code"
                return
            }

            self.callClient = CallClient()

            // Creates the call agent
            self.callClient?.createCallAgent(userCredential: userCredential!) { (agent, error) in
                if error != nil {
                    self.message = "Failed to create CallAgent."
                    return
                } else {
                    self.callAgent = agent
                    self.message = "Call agent successfully created."
                }
            }
        }
    }

    func joinTeamsMeeting() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                let joinCallOptions = JoinCallOptions()
                let teamsMeetingLinkLocator = TeamsMeetingLinkLocator(meetingLink: self.meetingLink)
                self.callAgent?.join(with: teamsMeetingLinkLocator, joinCallOptions: joinCallOptions) {(call, error) in
                    if (error == nil) {
                        self.call = call
                        self.callObserver = CallObserver(self)
                        self.call!.delegate = self.callObserver
                        self.message = "Teams meeting joined successfully"
                    } else {
                        print("Failed to get call object")
                        return
                    }
                }
            }
        }
    }

    func leaveMeeting() {
        if let call = call {
            call.hangUp(options: nil, completionHandler: { (error) in
                if error == nil {
                    self.message = "Leaving Teams meeting was successful"
                } else {
                    self.message = "Leaving Teams meeting failed"
                }
            })
        } else {
            self.message = "No active call to hangup"
        }
    }
}

class CallObserver : NSObject, CallDelegate {
    private var owner:ContentView
    init(_ view:ContentView) {
        owner = view
    }

    public func call(_ call: Call, didChangeState args: PropertyChangedEventArgs) {
        owner.callStatus = CallObserver.callStateToString(state: call.state)
        if call.state == .disconnected {
            owner.call = nil
            owner.message = "Left Meeting"
        } else if call.state == .inLobby {
            owner.message = "Waiting in lobby !!"
        } else if call.state == .connected {
            owner.message = "Joined Meeting !!"
        }
    }
    
    public func call(_ call: Call, didChangeRecordingState args: PropertyChangedEventArgs) {
        if (call.isRecordingActive == true) {
            owner.recordingStatus = "This call is being recorded"
        }
        else {
            owner.recordingStatus = ""
        }
    }

    private static func callStateToString(state: CallState) -> String {
        switch state {
        case .connected: return "Connected"
        case .connecting: return "Connecting"
        case .disconnected: return "Disconnected"
        case .disconnecting: return "Disconnecting"
        case .earlyMedia: return "EarlyMedia"
        case .none: return "None"
        case .ringing: return "Ringing"
        case .inLobby: return "InLobby"
        default: return "Unknown"
        }
    }
}

struct ContentView_Previews: PreviewProvider {
    static var previews: some View {
        ContentView()
    }
}

```

## <a name="get-the-teams-meeting-link"></a>获取 Teams 会议链接

可以使用图形 API 来检索 Teams 会议链接。 [Graph 文档](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true)中对此进行了详细介绍。
通信服务呼叫 SDK 接受完整的 Teams 会议链接。 此链接将作为 `onlineMeeting` 资源的一部分返回，可在 [`joinWebUrl` 属性](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true)下访问。 还可以从 Teams 会议邀请本身内的“加入会议”URL 中获取所需的会议信息。

## <a name="launch-the-app-and-join-teams-meeting"></a>启动应用并加入 Teams 会议

可以通过选择“产品” > “运行”或使用 (&#8984;-R) 键盘快捷方式，在 iOS 模拟器上生成并运行应用。

:::image type="content" source="../../media/ios/acs-join-teams-meeting-quickstart.png" alt-text="显示已完成应用程序的屏幕截图。":::

将 Teams 上下文插入文本框，然后按“加入 Teams 会议”，从通信服务应用程序中加入 Teams 会议。
