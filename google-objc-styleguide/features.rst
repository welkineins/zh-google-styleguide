Cocoa 和 Objective-C 特性
==============================


成員變量應該是 ``@private``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    成員變量應該聲明為 ``@private``

.. code-block:: objc

    @interface MyClass : NSObject {
     @private
      id myInstanceVariable_;
    }
    // public accessors, setter takes ownership
    - (id)myInstanceVariable;
    - (void)setMyInstanceVariable:(id)theVar;
    @end



明確指定構造函數
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    註釋並且明確指定你的類的構造函數。

對於需要繼承你的類的人來說，明確指定構造函數十分重要。這樣他們就可以只重寫一個構造函數（可能是幾個）來保證他們的子類的構造函數會被調用。這也有助於將來別人調試你的類時，理解初始化代碼的工作流程。


重載指定構造函數
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    當你寫子類的時候，如果需要 ``init…`` 方法，記得重載父類的指定構造函數。

如果你沒有重載父類的指定構造函數，你的構造函數有時可能不會被調用，這會導致非常隱秘而且難以解決的 bug。


重載 ``NSObject`` 的方法
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    如果重載了 ``NSObject`` 類的方法，強烈建議把它們放在 ``@implementation`` 內的起始處，這也是常見的操作方法。

通常適用（但不局限）於 ``init...``，``copyWithZone:``，以及 ``dealloc`` 方法。所有 ``init...`` 方法應該放在一起，``copyWithZone:`` 緊隨其後，最後才是 ``dealloc`` 方法。

初始化
~~~~~~~~~~~

.. tip::

    不要在 init 方法中，將成員變量初始化為 ``0`` 或者 ``nil``；毫無必要。

剛分配的對象，默認值都是 0，除了 ``isa`` 指針（譯者註：``NSObject`` 的 ``isa`` 指針，用於標識對象的類型）。所以不要在初始化器裡面寫一堆將成員初始化為 ``0`` 或者 ``nil`` 的代碼。


避免 ``+new``
~~~~~~~~~~~~~~~

.. tip::

    不要調用 ``NSObject`` 類方法 ``new``，也不要在子類中重載它。使用 ``alloc`` 和 ``init`` 方法創建並初始化對象。


現代的 Ojbective-C 代碼通過調用 ``alloc`` 和 ``init`` 方法來創建並 retain 一個對象。由於類方法 ``new`` 很少使用，這使得有關內存分配的代碼審查更困難。

保持公共 API 簡單
~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    保持類簡單；避免 「廚房水槽（kitchen-sink）」 式的 API。如果一個函數壓根沒必要公開，就不要這麼做。用私有類別保證公共頭文件整潔。

與 C++ 不同，Objective-C 沒有方法來區分公共的方法和私有的方法 -- 所有的方法都是公共的（譯者註：這取決於 Objective-C 運行時的方法調用的消息機制）。因此，除非客戶端的代碼期望使用某個方法，不要把這個方法放進公共 API 中。盡可能的避免了你你不希望被調用的方法卻被調用到。這包括重載父類的方法。對於內部實現所需要的方法，在實現的文件中定義一個類別，而不是把它們放進公有的頭文件中。

.. code-block:: objc

    // GTMFoo.m
    #import "GTMFoo.h"

    @interface GTMFoo (PrivateDelegateHandling)
    - (NSString *)doSomethingWithDelegate;  // Declare private method
    @end

    @implementation GTMFoo(PrivateDelegateHandling)
    ...
    - (NSString *)doSomethingWithDelegate {
      // Implement this method
    }
    ...
    @end

Objective-C 2.0 以前，如果你在私有的 ``@interface`` 中聲明了某個方法，但在 ``@implementation`` 中忘記定義這個方法，編譯器不會抱怨（這是因為你沒有在其它的類別中實現這個私有的方法）。解決文案是將方法放進指定類別的 ``@implemenation`` 中。

如果你在使用 Objective-C 2.0，相反你應該使用 `類擴展 <http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/Articles/chapter_4_section_5.html>`_ 來聲明你的私有類別，例如：

.. code-block:: objc

    @interface GMFoo () { ... }

