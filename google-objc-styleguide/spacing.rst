留白和格式
==========

空格 vs. 製表符
~~~~~~~~~~~~~~~~~

.. tip::

    只使用空格，且一次縮進兩個空格。

我們使用空格縮進。不要在代碼中使用製表符。你應該將編輯器設置成自動將製表符替換成空格。

行寬
~~~~~~~

.. tip::

盡量讓你的代碼保持在 80 列之內。

我們深知 Objective-C 是一門繁冗的語言，在某些情況下略超 80 列可能有助於提高可讀性，但這也只能是特例而已，不能成為開脫。

如果閱讀代碼的人認為把把某行行寬保持在 80 列仍然有不失可讀性，你應該按他們說的去做。

我們意識到這條規則是有爭議的，但很多已經存在的代碼堅持了本規則，我們覺得保證一致性更重要。

通過設置 *Xcode > Preferences > Text Editing > Show page guide*，來使越界更容易被發現。

方法聲明和定義
~~~~~~~~~~~~~~

.. tip::

    - / + 和返回類型之間須使用一個空格，參數列表中只有參數之間可以有空格。

方法應該像這樣：

.. code-block:: objc

    - (void)doSomethingWithString:(NSString *)theString {
      ...
    }

星號前的空格是可選的。當寫新的代碼時，要與先前代碼保持一致。

如果一行有非常多的參數，更好的方式是將每個參數單獨拆成一行。如果使用多行，將每個參數前的冒號對齊。

.. code-block:: objc

    - (void)doSomethingWith:(GTMFoo *)theFoo
                       rect:(NSRect)theRect
                   interval:(float)theInterval {
      ...
    }

當第一個關鍵字比其它的短時，保證下一行至少有 4 個空格的縮進。這樣可以使關鍵字垂直對齊，而不是使用冒號對齊：

.. code-block:: objc

    - (void)short:(GTMFoo *)theFoo
        longKeyword:(NSRect)theRect
        evenLongerKeyword:(float)theInterval {
      ...
    }


方法調用
~~~~~~~~~~~~~~

.. tip::

    方法調用應盡量保持與方法聲明的格式一致。當格式的風格有多種選擇時，新的代碼要與已有代碼保持一致。

調用時所有參數應該在同一行：

.. code-block:: objc

    [myObject doFooWith:arg1 name:arg2 error:arg3];

或者每行一個參數，以冒號對齊：

.. code-block:: objc

    [myObject doFooWith:arg1
                   name:arg2
                  error:arg3];

不要使用下面的縮進風格：

.. code-block:: objc

    [myObject doFooWith:arg1 name:arg2  // some lines with >1 arg
                  error:arg3];

    [myObject doFooWith:arg1
                   name:arg2 error:arg3];

    [myObject doFooWith:arg1
              name:arg2  // aligning keywords instead of colons
              error:arg3];

方法定義與方法聲明一樣，當關鍵字的長度不足以以冒號對齊時，下一行都要以四個空格進行縮進。

.. code-block:: objc

    [myObj short:arg1
        longKeyword:arg2
        evenLongerKeyword:arg3];


``@public`` 和 ``@private``
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    ``@public`` 和 ``@private`` 訪問修飾符應該以一個空格縮進。

與 C++ 中的 ``public, private`` 以及 ``protected`` 非常相似。

.. code-block:: objc

    @interface MyClass : NSObject {
     @public
      ...
     @private
      ...
    }
    @end


異常
~~~~~~~~~~

.. tip::

    每個 ``@`` 標籤應該有獨立的一行，在 ``@`` 與 ``{}`` 之間需要有一個空格， ``@catch`` 與被捕捉到的異常對象的聲明之間也要有一個空格。

如果你決定使用 Objective-C 的異常，那麼就按下面的格式。不過你最好先看看 :ref:`避免拋出異常 <avoid-throwing-exceptions>` 瞭解下為什麼不要使用異常。

.. code-block:: objc

    @try {
      foo();
    }
    @catch (NSException *ex) {
      bar(ex);
    }
    @finally {
      baz();
    }


協議名
~~~~~~~~~~

.. tip::

    類型標識符和尖括號內的協議名之間，不能有任何空格。


這條規則適用於類聲明、實例變量以及方法聲明。例如：

.. code-block:: objc

    @interface MyProtocoledClass : NSObject<NSWindowDelegate> {
     @private
      id<MyFancyDelegate> delegate_;
    }
    - (void)setDelegate:(id<MyFancyDelegate>)aDelegate;
    @end


塊（閉包）
~~~~~~~~~~

.. tip::

    塊（block）適合用在 target/selector 模式下創建回調方法時，因為它使代碼更易讀。塊中的代碼應該縮進 4 個空格。

取決於塊的長度，下列都是合理的風格準則：

* 如果一行可以寫完塊，則沒必要換行。
* 如果不得不換行，關括號應與塊聲明的第一個字符對齊。
* 塊內的代碼須按 4 空格縮進。
* 如果塊太長，比如超過 20 行，建議把它定義成一個局部變量，然後再使用該變量。
* 如果塊不帶參數，``^{`` 之間無須空格。如果帶有參數，``^(`` 之間無須空格，但 ``) {`` 之間須有一個空格。
* 塊內允許按兩個空格縮進，但前提是和項目的其它代碼保持一致的縮進風格。

.. code-block:: objc

    // The entire block fits on one line.
    [operation setCompletionBlock:^{ [self onOperationDone]; }];

    // The block can be put on a new line, indented four spaces, with the
    // closing brace aligned with the first character of the line on which
    // block was declared.
    [operation setCompletionBlock:^{
        [self.delegate newDataAvailable];
    }];

    // Using a block with a C API follows the same alignment and spacing
    // rules as with Objective-C.
    dispatch_async(fileIOQueue_, ^{
        NSString* path = [self sessionFilePath];
        if (path) {
          // ...
        }
    });

    // An example where the parameter wraps and the block declaration fits
    // on the same line. Note the spacing of |^(SessionWindow *window) {|
    // compared to |^{| above.
    [[SessionService sharedService]
        loadWindowWithCompletionBlock:^(SessionWindow *window) {
            if (window) {
              [self windowDidLoad:window];
            } else {
              [self errorLoadingWindow];
            }
        }];

    // An example where the parameter wraps and the block declaration does
    // not fit on the same line as the name.
    [[SessionService sharedService]
        loadWindowWithCompletionBlock:
            ^(SessionWindow *window) {
                if (window) {
                  [self windowDidLoad:window];
                } else {
                  [self errorLoadingWindow];
                }
            }];

    // Large blocks can be declared out-of-line.
    void (^largeBlock)(void) = ^{
        // ...
    };
    [operationQueue_ addOperationWithBlock:largeBlock];

