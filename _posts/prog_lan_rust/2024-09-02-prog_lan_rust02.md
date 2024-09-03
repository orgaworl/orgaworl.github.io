---

title: "Rust"
subtitle: "Rust"
layout: post
author: "orgaworl"
header-style: text
tags:
   - Rust
   - 编程语言
  - 静态语言
  - 面向对象
  - 物理机运行
  - code
  - rust
---




## 语法
### 01数据类型
#### 基本数据结构

- integer
  `isize,usize`
	`65535u16`

- float
  `f32,f64`
	默认`f64`
- bool
  值为 `true,false`

- char
	4Byte大小，表示Unicode标量值, 使用`'`进行标识


#### 复杂数据结构

##### 单元类型 `()`
一个特殊的类型，通常用于表示没有有意义的值或者不返回任何值的情况。它是 Rust 中最简单的类型之一，实际上只有一个可能的值——单元值 `()`

- **单元类型**是一个表示“空”或“没有值”的类型。
- 它的唯一值是 `()`，这是一个表示无返回值的占位符。
- 如果一个函数没有显式返回值，它默认返回单元类型 `()`
- **大小**：`()` 类型的大小是 0，因为它没有实际的数据。
- **不可变**：`()` 类型的值不能改变，因为它只有一个值，即 `()`。
- **与其他类型的关系**：单元类型通常不会与其他类型直接转换，但它是许多函数和操作的默认返回类型。


```rust
use std::mem::size_of_val;
fn main() {
    let unit: () = ();
    assert!(size_of_val(&unit) == 0);
    assert_eq!(unit, implicitly_ret_unit());
}
fn implicitly_ret_unit() {
    println!("I will return a ()")
}
```

##### 不可达类型 `!`
- **定义**：`!` 是一个特殊的类型，用于表示一个函数永远不会返回。这种类型的函数要么陷入无限循环，要么在某个点终止整个程序。
    
- **用途**：主要用于标识那些不会正常返回的函数，例如：
```rust
fn loop_forever() -> ! {
    loop {
        // 永远循环
    }
}
fn panic_now() -> ! {
    panic!("This function will not return");
}

```

**发散函数**( Diverging function )不会返回任何值，因此它们可以用于替代需要返回任何值的地方,其返回值是`!`.





##### 元组  `(T1, T2, ...)`
元组是用一对 ( ) 包括的一组数据，可以包含不同种类的数据：

初始化
```rust
let tup:(i32,i64,u8)=(500,6.4,1);
let (x,y,z)=tup;          //默认为不可变变量
let (mut x,mut y)=(1,1);
```

元组可以嵌套元组
```rust

```


访问: 
```rust
// 使用下标做方法
tup.0


// 使用模式匹配来解构元组
let tup = (1, 6.4, "hello");
let (x,z,y) = tup;

//解构式赋值
let (x, y, z);
(y,z,x) = (1, 2, 3);


```

做函数参数与返回值
```rust
fn main() {
    let (x, y) = sum_multiply( (2,3) );
}
fn sum_multiply(nums: (i32, i32)) -> (i32, i32) {
    (nums.0 + nums.1, nums.0 * nums.1)
}
```


对输出元组元素个数有限制, 最多输出拥有**12个元素**的元组


##### 数组 `[T; Length]` 
要求数组长度编译时已知, 分配在栈上.

- 初始化
	```rust
	let c: [_; 5] = [1, 2, 3, 4, 5]; //自动推断类型
	let c: [i32; 5] = [1, 2, 3, 4, 5];
	let d: [i32;repeatTime] = [val; repeatTime]; 
	```

- 通过下标访问, 越界时会触发`panic`
	```rust
	let val=d[0];
	```
- 通过`.get(nth)`访问
	```rust
	let name0 = names.get(0).unwrap();
	```

##### 字符串
- str 
	正常情况下我们无法使用 `str` 类型, 使用需要配合`Box<str>`
	```rust
	fn main() {
	    let s: Box<str> = "hello, world".into();
	    greetings(&s)
	}
	fn greetings(s: &str) {}
	```
	
	```rust
	fn main() {
	    let s: Box<&str> = "hello, world".into();
	    greetings(*s)
	}
	fn greetings(s: &str) {}
	```

- &str  16Byte poi+len
	是`String`的子集, 只读的字符串指针
	```rust
	let t:&str="hello";
	let h:String=String::from("hello");
	let d:&str=&h[..]
	let d:&str=&h[0..2]
	
	```

- String 24Byte
	字符串智能指针,管理额外数据
	```rust
	let mut string = String::new();
	let mut string = String::from("");
	let mut string = String::with_capacity(cap);
	let mut string = val.to_string();
	
	.push_str(str);
	.push(char);
	.len(); // 字节长度
	.chars().count(); //字符长度
	.as_bytes()       //所有权仍保留
	.into_bytes()     //所有权赋予给方法
	.replace(oriStr,newStr)
	
	```

	字符串拼接
	```rust
	fn main() {
	    let s1 = String::from("hello,");
	    let s2 = String::from("world!");
	    let s3 = s1.clone()  + &s2;
	    // 先创建复制String对象,再在复制对象后面追加内容
	    assert_eq!(s3,"hello,world!");
	    println!("{}",s1);
	}
	```


字符串索引: 

- 按字节进行索引
	```rust
	let s1 = String::from("hi,中国");
	let h = &s1[0..1]; // `h` 字符在 UTF-8 格式中只需要 1 个字节来表示
	assert_eq!(h, "h");

	let h1 = &s1[3..6];// `中` 字符在 UTF-8 格式中需要 3 个字节来表示
	assert_eq!(h1, "中");
	```