這麼做確保如果聲明的方法沒有在 ``@implementation`` 中實現，會觸發一個編譯器告警。

再次說明，「私有的」 方法其實不是私有的。你有時可能不小心重載了父類的私有方法，因而製造出很難查找的 Bug。通常，私有的方法應該有一個相當特殊的名字以防止子類無意地重載它們。

Ojbective-C 的類別可以用來將一個大的 ``@implementation`` 拆分成更容易理解的小塊，同時，類別可以為最適合的類添加新的、特定應用程序的功能。例如，當添加一個 「middle truncation」 方法時，創建一個 ``NSString`` 的新類別並把方法放在裡面，要比創建任意的一個新類把方法放進裡面好得多。

``#import`` and ``#include``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    ``#import`` Ojbective-C/Objective-C++ 頭文件，``#include`` C/C++ 頭文件。

基於你所包括的頭文件的編程語言，選擇使用 ``#import`` 或是 ``#include``：

* 當包含一個使用 Objective-C、Objective-C++ 的頭文件時，使用 ``#import`` 。
* 當包含一個使用標準 C、C++ 頭文件時，使用 ``#include``。頭文件應該使用 `#define 保護 <http://google-styleguide.googlecode.com/svn/trunk/cppguide.xml?showone=The__define_Guard#The__define_Guard>`_。

一些 Ojbective-C 的頭文件缺少 ``#define`` 保護，需要使用 ``#import`` 的方式包含。由於 Objective-C 的頭文件只會被 Objective-C 的源文件及頭文件包含，廣泛地使用 ``#import`` 是可以的。

文件中沒有 Objective-C 代碼的標準 C、C++ 頭文件，很可能會被普通的 C、C++ 包含。由於標準 C、C++ 裡面沒有 ``#import`` 的用法，這些文件將被 ``#include``。在 Objective-C 源文件中使用 ``#include`` 包含這些頭文件，意味著這些頭文件永遠會在相同的語義下包含。

這條規則幫助跨平台的項目避免低級錯誤。某個 Mac 開發者寫了一個新的 C 或 C++ 頭文件，如果忘記使用 ``#define`` 保護，在 Mac 下使用 ``#import`` 這個頭文件不回引起問題，但是在其它平台下使用 ``#include`` 將可能編譯失敗。在所有的平台上統一使用 ``#include``，意味著構造更可能全都成功或者失敗，防止這些文件只能在某些平台下能夠工作。

.. code-block:: objc

    #import <Cocoa/Cocoa.h>
    #include <CoreFoundation/CoreFoundation.h>
    #import "GTMFoo.h"
    #include "base/basictypes.h"


使用根框架
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    ``#import`` 根框架而不是單獨的零散文件

當你試圖從框架（如 Cocoa 或者 Foundation）中包含若干零散的系統頭文件時，實際上包含頂層根框架的話，編譯器要做的工作更少。根框架通常已經經過預編譯，加載更快。另外記得使用 ``#import`` 而不是 ``#include`` 來包含 Objective-C 的框架。

.. code-block:: objc

    #import <Foundation/Foundation.h>     // good

    #import <Foundation/NSArray.h>        // avoid
    #import <Foundation/NSString.h>
    ...


構建時即設定 ``autorelease``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    當創建臨時對像時，在同一行使用 ``autolease``，而不是在同一個方法的後面語句中使用一個單獨的 ``release``。

儘管運行效率會差一點，但避免了意外刪除 ``release`` 或者插入 ``return`` 語句而導致內存洩露的可能。例如：

.. code-block:: objc

    // AVOID (unless you have a compelling performance reason)
    MyController* controller = [[MyController alloc] init];
    // ... code here that might return ...
    [controller release];

    // BETTER
    MyController* controller = [[[MyController alloc] init] autorelease];


``autorelease`` 優先 ``retain`` 其次
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    給對像賦值時遵守 ``autorelease``之後 ``retain`` 的模式。

