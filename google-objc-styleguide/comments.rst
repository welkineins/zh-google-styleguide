註釋
=======

雖然寫起來很痛苦，但註釋是保證代碼可讀性的關鍵。下面的規則給出了你應該什麼時候、在哪進行註釋。記住：儘管註釋很重要，但最好的代碼應該自成文檔。與其給類型及變量起一個晦澀難懂的名字，再為它寫註釋，不如直接起一個有意義的名字。

當你寫註釋的時候，記得你是在給你的聽眾寫，即下一個需要閱讀你所寫代碼的貢獻者。大方一點，下一個讀代碼的人可能就是你！

記住所有 C++ 風格指南裡的規則在這裡也同樣適用，不同的之處後續會逐步指出。

文件註釋
~~~~~~~~~~

.. tip::

    每個文件的開頭以文件內容的簡要描述起始，緊接著是作者，最後是版權聲明和/或許可證樣板。

版權信息及作者
^^^^^^^^^^^^^^^^^^

每個文件應該按順序包括如下項：

* 文件內容的簡要描述
* 代碼作者
* 版權信息聲明（如：``Copyright 2008 Google Inc.``）
* 必要的話，加上許可證樣板。為項目選擇一個合適的授權樣板（例如，``Apache 2.0, BSD, LGPL, GPL``）。

如果你對其他人的原始代碼作出重大的修改，請把你自己的名字添加到作者裡面。當另外一個代碼貢獻者對文件有問題時，他需要知道怎麼聯繫你，這十分有用。

聲明部分的註釋
~~~~~~~~~~~~~~~~

.. tip::

    每個接口、類別以及協議應輔以註釋，以描述它的目的及與整個項目的關係。

.. code-block:: objc

    // A delegate for NSApplication to handle notifications about app
    // launch and shutdown. Owned by the main app controller.
    @interface MyAppDelegate : NSObject {
      ...
    }
    @end

如果你已經在文件頭部詳細描述了接口，可以直接說明 「完整的描述請參見文件頭部」，但是一定要有這部分註釋。

另外，公共接口的每個方法，都應該有註釋來解釋它的作用、參數、返回值以及其它影響。

為類的線程安全性作註釋，如果有的話。如果類的實例可以被多個線程訪問，記得註釋多線程條件下的使用規則。

實現部分的註釋
~~~~~~~~~~~~~~~~

.. tip::

    使用 ``|`` 來引用註釋中的變量名及符號名而不是使用引號。

這會避免二義性，尤其是當符號是一個常用詞彙，這使用語句讀起來很糟糕。例如，對於符號 ``count`` ：

.. code-block:: objc

    // Sometimes we need |count| to be less than zero.

或者當引用已經包含引號的符號：

.. code-block:: objc

    // Remember to call |StringWithoutSpaces("foo bar baz")|



對像所有權
~~~~~~~~~~~~~~~~

.. tip::

    當與 Objective-C 最常規的作法不同時，盡量使指針的所有權模型盡量明確。

繼承自 ``NSObject`` 的對象的實例變量指針，通常被假定是強引用關係（retained），某些情況下也可以註釋為弱引用（weak）或使用 ``__weak`` 生命週期限定符。同樣，聲明的屬性如果沒有被類 ``retained``，必須指定是弱引用或賦予 ``@property`` 屬性。然而，Mac 軟件中標記上 ``IBOutlets`` 的實例變量，被認為是不會被類 ``retained`` 的。

當實例變量指向 ``CoreFoundation``、C++ 或者其它非 Objective-C 對像時，不論指針是否會被 ``retained``，都需要使用 ``__strong`` 和 ``__weak`` 類型修飾符明確指明。``CoreFoundation`` 和其它非 Objective-C 對像指針需要顯式的內存管理，即便使用了自動引用計數或垃圾回收機制。當不允許使用 ``__weak`` 類型修飾符（比如，使用 clang 編譯時的 C++ 成員變量），應使用註釋替代說明。

注意：Objective-C 對像中的 C++ 對象的自動封裝，缺省是不允許的，參見 `這裡 <http://chanson.livejournal.com/154253.html>`_ 的說明。

強引用及弱引用聲明的例子：

.. code-block:: objc

    @interface MyDelegate : NSObject {
     @private
      IBOutlet NSButton *okButton_;  // normal NSControl; implicitly weak on Mac only

      AnObjcObject* doohickey_;  // my doohickey
      __weak MyObjcParent *parent_;  // so we can send msgs back (owns me)

      // non-NSObject pointers...
      __strong CWackyCPPClass *wacky_;  // some cross-platform object
      __strong CFDictionaryRef *dict_;
    }
    @property(strong, nonatomic) NSString *doohickey;
    @property(weak, nonatomic) NSString *parent;
    @end

（譯註：強引用 - 對像被類 ``retained``。弱引用 - 對像沒有被類 ``retained``，如委託）
