# objc4-841.13

官网地址 : https://opensource.apple.com/releases/
ENV :   Mac OS : 12.3  Xcode : 13.3

下载后的设置：
1. 以下两处均设置为 macOS
PROJECT -> objc -> BuildSettings -> BaseSDK
TARGETS -> objc -> BuildSettings -> BaseSDK

2.根目录创建sysInclude文件夹,存放所需库文件（从官网下载而来）,并配置到工程
    TARGETS -> objc -> BuildSettings -> Header Search Paths ->
新增 $(SRCROOT)/sysInclude 

3.
