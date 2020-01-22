# Creating an Effective watchOS Experience
>创建一个高效的watchOS体验

## Develop a complete watch experience that emphasizes brief interactions and presents the right information at exactly the right time.
> 打造一个完整的手边体验,其包括强调简短的交互和在准确的时间点上展现正确的信息

### Overview
>序


Apple Watch provides easy access to vital information right on the user’s wrist. The watchOS experience focuses on quick actions that achieve useful tasks through brief, punctuated interactions.
> 苹果手表为用户在手腕上查看虚拟信息提供了非常便捷的方式. WatchOS体验集中在通过简短,间断的交互快速实现任务上.

On Apple Watch, keep user interactions as short as possible. Make sure your users can see vital information at a glance, respond with just a few taps, and then drop their wrist and move on. They don’t need to wait to see if the action succeeds; instead, the watchOS app automatically notifies them of any important updates.
>苹果手表坚持把交互尽可能的缩短. 确保你的用户可以瞥一眼就可以看到信息, 点几下就能做出响应, 然后就可以放下手腕继续其他的事情. 他们不需要等待查看动作是否成功; 从而 watchOS app可以在有任何重要跟新的时候通知到他们.

To achieve this level of integration, you must use a variety of technologies to communicate and interact with the user:
>为了实现这种级别的集成, 你必须使用各种技术来和用户交流和沟通. 

- The watchOS app serves as the foundation for a user’s watch experience. Users can launch and interact with your app directly. However, the main app interface isn’t necessarily the primary way users interact with your app. Users often prefer to interact through complications or notifications, and may never explicitly launch your app.
> watchOS app 是用户手表体验中的基础服务. 用户可以直接和你的应用启动,和它交互. 但是主界面并不是用户和你进行交流的必要的方式. 用户通常愿意通过Complecation或者通知来进行交互,也可能从不打开你的应用

- Complications provide small glimpses into your app’s data directly on the watch face. Users can add complications to most watch faces, but space is limited. Design complications to show information that is timely, up-to-date, and useful. Users can also launch the watchOS app quickly and easily by tapping the complication. For more information, see Adding a Complication to Your watchOS App.
>Complication为你提供了很小的空间来让用户在表盘上直接访问你的数据.用户可以把complication添加到多数的表盘上,只是空间有限.设计complication来展示那些及时的,更新的,有用的信息. 用户也可以点击complication来快速启动app.关于更多,请查看[Adding a Complication to Your watchOS App](https://developer.apple.com/documentation/clockkit/adding_a_complication_to_your_watchos_app).

- Notifications alert users of significant events that they can respond to directly without opening your app. You can use either local or remote notifications to communicate with the user, even when your app isn’t running. For more information, see Enhancing Your watchOS App with Notifications.
> 通知提醒用户一些重要的事情,他们能够不打开app,直接响应. 即便你的app没有运行也可以利用本地通知或者远程通知来与用户进行交互.关于更多查看[Enhancing Your watchOS App with Notifications](https://developer.apple.com/documentation/watchkit/enhancing_your_watchos_app_with_notifications).


- Siri can provide an integral part of the Apple Watch experience. With SiriKit Intents, users can control your app with their voice. Similarly, your app can donate shortcuts to the system that appear on the Siri watch face. For more information, see Creating an Intents App Extension, and Defining Relevant Shortcuts for Your App.
>Siri可以为苹果手表体验提供完整性的一部分.通过SiriKit Intents,用户可以通过声音来控制app.类似的是,你的app可以在Siri表盘上添加快捷方式. 更多信息,参考[Creating an Intents App Extension](https://developer.apple.com/documentation/sirikit/creating_an_intents_app_extension), and [Defining Relevant Shortcuts for Your App](https://developer.apple.com/documentation/sirikit/relevant_shortcuts/defining_relevant_shortcuts_for_your_app).


- For watchOS, expect to spend more time planning, designing, and refining your app’s experience than writing the actual code. For guidance on designing for Apple Watch, see the Apple Watch Human Interface Guidelines. Then consider each of the above technologies as you create your experience for watchOS.
>对于watchOS来说,期待你花费更多的时间在策划,设计和改善你应用的用户体验上,而不是编码上.关于设计指引请参考[Apple Watch Human Interface Guidelines](https://developer.apple.com/design/human-interface-guidelines/watchos/overview/themes/).然后在打造你的watchOS体验的时候,请参考以上每一种技术.
