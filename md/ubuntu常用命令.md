## ubuntu常用命令

#### enca 转换编码工具

傻瓜型命令行工具enca，它不但能智能的识别文件的编码，而且还支持成批转换编码。

安装方法：

    $sudo apt-get install enca

转换编码命令如下：

    $enca -L 当前语言 -x 目标编码 文件名

例如要把当前目录下的所有文件都转成utf-8

    $enca -L zh_CN -x utf-8 *

不过用的时候发现有一部分文件无法传转，具体原因不祥，不过enca会把无法转换的文件告诉你的。对于这些不能转的文件，可以用gedit另存为的方式转。

#### axel 下载工具

安装：

    $sudo apt-get install axel

使用方法：

    $axel -n 5 -o ~/下载 "url"
    //注意此下载地址url应该使用引号，避免因链接太长而发生错误。

上述命令表示下载使用线程数为5条，输出文件到下载目录。

#### 切换输入法

命令：

+ im-switch -c //使用目录来显示和设置输入法 
+ im-switch -s ibus //改变你的输入法为ibus 
+ im-switch -z zh_CN -s ibus //将ibus输入法设为zh_CN下的输入法 
+ im-switch -s ibus -z default　//设定ibus输入法为默认语言下的输入法。 
+ im-switch -s scim -a default 

#### 安装fcitx输入法

命令：sudo apt-get install fcitx fcitx-table-wbpy fcitx-config-gtk fcitx-frontend-all fcitx-module-cloudpinyin fcitx-ui-classic

#### 安装chromium浏览器

+ sudo add-apt-repository ppa:a-v-shkop/chromium
+ sudo apt-get update
+ sudo apt-get install chromium-browser

#### 查杀进程

+ 查进程：$ps -aux | grep [进程名]
+ 杀进程：$sudo kill -9 [PID] //[PID]即为上一步查进程时查到的进程号