當給一個變量賦值新的對象時，必須先釋放掉舊的對象以避免內存洩露。有很多 「正確的」 方法可以處理這種情況。我們則選擇 「``autorelease`` 之後 ``retain``」 的方法，因為事實證明它不容易出錯。注意大的循環會填滿 ``autorelease`` 池，並且可能效率上會差一點，但權衡之下我們認為是可以接受的。

.. code-block:: objc

    - (void)setFoo:(GMFoo *)aFoo {
      [foo_ autorelease];  // Won't dealloc if |foo_| == |aFoo|
      foo_ = [aFoo retain];
    }


``init`` 和 ``dealloc`` 內避免使用訪問器
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    在 ``init`` 和 ``dealloc`` 方法執行的過程中，子類可能會處在一個不一致的狀態，所以這些方法中的代碼應避免調用訪問器。

子類尚未初始化，或在 ``init`` 和 ``dealloc`` 方法執行時已經被銷毀，會使訪問器方法很可能不可靠。實際上，應在這些方法中直接對 ivals 進行賦值或釋放操作。

正確：

.. code-block:: objc

    - (id)init {
      self = [super init];
      if (self) {
        bar_ = [[NSMutableString alloc] init];  // good
      }
      return self;
    }

    - (void)dealloc {
      [bar_ release];                           // good
      [super dealloc];
    }

錯誤：

.. code-block:: objc

    - (id)init {
      self = [super init];
      if (self) {
        self.bar = [NSMutableString string];  // avoid
      }
      return self;
    }

    - (void)dealloc {
      self.bar = nil;                         // avoid
      [super dealloc];
    }


按聲明順序銷毀實例變量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    ``dealloc`` 中實例變量被釋放的順序應該與它們在 ``@interface`` 中聲明的順序一致，這有助於代碼審查。

代碼審查者在評審新的或者修改過的 ``dealloc`` 實現時，需要保證每個 ``retained`` 的實例變量都得到了釋放。

為了簡化 ``dealloc`` 的審查，``retained`` 實例變量被釋放的順序應該與他們在 ``@interface`` 中聲明的順序一致。如果 ``dealloc`` 調用了其它方法釋放成員變量，添加註釋解釋這些方法釋放了哪些實例變量。

``setter`` 應複製 NSStrings
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    接受 ``NSString`` 作為參數的 ``setter``，應該總是 ``copy`` 傳入的字符串。

永遠不要僅僅 ``retain`` 一個字符串。因為調用者很可能在你不知情的情況下修改了字符串。不要假定別人不會修改，你接受的對象是一個 ``NSString`` 對像而不是 ``NSMutableString`` 對象。

.. code-block:: objc

    - (void)setFoo:(NSString *)aFoo {
      [foo_ autorelease];
      foo_ = [aFoo copy];
    }



.. _avoid-throwing-exceptions:

避免拋異常
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    不要 ``@throw`` Objective-C 異常，同時也要時刻準備捕獲從第三方或 OS 代碼中拋出的異常。

我們的確允許 ``-fobjc-exceptions`` 編譯開關（主要因為我們要用到 ``@synchronized`` ），但我們不使用 ``@throw``。為了合理使用第三方的代碼，``@try``、``@catch`` 和 ``@finally`` 是允許的。如果你確實使用了異常，請明確註釋你期望什麼方法拋出異常。

不要使用 ``NS_DURING``、``NS_HANDLER``、``NS_ENDHANDLER``、``NS_VALUERETURN`` 和 ``NS_VOIDRETURN`` 宏，除非你寫的代碼需要在 Mac OS X 10.2 或之前的操作系統中運行。

注意：如果拋出 Objective-C 異常，Objective-C++ 代碼中基於棧的對象不會被銷毀。比如：

.. code-block:: objc

    class exceptiontest {
     public:
      exceptiontest() { NSLog(@"Created"); }
      ~exceptiontest() { NSLog(@"Destroyed"); }
    };

    void foo() {
      exceptiontest a;
      NSException *exception = [NSException exceptionWithName:@"foo"
                                                       reason:@"bar"
                                                     userInfo:nil];
      @throw exception;
    }

    int main(int argc, char *argv[]) {
      GMAutoreleasePool pool;
      @try {
        foo();
      }
      @catch(NSException *ex) {
        NSLog(@"exception raised");
      }
      return 0;
    }

