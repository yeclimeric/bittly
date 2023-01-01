# Bittly 数据格式化与解析

当请求内容比较复杂的时候，通过手动创建二进制数据就变得十分棘手，现在我们可以通过 Bittly 来改变这种情况。 

## 请求参数构建

例如，我们以Modbus协议中读取数据为例， 在文档中要求我们传递的参数如下：

![](/res/2022111921485701.png)

则我们我们可以使用表单来进行创建该结构：

![](/res/2022111921381401.png)

- `名称` ：用于标记该属性的名称
- `类型` ：指定该属性的数据类型，支持的数据结构如下：`字节(uint8)`，`单字节整型(int8)`，`字符(int8/char)`, `无符号字符(uint8)`, `短整型(uint16)`， `无符号短整型(int16)`, `整型(int32)`, `无符号整型(uint32)`，`长整型(int32)`, `无符号长整型(uint32)` , `长长整型(int64)`, `无符号长长整型(uint64)`, `单精度浮点型`, `双精度浮点型`, `字符串`, `字节数组`, `文件` 。
- `取值`：编辑属性取值。
  - 当属性为无符号数据时，可以指定取值格式为 `二进制(BIN)`, `八进制(OCT)`, `十进制(DEC)` 或 `十六进制(HEX)`， 并且在数据发送时自动进行转换操作。 
  - 可以使用占位符 `{{env.name}}` 来使用环境变量中的数据，例如有环境变量 `测试=YES`, 则在数据发送时 `{{env.测试}}` 会被替换为 `YES`。
  - 可以使用占位符 `{{name}}` 来使用脚本中的变量，例如在脚本中执行 `$this.variableSet("变量","HELLO");` 则 `{{变量}}` 在发送时会被替换为 `HELLO`
  - 可以使用状态占位符 `{{status.name}}` 来使用指令状态中的值， 例如存在状态 `COUNT=1`, 则 `{{status.COUNT}}` 在发送时会被替换为 `1`
  - 可以使用`{{@func()}}` 来执行快速调用的函数，例如上图中 `{{@crc16modbus($1,$2,$3,$4)}}` 则会在发送时，调用`crc16modbus` 函数，并将前四个属性作为参数执行该函数，最后讲函数结果作为数据发送出去。
- `描述` ： 用于备注并说明属性。



## 数据响应解析

当 Bittly 收到响应内容后，我们可以配置响应数据的格式化方式，如下：

![](/res/2022111922092101.png)

这样配置完成后，以后再次执行将会自动进行解析操作。

其中：

- `名称` :  用于说明响应属性的名称
- `类型` :  指定响应属性的数据类型， 支持的数据类型如下：`字节(uint8)`，`单字节整型(int8)`，`字符(int8/char)`, `无符号字符(uint8)`, `短整型(uint16)`， `无符号短整型(int16)`, `整型(int32)`, `无符号整型(uint32)`，`长整型(int32)`, `无符号长整型(uint32)` , `长长整型(int64)`, `无符号长长整型(uint64)`, `单精度浮点型`, `双精度浮点型`, `字符串`, `字节数组`； 对于不定长数据，例如字符串和字节数组，则需要指定数据长度。
- `取值` : 用于显示解析后的数据结果，当数据类型为无符号时，可指定其数值进制类型。
- `描述` : 用于备注属性其他信息。