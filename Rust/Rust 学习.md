#Rust 

[Rust编程语言入门教程（Rust语言/Rust权威指南配套）【已完结】_哔哩哔哩_bilibili](https://www.bilibili.com/video/BV1hp4y1k7SV?p=17)


# 安装

`brew install rust` 

- 更新 Rust：`rustup update`
- 卸载 Rust: `rustup self uninstall`
- 版本验证：`rustc --version`
- 浏览器打开本地文档：`rustup doc`
- 库查找网站：[crates.io: Rust Package Registry](https://crates.io/)

# Hello world

- rs 作为后缀
- println! 是一个宏
- main 函数是每个 rust可执行程序最先运行的代码
- Rust 的缩进是 4 个空格而不是 tab
- 代码以分号结尾

```rust
fn main() {

println!("Hello World");

}
```


# 编译与运行

- 运行 Rust 程序之前需要先编译： `rustc main.rs`
- 编译成功后悔生成一个二进制文件
	- 在 Windows 上还会生成一个 `.pdb` 文件，里面包含调试信息
- Rust 是预先编译语言 ahead-of-time 可以先编译程序，然后将可执行文件交给别人运行（无需安装 Rust）
- ==rustc 只适合简单的 Rust 程序==

```ad-question
预先编译语言，生成的可执行文件是适应那种机器/操作系统的可执行文件？
不同操作系统的可执行文件应该是不一样的？
```

# Cargo

rustc 只适合简单的小项目，大型的系统项目需要使用 Cargo

```ad-question
为什么构建复杂项目的时候要使用 Cargo？
```

Cargo 是 Rust 的构建系统和包管理工具，可以构建代码、下载依赖的库、构建这些库

安装 Rust 的时候会自动安装 Cargo： `cargo --version`

## 创建项目

命令： `cargo new hello_world_new`
会创建一个名为 hello_world_new 的项目（目录）

会自动创建以下目录结构
![](assets/Pasted%20image%2020220504000652.png)

target 目录以及 Cargo.lock 文件是因为 VSCode 安装了 Rust 插件自动生成的，是编译结果，一般默认不会生成。

### Cargo.toml 文件

- TOML （Tom's Obvious, Minimal Language ） 格式，是 Cargo 的配置格式
- `[package]` 表示一个区域的标题，下方的内容是用来配置 package 的
	- edition：使用的 Rust 版本
- `[dependencied]` 另一个区域的开始，它会列出项目的依赖项
- 在 Rust 中，代码的包称作`crate`


```toml
[package]

name = "hello_world_new"

version = "0.1.0"

edition = "2021"

  

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

  

[dependencies]
```

## 构建 Cargo 项目

- cargo build
	- 会创建可执行文件，创建目录 `target/debug/hello_cargo`
	- 第一次运行 `cargo build` 会在顶层目录生成 `cargo.lock` 文件
		- 此文件负责追踪项目依赖的精确版本
		- 不需要手动修改此文件
- cargo run
	- 创建并运行可执行文件
	- 如果之前编译成功过，并且源代码没有修改，会直接执行可执行文件
- cargo check
	- 检查代码，确保通过编译，但是不产生任何可执行文件
	- 要比 cargo build 快的多
		- 编写代码的时候可以连续反复使用此命令检查代码，提高效率
- cargo build --release 
	- 编译时会进行优化，代码会运行得更快，但是编译时间更长
	- 会在 target/release 目录而不是 target/debug 生成可执行文件


## 安装依赖

很怪异的依赖安装方式。

Rust 是通过 cargo 进行包管理的，有一个 toml 文件记录了所有的 Rust 配置，其中有一个 `[dependencies]` 项，在它下面书写要安装的依赖。

```toml
[dependencies]

rand = "^0.3.14"
```

```ad-note
^符号表示会下载最新的兼容 0.3.14 版本的 crate
```

```ad-note
依赖居然要手写，感觉有点离谱
```

VSCode 有一个 `Rust language server` 的服务一直处于运行之中，在完成依赖书写之后，会自动运行，从远程服务器下载相应的包到本地。

也可以手动执行 `cargo build` 操作，初次执行同样会下载crate。
![](assets/Pasted%20image%2020220504102959.png)

可以在 [crates.io: Rust Package Registry](https://crates.io/) 查找要安装的库

下载的时候 cargo 会将 crate.io 中最新的注册表下载下来，之后会检查 Cargo.toml 的`[dependencies]` 字段，如果检测到没有下载的库，就会下载。

```ad-question
cargo 是和 pnpm类似，同一个计算机使用同一份依赖？
```


# 项目实战：猜数

```rust
use std::io;

fn main() {

println!("猜数！");

println!("猜测一个数");

  

let mut guess = String::new();

  

io::stdin().read_line(&mut guess).expect("无法读取行");

  

println!("你猜测的数是：{}", guess);

}
```

- `use std::io` ：std 标准库，导入方式
- 默认情况，Rust 会将一个`prelude` 模块导入
	- String 就在 prelude 中
- rust 中默认所有变量是不可改变的
	- 想要定义一个可改变的变量，需要在定义的时候加上一个 `mut` 关键字
	- 不可改变：immutable
- `let mut guess = String::new()`
	- `::` 表示关联函数 new 是 String 的一个关联函数
- read_line：将用户的输入以字符串的形式放到一个变量中
- read_line(&mut guess): 参数表示可变的字符串。& 取地址符号， 表示这是一个引用
	- rust 的核心竞争力，可以保证用户可以安全地使用引用
	- 引用默认也是不可变的，所以需要加上 mut 关键字
- read_line 的返回值是一个 Result，是一个枚举类型，每一种类型表示美剧类型的一个变体
	- 这里的变体有两个 一个是 Ok 一个是 Err
![](assets/Pasted%20image%2020220504100813.png)
```ad-note
这里的返回值调用有点讲不清楚
```

- `println!("你猜测的数是：{}", guess);` `{}` 表示占位符，在输出的时候会使用后面的变量来替换占位符

## 猜数：生成随机数

Rust 中的 crate 表示包。有两种，上面的程序可以直接执行，叫做二进制 crate。还有一种，比如 rand，叫做 library crate “库包”，是提供给他人调用的

引入 rand 包 `use rand::{thread_rng, Rng};`

- 注意包中的函数，可以直接在引入的时候展开
- match 用于作比较，这里就是用于 guess 和 secret_number 作比较
	- 比较结果使用 std::cmp::Ordering 枚举表示
	- 类似 switch
- 随机出来的是数字，match 参数要求是字符串，所以需要转换类型
	- 有两个 let guess 的定义操作，rust 中是允许的
	- guess.trim(): guess 中存储的是用户输入的内容， 用户终端输入的时候会以 Enter 作为结束，这个时候会在输入内容末尾加上 `\n` , trim 会删除这个。
	- parse 会将输入字符串解析为数字，如果无法解析成功，执行 expect 语句报错

```ad-tip
match 表示匹配，最后跟随一个 Map 类型， 比较操作是 guess.cmp 完成的
```

```rust
match guess.cmp(&secret_number) {

	Ordering::Less => println!("Too Small"),
	
	Ordering::Greater => println!("Too Big"),
	
	Ordering::Equal => println!("You win"),

}
```

```rust
use std::io;

use rand::{thread_rng, Rng};

use std::cmp::Ordering;

  

fn main() {

println!("猜数！");

// 在本地线程空间获取随机数的种子, 包含 1 不包含 101

  

// let mut secret_number = thread_rng();

// secret_number = secret_number.gen_range(0, 10);

let mut rng = thread_rng();

let secret_number: u32 = rng.gen_range(1, 101);

  

println!("生成的随机数 {}", secret_number);

println!("猜测一个数");

  

let mut guess = String::new();

  

io::stdin().read_line(&mut guess).expect("无法读取行");

  

let guess: u32 = guess.trim().parse().expect("Please type a number");

  

println!("你猜测的数是：{}", guess);

  

match guess.cmp(&secret_number) {

	Ordering::Less => println!("Too Small"),
	
	Ordering::Greater => println!("Too Big"),
	
	Ordering::Equal => println!("You win"),

}

}
```

## 猜数：允许多次猜测

循环 loop
- 包裹在 loop 中的代码块会无限循环执行
- 使用 break 跳出循环

```rust
loop {

	println!("猜测一个数");
	
	let mut guess = String::new();
	
	io::stdin().read_line(&mut guess).expect("无法读取行");
	
	let guess: u32 = guess.trim().parse().expect("Please type a number");
	
	println!("你猜测的数是：{}", guess);
	
	match guess.cmp(&secret_number) {
	
	Ordering::Less => println!("Too Small"),
	
	Ordering::Greater => println!("Too Big"),
	
	Ordering::Equal => {

		println!("You win");
		
		break;
		
	}

}

```


## 猜数：输入错误类型处理

还是使用 match 语法，guess.trim().parse() 结果是一个 Result 类型，是一个枚举类型。此处的枚举包含两个情况 Ok 与 Err

match 的处理对象，就是用来处理这两种情况的


```rust
let guess: u32 = match guess.trim().parse() {

Ok(num) => num,

Err(_) => continue,

};
```

# 通用的编程概念

## 变量与常量

除了上面讲到的通过 let 声明的变量之外，还存在常量（constant）

常量在绑定值之后也是不可改变的，但是与 let 变量有区别
- 不可以使用 mut，常量永远都是不可变的
- 常量使用 const 关键字，它的类型必须被标注
- 常量可以在任何作用于内进行声明，包括全局作用于
- 常量只可以绑定到常量表达式，无法绑定到函数的调用结果或只能在运行时才能计算出的值

- 命名规范：MAX_POINTS
- const MAX_POINTS: u32 = 100_000;


## Shadowing(隐藏)

可以使用相同的名字声明新的变量，新的变量就会 shadow 之前声明的同名变量

```ad-tip
感觉和 js 的变量覆盖差不多，后面定义的变量会覆盖之前的变量
```

- 如果不适用 let 关键字，那么重新给非 mut 的变量复制会导致编译时报错
- 使用 let 声明的同名新变量，是不可变的
- ==使用 let 声明的同名新变量，它的类型可以与之前不同==



## 数据类型

- 标量与复合类型

在这里，代码的作用是将字符串转成数字类型。必须指定类型，否则就会报错

rust 中的类型与 ts 类型的作用并不完全一样，下面的代码，将类型指定为 u32，parse 的时候才知道到底是将数据转换成什么类型。

```rust
let guess: u32 = "42".parse().expect("Not a number");
```

parse 函数的文档有提到这个操作

> As such, `parse` is one of the few times you'll see the syntax affectionately known as the 'turbofish': `::<>`. This helps the inference algorithm understand specifically which type you're trying to parse into.

## 标量类型

- 一个标量类型代表一个单一的值
- Rust 有四个主要的标量类型
	- 整数类型
	- 浮点类型
	- 布尔类型
	- 字符类型

### 整数类型

- u32 就是一个无符号的整数类型，占据 32 位的空间
	- 范围是 `-(2^32 - 1) 到 2^(n-1) - 1`  闭区间
- 无符号整数类型使用 u 开头
- 有符号整数类型使用 i 开头

Rust 整数类型列表

![](assets/Pasted%20image%2020220504144833.png)

![](assets/Pasted%20image%2020220504145041.png)

**isize 和 usize 类型**
- 这两种类型的位数由程序运行的计算机的架构决定，如果是 64 位，那就是 64 位 的
- 使用 isize 或 usize 的主要场景是对某中级和进行索引操作。

**整数字面值**
![](assets/Pasted%20image%2020220504145318.png)

不知道使用什么类型可以使用 i32 类型

**整数溢出**

如果设置的整数数值超出给定个类型的范围，在调试模式与发布模式下会有两种不同的处理方式
- 调试模式下编译：Rust 会检查整数溢出，如果发生溢出，程序会在运行时就会出现 panic
- 发布模式（--release）编译，Rust 不会检查可能导致 panic 的整数溢出
	- 如果发生溢出：rust 会执行环绕操作
	- 256 变成 0，274 变成 1
	- 但程序不会 panic


### 浮点类型

![](assets/Pasted%20image%2020220504153849.png)

### 数值运算

![](assets/Pasted%20image%2020220504154015.png)

### 布尔类型

![](assets/Pasted%20image%2020220504154049.png)

### 字符类型

![](assets/Pasted%20image%2020220504154139.png)

![](assets/Pasted%20image%2020220504154226.png)


## 复合类型

![](assets/Pasted%20image%2020220504154331.png)

- 元组
- 数组

### 元组

![](assets/Pasted%20image%2020220504154418.png)
![](assets/Pasted%20image%2020220504154444.png)

![](assets/Pasted%20image%2020220504154738.png)


```ad-note
注意：
1. 使用的是小括号定义元组
2. 可以使用解构的方式获取元组中的值
3. 读取元组中的值的时候，并不是使用[]中括号包裹，而是使用点语法
```


### 数组
![](assets/Pasted%20image%2020220504154822.png)

```ad-note
Rust 要求数组中每个元素的类型必须相同，且数组长度固定
```
---
**数组的用处**

数组 prelude 提供
 ![](assets/Pasted%20image%2020220504155514.png)


**数组的类型表示**

![](assets/Pasted%20image%2020220504155710.png)


**另一种声明数组的方法**

![](assets/Pasted%20image%2020220504155801.png)

**访问数组的元素**

和其他语言访问数组一样

![](assets/Pasted%20image%2020220504160037.png)

`cargo build` 能够通过， 但是运行的时候会报错。
书写的时候，IDEA 会有错误提示

```ad-tip
这就是 Rust 更加安全的原因，如果是 c/c++ 的话，这里不会报错，但是其他地方会出现完全无法定位的报错问题
```



# 函数

![](assets/Pasted%20image%2020220504160514.png)

![](assets/Pasted%20image%2020220504160608.png)

函数还有一种特殊的声明方式，类似 js 的箭头函数的写法

```rust
fn five -> i32 {
	5
}
```

```ad-key
注意：这里并没有其他语言对函数声明先后的要求
```

![](assets/Pasted%20image%2020220504160752.png)

- parameters 理解为形参，声明函数是需要
- arguments 叫做实参，调用函数时传递的参数

## 函数体中的语句和表达式

![](assets/Pasted%20image%2020220504161008.png)

语句：statement
表达式：expressions
等号右边不能是一个语句，区别于 C 语言

![](assets/Pasted%20image%2020220504161136.png)

下面的代码，对于 y 的赋值很清奇。 代码块中的最后一个表达式会成为 y 的值。
需要注意的是，不能在最后一处表达式后面加上分号。

```rust
fn main() {

	let y = {
	
		let x = 1;
		
		x + 3
	
	};
	
	  
	
	println!("x is {}", y);

}
```

加上了分号会报错：此时就相当于代码块中返回一个空的 tuple

![](assets/Pasted%20image%2020220504163359.png)


## 函数的返回值

![](assets/Pasted%20image%2020220504163524.png)

# 注释

和 JS 的注释方式差不多

![](assets/Pasted%20image%2020220504163920.png)

存在一种特殊的文档注释

- [ ]  #todo  学习 Rust 文档注释方式，作用

# 控制流

## if 表达式

![](assets/Pasted%20image%2020220504164114.png)

```ad-tip
条件必须是 bool 类型：rust 中的类型应该没有自动转换的话，应该怎么写？
```

```ad-note
和 JS 中的分支语句差不多
```

![](assets/Pasted%20image%2020220504164356.png)

**如果使用了多余一个 else if 那么最好使用 match 来重构代码**

---

**if 是一个表达式，那么它就可以放在等号的右边**

需要注意的是，两个分支的返回数据类型应该是一样的，否则无法在编译前确定类型

```rust
let hasFive = if (five() == 5) {

	"没错，就是五"
	
	} else {
	
	"猜错了"

};
```
```ad-note
这种写法实在是太新潮了。 这种用法类似三元运算符，三元运算符没有这个语意清晰
```



## 循环

### loop
![](assets/Pasted%20image%2020220504165350.png)

- 使用 break 来停止循环
![](assets/Pasted%20image%2020220504165452.png)

```ad-key
break 后面是存在表达式的，他会作为loop 代码块的返回
```


### while
![](assets/Pasted%20image%2020220504173132.png)

![](assets/Pasted%20image%2020220504173146.png)


### for
![](assets/Pasted%20image%2020220504173526.png)

![](assets/Pasted%20image%2020220504173302.png)


**使用 for 循环遍历数组**

![](assets/Pasted%20image%2020220504173348.png)

```ad-key
注意 a.iter() 这个方法会返回一个可以遍历的东西
```

```ad-question
如何获取到索引？
```

 ### Range
 ![](assets/Pasted%20image%2020220504173706.png)

![](assets/Pasted%20image%2020220504173652.png)

```ad-key
注意：结束边界为开区间
```

# 所有权

**最独特**的特性，让 Rust 无需 GC 就可以保证内存安全

![](assets/Pasted%20image%2020220504174150.png)

Rust 将所有权管理的操作都提升到了编译时，不会影响速度

---

## Stack Heap 栈内存/堆内存

![](assets/Pasted%20image%2020220504174324.png)

- Stack 按值得接受顺序来存储，按相反的顺序将他们移除（先进后出， LIFO）
- 所有存储在 Stack 上的数据必须拥有已知的固定的大小
	- 编译时大小位置的数据或运行时大小可能发生变化的数据必须存放在 heap 上
- Heap 内存组织性差一些
	- 当把数据放入 heap 时，会请求一定数量的空间
	- 操作系统在 heap 中找到一块足够大的口空间，将其标记为在用，并返回一个指针，也就是这个空间的地址
	- 这个过程叫做在 heap 上的分配

```ad-question
什么时候会有不定大小的空间存在？Vector 么？
stack 存入数据的方式和其他语言一致么？
```
- [ ] #todo javascript 是如何对 stack 以及 heap 进行操作的？


### 数据的存储

- 将值压到 stack 上不叫分配
- 因为指针是已知的固定大小，可以把指针放在 stack 上
	- 想要实际数据，必须使用指针来定位

![](assets/Pasted%20image%2020220504195433.png)

### 访问数据

![](assets/Pasted%20image%2020220504195546.png)

### 函数调用

![](assets/Pasted%20image%2020220504195613.png)

## 所有权存在的原因


![](assets/Pasted%20image%2020220504195709.png)

```ad-question
1. 最小化 heap 上的重复数据量
	这一步是如何进行的？
2. 清理 heap 上未使用的数据以避免空间不足
	什么时候清理，以什么标准判断是否需要清理
	这个和 GC 有什么区别，感觉这就是垃圾回收机制

```

## 所有权规则


![](assets/Pasted%20image%2020220504200055.png)

```ad-question
1. 这里的变量指的是什么？
2. 如果有多个变量指向同一个引用？
3. 什么时候会超出作用域
```

```key
所有权的规则只适用与对 heap 内存的管理
```


---
**作用域**：程序中一个项目的有效范围

![](assets/Pasted%20image%2020220504200315.png)


## String 类型

![](assets/Pasted%20image%2020220504200442.png)


```ad-question
不可变的？ 即使加上了 mut 也是不可变的？
试验了下，是可以改变的，所以这在说什么？
理解错误：这里的不可变，不能直接修改原本的空间存储的内容。重新赋值不是改变，是一个新的值覆盖了原本的值
```

**String 类型创建**

```rust
let mut s = String::from("Hello");
s.push_str(", World");
```


### 字符串字面值与 String 类型区别

此二者处理内存的方式不同

![](assets/Pasted%20image%2020220504213223.png)

```ad-tip

在编译时就确定内容的变量，是直接将内容硬编码到可执行文件中的。这里除了 字符串字面量，其他类型的变量也应该如此

一个变量想要支持不可变性，需要在运行时动态申请内存

```

**Rust对于内存的释放大有不同：对于某个值来说，当拥有它的遍历阿昂走出作用范围时，内存会立即自动交还给操作系统**
**当变量走出作用域的时候，会自动调用 drop 函数** 

```ad-question
什么是 GC？我之前的理解，自动执行内存归还操作的程序语言内置的机制，叫做 GC。
```

### String 实例在内存中的存储

一个 String 由三部分组成
- 一个指向存放字符串内容的内存的指针
- 一个长度
- 一个容量

这些东西存放在 stack 上
存放字符串内容的部分在 heap 上
长度 len，就是存放字符串内容所需的字节数
容量 capacity 指的是 String 从操作系统总共获得内存的总字节数

![](assets/Pasted%20image%2020220504215354.png)


```ad-question
这里说的是字节数而不是字符串长度？
尝试了一下，输出的是字符串的长度
需要注意的是，我是将 len capacity 当做方法调用才能获取到值
```




## 变量与数据的交互方式：移动

```ad-note

其他语言在通过 `=`  符号对复杂类型进行的赋值操作，并不适用于 Rust。
Rust 会在变量离开作用域的时候释放变量对应的内存，如果多个变量持有相同内存，在它们离开作用域的时候，就会释放两次内存，会报错。

```


![](assets/Pasted%20image%2020220504215706.png)

**为了保证内存安全，Rust 采用了另外一种方法：Move**

  ![](assets/Pasted%20image%2020220504215831.png)
![](assets/Pasted%20image%2020220504215909.png)

![](assets/Pasted%20image%2020220504220056.png)


## 变量与数据交互的方式：克隆（Clone）

![](assets/Pasted%20image%2020220504220430.png)

![](assets/Pasted%20image%2020220504220715.png)

## Stack 上的数据：复制

Stack 上的数据在执行赋值语句的时候自动发生复制行为

![](assets/Pasted%20image%2020220504221405.png)

```ad-tip
Copy trait 是一种隐式的 trait，它的调用是语言内置的某种机制自动执行的。在进行赋值语句的时候自动执行
这样的隐式调用，编译器很难预测在什么时候调用 Drop 函数

区别于 Clone trait，这是需要用户手动调用的显式 trait，编译器能够通过这种显式的 clone，确定被 clone 的变量的位置，决定何时调用 Drop trait

```

> 实现 Copy_trait 需要先实现 Clone_trait ，因为 Clone 比 Copy 更加通用

```ad-question
那些拥有 Copy trait 的类型，不能存在 Drop trait，那么是如何管理这部分的内存的？
```

## 拥有 Copy_trait 的类型

![](assets/Pasted%20image%2020220504222326.png)

```ad-tip
[Stack 上的数据：复制](#Stack%20上的数据：复制) 这里有提到

> 如果一个类型或者该类型的一部分实现了 Drop_trait，那么 Rust 不允许它再去实现 Copy_trait 了

所以，一个元素中的元素是否有 Drop_trait 还要看它的子类型是否有 Copy_trait
```



## 所有权与函数

在语义上，将值传递给函数和把值赋给变量是类似的
**将值传递给函数将发生移动或者复制**

**函数在返回值的过程中同样也会发生所有权的转移**

一个变量的值总是遵循同样的模式
- 把一个值赋给其他变量时就会发生移动
- 当一个包含heap数据的变量离开作用域时，它的值就会被drop函数清理，除非数据的所有权移动到了另一个变量上了

```ad-tip
需要在 heap 上有数据的值在发生复制操作的时候才会移动
```

---

**让函数使用某个值，但是不获得其所有权**

这里的不获得所有权，指的是 `calculate_length` 函数在调用时取得了参数的所有权，但是在调用结束后，又将所有权返回。
hello 这个存于heap的字符串，没有在`calculate_length` 函数结束时被销毁

![](assets/Pasted%20image%2020220505221647.png)

上面的方法有些笨重，为了比较方便解决这个问题，Rust有一个特性叫做**引用**


## 引用与借用

- 参数的类型是 `&String` 而不是 `String`
- `&` 符号就表示引用：允许你饮用某些值而不取得其所有权

![](assets/Pasted%20image%2020220505222941.png)

calculate_length函数调用时传递的是 s1 创建的引用，对应的是上图中 s 的位置。它与heap中的数据并不直接相连，所以 drop的时候并不会将heap中的数据 drop

![](assets/Pasted%20image%2020220505223101.png)

**把引用作为函数参数这个行为叫做借用。**

```ad-key
借用在默认情况下是不可以修改的，和变量一样

想要对引用进行修改，需要对引用添加 mut 字符
```


![](assets/Pasted%20image%2020220506070715.png)

```ad-key
可变引用有一个重要限制：在特定作用域内，对某一块数据，只能有一个可变引用
```

![](assets/Pasted%20image%2020220506071108.png)

会出现编译报错

![](assets/Pasted%20image%2020220506071137.png)

 **这样做的好处是可以在编译时防止数据竞争**

一下三种行为下会发生 数据竞争：
- 两个或者多个指针同时访问一个数据
- 至少有一个指针用于写入数据
- 没有使用任何机制来同步对数据的访问

```ad-question
应该使用什么办法同步对数据的访问？
```

可以通过创建新的作用域，来允许非同时的创建多个可变引用

```ad-tip
对于同一个变量的饮用不
```

![](assets/Pasted%20image%2020220506071535.png)

---

```ad-key
不可以同时拥有一个可变引用和一个不可变引用，但是多个不可变的引用是可以的
```

![](assets/Pasted%20image%2020220506071949.png)

否则编译时会报错

![](assets/Pasted%20image%2020220506072041.png)

---

**悬空引用**

有一种悬空指针的错误：
一个指针引用了内存中的某个地址，而这块内存可能已经释放并分配给其他人使用了

在Rust中，编译器可保证引用永远都不是悬空引用：
如果你因用了某些数据，编译器将保证在饮用离开作用于之前，数据不会离开作用域

---
下面的 dangle 函数，内部定义了 s 变量 返回的 &s 指向 s ，但是在s调用结束之后，s 变量会被销毁，此时，返回的 &s 引用就会指向一个悬空的地址


![](assets/Pasted%20image%2020220506072446.png)

编译器会对这种情况报错

![](assets/Pasted%20image%2020220506072700.png)

```ad-key
引用的规则：
1. 在任何给定的时刻，只能满足下列条件之一
	1. 一个可变的引用
	2. 任意数量不可变的引用
2. 引用必须一直有效（不能出现悬空指针）
```


## 切片

Rust 的另外一种**不持有所有权**的数据类型：切片

```ad-question
1. 在 Rust 中，不持有所有权有什么影响
2. 除了切片，还有什么东西是不持有所有权的？
```

对字符串的切片，能够获取部分引用

![](assets/Pasted%20image%2020220506074301.png)

左闭右开

![](assets/Pasted%20image%2020220506073853.png)

```ad-key
切片操作，需要在字符串的引用上完成
```

---

**语法糖**

1. 如果切片的内容包含 String 的首尾处，那么首尾切片的 index 可以省略

![](assets/Pasted%20image%2020220506074132.png)

如果切片需要获取整个字符串，那么首尾都可以不写
![](assets/Pasted%20image%2020220506074228.png)

---

**注意：**

![](assets/Pasted%20image%2020220506074359.png)

```ad-question
什么时候会出现多字节字符？记得英文是用两个字节表示，中文是用三个，这不算是多字节么？
```

### 切片解决的问题



```rust
fn main() {

	let mut s = String::from("Hello world");
	
	// 此处返回的是字符串的 index
	let wordIndex = first_world(&s);
	// 此处会将字符串清空，问题便来自于此
	s.clear();
	// wordIndex 不会与 字符串保持同步，字符串清空后 wordIndex不会发生改变
	println!("{}", wordIndex);

}

  

fn first_world(s: &String) -> usize {

	let bytes = s.as_bytes();
	
	for (i, &item) in bytes.iter().enumerate() {
	
		if item == b' ' {
		
			return i;
		
		}
	
	}
	
	s.len()

}
```


如上问题描述，想要解决这个问题，就要用到字符串切片。
因为这里有切片，所以，`s.clear` 会报错。`first_world` 返回的切片是不可改变的引用（因为入参也是不可改变的），所以会引起 s 发生变化的地方就会报错
**注意：**
1. 字符串切片的类型可以写成 `&str`
2. 字符串切片要在 &String 的 饮用上执行
3. `b' '` 表示 byte 形式的 空格

```rust
fn main() {
	
	let mut s = String::from("Hello world");
	
	let wordIndex = first_world(&s);
	
	s.clear();
	
	println!("{}", wordIndex);

}

  

fn first_world(s: &String) -> &str {
	
	let bytes = s.as_bytes();

	for (i, &item) in bytes.iter().enumerate() {

		if item == b' ' {
		
			return &s[..i];
		
		}
	
	}
	
	&s[..]

}
```

![](assets/Pasted%20image%2020220507104257.png)

**注意：字符串字面值的类型其实就是字符串切片**

![](assets/Pasted%20image%2020220507104423.png)

```ad-question
为什么，要使用 bytes 的方式来使用字符串？ 字符串切片可以通过某种方式直接使用么？ 和 JS 中一样？
```


### 其他类型的切片
![](assets/Pasted%20image%2020220507104752.png)


# struct
![](assets/Pasted%20image%2020220507104839.png)


## struct 定义
![](assets/Pasted%20image%2020220507104934.png)

## struct 实例化

![](assets/Pasted%20image%2020220507105104.png)

![](assets/Pasted%20image%2020220507105127.png)

## 访问 struct 字段

![](assets/Pasted%20image%2020220507105232.png)


```ad-key
一旦 struct 的实例是可变的，那么实例中的所有字段都是可变的
rust 不允许只让 struct 中的部分字段可变
```

## struct 作为函数的返回值

![](assets/Pasted%20image%2020220507105429.png)

## 字段初始化简写

和 JS 差不多

![](assets/Pasted%20image%2020220507105529.png)


## struct 更新语法（扩展运算符）

和 js 差不多，不过使用的是两个点

![](assets/Pasted%20image%2020220507105708.png)

## Tuple struct

![](assets/Pasted%20image%2020220507105909.png)

## Unit-Like  Struct

 
![](assets/Pasted%20image%2020220507110022.png)


## struct 数据的所有权


![](assets/Pasted%20image%2020220507110219.png)

```ad-key
struc 整体与其中字段的所有权需要分开看待
如果其中的某个字段是一个引用，就需要考虑生命周期，否则就会报错
```

## 练习：计算一个长方形面积

```rust
fn main() {

	let w = 30;
	
	let l = 50;
	
	println!("square area: {}", area(w, l));

}

  

fn area(width: u32, length: u32) -> u32 {

	width * length

}
```

area 函数能够返回正确的长方形面积。 但是 width 以及 length 参数在 area 函数的接口上没有表现出任何关联性。如果能够将长和宽组合到一起，这样的代码将会更容易理解和维护。

采用元素的方式将长款组合到一起，得到下面的代码。下面的代码虽然将长宽组合到了一起，但是代码的可读性变差了，无法直接区分哪个是长，哪个是宽

```rust
fn main() {

	let rect = (30, 50);
	
	println!("square area: {}", area(rect));

}

  

fn area(dim: (u32, u32)) -> u32 {

	dim.0 * dim.1

}
```

无法对 Tuple 的元素进行标注，即使是 Tuple struct 也不行。最终使用 struct 进行重构

```rust

struct Rectangle {

	width: u32,
	
	length: u32,

}

  

fn main() {

	let rect = Rectangle {
	
		width: 30,
		
		length: 50,
	
	};
	
	println!("square area: {}", area(rect));

}

  

fn area(dim: Rectangle) -> u32 {

	dim.width * dim.length

}
```


在这里，需要注意所有权、引用与借用。 area 函数接收的参数是 rect ，是一个 struct 的实例。
rect 作为参数传入 area 之后，所有权进行了移交，rect 在 area 函数调用完毕之后就不能再使用了。

main 函数中不能再使用 react 变量

![](assets/Pasted%20image%2020220507112915.png)

rust 中有两种方法能够在函数传参的时候保持所有权
- 引用、借用
- 切片

这里只能使用借用

```rust
struct Rectangle {

	width: u32,

	length: u32,

}

  

fn main() {

	let rect = Rectangle {
	
		width: 30,
		
		length: 50,
	
	};

	println!("square area: {}", area(&rect));

  

	println!("rect msg {}", rect.length);

}

  

fn area(dim: &Rectangle) -> u32 {

	dim.width * dim.length

}
```


## 类型提示，打印以及 debug 模式


![](assets/Pasted%20image%2020220507141818.png)

在上面的代码中，直接 `println!("rect msg {}", rect.lenth)` ，会有错误提示。

这个意思是 struct Rectangle 没有实现 `std::fmt::Display`  trial ，所以无法打印输出

![](assets/Pasted%20image%2020220507141939.png)

根据日志提示，将 `{}` 换成 `{:?}` ，仍然报错，不过错误信息有改变

![](assets/Pasted%20image%2020220507142416.png)

根据错误提示为 struct Rectangle 添加`#[derive(Debug)]` ，错误消失
![](assets/Pasted%20image%2020220507142522.png)

运行结果：可以将 rect struct 实例完整输出
![](assets/Pasted%20image%2020220507142602.png)

还有另一种方法，将 `{}` 改成 `{:#?}` 能够将 struct 实例格式化输出

![](assets/Pasted%20image%2020220507142739.png)

rust 提供了很多 trait 可以让我们 derive（派生），这些 trait 可以为我们自定义的 struct 添加很多功能。

```rust
// 调用的是 std::fmt::Display trait
println!("{}", rect)

// 调用的是 std::fmt::Debug trait 
println!("{:#?}", rect)
```


## struct 上的方法

![](assets/Pasted%20image%2020220507143608.png)


### 方法的定义

```rust
#[derive(Debug)]
struct Rectangle {

	width: u32,
	
	length: u32,

}

  

impl Rectangle {

	fn area(self: &Rectangle) -> u32 {
	
		self.width * self.length
	
	}

}

  

fn main() {

	let rect = Rectangle {
	
		width: 30,
		
		length: 50,

	};

println!("square area: {}", rect.area());

  

println!("rect msg {:#?}", rect);

}
```

### 方法的调用

![](assets/Pasted%20image%2020220507144455.png)

```ad-question
为什么说效果相同？ struct 原本是无法调用 impl 定义的方法，必须要使用 &p1 才能调用？
&p1 不是一个引用指向 p1 在 stack 上的内容么？
```

###  关联函数

![](assets/Pasted%20image%2020220507150808.png)

这里的square 方法就是一个关联函数

![](assets/Pasted%20image%2020220507150229.png)


# 枚举与模式匹配

## 定义枚举

![](assets/Pasted%20image%2020220507150958.png)

## 创建枚举值

![](assets/Pasted%20image%2020220507151050.png)

```ad-key
这里自定义的枚举类型，貌似并不和其他语言一样，依托于本语言的基础类型
```

![](assets/Pasted%20image%2020220507151249.png)


## 将数据附加到枚举的变体中

![](assets/Pasted%20image%2020220507155026.png)

![](assets/Pasted%20image%2020220507155056.png)

我们可以在枚举的变体中嵌入任意类型的数据，无论是字符串，数值还是结构体

![](assets/Pasted%20image%2020220507155127.png)

![](assets/Pasted%20image%2020220507155239.png)


 ![](assets/Pasted%20image%2020220507155338.png)

在枚举类型中加入方法，同样是通过 impl 关键字来进行这个操作

```ad-question
为枚举添加一个方法，感觉已经和 struct 重复了，那么什么时候使用 struct 什么时候使用 enum 呢？

```

## Options 枚举

![](assets/Pasted%20image%2020220507155607.png)

## Rust 没有 Null

![](assets/Pasted%20image%2020220507155722.png)

09 年，null 的发明者说 null 是一个价值数百万的错误

 ![](assets/Pasted%20image%2020220507155834.png)

![](assets/Pasted%20image%2020220507155937.png)

**`Option<T>` 比起 Null 好在哪里？**

![](assets/Pasted%20image%2020220507160303.png)

```rust
fn main() {
	let x: i8 = 5;
	let y: Option<i8> = Some(4);
	let sum = x + y;
	// let sum = x + y.expect("y 是 null");
	println!("{}", sum)

}
```

上面的代码直接使用是会报错的：

![](assets/Pasted%20image%2020220507161104.png)

因为此时 y x 类型不一致无法直接相加。

想要相加需要将 y 的类型转换成 i8

转换方法有三种：
- expect：`y.expect("y 的值为 none ")`
- unwrap：`y.unwrap()`
- match

前两种会将 `Option<T>` 中的 T 类型提取出来，但是遇到值为 None 的情况就会报错，并中断程序运行

match 可以对 None 的情况进行处理，保证程序不中断。

```rust
fn main() {

	let x: i8 = 5;
	
	let y: Option<i8> = Some(5);
	
	  
	
	// let sum = x + y;
	
	let sum = x + match y {
	
		None => 6,
		
		Some(d) => d,
	
	};
	
	println!("{}", sum)

}
```

```ad-key
match 语法使用的箭头与函数返回值的箭头不一样，需要注意区别
```

## Match 控制流运算符

![](assets/Pasted%20image%2020220507162254.png)

模式对应的代码比较简单的时候，直接使用 `=> ` +  值即可，如果有复杂的代码，后面就需要街上一个 `{}`

匹配成功的代码会作为整个 match 的结果返回

![](assets/Pasted%20image%2020220507162529.png)

## 绑定值的模式

![](assets/Pasted%20image%2020220507162806.png)

![](assets/Pasted%20image%2020220507163339.png)


![](assets/Pasted%20image%2020220507163319.png)

##  匹配`Option<T>`

![](assets/Pasted%20image%2020220507164124.png)

**需要注意的是：Match 匹配必须穷举所有可能**

如果少写一种可能，就会报错

![](assets/Pasted%20image%2020220507164300.png)

![](assets/Pasted%20image%2020220507164330.png)

可以使用 `_` 表示其余情况，需要注意的是，下划线通配符要放到最后面

![](assets/Pasted%20image%2020220507164450.png)


##  if let

一种简单的匹配
- 处理只关心一种匹配而忽略其他匹配的情况。
- 更少的代码，更少的缩进，更少的模板代码
- 放弃了穷举的可能

![](assets/Pasted%20image%2020220507164732.png)

```ad-key
这里的 if let 后面只使用了一个等号
```

```ad-question
为什么不直接写 if 条件判断语句呢，非要写匹配
```

![](assets/Pasted%20image%2020220507165107.png)


# Package, Crate, Module


![](assets/Pasted%20image%2020220507191030.png)

```ad-question
Path: 是 “命名的方式”？
```

## Package 和 Crate

![](assets/Pasted%20image%2020220507191302.png)

**binary crate:** 二进制的 crate


**package：cargo的特性，让你构建、测试、共享crate**
使用cargo创建项目的时候，就会有输出，创建了一个 二进制的package 名字叫”my-project“
![](assets/Pasted%20image%2020220507191514.png)


## cargo 惯例

![](assets/Pasted%20image%2020220507191854.png)

 ![](assets/Pasted%20image%2020220507192058.png)

## crate


---

![](assets/Pasted%20image%2020220507220148.png)

## module


![](assets/Pasted%20image%2020220507220306.png)

lib.rs 中定义的多个嵌套 mod

![](assets/Pasted%20image%2020220507220433.png)

右侧的树形结构，root 应该是 lib.rs

![](assets/Pasted%20image%2020220507220559.png)

## path

![](assets/Pasted%20image%2020220507220908.png)

self 指本身，super 指的是上一级

## 私有边界

![](assets/Pasted%20image%2020220507221435.png)

- 子模块中的内容，默认都是私有的，父模块理所应当无法访问
- 子模块定义在父模块的上下文中，理所应当具有访问父模块所有条目的能力


```rust
mod front_of_hohuse {

	pub mod hosting {
	
	pub fn add_to_waitlist() {}
	
	}

}

  

pub fn eat_at_restaurant() {

	crate::front_of_hohuse::hosting::add_to_waitlist();
	
	  
	
	front_of_hohuse::hosting::add_to_waitlist();

}
```

```ad-key
同时处于文件根级的两个作用域可以互相调用，无所谓私有共有
```




##  pub 关键字

- mod 中的所有条目默认都是私有的
- 就是用来将某些条目标记为公共的


## super 关键字

![](assets/Pasted%20image%2020220507222537.png)

![](assets/Pasted%20image%2020220507222653.png)

## pub struct

![](assets/Pasted%20image%2020220507222808.png)



 ![](assets/Pasted%20image%2020220507223023.png)
seasonal_fruit 是私有字段，无法访问，会报错。

```ad-tip
根据实验，当 struct 单独定义时，struct 中的所有字段都是可以访问的
但是置于 mod 中的时候，就变成默认私有的了，需要手动设置 pub 才能访问
```

## pub enum

![](assets/Pasted%20image%2020220507224827.png)

## use 关键字

![](assets/Pasted%20image%2020220507224905.png)

只有公共的内容才可以在引入之后进行使用

![](assets/Pasted%20image%2020220507225448.png)

![](assets/Pasted%20image%2020220507225513.png)

## pub use

![](assets/Pasted%20image%2020220507230016.png)




## as 关键字

![](assets/Pasted%20image%2020220507225756.png)


## 使用外部的包


![](assets/Pasted%20image%2020220507230439.png)

## use 批量引入语法

![](assets/Pasted%20image%2020220507230904.png)

![](assets/Pasted%20image%2020220507230746.png)

![](assets/Pasted%20image%2020220507230839.png)


## 通配符 *

![](assets/Pasted%20image%2020220507230955.png)


```rust
use std::collections::*;
```


## 将模块内容移动到其他文件


![](assets/Pasted%20image%2020220507233052.png)


![](assets/Pasted%20image%2020220507232919.png)

# 常用集合

##  Vector

![](assets/Pasted%20image%2020220508125129.png)

![](assets/Pasted%20image%2020220508125321.png)


![](assets/Pasted%20image%2020220508125300.png)

![](assets/Pasted%20image%2020220508125426.png)

![](assets/Pasted%20image%2020220508125523.png)


注意：Vector 中有引用的情况，就会变得复杂

![](assets/Pasted%20image%2020220508125606.png)

 ![](assets/Pasted%20image%2020220508125742.png)

![](assets/Pasted%20image%2020220508144926.png)


![](assets/Pasted%20image%2020220508145105.png)


```ad-key
此处，为什么要求 v 以及 first 两个变量统一可变不可变规则？
Vector 是一个连续的内存空间，当 push 发生的时候，这份连续的内存空间地址可能不会太够用了，所以需要重新分配内存空间，释放原本的空间。 这个时候，如果规则允许的话，它仍会指向原本的内存地址。 这样的话借用规则就会出现问题，所以Rust 为了防止这样的事情发生......
```

```ad-tip
感觉上面的话有问题。 如果 v 可变，那么就可能会发生 push 行为，vector 就可能会重新分配内存。所以就要求 first 同样是可变的
```


**使用 for 循环遍历 vector**

![](assets/Pasted%20image%2020220508150026.png)

![](assets/Pasted%20image%2020220508150118.png)

```ad-question
为什么这里解引用加上五十就可以输出？
```

### vector + enum 存储多种数据类型

![](assets/Pasted%20image%2020220508150314.png)

![](assets/Pasted%20image%2020220508150353.png)

需要提前知道所有的类型才可以。

---

## String

![](assets/Pasted%20image%2020220508162318.png)

![](assets/Pasted%20image%2020220508162345.png)

```ad-question
如何理解字符串是字符的集合
```
 ![](assets/Pasted%20image%2020220508162449.png)

![](assets/Pasted%20image%2020220508162517.png)

![](assets/Pasted%20image%2020220508162609.png)

![](assets/Pasted%20image%2020220508162712.png)

![](assets/Pasted%20image%2020220508162823.png)

因为是 UTF-8 编码可以将所有合法数据都编进字符串中

![](assets/Pasted%20image%2020220508162838.png)

###  更新String

- **push_str(): 把一个字符串切片附加到 String**
```rust
fn main() {

	let mut s = String::from("foo");
	
	// 此方法不会获得参数的所有权，看一下函数签名，两个参数都是通过借用的方式进行传递的
	
	let s1 = String::from("bar");
	
	s.push_str(&s1);
	
	println!("Hello, world!, {}", s);

}
```

函数传递的都是引用，所以，函数并不会获取所有权，函数调用之后，s 以及参数都可以继续使用

![](assets/Pasted%20image%2020220508164626.png)

- **push() ：将单个字符附加到 String**

```rust
let s1 = String::from("bar");
s.push('d');
```

需要注意的是，push 方法需要的是单个字符，类型是 char 。
- 使用双引号会创建 &str 字符串切片类型
- 需要使用单引号才会创建 char 类型
- 使用单引号时，单引号内部的字符串只能有一个，否则会报错

![](assets/Pasted%20image%2020220508165914.png)

![](assets/Pasted%20image%2020220508170117.png)

- **`+` 加号进行字符串拼接**

需要注意的是，第二个参数希望是 &str 字符串切片类型的

![](assets/Pasted%20image%2020220508170306.png)

```rust
fn main() {


	let s1 = String::from("foo");
	
	let s2 = String::from("bar");

	let s3 = s1 + &s2;

	println!("Hello, world!, {}", s3);

}
```

这种用法相当奇怪... 

注意：在执行了 + ，字符串拼接之后，由于 s1 传递的不是引用，所以在执行 + 的时候，发生了 move 所有权的移动，此作用域失去了 s1 的所有权

![](assets/Pasted%20image%2020220508170752.png)

![](assets/Pasted%20image%2020220508170843.png)

---

```rust
let s1 = String::from("foo");
let s2 = String::from("bar");

let s3 = s1 + &s2;
println!("Hello, world!, {}, {}, {}", s3, s1, s2);
```

连接字符串类似调用这个签名的方法：`fn add(self, s: &str) -> String {...}`

而代码中 &s2 只是 String 类型的引用，之所以能够调用成功，不报错。 是因为 rust 中有一种解引用强制转换的技术，将 &String 转换成了 &str

加号拼接，可以连用

![](assets/Pasted%20image%2020220508171453.png)

![](assets/Pasted%20image%2020220508171316.png)

- **format!: 连接多个字符串**

比较灵活，代码更容易 阅读，并且不会取得任何参数的所有权

```rust
 fn main() {

	let s1 = String::from("foo");

	let s2 = String::from("bar");

	let s3 = format!("{}-{}", s1, s2);

	println!("{}, {}, {}", s3, s1, s2);

}
```

```ad-question
format！参数传递的都不是引用，为什么不会取得所有权呢？
```


### 索引方式访问 String
![](assets/Pasted%20image%2020220508173611.png)

**String 是对 `Vec<u8>` 的包装**

String 上有一个 len 方法， 可以返回 String 所占据的字节数

字符串是英文字母的时候，每个字母占据一个字节
![](assets/Pasted%20image%2020220508211700.png)

但是并不是所有字符串都是每个字符占据一个字节，下面的字符串会占据 24 个字节

 ![](assets/Pasted%20image%2020220508211738.png)

每个字母可以叫做 ”unicode 标量值“，在上面的代码中，每个 unicode 标量值都会对应两个字节

String 中的字母，并不总是能够对应到一个有效的 unicode 标量值

下面的代码中，通过索引来获取 字节类型的第一个值。因为第一个字母占据了两个字节，`&hello[0]` 获取到的可能就是 208 这样的数据，这并不是用户期待看到的

![](assets/Pasted%20image%2020220508212027.png)
为了避免这样的情况出现，并且为了避免引起未能及时发现的 bug，rust 语言直接拒绝了这样的代码。

---

### 字节、标量值、字形簇

![](assets/Pasted%20image%2020220508212331.png)

#### 字节

![](assets/Pasted%20image%2020220508212432.png)

- `w.bytes` 可以获取这个字符串的所有字节，返回的是一个 iterate ，可以遍历
这些字节就是计算机存储这些字符串的样子。
![](assets/Pasted%20image%2020220508212532.png)

#### 标量值

- `w.charts()` 会返回类型为 chart 的一个遍历器
chart 对应的就是所谓的 uncode 标量值
![](assets/Pasted%20image%2020220508212648.png)

返回的是这些东西，但是最后一个字符没有意义，就像是一个音标。需要结合前面的东西放在一起才算是一个字母
![](assets/Pasted%20image%2020220508212721.png)

#### 字形簇

![](assets/Pasted%20image%2020220508212850.png)

从 String 中获取字形簇相对复杂一些，标准库就没有提供这些功能。可以使用第三方库

---

#### Rust 不允许 String 进行索引的最后一个原因：

![](assets/Pasted%20image%2020220508213056.png)

```ad-question
为什么需要遍历所有内容，确定合法字符的数量？
```

### 切割 String

![](assets/Pasted%20image%2020220508213237.png)

如果某种语言两个字节一个字符，那么切割的时候就必须两个字符两个字符地进行切割，否则就会报错。

如下，下面切割的时候，切割了三个字节，有一个字符没有切割完整，就会报错。

![](assets/Pasted%20image%2020220508213405.png)

![](assets/Pasted%20image%2020220508213457.png)

提示 不是一个字符边界。

---

### String 的方法

![](assets/Pasted%20image%2020220508213600.png)


![](assets/Pasted%20image%2020220508213629.png)


## HashMap<K, V>

![](assets/Pasted%20image%2020220508213732.png)

![](assets/Pasted%20image%2020220508213906.png)

![](assets/Pasted%20image%2020220508213947.png)

![](assets/Pasted%20image%2020220508214040.png)

```rust
let team = vec![String::from("Blue"), String::from("Yellow")];

let initial_scores = vec![10, 50];

  

// 使用了 collect ，就需要显示指明返回的类型，否则就会报错。

// 因为 collect 可能返回多种集合数据结构，编译器无法知道具体想要的结构

// 指定的类型关键在与 HashMap 而不是泛型类型。 这里使用 _ 代替，因为 rust 会自动推导出类型

// zip 有拉链的意思，这是将两个 vector 合并成一个元素为元组的数组了

let scores: HashMap<_, _> = team.iter().zip(initial_scores.iter()).collect();
```

### HashMap 和所有权

![](assets/Pasted%20image%2020220508215406.png)

将原本作用域中的两个变量插入到 HashMap 中，就不能再使用了。发生了所有权的移动

![](assets/Pasted%20image%2020220508215316.png)

如果向 hashmap 中传递的是引用，可以避免所有权的 move
![](assets/Pasted%20image%2020220508215439.png)

### 访问 HashMap 中的值

- 调用 HashMap get 方法的时候，参数是 &str
- ==team_name 与 HashMap 中存入的数据并不属于同一个引用，但是 HashMap 获取的时候依旧能获取到它。==
- hashmap get 方法返回的是一个 Option

```rust
use std::collections::HashMap;

  

fn main() {

	let mut scores = HashMap::new();

  

	scores.insert(String::from("Blue"), 10);
	
	scores.insert(String::from("Yellow"), 50);
	
	  
	
	let team_name = String::from("Blue");
	
	let score = scores.get(&team_name);
	
	  
	
	match score {
	
		Some(d) => println!("{}", d),
		
		None => println!("team not exit!!"),
		
	}

}
```

### 遍历 HashMap
![](assets/Pasted%20image%2020220508220424.png)

### 更新 HashMap
![](assets/Pasted%20image%2020220508220523.png)
 
需要注意这里更新的规则实际上比较复杂，如果存在key，就有三种处理办法。

---

![](assets/Pasted%20image%2020220508220701.png)

![](assets/Pasted%20image%2020220508220715.png)

下面的内容，如果不使用 entry，而是使用 insert，那么不管有没有 K，新 insert 的内容，就会成为 就会成为全新的内容。

**基于现有的 V 来更新 V**

下面的代码是为了统计各个单词出现的频次
![](assets/Pasted%20image%2020220508221118.png)

实际上就是分为两步：
- 通过 entry 获取到 K 对应的 V 的引用 如果没有的话插入一个默认的 0
- 然后通过解引用之后对值加一

 ### Hash 函数

![](assets/Pasted%20image%2020220508222052.png)


# 错误处理

## panic！不可恢复的错误

### rust 错误处理概述
![](assets/Pasted%20image%2020220508222243.png)

#todo
```ad-question
可恢复错误与不可恢复错误区别
```

 
### 不可恢复的错误 panic
![](assets/Pasted%20image%2020220508222400.png)

![](assets/Pasted%20image%2020220508222502.png)

`profile.release` 表示生产环境中
![](assets/Pasted%20image%2020220508222700.png)

---

下面是我们自行调用 panic 产生的错误
![](assets/Pasted%20image%2020220508222752.png)

![](assets/Pasted%20image%2020220508222808.png)

---
下面是调用一个 lib 时产生的 panic

![](assets/Pasted%20image%2020220508223041.png)

![](assets/Pasted%20image%2020220508223057.png)

根据依赖设置 RUST_BACKTRACE=1 查看回溯信息

![](assets/Pasted%20image%2020220508223132.png)


![](assets/Pasted%20image%2020220508223343.png)


## Result 枚举与可恢复的错误

![](assets/Pasted%20image%2020220508224110.png)
![](assets/Pasted%20image%2020220508224216.png)
![](assets/Pasted%20image%2020220508224308.png)

### 匹配不同的错误

```rust
use std::fs::File;

use std::io::ErrorKind;

  

fn main() {

let f = File::open("hello.txt");

  

let f = match f {

Ok(file) => file,

Err(error) => match error.kind() {

ErrorKind::NotFound => match File::create("hello.txt") {

Ok(fc) => fc,

Err(e) => panic!("Error creating file: {:?}", e),

},

oe => panic!("Error opening the file: {:?}", oe),

},

};

}Ï
```

![](assets/Pasted%20image%2020220508224409.png)

![](assets/Pasted%20image%2020220508225003.png)

![](assets/Pasted%20image%2020220508225036.png) 

---

![](assets/Pasted%20image%2020220508225515.png)![](assets/Pasted%20image%2020220508225603.png)

---

### 传播错误
![](assets/Pasted%20image%2020220508225716.png)
![](assets/Pasted%20image%2020220508225913.png)

![](assets/Pasted%20image%2020220508230139.png)

![](assets/Pasted%20image%2020220508230007.png)

成功的话进行下一步，失败的话，中断函数执行，并将错误返回 return

---

![](assets/Pasted%20image%2020220508230318.png)

![](assets/Pasted%20image%2020220508230417.png)

并不是所有的错误都可以被 from 函数处理转换，需要定义的 from 函数返回值是对应的类型才可以转换。

**？ 运算符设置还可以实现链式调用**

![](assets/Pasted%20image%2020220508230652.png)

**？运算符只能用于返回 Result 的函数**
![](assets/Pasted%20image%2020220508230827.png)
![](assets/Pasted%20image%2020220508230857.png)

 ---
main 函数可以指定返回类型，这个时候就可以使用 ？运算符
![](assets/Pasted%20image%2020220508231118.png)


![](assets/Pasted%20image%2020220508231047.png)

## 什么时候应该使用 panic

![](assets/Pasted%20image%2020220508232532.png)

![](assets/Pasted%20image%2020220508232620.png)
![](assets/Pasted%20image%2020220508232645.png)

这里绝对不会出现 panic 因为这个 IP 是合理的
![](assets/Pasted%20image%2020220508232724.png)
![](assets/Pasted%20image%2020220508232842.png)


![](assets/Pasted%20image%2020220508233000.png)

```ad-question
如果编写一个 server，是不是就完全不能出现 panic  了？
```












 












