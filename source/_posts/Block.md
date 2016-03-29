title: Block
date: 2016-03-29 19:23:19
categories: iOS
tags: Block
---
**作者：秋儿（lvruifei@foxmail.com）**

`Blocks：`

C 语言的扩充功能。带有自动变量（局部变量）的匿名函数。

匿名函数：不带名称的函数。C 语言的标准不允许存在这样的函数，但可以使用函数的指针来代替直接调用函数。

Blocks 中将该匿名函数部分称为 “Block Literal”，简称 “BlocK”。

“带有自动变量的匿名函数” 这一概念并不仅指 Blocks ，在计算机科学中，也称为闭包（Closure）、lambda 计算（lambda calculus）等。

程序语言		|Block 的名称
------------|------------
C + Blocks  |Block
Smalltalk   |BlocK
Ruby        |Block
LISP        |Lambda
Python      |Lambda
C++ 11      |Lambda
Javascript  |Anonymous function

`Block 语法：`

与 C 语言函数 定义相比有两点不同：

* 没有函数名
* 带有 “^”， 插入记号，便于查找


		^ void (int event) {
			printf("buttonId:%d event=%d\n",i,event);
		}
<!-- more -->
Block 语法的 BN范式：

	Block_literal_expression :: = ^ block_decl compound_statement_body
	block_decl ::=
	block_decl ::= parameter_list
	block_decl ::= type_expression

**^ 返回值类型 参数列表 表达式**

	^init (init count){ return count +1;}
	
Block 语法可省略的几个项目：

* 返回值：如果表达式中有 return 语句，就使用该返回值的类型，拖过没有 return 则使用 void 类型。 

		^(int count){return count + 1;}// 返回 int 型返回值 
* 参数列表：

		^void (void){printf("Blocks\n");}
		^{printf("Blocks\n");}// 与上面代码等价

`Block 类型变量：`

C 语言函数，将所定义函数的地址赋值给函数指针类型变量中。

	int func (ini count) {
		return count + 1;
	}
	int (* funcptr)(int) = &func;

Block 类型变量示例：


	int (^blk)(init);
	
对比可知：声明 Block 类型变量仅仅是将声明函数指针类型变量 的 * 变为 ^

Block 语法将 Block 赋值为Block 类型变量

	int (^blk)(int) = ^(ini count){
		return count +1;
	} 
	
	int (^blk1)(int) = blk;
	int (^blk2)(int);
	blk2 = blk1;
	
	// 将 Block 作为参数
	void func(int (^blk)(init)){}
	
	// 将 Block 作为返回值
	int (^func()(int)) {
		return ^(int count){
		 	return count + 1;
		}
	}

使用 typedef 来解决 函数参数和返回值中使用Block 类型变量太复杂的问题。

	typedef int (^blk_t)(int);
	
	void func(blk_t blk) {}
	
	blk_t func() {}
	
调用函数的方法与使用函数指针变量调用函数的方法相同：

	int result = (*funcptr)(10);
	
	int result = blk(10);
	
指向 Block 类型变量的指针，即 Block 的指针类型变量

	typedef int (^blk_t)(int);
	blk_t blk = ^(int count){ return count +1;};
	blk_t *blkptr = &blk ;
	(*blkptr)(10);
	
`截获自动变量值：`

	int main(){
		int dmy = 256;
		int val = 10;
		const char *fmt = "val = %d\n";
		void (^blk)(void) = ^{printf(fmt,val);};
		
		val = 2；
		fmt ="These values were changed. val = %d\n";
		
		blk();// val = 10;
		return 0;
	}

`__block 说明符：`

若想在 Block 语法表达式中将 Block 语法外声明的自动变量的值进行改写，则该自动变量需加上 __block 说明符。

	__block int val = 0;
	void (^blk)(void) =^{ val = 1};
	blk();
	// val = 1;
	
`截获的自动变量：`

不加 __block 说明符，使用截获的自动变量不会出错，但赋值给截获的自动变量会编译错误：

	id array = [[NSMutableArray alloc] init];
	void (^blk)(void) = ^{
		id obj = [[NSObject alloc] init];
		[array addObject:obj]; // 不会出错
		array = [[NSMutableArray alloc] init]; // 编译错误， 需在外面的array 声明的地方添加 __block 说明符
		
	}
	
在使用 C 语言数组时必须小心使用其指针，在 Blocks 中，截获自动变量的方法并没有实现对 C 语言数组的截获，应使用指针解决。

	// 不正确
	const char text[] = "hello";
	void (^blk)(void) = ^{
		printf("%c\n",text[2]);
	};
	
	// 正确
	const char *text[] = "hello";
	void (^blk)(void) = ^{
		printf("%c\n",text[2]);
	};
	
`Blocks 的实现：`

Block 实际上是作为极普通的 C 语言源代码来处理的。通过支持 Block 的编译器，含有 Block 语法的源代码转换为一般的 C 语言编译器能够处理的源代码，然后被编译。

将含有 Block 语法的源代码变换为 C++ 的源代码。仅使用了 struct 结构， 本质是 C 语言源代码。
	
	clang -rewrite-objc 源代码文件名
	
> 各种实现方式，现在无法理解，之后再补。