- 按字符进行索引
	```rust
	use utf8_slice;
	fn main() {
	    let s = "The 🚀 goes to the 🌑!";
	    let rocket = utf8_slice::slice(s, 4, 5);
	    // 结果是 "🚀"
	}
	```

```rust
for c in "你好，世界".chars(){
}
```

类型转换
- `String` => `&str`
```rust
var.as_str();

let s1: &str = &var;

```

- `String` <= `&str`
```rust
String::from(var:&str)
var.to_string()

```

- `&String` 可以被隐式地转换成 `&str` 类型.

##### 字符串转义

转义
- `\\`表示`\`
- `\"`表示`"`
- `\x41` 使用十六进制的值
- `\u{211D}` 使用 Unicode 形式的转义字符
- 使用`\` 来连接多行字符串

raw string
- `r" "` 
- `r#" "#` 使用双引号
- `r###"  "###` 使用 # 号

```rust
fn main() {
    let raw_str = r"Escapes don't work here: ? ℝ";
    assert_eq!(raw_str, "Escapes don't work here: ? ℝ");
    let quotes = r#"And then I said: "There is no escape!""#;
    let  delimiter = r###"A string with "# in it. And even "##!"###;
    
    let long_delimiter = r###"Hello, "##""###;
    assert_eq!(long_delimiter, "Hello, \"##\"")
}
```

##### 字节数组 `&[u8; len]`
可以将字符串按字节进行转义,保存到字节数组中
字节数组中内容可以不是UTF-8编码
所以将字节数组转成 `str` 类型可能会失败
```rust
    if let Ok(my_str) = str::from_utf8(raw_bytestring) {
        println!("And the same as text: '{}'", my_str);
    }
```






##### 向量Vec
```rust
let vector:Vec<type> = Vec::new();
let vector           = Vec![1,2,4,8];
.push(val);
.append(Vec); //向量拼接
.get(val);    //返回枚举类

```

##### HashMap
```rust
let mut map = HashMap::new();  
map.insert("color", "red");          //直接插入
map.insert("size", "10 m^2");
map.get("color").unwrap()
map.entry("color").or_insert("red"); //安全插入
```



##### 切片(引用) Slice `&[T]`
>切片的长度无法在编译期得知, 因此无法直接使用切片类型, 事实上使用的是切片引用, 而非类型本身.
>实质是一个胖指针, 一个切片引用占用了2个字大小的内存空间,包含指针和长度字.

切片( 引用 )可以用来借用数组的某个连续的部分，对应的签名是 `&[T]`，数组的签名 `[T; Length]`。

```rust
let slice=&vec[0..n]


```


`str` 和`String`都支持切片操作

### 02变量常量

- 常量
- 不可变变量
- 可变变量
>变量必须经过初始化才能使用

```rust
let a = 123;       //不可变变量
let mut a = 123;   //可变变量
const a: i32 = 123;//常量 
```

常量与不可变变量的区别
- 变量的值可以"重新绑定"，但在"重新绑定"以前不能私自被改变
- 常量的值永远不可改变
#### 遮断（Shadowing）
重影就是指变量的名称可以被重新使用的机制：

Shadowing与赋值的对比**
- Shadowing是指用同一个名字重新代表另一个变量实体，其类型、可变属性和值都可以变化。
- 可变变量赋值仅能发生值的变化

```rust
fn main() {
    let x: i32 = 5;
    {
        let x = 12;
        assert_eq!(x, 12);
    }
    assert_eq!(x, 5);

    let x = 42;
    println!("{}", x); // 输出 "42".
}
```

```rust
    let mut x: i32 = 1;
    x = 7;
    let mut x = x; // 遮蔽且再次绑定, 必须为mut类型
    x += 3;
```

#### 解构式赋值
可以在赋值语句的左式中使用元组、切片或结构体进行匹配赋值。
```rust
fn main() {
    let (x, y);
    (x,..) = (3, 4);
    [.., y] = [1, 2];
    assert_eq!([x,y], [3,2]);
} 
```



### 04运算

```rust
= //赋值
```

#### 浮点数计算损失

```rust
std::f32::EPSILON

```


#### 整数溢出

手动环绕运算
`let sum = x.wrapping_add(y);`

饱和运算: 溢出时返回最大值或最小值
`let sum = x.saturating_add(y);`

#### 显式类型转换

```rust
let a: u32 = 10;
let b = a as u64;
let c: u64 = a as _;  // 自动推断
```




### 05函数与语句块

```rust
//函数
fn <funcName> (varName:varType,)->resType{
    ......
    return res;
}

//语句块
//最后一个步骤是表达式，此表达式的结果值是整个表达式块所代表的值
let res={
         ;
    value  
};
```

特点
- 定义位置无要求
- 函数参数必须声明类型
-  不支持自动返回值类型判断，未指定返回值类型则不允许返回值

---

### 06流程控制

```rust
//条件表达式必须是 bool 类型,block可以是函数体表达式
if condition {block1}
else if condition {block2}
else {block3}

while condition {block}

for i in iterObj {block}

loop {block} //无限循环
```

```rust
break;
break value; // 可以结合循环和break, 实现返回值的效果
continue;

```

多层`loop`时,可为每层`loop`添加标签, 实现控制外层流的效果

```rust
let mut count = 0;
'outer: loop {
	'inner1: loop {
		if count >= 20 {
			break 'inner1;
		}
		count += 2;
	}

	count += 5;

	'inner2: loop {
		if count >= 30 {
			break 'outer;
		}
		continue 'outer;
	}
}

```



---

author email: orgaworl@outlook.com