會輸出：

.. ：：

    2006-09-28 12:34:29.244 exceptiontest[23661] Created
    2006-09-28 12:34:29.244 exceptiontest[23661] exception raised


注意：這裡析構函數從未被調用。這主要會影響基於棧的 ``smartptr``，比如 ``shared_ptr``、``linked_ptr``，以及所有你可能用到的 STL 對象。因此我們不得不痛苦的說，如果必須在 Objective-C++ 中使用異常，就只用 C++ 的異常機制。永遠不應該重新拋出 Objective-C 異常，也不應該在 ``@try``、``@catch`` 或 ``@finally`` 語句塊中使用基於棧的 C++ 對象。

nil 檢查
~~~~~~~~~~~~~~

.. tip::

    ``nil`` 檢查只用在邏輯流程中。



使用 ``nil`` 的檢查來檢查應用程序的邏輯流程，而不是避免崩潰。Objective-C 運行時會處理向 ``nil`` 對像發送消息的情況。如果方法沒有返回值，就沒關係。如果有返回值，可能由於運行時架構、返回值類型以及 OS X 版本的不同而不同，參見 `Apple』s documentation <http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/Articles/chapter_2_section_3.html>`_ 。

注意，這和 C/C++ 中檢查指針是否為 ‵‵NULL`` 很不一樣，C/C++ 運行時不做任何檢查，從而導致應用程序崩潰。因此你仍然需要保證你不會對一個 C/C++ 的空指針解引用。

BOOL 若干陷阱
~~~~~~~~~~~~~~~~~~~~~

.. tip::

    將普通整形轉換成 ``BOOL`` 時要小心。不要直接將 ``BOOL`` 值與 ``YES`` 進行比較。

Ojbective-C 中把 ``BOOL`` 定義成無符號字符型，這意味著 ``BOOL`` 類型的值遠不止 ``YES``(1)或 ``NO``(0)。不要直接把整形轉換成 ``BOOL``。常見的錯誤包括將數組的大小、指針值及位運算的結果直接轉換成 ``BOOL`` ，取決於整型結果的最後一個字節，很可能會產生一個 ``NO`` 值。當轉換整形至 ``BOOL`` 時，使用三目操作符來返回 ``YES`` 或者 ``NO``。（譯者註：讀者可以試一下任意的 256 的整數的轉換結果，如 256、512 …）

你可以安全在 ``BOOL``、``_Bool`` 以及 ``bool`` 之間轉換（參見 C++ Std 4.7.4, 4.12 以及 C99 Std 6.3.1.2）。你不能安全在 ``BOOL`` 以及 ``Boolean`` 之間轉換，因此請把 ``Boolean`` 當作一個普通整形，就像之前討論的那樣。但 Objective-C 的方法標識符中，只使用 ``BOOL``。

對 ``BOOL`` 使用邏輯運算符（``&&``，``||`` 和 ``!``）是合法的，返回值也可以安全地轉換成 ``BOOL``，不需要使用三目操作符。

錯誤的用法：

.. code-block:: objc

    - (BOOL)isBold {
      return [self fontTraits] & NSFontBoldTrait;
    }
    - (BOOL)isValid {
      return [self stringValue];
    }


正確的用法：

    .. code-block:: objc

    - (BOOL)isBold {
      return ([self fontTraits] & NSFontBoldTrait) ? YES : NO;
    }
    - (BOOL)isValid {
      return [self stringValue] != nil;
    }
    - (BOOL)isEnabled {
      return [self isValid] && [self isBold];
    }


同樣，不要直接比較 ``YES/NO`` 和 ``BOOL`` 變量。不僅僅因為影響可讀性，更重要的是結果可能與你想的不同。

錯誤的用法：

.. code-block:: objc

    BOOL great = [foo isGreat];
    if (great == YES)
      // ...be great!

正確的用法：

.. code-block:: objc

    BOOL great = [foo isGreat];
    if (great)
      // ...be great!

屬性（Property）
~~~~~~~~~~~~~~~~~~~~~

