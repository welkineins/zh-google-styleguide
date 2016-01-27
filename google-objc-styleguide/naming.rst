命名
=========


對於易維護的代碼而言，命名規則非常重要。Objective-C 的方法名往往十分長，但代碼塊讀起來就像散文一樣，不需要太多的代碼註釋。

當編寫純粹的 Objective-C 代碼時，我們基本遵守標準的 `Objective-C naming rules <http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/CodingGuidelines.html>`_，這些命名規則可能與 C++ 風格指南中的大相逕庭。例如，Google 的 C++ 風格指南中推薦使用下劃線分隔的單詞作為變量名，而(蘋果的)風格指南則使用駝峰命名法，這在 Objective-C 社區中非常普遍。

任何的類、類別、方法以及變量的名字中都使用全大寫的 `首字母縮寫 <http://en.wikipedia.org/wiki/Initialism>`_。這遵守了蘋果的標準命名方式，如 URL、TIFF 以及 EXIF。

當編寫 Objective-C++ 代碼時，事情就不這麼簡單了。許多項目需要實現跨平台的 C++ API，並混合一些 Objective-C、Cocoa 代碼，或者直接以 C++ 為後端，前端用本地 Cocoa 代碼。這就導致了兩種命名方式直接不統一。

我們的解決方案是：編碼風格取決於方法/函數以哪種語言實現。如果在一個 ``@implementation`` 語句中，就使用 Objective-C 的風格。如果實現一個 C++ 的類，就使用 C++ 的風格。這樣避免了一個函數里面實例變量和局部變量命名規則混亂，嚴重影響可讀性。

文件名
~~~~~~~~

.. tip::

    文件名須反映出其實現了什麼類 -- 包括大小寫。遵循你所參與項目的約定。

文件的擴展名應該如下：

=======  ==========================
``.h``   C/C++/Objective-C 的頭文件
``.m``   Ojbective-C 實現文件
``.mm``  Ojbective-C++ 的實現文件
``.cc``  純 C++ 的實現文件
``.c``   純 C 的實現文件
=======  ==========================

類別的文件名應該包含被擴展的類名，如：``GTMNSString+Utils.h`` 或``GTMNSTextView+Autocomplete.h``。


Objective-C++
~~~~~~~~~~~~~~~~

.. tip::

    源代碼文件內，Ojbective-C++ 代碼遵循你正在實現的函數/方法的風格。

為了最小化 Cocoa/Objective-C 與 C++ 之間命名風格的衝突，根據待實現的函數/方法選擇編碼風格。實現 ``@implementation`` 語句塊時，使用 Objective-C 的命名規則；如果實現一個 C++ 的類，就使用 C++ 命名規則。

.. code-block:: objc

    // file: cross_platform_header.h

    class CrossPlatformAPI {
     public:
      ...
      int DoSomethingPlatformSpecific();  // impl on each platform
     private:
      int an_instance_var_;
    };

    // file: mac_implementation.mm
    #include "cross_platform_header.h"

    // A typical Objective-C class, using Objective-C naming.
    @interface MyDelegate : NSObject {
     @private
      int instanceVar_;
      CrossPlatformAPI* backEndObject_;
    }
    - (void)respondToSomething:(id)something;
    @end
    @implementation MyDelegate
    - (void)respondToSomething:(id)something {
      // bridge from Cocoa through our C++ backend
      instanceVar_ = backEndObject->DoSomethingPlatformSpecific();
      NSString* tempString = [NSString stringWithInt:instanceVar_];
      NSLog(@"%@", tempString);
    }
    @end

    // The platform-specific implementation of the C++ class, using
    // C++ naming.
    int CrossPlatformAPI::DoSomethingPlatformSpecific() {
      NSString* temp_string = [NSString stringWithInt:an_instance_var_];
      NSLog(@"%@", temp_string);
      return [temp_string intValue];
    }


類名
~~~~~

