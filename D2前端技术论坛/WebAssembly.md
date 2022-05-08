在浏览器中运行C /C++ 程序
甚至能够在浏览器中运行PS程序

1. 浏览器中运行其他代码安全隔离
2. 这个也能用于后端，作为容器隔离保证安全性


在serverless 中取代容器的选择

 CDN 网络上广泛使用 WebAssembly
 
**全栈的轻量级容器，不只是虚拟机**
- 安全性
	- 网络安全
	- 内存安全
- 可调度行


WebAssembly 具有访问系统的功能。

WebSssembly 与 Docker 相似之处
- runtime 隔离
	- 安全
		- docker 安全性并不够
		- wasm 一开始就是为了在非常开发的环境下进运行的，设计之初就考虑了开放系统的安全性
			- 开放系统的hacker会比较多
	- 资源管理
- 可移植
	- wasm 优于 docker
- 容易部署
	- 不需要考虑底层硬件、操作系统。

**wasm 更快，更轻**
docker 需要模拟一个操作系统，wasm 只需要一个进程启动。
![[Pasted image 20211223003748.png]]
启动速度要快很多，大小一集运行时的速度也要更加优秀

![[Pasted image 20211223003904.png]]

wasm比起docker抽象程度更
高

![[Pasted image 20211227225309.png]]








	