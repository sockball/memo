[参考](https://docs.phpdoc.org/references/phpdoc/index.html)
* api 表示这是一个提供给第三方使用的API接口
* <font style='color:red'>author 函数作者名和邮箱地址</font>
* category 组织packages
* copyright 指明版权信息
* deprecate 指明不推荐或是废弃的信息
* <font style='color:red'>example 示例</font>
* exclude 指明当前注释不进行分析 不出现在文档中
* global 指明引用的全局变量
* include 指明包含的文件信息
* link 链接，可用于辅助说明、引用文档等
* <font style='color:red'>package 定义归属包信息, php用于存放命名空间</font>
* <font style='color:red'>param 定义参数信息</font>
* <font style='color:red'>property 定义的类属性</font>
* <font style='color:red'>property-read 只读的属性。例如__get魔术方法能够取到的属性</font>
* <font style='color:red'>property-write 只可写的属性。例如__set魔术方法能够设置的属性</font>
* <font style='color:red'>return 返回值</font>
* see 指明需要参考的内容, 并加入相应超级链接
* since 定义从哪个版本开始引入
* <font style='color:red'>throws 指明可能抛出的异常</font>
* todo 指明应该改进或没有实现的地方  
` // todo `
* var 定义说明变量/属性  
`/** @var $user \backend\models\User */ `
* version 定义版本信息