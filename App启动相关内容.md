# -wwq
一些 书籍 资料 阅读随笔

###1. App 整个启动大概分为： 编译，汇编，链接（静态链接），代码签名，启动执行（动态链接），Main，appdelegate（启动方案），显示窗口相关控件。

###2. 大范围分成四个过程： 编译，链接，装载，呈现</br>
 a。编译： 编译系统读取文本字符串的含义，然后翻译成机器能看懂的汇编语言： 目标文件 </br>
b。链接： 每个类。每个文件会被编译成不同的目标文件，连接器将这枚哥文件串起来，相互调用，最后生成可执行文件。 </br>
c。装载： 把已经生成的可执行文件放到操作系统里，专属进行与内存控制下，找到机器可识别的汇编代码入口，开始按着汇编去执行机器码，并能与操作系
统级别的各种系统api 对接起来。</br> 
d。呈现： 整个编译，链接，装载完成之后，就会重新回到main函数，清空之前所有的现场，并开始根据代码逻辑进行展示我们的app。  </br>
</br>
###3. Main函数作为分水岭，分成main函数之前的事情。与之后的事情 

</br>
####4. 整个文件和资源文件的加载过程 a。编译信息写入辅助文件，创建文件架构 .app 文件 b。处理文件包信息 c。执行 cocoapod 编译前脚本， checkPods MainFest.lock d。编译 。m 文件，使用compilec 和 clang 命令 e。编译需要的framework f。编译xib g。拷贝xib，资源文件 h。编译 imageAssets I。处理 info。plist j。执行cocoapod 脚本 k。拷贝标准库
