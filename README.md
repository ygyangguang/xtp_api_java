# 中泰证券量化交易平台XTP JAVA API接口
    本项目是中泰证券XTP极速交易JAVA接口的开源实现，供客户在量化交易中使用JAVA接口快速接入XTP系统。中泰证券XTP是为股票交易而生的极速交易系统，为投资者提供极速交易、极速行情、Level2行情。

    目前支持xtp版本为1.1.18.19，支持win、linux、mac平台运行
    
    请先到中泰证券xtp官方网站申请测试账号 https://xtp.zts.com.cn/register 及测试环境的连接ip、端口等信息
                                                                                        
    API参考官方C++版本的接口文档https://xtp.zts.com.cn/home
    
    如果您的linux是glibc2.12版本，请先手工将cpp/lib/linux_glibc2.12下的两个dll覆盖到cpp/lib/linux下，默认cpp/lib/linux下是同cpp/lib/linux_glibc2.14一样的glibc2.14版本编译的
   
#Version history
    v1.1.18.19-1.0.1 优化逐笔行情推送性能，废除原逐笔行情推送java接口onTickByTick，改为逐笔委托onTickByTickEntrust和逐笔成交行情推送onTickByTickTrade两个java api实现，对应的quoteplugin动态库也进行了修改。
    v1.1.18.19-1.0.0 支持xtp 1.1.18.19