.. tip::

    屬性（Property）通常允許使用，但需要清楚的瞭解：屬性（Property）是 Objective-C 2.0 的特性，會限制你的代碼只能跑在 iPhone 和 Mac OS X 10.5 (Leopard) 及更高版本上。點引用只允許訪問聲明過的 ``@property``。

命名
^^^^^^

屬性所關聯的實例變量的命名必須遵守以下劃線作為後綴的規則。屬性的名字應該與成員變量去掉下劃線後綴的名字一模一樣。

使用 ``@synthesize`` 指示符來正確地重命名屬性。

.. code-block:: objc

    @interface MyClass : NSObject {
     @private
      NSString *name_;
    }
    @property(copy, nonatomic) NSString *name;
    @end

    @implementation MyClass
    @synthesize name = name_;
    @end


位置
^^^^^^

屬性的聲明必須緊靠著類接口中的實例變量語句塊。屬性的定義必須在 ``@implementation`` 的類定義的最上方。他們的縮進與包含他們的 ``@interface`` 以及 ``@implementation`` 語句一樣。

.. code-block:: objc

    @interface MyClass : NSObject {
     @private
      NSString *name_;
    }
    @property(copy, nonatomic) NSString *name;
    @end

    @implementation MyClass
    @synthesize name = name_;
    - (id)init {
    ...
    }
    @end


字符串應使用 ``copy`` 屬性（Attribute）
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

應總是用 ``copy`` 屬性（attribute）聲明 ``NSString`` 屬性（property）。

從邏輯上，確保遵守 ``NSString`` 的 ``setter`` 必須使用 ``copy`` 而不是 ``retain`` 的原則。


原子性
^^^^^^^^^^

一定要注意屬性（property）的開銷。缺省情況下，所有 ``synthesize`` 的 ``setter`` 和 ``getter`` 都是原子的。這會給每個 ``get`` 或者 ``set`` 帶來一定的同步開銷。將屬性（property）聲明為 ``nonatomic``，除非你需要原子性。


點引用
^^^^^^^^^^

點引用是地道的 Objective-C 2.0 風格。它被使用於簡單的屬性 ``set``、``get`` 操作，但不應該用它來調用對象的其它操作。

正確的做法：

.. code-block:: objc

    NSString *oldName = myObject.name;
    myObject.name = @"Alice";

錯誤的做法：

.. code-block:: objc

    NSArray *array = [[NSArray arrayWithObject:@"hello"] retain];

    NSUInteger numberOfItems = array.count;  // not a property
    array.release;                           // not a property


沒有實例變量的接口
~~~~~~~~~~~~~~~~~~~~~

.. tip::

    沒有聲明任何實例變量的接口，應省略空花括號。

正確的做法：

    @interface MyClass : NSObject
    // Does a lot of stuff
    - (void)fooBarBam;
    @end

錯誤的做法：

    @interface MyClass : NSObject {
    }
    // Does a lot of stuff
    - (void)fooBarBam;
    @end



自動 ``synthesize`` 實例變量
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    只運行在 iOS 下的代碼，優先考慮使用自動 ``synthesize`` 實例變量。

``synthesize`` 實例變量時，使用 ``@synthesize var = var_;`` 防止原本想調用 ``self.var = blah;`` 卻不慎寫成了 ``var = blah;``。


不要synthesize CFType的屬性 CFType應該永遠使用@dynamic實現指示符。 儘管CFType不能使用retain屬性特性，開發者必須自己處理retain和release。很少有情況你需要僅僅對它進行賦值，因此最好顯示地實現getter和setter，並作出註釋說明。 列出所有的實現指示符 儘管@dynamic是默認的，顯示列出它以及其它的實現指示符會提高可讀性，代碼閱讀者可以一眼就知道類的每個屬性是如何實現的。

.. code-block:: objc

    // Header file
    @interface Foo : NSObject
    // A guy walks into a bar.
    @property(nonatomic, copy) NSString *bar;
    @end

    // Implementation file
    @interface Foo ()
    @property(nonatomic, retain) NSArray *baz;
    @end

    @implementation Foo
    @synthesize bar = bar_;
    @synthesize baz = baz_;
    @end

