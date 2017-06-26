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

### Android 技术点总结【Gradle 配置】

gradle 配置的话有两点：

1. Setting 里的 gradle 这个地方是配置用 wrapper 还是用本地的 gradle 版本来构建项目，如果是 wrapper 那简单些，gradle 版本就在 wrapper 里的 gradle-wrapper 配置文件中；如果是本地，那么指向本地安装的 gradle 版本的安装路径就可以了。所以在 console 中如果用 gradle 带头的命令，那用的就是本地的gradle, 如果用 gradlew 就是用的 gradle wrapper 里的命令。
2. 在项目工程里的 build.gradle 文件中也有一个 gradle 版本，这个版本是 studio 的 gradle plugin 版本，这个版本是指插件版本。在 project structure 里面的 project 的 gradle version 就是 wrapper 里的版本，而 gradle plugin version 就是项目工程里的 build.gradle 的插件版本号。

关于插件版本，gradle 构建版本以及 module 的 buildtool 版本之间存在着一个关系，后面再研究一下。