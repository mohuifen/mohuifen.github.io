title: ARC规则
date: 2016-03-17 16:49:05
categories: iOS
tags: Objective-C高级编程 iOS与OS X多线程和内存管理
---
**作者：秋儿（lvruifei@foxmail.com）**

> 学习笔记 二 之《Objective-C高级编程 iOS与OS X多线程和内存管理》

#####	第一章：自动引用计数

**ARC 规则**


`ARC 所有权修饰符：`

* __strong：

	对对象强引用，变量超出其作用域时被废弃，强引用失效，对象也随之释放（id 类型和对象类型默认为 __strong 修饰符）

* __weak：

<!--more-->

	在持有某对象的弱引用时，若对象被废弃，则此弱引用将自动失效且处于nil 被赋值的状态（空弱引用）。只能用于 iOS5 和 OS X Lion 以上版本，以下可使用 __unsafe_unretained 修饰符来代替。 

* __unsafe_unretained：

	被修饰的变量不属于编译器的内存管理对象，是 __weak 的代替

* __autoreleasing：

	@autoreleasepool 块代替 NSAutoreleasePool 类对象生成，持有，废弃这一范围
	
	__autoreleasing 修饰符 等价于在 ARC 无效时调用对象的 autorelease 方法
	
	编译器会检查方法名是否是以 alloc/new/copy/mutableCopy 开始，不是，则自动将返回值得对象注册到 autoreleasepool
	
	访问 __weak 修饰符的变量时必须访问注册到 autoreleasepool 的对象
	
	id 的指针或对象的指针会默认附加上 __autoreleasing 修饰符
	
	赋值给对象指针时，所有权修饰符必须一致：
	
		NSError *error = nil;
		NSError * __strong *pError = &error;
		
		NSError _weak *error = nil;
		NSError * __weak *pError = &error;
		
	NSRunLoop 等实现不论 ARC 有效还是无效，均能够随时释放注册到 autoreleasepool 中的对象。
	
	ARC 无效时 @autoreleasepool 块 _objc_autoreleasePoolPrint() 均能使用。
	
`ARC 规则：`	

* 不能使用 retain/release/retainCount/autorelease：

	内存管理是编译器的工作

* 不能使用 NSAllocateObject/NSDeallocateObject
* 需遵守内存管理的方法命名规则：

	init 开始的方法返回的对象并不注册到autoreleasepool 上
	
	init 方法返回的对象应为 id 类型或该方法声明类的对象类型，或者是该类的超类型或子类型
	
* 不要显式调用 dealloc
* 使用 @autoreleasepool 块替代 NSAutoreleasePool
* 不能使用区域（NSZone）：

	不管 ARC 是否有效，区域在现在的运行时系统中已单纯的被忽略
	
* 对象型变量不能作为 C 语言结构体（struct/union）的成员：
	
	要把对象型变量加入到结构体成员中时，可强制转换为 void * 或者是附加 __unsafe_unretained 修饰符
	
		struct Data {
			NSMutableArray __unsafe_unretained *array;
		};
	
* 显式转换 "id" 和 "void *"

	在 ARC 无效时，将 void * 变量赋值给 id 变量，调用其实例方法，运行时不会有问题

		//ARC 无效
		id obj = [[NSObject alloc] init];
		void *p = obj;
		id o = p;
		[o release];
		
	在 ARC 有效时，上面的代码会引起编译错误。id 型或对象型变量赋值给 void * 或者逆向赋值时都需要进行特定的转换。如果只想单纯地赋值，则可以使用 “__bridge 转换”
	
		id obj = [[NSObject alloc] init];
		void *p = (__bridge void *)obj;
		id o = (__bridge id)p;

	转换为 void * 的 __bridge 转换，其安全性与赋值给 __unsafe_unretained 修饰符相近，甚至更低。如果管理时不注意赋值对象的所有者，就会因悬垂指针而导致程序崩溃。
	
	__bridge 转换中还有 “__bridge_retained 转换” 和 “__bridge_transfer转换”

	* __bridge_retained 使要转换赋值的变量也持有所赋值的对象， 与retain 相似
	* __bridge_transfer 与上面的相反，被转换的变量所持有的对象在该变量被赋值给转换目标变量后随之释放，与 release 相似
	
	
	Objective-C 对象和 Core Foundation 对象，后者主要使用在用 C 语言编写的 Core Foundation 框架中，使用引用计数的对象。两者区别很小，不同之处只在于由哪一个框架所生成。一旦生成，便能在不同的框架中使用。


		CFMutableArrayRef cgObject = NULL;
		{
			id obj = [[NSMutableArray alloc] init];
			cfObject = CFBridgeRetain(obj);
			CFShow(cfObject);
			printf("retain count = %d\n",CFGetRetainCount(cfObject));// 2
		}
		printf("retain count after scope = %d\n",CFGetRetainCount(cfObject));// 1
		CFRelease（cfObject）;
	
	__bridge_retained 转换 可以代替 CFBridgeRetain，当然，__bridge_transfer 转换也可以替代 CFBridgeRelease

`属性：`

 属性声明的属性   |所有权修饰符 	 
 ---------------|----------------------------
 assin 		    |__unsafe_unretained 修饰符  	
 copy			|__strong 修饰符（但是赋值的是被复制的对象）
 retain			|__strong 修饰符
 strong			|__strong 修饰符
 unsafe_unretained			|__unsafe_unretained 修饰符
 weak			|__weak 修饰符

声明类成员变量时，如果同属性声明中的属性不一致，则会引起编译错误，如：

	// 错误
	id obj;
	@property (nonatomic, weak) id obj;
	
	
	// 正确
	id __weak obj;
	@property (nonatomic, weak) id obj;
	
	// 正确
	id obj;
	@property (nonatomic, strong) id obj;
	

`数组：`

数组超出其变量作用域时，数组中各个附有 __strong 修饰符的变量也随之失效，其强引用消失，对象随之释放。

* calloc 函数分配内存会使分配区域初始化为0
* malloc 函数分配内存后区域没有被初始化0，可用 memset 等函数将内存填充为 0 

`ARC 的实现：`

ARC 是由编译器进行内存管理，但是需要 Objective-C 运行时库的协助

* clang(LLVM 编译器)3.0 以上
* objc4 Objective-C 运行时库 493.9 以上

以下内容待学

`__strong 修饰符：`

`__weak 修饰符：`

`__autoreleasing 修饰符：`


	
