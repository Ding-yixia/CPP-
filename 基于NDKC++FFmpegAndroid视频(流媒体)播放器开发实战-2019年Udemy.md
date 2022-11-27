基于NDK/C++/FFmpeg/Android视频(流媒体)播放器开发实战-2019年Udemy



链接: https://pan.baidu.com/s/1f1jbRZ-6Z7zgh9h2YQsQBg?pwd=xjea 提取码: xjea 复制这段内容后打开百度网盘手机App，操作更方便哦

  课时1课程介绍18:065 s) w Y8 q- g. q* M% Z
     课时2音视频基础知识Mpeg4封装格式音视频编码格式讲解10:15( H5 m8 |0 M" W) ]9 |1 P$ H* z
     课时3音视频解封装和解码原理分析18:013 M8 e% K3 c0 L4 K( J5 Q" U% }: \0 O
     课时4图形像素格式RGB和YUV格式分析15:36
     课时5PCM音频采样率_通道_planar等格式参数分析08:37
     课时6MP4标准和h264格式的NAL的GOP分析15:53' Y0 K D5 T" e
     章节2:AndroidStudio开发工具和开发环境准备1 X* s; G0 U6 ]. Y/ D4 L7 T
     课时7开发所用到的工具和sdk介绍07:29& ~7 T8 S, E w% H; o; U* h% {- c
     课时8AndroidStudio开发工具安装使用讲解09:22* m0 o, ]. C" D8 }
     课时9Android SDK目录内容分析04:59
     课时10AndroidNDK工具目录功能分析05:43
     课时11安卓模拟器安装和第一个c++安卓程序编译运行08:414 F( T. V6 R; b( o% r7 x
     章节3:Android NDK交叉编译FFMpeg" }, G: ^, |2 X7 e& K' m6 t
     课时12安卓NDK相关概念ABI、NEON、JNI和CMAKE分析12:32
     课时13NDK中CPU的应用二进制界面ABI09:004 L+ ?; B; u, r( z- C# k% F! {. G
     课时14ubuntu虚拟机及常用工具安装并配置samba共享目录19:109 C% \. l9 x5 e& g" g V0 c
     课时15ffmpeg源码下载在android下的编译参数分析讲解12:46! r4 T* O# @; }3 l5 v7 o! D
     课时16在ubuntu下交叉编译ffmpeg的android版本库20:28 V" M0 o/ K5 t0 S& u3 \8 R
     课时17ffmpeg通用编译bash脚本编写、硬解码、neon、多线程解码性能对比24:19
     课时18AndroidStudio项目配置权限、jni库路径、ABI和CMake参数和讲解07:56
     课时19cmake配置（导入库、头文件、链接）分析10:446 @* z& {( C6 C: w& j- F
     课时20创建支持ffmpeg的AndroidStudio项目并调用接口在界面显示库配置24:19) f n7 X d9 o6 v+ d9 ?
     课时21JNI和c传递文件路径并设置app的读写权限17:51
     章节4:FFmpeg解封装(C++ NDK)
     课时22ffmpeg SDK软硬解码流程说明05:084 G. n4 K! o3 R; O t
     课时23ffmpeg音视频解封装用到的函数和结构体详解10:11
     课时24avformat_open_input函数详解并示例打开mp4文件17:54
     课时25AVFormatContext结构分析和清理代码示例08:23
     课时26avformat_find_stream_info探测获取封装上下文的格式信息09:04- M+ P. x6 y9 E6 d+ R& h O
     课时27AVStream和AVCodecParameters分析13:16
     课时28通过遍历获取AVStream音视频流信息并打印参数16:08
     课时29通过av_find_best_stream获取音视频流索引04:23
     课时30av_read_frame读取帧数据函数分析和产生的空间问题分析07:065 S0 D# m% e, }
     课时31AVPacket结构解析空间申请-初始化-复制-引用计数函数讲解10:27
     课时32av_seek_frame改变播放进度函数详解08:37& _! N! o- r2 Y. L& c% ]
     课时33av_read_frame和av_seek_frame代码示例并分析内存占用和清理情况10:03
     章节5:FFmpeg软硬解和多线程解码(C++ NDK)
     课时34AVCodecContext解码上下文初始化讲解和视频解码器打开的代码演示19:32
     课时35打开音频解码器上下文05:14+ R9 v! [ }+ s& t! O" q2 c
     课时36AVFrame格式解析和空间处理函数09:37
     课时37avcodec_send_packet和avcodec_receive_frame函数讲解.07:22
     课时38音视频多线程解码代码演示09:06; q9 ~1 Y5 l/ i
     课时39音视频解码性能测试neon单线程和八线程解码测试代码13:56
     课时40ffmpeg调用MediaCodec实现硬解码代码演示04:55! D0 y% _5 ?+ b
     章节6:FFMpeg音频重采样和视频格式转换和显示
     课时41sws_getContext和sws_scale像素格式和尺寸转换函数讲解12:43
     课时42sws_scale像素格式转换代码演示15:45
     课时43音频重采样SwrContext格式和swr_convert相关函数详解05:17
     课时44swr_init音频重采样上下文初始化和swr_convert音频重采样代码17:08
     课时45分析GLSurfaceView和NDK接口直接显示RGB视频数据相关函数09:34
     课时46使用NDK通过GLSurfaceView双缓冲空间复制完成视频的播放22:316 H3 Q7 O% y$ [+ R' `" P: A
     课时47安卓视频播放窗口去掉标题栏、全屏、横屏06:42
     章节7:OpenSLES音频播放5 }/ H% I1 Q7 P; u. z6 M( K. H
     课时48OpenSLES播放音频步骤和接口讲解19:347 M+ A4 c; O! u2 _
     课时49创建OpenSLES音频播放测试项目并完成引擎初始化12:164 |8 }- E C8 N
     课时50完成OpenSLES混音器CreateOutputMix初始化06:118 M/ T* P; H! R1 O9 y2 m
     课时51配置OpenSL的PCM音频输入格式04:39& o/ N% O- ?8 m/ k6 ?+ u. p% j
     课时52读取pcm音频文件并使用OpenSL播放23:41
     章节8:EGL和OpenGLES Shader显示YUV视频4 x' `; ? Z- Q( z4 m" w1 W
     课时53egl和opengl_shader_glsl讲解13:55- s z8 [. Q* F/ U8 m
     课时54testopengles_shader项目配置和创建_完成java部分代码11:47
     课时55使用NDKC++完成EGL、display、surface、contex配置和初始化20:21
     课时56Opengl ES Shader顶点坐标和材质坐标分析10:52
     课时57顶点着色器代码glsl代码编写07:01
     课时58yuv420p片元着色器glsl编写14:220 h9 Z7 _3 U* g: g! H0 J
     课时59shader初始化完成并编译顶点和片元着色器代码11:40/ A9 A2 q7 D2 U5 A ^- n
     课时60program渲染程序初始化给shader传递顶点和材质顶点数据16:05. H+ j4 r6 \7 ?0 S0 D! B/ p
     课时61opengl的yuv纹理的创建和初始化18:06
     课时62OpenglES纹理数据修改和显示09:16
     课时63通过OpenglES纹理修改完成yuv文件的播放显示05:22
     章节9:视频播放器项目实战1-需求和设计模式! _ Z6 b" ]4 I+ a' n5 ]
     课时64视频播放器项目设计说明和UML类图知识讲解13:28
     课时65模块分解和用到的7个设计模式讲解13:31
     课时66适配器、建造者、代理模式讲解14:160 ], l; o- e4 J
     课时67门面模式&生产者消费者模式07:11) E7 v; j' X0 K5 Q+ A( T9 o% F
     课时68Observer观察者模式讲解和应用05:18. _3 T3 i1 j* e9 O
     课时69解码显示帧数据处理顺序图和开发计划讲解07:096 p( r$ q( b/ A, o3 v% s
     章节10:视频播放器项目实战二 音视频解码解封装. S6 `* r4 f- k( r
     课时70项目创建权限ABI和CMake相关库和头文件配置09:17: i) P& s$ f3 t- j H( m: Y" `
     课时71创建安卓流媒体播放器XPlay项目并配置添加了解封装模块及日志类14:29
     课时72FFDemux的Open实现打开媒体文件16:267 r8 V ]4 k! t
     课时73FFdemux的Read读取帧数据接口编写11:21. b$ j) x2 f( g
     课时74完成XThread线程类IDemux继承后在线程中读取帧数据13:57/ y+ S' g" C/ d
     课时75添加了XSleep完成XThread线程函数的停止和超时判断09:53, w8 W0 ?8 g8 Y& ^) s4 G4 o# ]
     课时76观察者Observer模式的代码实现并使用IDemux进行测试14:11
     课时77IDecode和FFDecode模块代码创建09:58
     课时78FFDecode的Open打开解码器接口编写和Observer基类添加10:07
     课时79添加IDemux的GetVPara获取视频XParamter参数接口用IDecode的Open05:55 s( {# n) R+ `5 `
     课时80封装FFDecode解码的Send和Recv接口14:02( ^6 W: W) ?# {% l* I# @+ u" J
     课时81读取媒体帧数据获取类型信息（音频、视频）存入XData08:13
     课时82完成音视频解码和并重构解码器加入观察者模式12:13
     课时83加入生产者消费者模式控制音视频缓冲19:14
     章节11:视频播放器项目实战三 音视频播放
     课时84IVideoView显示模块架构讲解和代码创建16:27
     课时85IEGL类初始化封装java部分添加XPlay窗口类19:28
     课时86XShader基于yuv420p的初始化Init代码完成并测试13:46 y7 G* t. W% m% d# G8 p9 s
     课时87XTexture初始化并完成GLVideoView的调用14:06! t, C! L0 ?1 @ f
     课时88GLVideoView完成使用shader显示视频27:49
     课时89完成音频重采样IResample模块初始化代码09:40
     课时90完成音频重采样IResample模块的处理功能22:23# W3 v% a; m& L/ S; E
     课时91完成了IAudioPlay和SLAudioPlay的音频播放环境初始化32:09
     课时92IAudioPlay的Update将音频压入缓冲队列并测试09:44
     课时93SLAudioPlay音频播放完成并测试对MP4文件音视频播放09:531 P. t: M, c U/ L$ O) H. e
     课时94完成硬解码并完成NV21和NV12格式的shader显示编写35:327 {+ K# S+ [$ L: s, c
     课时95解决android8.0下opengles不能播放的问题和音频播放的回音问题04:32
     章节12:频播放器项目实战四 完成架构解决同步和seek8 A& x& [* V2 O* N
     课时96完成facade模式的IPlayer并实现Open接口.26:26
     课时97完成Iplayer开始播放和窗口初始化接口08:30( P1 k4 T6 }/ p7 f
     课时98完成Builder模式的IPlayerBuilder构建IPlayer对象26:38
     课时99完成IPlayerProxy代理模式并进行测试架构搭建完成12:19
     课时100统一换算pts为毫秒使用IPlayer完成音视频同步播放25:41
     课时101IDemux&FFdemux线程安全和Close清理函数编写07:00
     课时102FFdecode和FFResample线程安全处理和Close清理函数编写10:44, G+ M0 H1 A- y3 c1 S- w6 a3 b( N
     课时103SLAudioPlay的线程安全处理和Close清理函数编写11:25
     课时104XEGL的线程安全处理和Close清理函数编写06:47
     课时105XShader线程安全处理和Close清理函数编写08:016 k) A9 I9 @ w+ ~" Y8 C
     课时106XTexture和GLVideoView的线程安全和清理函数编写07:571 C) G7 Z- `- T2 V# ]9 _# r+ f* R: h
     课时107IPlayer完成Close清理函数编写并完成多次打开视频文件的测试17:03* N0 A. z$ q3 J0 S c* k
     课时108添加打开视频按钮和进度滑动条并响应打开事件08:32$ w! V; O: }% u( D% e* J0 M7 |
     课时109添加视频打开路径和香港卫视rtmp打开路径的界面05:29
     课时110完成通过界面多次打开视频文件，修正了多次打开显示出现的bug12:22, N- M2 h8 X' e5 ]: b" c5 R
     课时111在835cpu的手机中测试并修正了音频播放Close的bug09:48 @/ N8 y2 g+ ?: B6 e, {$ @5 G
     课时112完成了播放进度的显示并修正了硬解码参数传递错误16:43
     课时113滑动条拖动Seek视频开发策略详细分析07:08
     课时114完成了IDemux的Seek和界面SeekBar的事件响应14:32; r* F$ z( E1 K# n& s& a! e
     课时115添加了视频播放的暂停和恢复16:291 _: l! R9 M! O6 w
     课时116修改了视频播放到结尾再次打开会锁死的bug08:30
     课时117通过手动解码帧数据完成了IPlayer控制视频播放进度的功能15:31