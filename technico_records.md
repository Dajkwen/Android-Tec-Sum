### Android 技术点总结【后台定时服务】

定时任务目前知道有两种实现形式，一种是用timer；一种是alarmManager；前者局限性较大，后者比较好用，因为可以结合service一起做到后台定时服务。

###### AlarmManager

可以设置单次定时也可以设置重复定时。

时间的设置也有两种，一种基于系统当前运行的时间；一种是1970年那个时间。

执行方式也有两种，一种是当cpu休眠时，不会执行，等到cpu唤醒之后才会继续；一种是即使cpu休眠了，也会唤醒cpu执行定时操作。

###### BroadcastReceiver

通过alarmManager设置的pendingIntent将定时结果广播出来。

最后在广播接收器中执行要处理的定时任务。

##### 注：

结束这个后台服务的时候要通过alarmManager取消掉这个定时，否则若是设置的重复定时，那是结束不掉的。



近日在查询定时任务相关资料的时候，补充一种定时方式：

Handler 加 Thread 实现。这种方式就是通过 handler 每隔一个时间发送一个消息来处理，加上 thread 的目的是，将耗时操作放在子线程中执行，防止主线程卡死。



### Android 技术点总结【Fragment 切换方式】

多个 fragment 使用同一个容器时就需要切换，谁在当前使用，谁就展示。但展示方式不同，加载的生命周期就有所区别。

先说说 replace 方式加载。

当前展示的 fragment 会先被替换掉，将要展示的 fragment 会重新实例化，相当于重新初始化加载。

再说说 add, hide, show, isAdded 的方式。

通过 add 加载的 fragment 只会添加到容器里，通过 isAdded 判断这个 fragment 是否是当前正在展示的， 想要替换，则通过 hide 和 show 来进行切换，这样保证了不会重新初始化加载。

两种方式可以根据具体业务进行使用。

### Android 技术点总结【fragment 加载方式】

上文讲到了 fragment 的切换方式，那都是通过动态方式切换的，对应到的场景是 fragment 的动态加载方式，使用同一个容器，实现多个 fragment 的使用。

另外一种加载方式是静态加载。对应到的场景是一个容器或者一个容器的某个区域只展示一种布局，这时可以考虑直接在布局文件中加载好 fragment。

### Android 技术点总结【权限管理】

对于 android sdk 23 之前的权限使用是在 manifest 文件里事先申请好就好了的，但 23 及以后的版本就是通过动态申请了。并且权限也被划分成了三类，一类中按组划分。

Normal Permissions

这些权限是不会涉及到用户隐私及个人安全的，所以这些权限在 manifest 文件里被事先授权，使用时可以直接使用。具体的权限有：

| 名称                                   | 说明                                       |
| ------------------------------------ | ---------------------------------------- |
| ACCESS_LOCATION_EXTRA_COMMANDS       | 获取定位的额外指令（不懂哪里用的到）                       |
| ACCESS_NETWORK_STATE                 | 获取网络信息                                   |
| ACCESS_NOTIFICATION_POLITY           | 希望获取通知政策                                 |
| ACCESS_WIFI_STATE                    | 获取无线网络的信息                                |
| BLUETOOTH                            | 允许应用连接已配对过的蓝牙设备                          |
| BLUETOOTH_ADMIN                      | 允许应用发现和连接找到的蓝牙设备                         |
| BROADCAST_STICKY                     | 允许应用广播粘性意图（这应该是广播一些系统意图）                 |
| CHANGE_NETWORK_STATE                 | 允许应用改变网络连接状况                             |
| CHANGE_WIFI_MULTICAST_STATE          | 允许应用进入无线多播模式                             |
| CHANGE_WIFI_STATE                    | 允许应用改变无线网络连接状况                           |
| DISABLE_KEYGUARD                     | 允许应用禁用锁屏                                 |
| EXPAND_STATUS_BAR                    | 允许应用展开或者合起状态栏                            |
| GET_PACKAGE_SIZE                     | 允许应用获取任意软件的使用空间（就是看看软件占多大）               |
| INSTALL_SHORTCUT                     | 允许应用安装快捷启动                               |
| INTERNET                             | 允许应用打开网络端口                               |
| KILL_BACKGROUND_PROCESSES            | 允许应用调用 killBackgroundProcesses( String ) 来杀死后台进程 |
| MODIFY_AUDIO_SETTINGS                | 允许应用修改全局的声音设置                            |
| NFC                                  | 允许应用通过 NFC 进行数据通信                        |
| READ_SYNC_SETTINGS                   | 允许应用访问同步设置                               |
| READ_SYNC_STATS                      | 允许应用获取同步状态                               |
| RECEIVE_BOOT_COMPLETED               | 允许应用接收到系统启动成功的广播消息                       |
| REORDER_TASKS                        | 允许应用重拍任务顺序                               |
| REQUEST_IGNORE_BATTERY_OPTIMIZATIONS | 跟电池相关，应该是允许忽略电池优化之类的                     |
| REQUEST_INSTALL_PACKAGES             | 允许应用安装，在 api 25 及以后必须要有这个权限              |
| SET_ALARM                            | 允许应用以广播的形式向用户发出警告                        |
| SET_TIME_ZONE                        | 允许应用设置时区                                 |
| SET_WALLPAPER                        | 允许应用设置墙纸                                 |
| SET_WALLPAPER_HINTS                  | 允许应用设置墙纸提示                               |
| TRANSMIT_IR                          | 允许应用使用设备的红外线发射器                          |
| UNINSTALL_SHORTCUT                   | 不再使用了                                    |
| USE_FINGERPRINT                      | 允许应用使用指纹设备                               |
| VIBRATE                              | 允许应用使用振动器                                |
| WAKE_LOCK                            | 允许应用唤醒                                   |
| WRITE_SYNC_SETTINGS                  | 允许应用设置同步参数                               |