.. tip::

    類名（以及類別、協議名）應首字母大寫，並以駝峰格式分割單詞。

*應用層* 的代碼，應該盡量避免不必要的前綴。為每個類都添加相同的前綴無助於可讀性。當編寫的代碼期望在不同應用程序間復用時，應使用前綴（如：``GTMSendMessage``）。


類別名
~~~~~~

.. tip::

    類別名應該有兩三個字母的前綴以表示類別是項目的一部分或者該類別是通用的。類別名應該包含它所擴展的類的名字。

比如我們要基於 ``NSString`` 創建一個用於解析的類別，我們將把類別放在一個名為 ``GTMNSString+Parsing.h`` 的文件中。類別本身命名為 ``GTMStringParsingAdditions`` （是的，我們知道類別名和文件名不一樣，但是這個文件中可能存在多個不同的與解析有關類別）。類別中的方法應該以 ``gtm_myCategoryMethodOnAString:`` 為前綴以避免命名衝突，因為 Objective-C 只有一個名字空間。如果代碼不會分享出去，也不會運行在不同的地址空間中，方法名字就不那麼重要了。

類名與包含類別名的括號之間，應該以一個空格分隔。


Objective-C 方法名
~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    方法名應該以小寫字母開頭，並混合駝峰格式。每個具名參數也應該以小寫字母開頭。

方法名應盡量讀起來就像句子，這表示你應該選擇與方法名連在一起讀起來通順的參數名。（例如，``convertPoint:fromRect:`` 或 ``replaceCharactersInRange:withString:``）。詳情參見 `Apple』s Guide to Naming Methods <http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/Articles/NamingMethods.html>`_。

訪問器方法應該與他們 ``要獲取的`` 成員變量的名字一樣，但不應該以get作為前綴。例如：

.. code-block:: objc

    - (id)getDelegate;  // AVOID
    - (id)delegate;     // GOOD

這僅限於 Objective-C 的方法名。C++ 的方法與函數的命名規則應該遵從 C++ 風格指南中的規則。


變量名
~~~~~~~~~~

.. tip::

    變量名應該以小寫字母開頭，並使用駝峰格式。類的成員變量應該以下劃線作為後綴。例如：``myLocalVariable``、``myInstanceVariable_``。如果不能使用 Objective-C 2.0 的 ``@property``，使用 KVO/KVC 綁定的成員變量可以以一個下劃線作為前綴。


普通變量名
^^^^^^^^^^^^

對於靜態的屬性（``int`` 或指針），不要使用匈牙利命名法。盡量為變量起一個描述性的名字。不要擔心浪費列寬，因為讓新的代碼閱讀者立即理解你的代碼更重要。例如：

* 錯誤的命名：

    .. code-block:: objc

        int w;
        int nerr;
        int nCompConns;
        tix = [[NSMutableArray alloc] init];
        obj = [someObject object];
        p = [network port];

* 正確的命名：

    .. code-block:: objc

        int numErrors;
        int numCompletedConnections;
        tickets = [[NSMutableArray alloc] init];
        userInfo = [someObject object];
        port = [network port];

實例變量
^^^^^^^^^^^^

實例變量應該混合大小寫，並以下劃線作為後綴，如 ``usernameTextField_``。然而，如果不能使用 Objective-C 2.0（操作系統版本的限制），並且使用了 KVO/KVC 綁定成員變量時，我們允許例外（譯者註： ``KVO=Key Value Observing，KVC=Key Value Coding``）。這種情況下，可以以一個下劃線作為成員變量名字的前綴，這是蘋果所接受的鍵/值命名慣例。如果可以使用 Objective-C 2.0，``@property`` 以及 ``@synthesize`` 提供了遵從這一命名規則的解決方案。


常量
^^^^^^^^^^^^

常量名（如宏定義、枚舉、靜態局部變量等）應該以小寫字母 ``k`` 開頭，使用駝峰格式分隔單詞，如：``kInvalidHandle，kWritePerm``。


