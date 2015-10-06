< [Back](README.md)

Objective-C 编码规范
======================

<a name='TOC'/></a>目录
----
* [命名](#naming)
  * [基本原则](#naming-basic-principle)
  * [命名空间](#namespace)
  * [前缀](#prefix)
  * [书写约定](#writerule)
  * [class与protocol命名](#class-protocol)
  * [头文件](#header-file)
  * [方法名](#naming-method)
  * [函数](#naming-function)
  * [Property及其他](#naming-property)
  * [协议名](#naming-protocol)
  * [通知命名](#naming-notifications)
  * [临时变量命名](#naming-temporary-variable)
  * [常量](#naming-constant)
  * [例外](#naming-exceptions)
  * [大小写](#naming-match-case)
  * [缩写](#abbreviation)
  * [其他](#naming-others)
* [代码格式化](#formatting)
  * [空格](#spaces)
  * [花括号](#braces)
  * [折行](#line-wrap)
* [代码组织](#code-organization)
* [类](#class)
  * [Property attributes](#property-attributes)
* [注释](#comment)
  * [块注释](#block-comment) 
* [其他](#others)
  * [异常](#exception)
* [参考](#reference)

<a name='naming'/></a>命名
-----
### <a name='naming-basic-principle'></a>基本原则

* 1.1 清晰
 尽量清晰又简洁，无法两全时清晰更重要

Code                     | Commentary
-------------------------|-----------
insertObjectatIndex      | √
insertat                 | 不清晰，insert什么？at表示什么？
removeObjectAtIndex      | √ 因为参数指明了移除的对象
removeObject             | √
remove                   | 不清晰，要移除什么？

通常不应缩写名称，即使方法名很长也应完整拼写
```C
 * 你可能认为某个缩写众所周知，但其实未必，特别是你周围的开发者语言文化背景不同时
 * 有一些历史悠久的缩写还是可以使用的
```
Code                     | Commentary
-------------------------|-----------
destinationSelection     | √
destSet                  | 不清晰
setBackgroundColor       | √
setBkgdColor             | 不清晰

API命名避免歧义

* 1.2 一致
尽力保持Cocoa编程接口命名一致
```C
如果有疑惑，请浏览当前头文件或者参考文档
当某个类的方法使用了多态时，一致性尤其重要
不同类里，功能相同的方法命名也应相同
```
Code                                      | Commentary
------------------------------------------|-----------
-(NSInteger)tag                           | 定义在NSView,NSCell,NSControl
-(void)setStringValue:(NSString *)        | 定义在许多Cocoa类里

* 1.3 避免自引用（self Reference）
```C
命名不应自引用
这里的自引用指的是在词尾引用自身
```
Code                                | Commentary
------------------------------------|-----------
NSString                            | √
NSStringObject                      | 自引用，所有NSString都是对象，不用额外声明

```C
Mask与Notification忽略此规则
```
Code                                                      | Commentary
----------------------------------------------------------|-----------
NSUnderlineByWordMark                                     | √
NSTableViewColumnDidMoveNotification                      | √

* 1.4 仿照 Cocoa 风格来，使用长命名风格
* 1.5 变量命名推荐的命名语素顺序是：最开头是命名空间简写，然后越重要、区别度越大的语素越要往前放。经典的结构是：作用范围+限定修饰+类型。例：

```C
extern ushort APIDefaultPageSize;        // 还行，能明白意思了
extern ushort APIDefaultFetchPageSize;   // 加上些限定更好一些
extern ushort APIFetchPageSizeDefault;   // 再好些，把重要的往前放

YHToolbarComment    // 不推荐
YHCommentToolbar    // OK，把类型（toolbar）置后
```
### <a name='prefix'></a>前缀
前缀是编程接口命名的重要部分，它们区分了软件的不同功能区域：
* 前缀可以防止第三方开发者与Apple的命名冲突
  * 同样可以防止Apple内部的命名冲突
* 前缀有指定格式
  * 它由二到三个大写字母组成，不使用下划线和子前缀
* 命名类、协议、函数、常量和typedef结构体时使用前缀
  * 方法名不使用前缀（因为它存在于特定类的命名空间中）
  * 结构体字段不使用前缀
 
### <a name='writerule'></a>书写约定
在命名API元素时， 使用驼峰命名法（如runTheWordsTogether），并注意以下书写约定：

* 方法名
  * 小写第一个字母，大写之后所有单词的首字母，不使用前缀
  * 如果方法名以一个众所周知的大写缩略词开始，该规则不适用

如TIFFRepresentation (NSImage)
```Objective-C
fileExistsAtPath:isDirectory:
```
* 函数及常量名
  * 使用与其关联类相同的前缀，并大写首字母

```Objective-C
NSRunAlertPanel
NSCellDisabled
```
标点符号
* 由多个单词组成的名称，别使用标点符号作为名称的一部分
  * 分隔符（下划线、破折号等）也不能使用
* 避免使用下划线作为私有方法的前缀，Apple保留这一方式的使用
  * 强行使用可能会导致命名冲突，即Apple已有的方法被覆盖，这会导致灾难性后果
  * 实例变量使用下划线作为前缀还是允许的


### <a name='class-protocol'></a>class-protocol命名

class
* class的名称应该包含一个名词，用以表明这个类是什么（或者做了什么），并拥有合适的前缀
* 如NSString、NSDate、NSScanner、UIApplication、UIButton

不关联class的protocol
* 大多数protocol聚集了一堆相关方法，并不关联class
* 这种protocol使用ing形式以和class区分开来

Code                     | Commentary
-------------------------|-----------
NSLocking                | √
NSLock                   | 不好，看起来像一个class


关联class的protocol
* 一些protoco聚集了一堆无关方法，并试图与某个class关联在一起，由这个class来主导
  * 这种protocol与class同名 如NSObject protocol


### <a name='header-file'></a>头文件
头文件的命名极其重要，因为它可以指出头文件包含的内容：

* 声明一个孤立的class或protocol
  * 将声明放入单独的文件
  * 使头文件名与声明的class/protocol相同

Header file                  | Declares
-----------------------------|-----------
NSLocale.h                   | 包含NSLocale class

* 声明关联的class或protocol
  * 将关联的声明（class/category/protocol）放入同一个头文件
  * 头文件名与主要的class/category/protocol相同

Header file                  | Declares
-----------------------------|-----------
NSString.h                   | 包含NSString 和 NSMutableString classes
NSLock.h                     | 包含NSLocking protocol 和 NSLock,NSConditionLock,NSRecursiveLock classes


* Framework头文件
  * 每个framework都应该有一个同名头文件
  * Include了框架内其他所有头文件

Header file                  | Declares
-----------------------------|-----------
Foundation.h                 | Foundation.framework

* 添加API到另一个framework
  * 如果要在一个framework中为另一个framework的class catetgory添加方法，加上单词“Additions” 
  * 如Application Kit的NSBundleAdditions.h 文件

* 关联的函数、数据类型
  * 如果你有一组关联的函数、常量、结构体或其他数据类型，将它们放到一个名字合适的头文件中
  * 如Application Kit的NSGraphics.h 



### <a name='namespace'></a>命名空间
* 类名、protocols、C 函数、常量、结构体和枚举应带有命名空间前缀；
* 类方法不要带前缀，结构体字段也不要带前缀

Prefix                       | Cocoa Framework
-----------------------------|-----------
NS                           | Foundation
NS                           | Application Kit
AB                           | Address Book
IB                           | Interface Builder

### <a name='naming-method'></a>方法名



* 以小写字母开始，之后单词的首字母大写。
  1. 以众所周知的缩写开始可以大写，如TIFF、PDF
  2. 私有方法可以加前缀

* 如果方法代表对象接收的动作，以动词开始
  * 不要使用 do 或 does 作为名字的一部分，因为助动词在这里很少有实际意义
  * 同样的，也别在动词之前使用副词和形容词
  1. - (void)invokeWithTarget:(id)target;
  2. - (void)selectTabViewItem:(NSTabViewItem *)tabViewItem

* 如果方法返回接收者的属性，以 接收者 + 接收的属性 命名
  * 除非间接返回多个值，否则不要使用 get 单词（为了与accessor methods区分）

Code                         | Commentary
-----------------------------|-----------
-(NSSize)cellSize            | √
-(NSSize)calcCellSize        | x
-(NSSize)getCellSize         | x

在所有参数之前使用关键字

Code                                                                                      | Commentary
------------------------------------------------------------------------------------------|-----------
-(void)sendAction:(SEL)aSelector    toObject:(id)anObject     forAllCells:(BOOL)flag;     |√
-(void)sendAction:(SEL)aSelector    :(id)anObject     :(BOOL)flag;                        |x

确保参数之前的关键字充分描述了参数

Code                                               | Commentary
---------------------------------------------------|-----------
-(id)viewWithTag:(NSInteger)aTag                   |√
-(id)taggedView:(int)aTag                          |x

创建自定义 init 方法时，记得指明关联的元素

Code                                                    | Commentary
--------------------------------------------------------|-----------
-(id)initWithFrame:(CGRect)frameRect                    |√
-(id)initWithFrame:(NSRect)frameRect  mode:(int)aMode   cellClass:(Class)factoryid    numberOfRows:(int)rowsHigh   numberOfColums:(int) colsWide;                          |√

不要使用 and 来连接作为接收者属性的关键字
* 虽然下面的例子使用 and 看似不错，但是一旦参数非常多时就容易出现问题

Code                                                                                                    | Commentary
--------------------------------------------------------------------------------------------------------|-----------
-(int)runModeForDirectory:(NSString *)path   file:(NSString *)name    types:(NSArray *)fileTypes        |√
-(int)runModeForDirectory:(NSString *)path  andFile:(NSString *)name  andTypes:(NSArray *)fileTypes     |x

* 除非方法描述了两个独立的操作，才使用 and 来连接它们

Code                                                                                                             | Commentary
-----------------------------------------------------------------------------------------------------------------|----------
-(BOOL)openFile:(NSString *)fullPath      withApplication:(NSString *)appname      andDeactivate:(BOOL)flag      |√


* 以 `alloc`、`copy`、`init`、`mutableCopy`、`new` 开头的方法要注意，它们会改变ARC的行为。[^1]


2.getter和setter方法（Accessor Methods）
* 如果property表示为名词，格式如下
  * - (type)noun;
  * - (void)setNoun:(type)aNoun; 
  * - (BOOL)isAdjective;

  1.- (NSString *)title;
  2.- (void)setTitle:(NSString *)aTitle;


* 如果property表示为形容词，格式如下
  * - (BOOL)isAdjective;
  * - (void)setVerbObject:(BOOL)flag;    

  1.- (BOOL)isEditable; 
  2.- (void)setEditable:(BOOL)flag;


* 如果property表示为动词，格式如下（动词用一般现在时）
  * - (BOOL)verbObject;
  * - (void)setVerbObject:(BOOL)flag;

  1.- (BOOL)showsAlpha;
  2.- (void)setShowsAlpha:(BOOL)flag;


不要把动词的过去分词形式当作形容词使用 

Code                                              | Commentary
--------------------------------------------------|-----------
-(void)setAcceptsGlyphInfo:(BOOL)flag;            | √
-(BOOL)acceptsGlyphInfo;                          | √
-(void)setGlyphInfoAccepted:(BOOL)flag;           | x
-(BOOL)glyphInfoAccepted;                         | x

你可能使用情态动词（can、should、will等）来增加可读性，不过不要使用 do或 does

Code                                              | Commentary
--------------------------------------------------|-----------
-(void)setCanHide:(BOOL)flag;                     | √
-(BOOL)canHide;                                   | √
-(void)setShouldCloseDocument:(BOOL)flag;         | √
-(BOOL)shouldCloseDocument;                       | √
-(void)setDoesAcceptGlyphInfo:(BOOL)flag;         | x
-(BOOL)doesAcceptGlyphInfo;                       | x


只有方法需要间接返回多个值的情况下才使用 get
像这种接收多个参数的方法应该能够传入nil，因为调用者未必对每个参数都感兴趣

Code                                                                                        | Commentary
--------------------------------------------------------------------------------------------|-----------
-(void)getLineDash:(float *)pattern   count:(int *)count   phase:(float *)phase;            | √

* 以 `get`、`set` 开头的方法有特殊的意义，不要随意定义。
  1. set 是属性默认的设置方法，如果函数不是为了设置类成员，则不要用 `set` 开头，可用 `setup` 替代。
  2. get 和属性方法无关，但在 Cocoa 中，其标准行为是通过引用传值，而不是直接返回结果的。欲获取变量，直接以变量名为名，如：`userInfomation`，而不是 `getUserInfomation`。

例：

```Objective-C
// OK
- (NSString *)name;

// 糟糕，应用上面的写法
- (NSString *)getName;

// OK，但极少使用
- (void)getName:(NSString **)buffer range:(NSRange)inRange;


// OK
- (NSSize)cellSize;

// 糟糕，应用上面的写法
- (NSSize)calcCellSize;
 

// 对 controller 做一般设置，OK
- (void)setupController;

// 列出具体设置的内容，更好
- (void)setupControllerObservers;

// 糟糕，set 专用于设置属性
- (void)setController;
```

```Objective-C
// 来自官方文档
insertObject:atIndex:    // OK
insert:at:               // 不清晰，插入了什么？at 具体指哪里？
removeObjectAtIndex:     // OK
removeObject:            // OK
remove:                  // 糟糕，什么被移除了？
```

<a></a>3.Delegate方法
以发送消息的对象开始

省略了前缀的类名和首字母小写
```Objective-C
- (BOOL)tableView:(NSTableView *)tableView shouldSelectRow:(int)row; 
- (BOOL)application:(NSApplication *)sender openFile:(NSString *)filename;
```
以发送消息的对象开始的规则不适用下列两种情况

只有一个sender参数的方法

```Objective-C
- (BOOL)applicationOpenUntitledFile:(NSApplication *)sender;
```
响应notification的方法（方法的唯一参数就是notification）

```Objective-C
- (void)windowDidChangeScreen:(NSNotification *)notification;
```
使用单词 did 和 will 来通知delegate

did 表示某些事已发生
will 表示某些事将要发生
```Objective-C
- (void)browserDidScroll:(NSBrowser *)sender;
- (NSUndoManager *)windowWillReturnUndoManager:(NSWindow *)window;
```

询问delegate是否可以执行某个行为时可以使用 did 或 will，不过 should 更完美  

```Objective-C
- (BOOL)windowShouldClose:(id)sender;
```

4.集合方法   

为了管理集合中的元素，集合应该有这几个方法

```Objective-C
- (void)addElement:(elementType)anObj;
- (void)removeElement:(elementType)anObj;
- (NSArray *)elements;

- (void)addLayoutManager:(NSLayoutManager *)obj;
- (void)removeLayoutManager:(NSLayoutManager *)obj;
- (NSArray *)layoutManagers;
```

如果集合是无序的，返回一个NSSet比NSarray更好

如果需要在集合中的特定位置插入元素，使用类似下面的方法

```Objective-C
- (void)insertLayoutManager:(NSLayoutManager *)obj atIndex:(int)index; 
- (void)removeLayoutManagerAtIndex:(int)index;
```

其他集合方法示例

```Objective-C
- (void)addChildWindow:(NSWindow *)childWin ordered:(NSWindowOrderingMode)place;
- (void)removeChildWindow:(NSWindow *)childWin;
- (NSArray *)childWindows;
- (NSWindow *)parentWindow;
- (void)setParentWindow:(NSWindow *)window;
```

5.方法参数 

参数名以小写字母开始，之后的单词首字母大写
```Objective-C
如：removeObject:(id)anObject
```
别使用 ”pointer” 或 ”ptr” 命名
参数类型里就已表明它是否是一个指针
避免只有一到二个字母的参数名
避免只有几个字母的缩写
```Objective-C
...action:(SEL)aSelector
...alignment:(int)mode
...atIndex:(int)index
...content:(NSRect)aRect
...doubleValue:(double)aDouble 
...floatValue:(float)aFloat 
...font:(NSFont *)fontObj  
...frame:(NSRect)frameRect 
...intValue:(int)anInt
...keyEquivalent:(NSString *)charCode
...length:(int)numBytes
...point:(NSPoint)aPoint
...stringValue:(NSString *)aString
...tag:(int)anInt
...target:(id)anObject
...title:(NSString *)aString
```

6.私有方法

不要使用下划线作为私有方法的前缀，Apple保留这一使用方式

因为若是你的私有方法名已被Apple使用，覆盖它将会产生极难追踪的BUG

如果继承自大型Cocoa框架（如UIView），请确保子类的私有方法名与父类不一样

可以为私有方法加一个前缀，如公司名或项目名：XX_

例如你的项目叫做Byte Flogger，那么前缀可能是：BF_addObject

总之，为子类的私有方法添加前缀是为了不覆盖其父类的私有方法


### <a name='naming-function'></a>函数

* 函数的命名类似方法，但有两点要注意
  * 你使用的类和常量拥有相同的前缀
  * 前缀后的首字母大写


许多函数名以描述其作用的动词开始
```Objective-C
NSHighlightRect
NSDeallocateObject
```
查询属性的函数有进一步的命名规则

如果函数返回首个参数的属性，省略动词
```Objective-C
unsigned int NSEventMaskFromType(NSEventType type) 
float NSHeight(NSRect aRect)
```
如果通过reference返回了值，使用 “Get”

```Objective-C
const char *NSGetSizeAndAlignment(const char *typePtr, unsigned int *sizep, unsigned int *alignp)
```
如果返回的是boolean值，应该灵活使用动词 

```Objective-C
BOOL NSDecimalIsNotANumber(const NSDecimal *decimal)
```

### <a name='naming-property'></a>Property及其他

1.1 Property

Property命名规则与第二章accessor methods一样（因为两者紧密联系）

如果property表示为一个名词或动词，格式如下

@property (…) 类型 名词/动词 ;

```Objective-C
@property (strong) NSString *title;
@property (assign) BOOL showsAlpha;
```
如果property表示为一个形容词

可省略 ”is” 前缀
但要指定getter方法的惯用名称
```Objective-C
@property (assign, getter=isEditable) BOOL editable;
```
1.2 实例变量

通常不应该直接访问实例变量
```Objective-C
init、dealloc、accessor methods等方法内部例外
```
实例变量以下划线 “_” 开始

确保实例变量描述了所存储的属性

```Objective-C
@implementation MyClass {
   BOOL _showsTitle;
}
```
如果想要修改property的实例变量名，使用 @synthesize语句

```Objective-C
@implementation MyClass
@synthesize showsTitle=_showsTitle;
```
为一个class添加实例变量时，有几点需要注意：

避免声明公有实例变量
开发者关注的应该是对象接口，而不是其数据细节
你可以通过使用property来避免声明实例变量
如果需要声明实例变量，指定关键字@private 或 @protected
如果你希望子类可以直接访问某个实例变量，使用 @protected 关键字
如果一个实例变量是某个类可访问的属性，确保写了accessor methods
如果有可能，还是使用property


### <a name='naming-protocol'></a>协议名
好的协议名应能立刻让人分辨出这不是一个类名，除了以常用的 delegate、dateSource 做结尾外，还可以使用 …ing 这种形式，如：`NSCoding`、`NSCopying`、`NSLocking`。


### <a name='naming-notifications'></a>通知命名
基本命名格式是：`[与通知相关的类名] + [Did | Will] + [UniquePartOfName] + Notification`，<br/>
`[Name of associated class] + [Did | Will] + [UniquePartOfName] + Notification` 例如：

```Objective-C
NSApplicationDidBecomeActiveNotification
NSWindowDidMiniaturizeNotification
NSTextViewDidChangeSelectionNotification
NSColorPanelColorDidChangeNotification
```

### <a name='naming-temporary-variable'></a>临时变量命名
* 临时变量可以写得很短，如 i、k、vc 这样；
* 临时变量可以使用匈牙利前缀，但数据类型不可以作为前缀：

```C
// OK
wCell, vcMaster, vToolbar

// 糟糕，数据类型作为前缀
bool_switchState, floatBoxHeight
```

推荐的前缀：

前缀 | 含义
-----|-----
ix   | 序号，起始为0
in   | 序号（自然数范围），起始为1
if   | 类型为浮点的“序号”
x    | 坐标
y    | 坐标
w    | 宽度
h    | 高度
vc   | 视图控制器
v    | 视图

### <a name='naming-constant'></a>常量
2.1 枚举常量

使用枚举来关联一组integer常量  

枚举常量和typedef遵循函数的命名规范，下面的例子是 NSMatrix.h

```Objective-C
typedef enum _NSMatrixMode {
    NSRadioModeMatrix            = 0,
    NSHighlightModeMatrix       = 1,           
    NSListModeMatrix            = 2, 
    NSTrackModeMatrix           = 3
} NSMatrixMode;
```
你可以为bit masks之类的东西创建一个匿名枚举 

```Objective-C
enum { 
    NSBorderlessWindowMask       = 0, 
    NSTitledWindowMask           = 1 << 0,
    NSClosableWindowMask         = 1 << 1,
    NSMiniaturizableWindowMask   = 1 << 2, 
    NSResizableWindowMask         = 1 << 3
};
```

2.2 使用const关键字的常量 
对于常量，尽量使用const 常量，不要使用宏

在方法体内使用
```Objective-C
static const CGFloat KCellHight = 126.f;
```
在类文件中使用, 在.m 文件中

```Objective-C
const CGFloat KXRBtnSendHeight = 44;
```

如果需要提供给外部使用，使用 extern 修饰：

只需要在.h 中使用 extern 外部声明即可

```Objective-C
extern const CGFloat KXRBtnSendHeight;/**< send按钮高度 */
```

使用const关键字来创建一个float常量

你可以使用const关键字来创建一个与其他常量不相关的integer常量，否则，使用枚举

使用const关键字的常量也遵循函数的命名规则 

```Objective-C
const float NSLightGray;
```
2.3 其他常量类型  

通常不应使用 #define 预编译指令来创建常量
integer常量，使用枚举
float常量，使用 const 修饰符
对 #define 预编译指令，大写所有字母
比如 DEBUG 判断
```Objective-C
#ifdef DEBUG
```
注意宏命令的字首和字尾都有双下划线 
```Objective-C
__MACH__
```
定义NSString常量来作为Notification和Key值
这样做可以有效防止拼写错误
```Objective-C
APPKIT_EXTERN NSString *NSPrintCopies;
```


除以上规则约定外，其他常量约定了以下前缀：

前缀   | 含义
-------|-----
k      | 宏常量
CDEN   | Core Data entity name
UDk    | User Default key
APIURL | 接口地址

另见：[常量管理](#constant)

### <a name='naming-exceptions'></a>例外

Exception的格式

'[Prefix] + [UniquePartOfName] + Exception'

```Objective-C
NSColorListIOException
NSColorListNotEditableException
NSDraggingException  
NSFontUnavailableException
NSIllegalSelectorException
```


### <a name='naming-match-case'></a>大小写
* 类名采用大驼峰（`UpperCamelCase`）
* 类成员、方法小驼峰（`lowerCamelCase`）
* 局部变量大小写首选小驼峰，也可使用小写下划线的形式（`snake_case`）
* C函数的命名用大驼峰

### <a name='abbreviation'/></a>缩写
可以使用广泛使用的缩写，如 `URL`、`JSON`，并且缩写要大写。但像将`download`简写为`dl`这种是不可以的。


```Objective-C
// OK
ID, URL, JSON, WWW

// 糟糕
id, Url, json, www

destinationSelection       // OK
destSel                    // 糟糕
setBackgroundColor:        // OK
setBkgdColor:              // 糟糕
```

设计编程接口时通常不应使用缩写，但下列已被广泛使用的缩写名称除外
标准C库中的缩写名，如：alloc、init
参数名可自由使用缩写，如：imageRep、col、obj、otherWin

缩写         | 全称
-------------|-----
alloc        | Allocate
alt          | Alternate
app          | Application
calc         | Calculate
dealloc      | Deallocate
func         | Function
horiz        | Horizontal
info         | Infomation
init         | Initialize
int          | Integer
max          | Maximum
msg          | Message
nib          | interface Builder archive
pboard       | Pasteboard
rect         | Rectangle
Rep          | Representation
temp         | Temporay
vert         | Vertical



缩写         | 全称
-------------|-----
ASCII        | American Standard Code for Information Interchange
PDF          | Portable Document Format
XML          | Extensible Markup Language
HTML         | HyperTextMarkup Language
URL          | Uninform Resource Locator
RTF          | Rich TextFormat
HTTP         | HyperTextTransfer Protocol
TIFF         | Tagged Image File Format
JPG/JPEG     | Joint Photographbic Experts GROUP
PNG          | Portable Network Graphic Format
GIF          | Graphic Interchange Format
LZW          | Lempel Ziv Welch
ROM          | Read-Only Memory
RGB          | R(red), G(green), B(blue)
CMYK         | C: Cyan  M:Magenta Y:Yellow  K:Key Plate(black)
MIDI         | Musical Instrument Digital Interface
FTP          | File Transfer Protocol


### <a name='naming-others'></a>其他
i，j专用于循环标号

为私有方法命名不要直接以“_”开头，而应以“命名空间_”开头。

<a name='formatting'/></a>代码格式化
-----


### 代码对齐
写参数超过2个的方法时，大家按照冒号对个齐，会好看很多，我也不是逼你这样做，你就随意感受下吧

![](http://static.oschina.net/uploads/space/2015/0514/181511_1zw7_735123.png)  


### <a name='spaces'></a>空格
类方法声明在方法类型与返回类型之间要有空格。

```Objective-C
// 糟糕
-(void)methodName:(NSString *)string;

// OK
- (void)methodName:(NSString *)string;
```

条件判断的括号内侧不应有空格。

```C
// 糟糕
if ( a < b ) {
    // something
}

// OK
if (a < b) {
    // something
}
```

关系运算符（如 `>=`、`!=`）和逻辑运算符（如 `&&`、`||`）两边要有空格。

```C
// OK
(someValue > 100)? YES : NO
```

二元算数运算符两侧是否加空格不确定，根据情况自己定。一元运算符与操作数之前没有空格。

多个参数逗号后留一个空格（这也符合正常的西文语法）。


### <a name='braces'></a>花括号
方法的花括号推荐另起一行。方法内部需要写在一行。

```Objective-C
  - (void)methodName:(NSString *)string {
   ↑空格                              ↑空格，推荐花括号在一行
      if () {
   空格↑  ↑空格，花括号不要另起一行
      }
      else {
 要换行↑ ↑空格，花括号不要另起一行
      }    
  }
```

**动机**
> Xcode 默认的花括号位置是这样的：方法内部的各种补全都是在同一行的；方法定义的比较混乱，默认模版另起一行，但从 Interface Builder 中连线生成的方法在同一行的。
> 
> 考虑到 Xcode 的默认行为，方法内部要另起一行，方法所在行不强制定死。另外，模版可以定制，而 IB 生成的代码不可定制，所以不另起一行的写法优先。
>
> 另起一行的写法在代码折叠后非常难看。
> 

### <a name='line-wrap'></a>折行
与多数其他规范不同，不建议手动折行。

**动机**
> 手动折行的效果严重宽度依赖于窗口宽度——窗口过宽浪费宝贵的屏幕空间，较窄时可能无法阅读。而且 Xcode 自动折行的效果还是不错的。

<a name='code-organization'></a>代码组织
----
* 函数长度（行数）不应超过2/3屏幕，禁止超过70行。
: 例外：对于顺序执行的初始化函数，如果其中的过程没有提取为独立方法的必要，则不必限制长度。
* 单个文件方法数不应超过30个
* 不要按类别排序（如把IBAction放在一块），应按任务把相关的组合在一起
* 禁止出现超过两层循环的代码，用函数或block替代。

尽早返回错误：

```Objective-C
// 为了简化示例，没有错误处理，并使用了伪代码

// 糟糕的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    Task *aTask;
    if ([path isURL]) {
        if ([fileManager isWritableFileAtPath:path]) {
            if (![taskManager hasTaskWithPath:path]) {
                aTask = [[Task alloc] initWithPath:path];
            }
            else {
                return nil;
            }
        }
        else {
            return nil;
        }
    }
    else {
        return nil;
    }
    return aTask;
}

// 改写的例子
- (Task *)creatTaskWithPath:(NSString *)path {
    if (![path isURL]) {
        return nil;
    }
    
    if (![fileManager isWritableFileAtPath:path]) {
        return nil;
    }
    
    if ([taskManager hasTaskWithPath:path]) {
        return nil;
    }
    
    Task *aTask = [[Task alloc] initWithPath:path];
    return aTask;
}
```



<a name='class'/></a>类
----
禁止在类的 interface 中定义任何 iVar 成员，只允许使用属性，但可以在特定情形中使用属性生成的 iVar。

尽量总是使用点操作符访问属性，而不是属性生成的 iVar 变量。以下情形除外：

* 明确要避免修改产生 KVO 通知的；
* 需重写属性 getter 或 setter 的；
* 性能分析确定使用属性会导致性能不可接受的；
* 多线程环境中，为防止互斥一次进行多个修改的；
* init、dealloc 方法中。

动机
> 如果使用 iVar，很多情况要特殊处理，容易出错。总是使用成员，规则简单，不易出问题。
> 
> 直接访问 iVar 的 block 会 retain iVar 所属的对象，这点很容易被忽略
>
> 定义和使用 iVar 都会产生编译警告，只不过默认设置没启用这两个警告

### <a name='property-attributes'></a>Property attributes

什么时候使用 copy？

* block 属性要定义成 copy。
* 当一个属性赋值后不期望改变时应当用 copy，最常见的类型如 NSString、NSURL。可变类型的成员，如 NSMutableArray、NSMutableDictionary 不能定成 copy 的。

相关 Demo 可在 https://github.com/BB9z/PropertyTest 获得。

<a name='constant'></a>常量
----
除非调试用的、控制不同编译模式行为的常量可用宏外，其他常量不得用宏定义。

常量定义示例：

```
// 头文件
extern ushort APIFetchPageSizeDefault;    // 无const，可在外部修改

// 实现文件
ushort APIFetchPageSizeDefault = 10;
```

<a name='comment'></a>注释
----
尽量让代码可以自表述，而不是依赖注释。

> 注释应该表达那些代码没有表达以及无法表达的东西。如果一段注释被用于解释一些本应该由这段代码自己表达的东西，我们就应该将这段注释看成一个改变代码结构或编码惯例直至代码可以自我表达的信号。我们重命名那些糟糕的方法和类名，而不是去修补。我们选择将长函数中的一些代码段抽取出来形成一些小函数，这些小函数的名字可以表述原代码段的意图，而不是对这些代码段进行注释。尽可能的通过代码进行表达。你通过代码所能表达的和你想要表达的所有事情之间的差额将为注释提供了一个合理的候选使用场合。对那些代码无法表达的东西进行注释，而不要仅简单地注释那些代码没有表达的东西。”[^2]

虽说好的代码不用注释,但是那得是好的代码..好记性不如烂笔头，好好写注释可以给自己和自己的小伙伴省下很多时间.

注释都是// 或是/* 注释 */ ，这样的通用注释不做多说明，这里介绍一些稍带技巧的注释：

####参数的注释

```Objective-C
UIButton *btnSend;/**< 发送按钮 */
```

在调用时可以得到提示，在内容比较多时比较好用，我有时候脑子短路要想好一会才能记得当初定义的变量是做什么用的。

![](http://static.oschina.net/uploads/space/2015/0514/161031_B8SF_735123.png)  

####方法的注释
如果你的方法是没有参数的，只需要写一句注释，那只需要在方法前加注释就行了
```Objective-C
UIButton *btnSend;/**< 发送按钮 */
```
type 1
```Objective-C
/** table 相关设置 */
-(void)configTable{}
```

type 2（插件：VVDocument）使用此插件可以很便捷的为自己的代码添加注释
```Objective-C
/*!
 *  @author joanfen, 15-05-14 12:05:22
 *
 *  @brief  相关设置
 */
-(void)configTable{}
```
这样的注释 在你调用时会显示你所添加注释，如图

![](http://static.oschina.net/uploads/space/2015/0514/121231_jCac_735123.png)  

有参数的注释
```Objective-C
/*!
 *  @Author joanfen, 15-05-13 14:01:51
 *  @method POST
 *  @see XRClass
 *  @brief  链接解析
 *
 *  @param linkAddressStr 链接地址
 */

```
大家使用VVDocument 的插件来写注释就对了。这样的注释自己写起来太费事，没有插件我真不愿意写。

####方法分区

```Objective-C
#pragma mark - <注释，也可不写，没有注释时就只显示一条分割线>
#pragma mark 注释
```
区别：带 - 的会显示一条分割线

![](http://static.oschina.net/uploads/space/2015/0514/162252_0ZSV_735123.png)  

便于简单快速的查找方法


####添加提示信息
```Objective-C
#error <提示信息>
```
如果加上这样的错误提示，在 Build 时 XCode 会提示编译错误：

![](http://static.oschina.net/uploads/space/2015/0514/162847_OcyP_735123.png)  
![](http://static.oschina.net/uploads/space/2015/0514/162847_FcKX_735123.png)  

在某部分代码没有完成，而且如果提交会导致问题时可以加上这样的提示信息来提醒自己。

如果你觉得只是想提醒自己来完成，并不需要加上红色的 error 信息，你可以尝试使用

```Objective-C
#warning <提示信息>
```
这样的话在编译时提示信息是这样的
![](http://static.oschina.net/uploads/space/2015/0514/163150_wHPF_735123.png) 

#####使用场景
在替换某个类时，需要删除原有的代码，再进行替换，每次删除一个我就加一行同样的 warning，最后新的写好之后，搜索这行 waring，将调用方法填充，大大提高效率；

UI 写好了，数据部分还没有好，将逻辑梳理好，方法写好，加上 warning ，包含 deadline，需要完成的工作，在后台数据可测时，再来完成，不写的话有时候真的脑子短路，有时候半天都不记得当时是准备怎么弄的，尤其在开发量大的时候，原谅我是一个容易脑子短路的人，-_-。


### <a name='block-comment'></a>块注释
方法内部禁止使用块注释。除非要临时注释大段代码，一般情况总应使用行注释。

**动机**
> 因为块注释不能正确嵌套。

<a name='others'></a>其他
------

### <a name='exception'></a>异常
* 作为被调用模块的维护者，当被调用不当时（参数有问题、不和时宜），如何处理需要考虑（抛出异常还是返回错误状态）；
* 不要依赖 try catch，它不是代替你做检查、填补遗漏的工具。


<a name='reference'></a>参考
------
* [Apple Coding Guidelines for Cocoa](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* [Google Objective-C Style Guide](https://google-styleguide.googlecode.com/svn/trunk/objcguide.xml?showone=Line_Length#Line_Length)
* [Objective-C开发编码规范：4大方面解决开发中的规范性问题](http://www.cocoachina.com/ios/20150507/11780.html)
* [我是如何收拾代码的](http://my.oschina.net/joanfen/blog/415058)
* [iOS开发，事半功倍基本心得](http://www.cocoachina.com/ios/20150615/12125.html)
* [Coding Guidelines for Cocoa](http://developer.apple.com/library/ios/#documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html)
* https://github.com/github/objective-c-conventions
* https://github.com/jverkoey/iOS-Best-Practices
* [你们是如何为 View Controller 的变量命名的呢？ - V2EX](//www.v2ex.com/t/25732)
* [代码大全(第2版) - 亚马逊](http://www.amazon.cn/dp/B0061XKRXA)

<a name='footnote'></a>
[^1]: [再谈ARC - 苹果核](http://pingguohe.net/2012/06/22/talk_arc_again/)
[^2]: [只对代码无法表达的东西写注释 - Tony Bai](http://bigwhite.blogbus.com/logs/125602412.html)