Dangerous Permissions

这些权限是涉及到用户隐私及安全，读取用户数据及资源，根据 android 版本不同获取这些权限的方式有所区别。要是手机的系统是 6.0及以上，或者 app 的 targetSdkVersion 是 6.0 及以上，权限是动态访问的，需要的时候去申请。要是 6.0 以下，则在 app 安装的时候回去询问获取权限。

权限组。在申请权限的时候，若该权限所在组里的权限都没有被授权过，则会提示用户授权；若是有其他权限已经获取到了授权，那该权限组的其他权限都可以被授权，直接通过。

| 权限组        | 权限                     | 说明                                       |
| ---------- | ---------------------- | ---------------------------------------- |
| CALENDAR   | READ_CALENDAR          | 读取日历数据                                   |
|            | WRITE_CALENDAR         | 写入日历数据                                   |
| CAMERA     | CAMERA                 | 要求访问摄像头设备                                |
| CONTACTS   | READ_CONTACTS          | 读取用户联系人数据                                |
|            | WRITE_CONTACTS         | 写入用户联系人数据                                |
|            | GET_ACCOUNTS           | 获取账号信息（不知道哪里用）                           |
| LOCATION   | ACCESS_FINE_LOCATION   | 获取精准定位信息                                 |
|            | ACCESS_COARSE_LOCATION | 获取近似定位信息                                 |
| MICROPHONE | RECORD_AUDIO           | 录取声音                                     |
| PHONE      | READ_PHONE_STATE       | 仅仅获取手机状态，包括手机序列号，移动网络信息，正在打的电话，以及注册的手机账号 |
|            | CALL_PHONE             | 发起打电话操作                                  |
|            | READ_CALL_LOG          | 获取通话记录，要是 minSdkVersion 或者 targetSdkVersion 小于等于 15，并且获取到 READ_CONTACTS 权限，这个权限也就自动获取了 |
|            | WRITE_CALL_LOG         | 写入通话记录                                   |
|            | ADD_VOICEMAIL          | 添加语音邮件到系统中                               |
|            | USE_SIP                | 允许使用 SIP 服务                              |
|            | PROCESS_OUTGOING_CALLS | 跟电话相关，呼叫转移？                              |
| SENSORS    | BODY_SENSORS           | 获取传感器数据，包括心率等身体数据                        |
| SMS        | SEND_SMS               | 允许发送短消息                                  |
|            | RECEIVE_SMS            | 获取短消息                                    |
|            | READ_SMS               | 读取短消息                                    |
|            | RECEIVE_WAP_PUSH       | 接收 wap 推送消息                              |
|            | RECEIVE_MMS            | 获取彩信消息                                   |
| STORAGE    | READ_EXTERNAL_STORAGE  | 读取外部存储数据，要是已经获取到了 WRITE_EXTERNAL_STORAGE，该权限将自动获取，从 19 开始，要求需要申请获取 |
|            | WRITE_EXTERNAL_STORAGE | 写入外部存储数据。                                |



