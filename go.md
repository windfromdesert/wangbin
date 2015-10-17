### Go 并发编程实战

#### Go 参数设置

+   Linux 系统中使用 export 命令查看系统参数
+   Linux 系统中安装 Go 后使用命令 go env 查看 Go 参数
    -   一般安装好 Go 后系统会自动设置好 $GOROOT
    -   可以在文件： /etc/profile 中手动设置各项参数
    -   也可以在 .xprofile 中设置

            export GOROOT=/usr/local/go
            export PATH=$PATH:$GOROOT/bin
            export GOPATH=$HOME/golang/lib:$HOME/golang/goc2p

        +   其中 export 命令用于设置环境变量
        +   为使上述设置立即生效，需再执行如下命令： source /etc/profile
        +   其中 source 命令是 bash shell 的内置命令，用于在当前shell环境下执行脚本。
        +   工作区的概念：工作区其实就是一个对应于特定工程的目录，它应包含3个子目录：src、 pkg、bin 目录，我们需要将工作区的目录路径添加到环境变量GOPATH中。
            -   src 目录用于以代码包的形式组织并保存 Go 源码文件
            -   pkg 目录用于存放经由 go install 命令构建安装后的代码包（包含Go库源码文件）的“.a”归档文件。
            -   bin 目录保存在通过 go install 命令完成安装后，由 Go 命令源码文件生成的可执行文件。

### 基本词法

+   