##如何使用：
* **方式一：直接使用成品**
    * 1）在项目根目录下手工拷贝动态库到系统目录 
    
        > linux：
        
            cp cpp/linux/* /usr/local/lib/
            cp cpp/buildcpp/linux/libtradeplugin.so /usr/local/lib/
            cp cpp/buildcpp/linux/libquoteplugin.so /usr/local/lib/
            
        > mac：
        
            cp cpp/mac/* /usr/local/lib/
            cp cpp/buildcpp/macosx/libtradeplugin.dylib /usr/local/lib/
            cp cpp/buildcpp/macosx/libquoteplugin.dylib /usr/local/lib/
            
        > win32：
        
            copy cpp\lib\win32\dll\*  c:\windows\system32\
            copy cpp\buildcpp\win32\dll\tradeplugin.dll  c:\windows\system32\
            copy cpp\buildcpp\win32\dll\quoteplugin.dll  c:\windows\system32\
            
         > win64：
                 
            copy cpp\lib\win64\dll\*  c:\windows\system32\
            copy cpp\buildcpp\win64\dll\tradeplugin.dll  c:\windows\system32\
            copy cpp\buildcpp\win64\dll\quoteplugin.dll  c:\windows\system32\
                     
    * 2）确保有JRE8及以上被安装在目标主机，如果是windows还需要安装Visual C++ Redistributable for Visual Studio 2015，下载地址：                 
         https://www.microsoft.com/zh-CN/download/details.aspx?id=48145
         
    * 3）在量化交易java代码中引入build/libs/xtpapi-1.1.18.19.jar并使用
    
         > 如需进行单元测试：
                           
            src/test/java/com.zts.xtp/trade/TradeApiTest.java是交易的单元测试：
            修改TradeApiTest.java：
            tradeApi.init((short)18, "23a71733bba3sd78722319b212e",
                       "/var/log/zts/xtp", XtpLogLevel.XTP_LOG_LEVEL_INFO, JniLogLevel.JNI_LOG_LEVEL_INFO);
            sessionId = tradeApi.login("xx.xx.xx.xx", 1234,
                       "15001030", "xxxxxx", TransferProtocol.XTP_PROTOCOL_TCP);
            init方法的第1个参数是xtp client id（不超过255），实盘环境配置请联系官方人员获取，
                     第2个参数为测试环境的serverkey，实盘环境请联系官方人员获取，
                     第3个参数为java api产生的日志路径，
                     第4个参数为java api交易日志级别，
            login方法的参数分别是测试xtp交易ip、xtp交易端口、资金账号、密码、传输方式(测试只支持TCP，实盘可配置TCP或UDP），
                     请分别填入测试环境的参数及申请到的测试账号口令，实盘环境配置请联系官方人员获取。
                                          
            src/test/java/com.zts.xtp/quote/QuoteApiTest.java是行情的单元测试：
            修改QuoteApiTest.java：
            quoteApi.init((short)18,"/var/log/zts/xtp",XtpLogLevel.XTP_LOG_LEVEL_INFO, JniLogLevel.JNI_LOG_LEVEL_INFO);
            int login_result = quoteApi.login("xx.xx.xx.xx",1234,"xxxxx","xxxxxx",1);
            login方法第1个参数为xtp行情ip、第2个参数为xtp行情端口、第3个参数为申请到的测试账号、第4个参数为账号密码，
                    实盘环境的环境信息及资金账号请联系官方人员获取。
                    
            上述参数也可以通过根目录下user.config.properites中配置。公网测试环境请使用TCP连接，UPD会收不到数据。
                    
            执行./gradlew build -x test进行重新编译java生成build/libs/xtpapi-1.1.18.19.jar
            分别执行TradeApiTest、QuoteApiTest中的junit单元测试。
                  
         > 如需进行demo测试：
                                     
            使用java ide导入xtp_api_demo工程，修改xtp_api_demo/src/main/java/com/zts/xtp/demo/Application.java
            修改如下参数（参考上面如需进行单元测试的说明）：
                    private static final String XTP_TRADE_SERVER_IP = "xx.xx.xx.xx";//xtp交易server的ip
                    private static final int XTP_TRADE_SERVER_PORT = 1234;//xtp交易server的端口
                    private static final String TRADE_KEY = "23a71733bba3sd78722319b212e";//xtp交易serverkey
                    private static final short CLIENT_ID = 18;//xtp允许的clientid
                    private static final String XTP_QUOTE_SERVER_IP = "xx.xx.xx.xx";//xtp行情server的ip
                    private static final int XTP_QUOTE_SERVER_PORT = 1234;//xtp行情server的端口
                    private static final String ACCOUNT = "xxxxxx";//xtp资金账号
                    private static final String PASSWORD = "xxxxxx";//xtp密码
                    private static final String DATA_FOLDER = "/var/log/zts/xtp";//java api输出日志的本地目录
            运行Application.java即可，上述参数也可以通过根目录下user.config.properites中配置。公网测试环境请使用TCP连接，UPD会收不到数据。

 
<br/>

* **方式二：源码编译后使用（用于当方式一无法正常使用或需要修改源码时）**
    * 1）安装C++编译工具
    
         > linux：yum install -y gcc 或apt-get install -y gcc
         
         > mac: xcode-select --install 或 安装brew并用brew install gcc@7
         
         > win：安装visual stuido 2015
         
    * 2）安装cmake
    
         > linux：yum install -y cmake 或apt-get install -y cmake
         
         > mac:  http://www.cmake.org/download/下载cmake-3.13.2.tar.gz 解压tar-zxvf cmake-3.13.2.tar.gz，进入解压目录后，sudo ./bootstrap && sudo make && sudo make install
         
         > win：下载安装https://github.com/Kitware/CMake/releases/download/v3.13.2/cmake-3.13.2-win32-x86.msi、安装visual studio(2005及以上)
         
    * 3）安装jdk8
    
        > 根据系统安装对应jdk https://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
   
    * 4）编译jni部分2个plugin动态库，在工程根目录下的mkdir cpp/build
                 
        > 如果是linux或mac：
        
         进入到cpp/build目录执行cmake .. 则继续执行 make&&make install 进行编译和安装 
         则在/usr/local/lib下会新增5个动态库：
         
            linux：libglog.so、libxtptraderapi.so、libxtpquoteapi.so、libtradeplugin.so、libquoteplugin.so
            
            mac：libglog.dylib、libxtptraderapi.dylib、libxtpquoteapi.dylib、libtradeplugin.dylib、libquoteplugin.dylib
                  
        > 如果是Win：
        
         打开CMake GUI，where is the source code选项目下cpp目录，where to build libraries选cpp/build目录, 点configure在弹出的对话框中，
         specify the generator for this project选择本机Visual stuido对应的版本（请务必先手工打开visual studio，防止license过期等导致不可用进而执行cmake失败），
         注意如果要编译64位，一定要选择有Win64字样的选项，如Visual Studio 14 2015 Win64,
         点击finish，点击configure右侧的generate，回到cpp/build目录下回生成cpp.sln文件即为visual stuidio的工程文件。
         先用管理员权限打开visual studio，然后打开cpp.sln，打开后在cpp解决方案点右键点生成解决方案，
         若编译都通过，然后选择名为install的工程右键执行生成（若报error MSB3073: 命令“setlocal错误，说明visual studio没有用管理员权限打开，无法写入系统目录，重新用管理员权限打开再执行生成即可），
         若扔不能通过install工程生成正确，执行则需要手工将编译生成2个plugin dll从cpp\build\Release拷贝到c:\windows\system32\，将cpp/lib/win32或win64/dll/下的3个dll也拷贝到c:\windows\system32\  
         注：cpp/buildWin32和buildWin64是按照Visual studio2015生成的工程目录，若不能正常使用，请按上述方式在build目录自行生成工程文件。
         注意visual studio中工具条Debug和Release版本的切换，当前选中哪个，执行生成解决方案就编译的哪个版本，生产环境建议采用Release版本以获取更高的性能。

         则在/usr/local/lib或c:\windows\system32\下会新增5个动态库：
         
             win：glog.dll、xtptraderapi.dll、xtpquoteapi.dll、tradeplugin.dll、quoteplugin.dll
    
    * 5）安装并配置gradle
    
    * 6）在工程根目录下执行./gradlew build -x test 执行成功后在项目根目录生成build/libs/xtpapi-1.1.18.19.jar
    
    * 7）在量化交易java代码中引入build/libs/xtpapi-1.1.18.19.jar并使用（注意如果在IDE中打开java代码及java测试用例，需要
         在IDE中安装lombok插件才能看源码不报错，如果不看源码与测试用例，无需安装）
           
         > 如需进行单元测试：
                   
          src/test/java/com.zts.xtp/trade/TradeApiTest.java是交易的单元测试：
          修改TradeApiTest.java：
          tradeApi.init((short)18, "23a71733bba3sd78722319b212e",
                                  "/var/log/zts/xtp", XtpLogLevel.XTP_LOG_LEVEL_INFO, JniLogLevel.JNI_LOG_LEVEL_INFO);
          sessionId = tradeApi.login("xx.xx.xx.xx", 1234,
                                            "xxxxxx", "xxxxxx", TransferProtocol.XTP_PROTOCOL_TCP);
          init方法的第1个参数是xtp client id（不超过255），实盘环境配置请联系官方人员获取，
                   第2个参数为测试环境的serverkey，实盘环境请联系官方人员获取，
                   第3个参数为java api产生的日志路径，
                   第4个参数为java api交易日志级别，
          login方法的参数分别是测试xtp交易ip、xtp交易端口、资金账号、密码、传输方式(测试只支持TCP，实盘可配置TCP或UDP），请分别
                   填入测试环境的参数及申请到的测试账号口令，实盘环境配置请联系官方人员获取。
                                  
          src/test/java/com.zts.xtp/quote/QuoteApiTest.java是行情的单元测试：
          修改QuoteApiTest.java：
          quoteApi.init((short)18,"/var/log/zts/xtp",XtpLogLevel.XTP_LOG_LEVEL_INFO, JniLogLevel.JNI_LOG_LEVEL_INFO);
          int login_result = quoteApi.login("23a71733bba3sd78722319b212e",1234,"xxxxxx","xxxxxx",1);
          login方法第1个参数为xtp行情ip、第2个参数为xtp行情端口、第3个参数为申请到的测试账号、第4个参数为账号密码，
                   实盘环境的环境信息及资金账号请联系官方人员获取。
                   
          上述参数也可以通过根目录下user.config.properites中配置。公网测试环境请使用TCP连接，UPD会收不到数据。
                   
          执行./gradlew build -x test进行重新编译java生成build/libs/xtpapi-1.1.18.19.jar
          分别执行TradeApiTest、QuoteApiTest中的junit单元测试。
          
         > 如需进行demo测试：
                             
          使用java ide导入xtp_api_demo工程，修改xtp_api_demo/src/main/java/com/zts/xtp/demo/Application.java
          修改如下参数（参考上面如需进行单元测试的说明）：
                   private static final String XTP_TRADE_SERVER_IP = "xx.xx.xx.xx";//xtp交易server的ip
                   private static final int XTP_TRADE_SERVER_PORT = 1234;//xtp交易server的端口
                   private static final String TRADE_KEY = "23a71733bba3sd78722319b212e";//xtp交易serverkey
                   private static final short CLIENT_ID = 18;//xtp允许的clientid
                   private static final String XTP_QUOTE_SERVER_IP = "xx.xx.xx.xx";//xtp行情server的ip
                   private static final int XTP_QUOTE_SERVER_PORT = 1234;//xtp行情server的端口
                   private static final String ACCOUNT = "xxxxxx";//xtp资金账号
                   private static final String PASSWORD = "xxxxxx";//xtp密码
                   private static final String DATA_FOLDER = "/var/log/zts/xtp";//java api输出日志的本地目录
          运行Application.java即可，上述参数也可以通过根目录下user.config.properites中配置。公网测试环境请使用TCP连接，UPD会收不到数据。
          
        
 #License 
    Licensed under the MIT License.  