最后一种特殊权限，涉及到的应该不多，暂且不做分析。



权限检查。调用 ContextCompat.checkSelfPermission() 

解释需要权限的原因。调用 shouldShowRequestPermissionRationale()，判断是否需要解释，解释的对话框或者提示就需要自定义了。

请求权限。异步调用 requestPermissions()

处理权限请求响应。onRequestPermissionsResult() 该方法入参有三个。requestCode 表示自定义的请求码用以区分，permissions[] 是我们请求时的权限们，grantResults[] 是请求结果，要是用户取消了，其长度为 0 ，要是授权或者拒绝那取数组的第一位。

### Android 技术点总结【Gradle 配置】

gradle 配置的话有两点：

1. Setting 里的 gradle 这个地方是配置用 wrapper 还是用本地的 gradle 版本来构建项目，如果是 wrapper 那简单些，gradle 版本就在 wrapper 里的 gradle-wrapper 配置文件中；如果是本地，那么指向本地安装的 gradle 版本的安装路径就可以了。所以在 console 中如果用 gradle 带头的命令，那用的就是本地的gradle, 如果用 gradlew 就是用的 gradle wrapper 里的命令。
2. 在项目工程里的 build.gradle 文件中也有一个 gradle 版本，这个版本是 studio 的 gradle plugin 版本，这个版本是指插件版本。在 project structure 里面的 project 的 gradle version 就是 wrapper 里的版本，而 gradle plugin version 就是项目工程里的 build.gradle 的插件版本号。

### Android 技术点总结【Handler】

handler 是什么

官方解释是说用来发送和处理消息，以及通过 runnable 来接收回调的。一个线程有且仅有一个 messagequene，可以有多个 handler。handler 就是通过 messagequene 来协作处理消息的。当我们的应用运行起来之后会有一个主线程 「UI 线程」自己就有一个 messagequene ，我们可以在自己的线程里创建自己的 messagequeue。



handler 怎么用

首先是在一个线程中，我们可以通过发消息或者实例化 runnable 来等待回调两种方式进行 handler 的使用。

1. 发消息的方法有 sendMessage(Message)；sendEmptyMessage(int)；sendEmptyMessageDelayed(int) 等。这里同样有一个点要注意，有些方法是告诉消息队列立马去加入队列执行的，有些则是延后一段时间，或者在未来一个固定时间执行。通过发消息的方式可以在 message 里面夹带一些数据，进行数据通信。
2. 使用 runnable 的方法有 postDelayed(Runnable, long)；post(Runnable)；postAtTime(Runnable, long)等。和发送消息一样，这里也有区分是立马执行还是延后执行。

然后是主线程与子线程的交互。因为主线程不能做耗时的事，所以要放在子线程里，但是我们有时候又需要子线程做完耗时任务之后来更新主线程的 UI 显示，这时候 handler 就起到了子主线程通信的目的，所以这里就只好用 sendMessage 这种形式了。

最后就是子线程与子线程的交互。

线程默认是没有自己的消息队列的，那么怎么才能有呢？这就要涉及到一个叫 Looper 的东西了。Looper 是为了给线程维护一个消息循环而存在的，线程可以通过它来建立自己个 messagequeue，但要注意消息队列也只能有且只有一个，之后在线程里新建的 handler 会自动依附到这个线程，发送及处理的消息也都来自线程里的 messagequeue。要使线程变成一个 looper 就先调用 prepare()，调用 loop() 表示开始消息循环，最后停止了要调用quit()



handler 要避开的误区

1. handler 有个方法叫 postDelayed(Runnable, long)。以为在这个 runnable 里面可以做耗时操作，前几日看了一篇文章之后，发现要对 handler 有所梳理，才知道这个 runnable 是运行在了 handler 所在的线程中，而要是 handler 所在线程是主线程，那么这个 runnable 必然不能做耗时操作。其实要是 handler 是在主线程中，其他方法的 runnable 都不可以做耗时。
2. 通过 handler 要发送消息的时候，message 可以创建，但这样会造成资源的开销，每次都要 new 一个出来，而其实在 Message 类里面有一个消息池，大小是 50 个，而如果我们去池子里去拿，就避免了重复创建新 message 了，如果池子里没有可用的，那其实它才会去创建一个新的给我们，这里我们就用 Handler 的 obtainMessage() 就可以，类似的方法还有几个。

