Javascript語言規則
==========

var關鍵字
----------------

總是用 ``var`` 關鍵字定義變量。

描述
~~~~~~

如果不顯式使用 ``var`` 關鍵字定義變量，變量會進入到全局上下文中，可能會和已有的變量發生衝突。另外，如果不使用var聲明，很難說變量存在的作用域是哪個（可能在局部作用域裡，也可能在document或者window上）。所以，要一直使用 ``var`` 關鍵字定義變量。

常量
----------------

* 使用字母全部大寫（如 ``NAMES_LIKE_THIS`` ）的方式命名

* 可以使用 ``@const`` 來標記一個常量 *指針* （指向變量或屬性，自身不可變）

* 由於IE的兼容問題，不要使用 `const關鍵字 <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FStatements%2Fconst>`_

描述
~~~~~~

常量值
########

如果一個值是恆定的，它命名中的字母要全部大寫（如 ``CONSTANT_VALUE_CASE`` ）。字母全部大寫意味著這個值不可以被改寫。

原始類型（ ``number`` 、 ``string`` 、 ``boolean`` ）是常量值。

對象的表現會更主觀一些，當它們沒有暴露出變化的時候，應該認為它們是常量。但是這個不是由編譯器決定的。

常量指針（變量和屬性）
########################

用 ``@const`` 註釋的變量和屬性意味著它是不能更改的。使用const關鍵字可以保證在編譯的時候保持一致。使用 `const <https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/const?redirectlocale=en-US&redirectslug=JavaScript%2FReference%2FStatements%2Fconst>`_ 效果相同，但是由於IE的兼容問題，我們不使用const關鍵字。

另外，不應該修改用 ``@const`` 註釋的方法。

例子
########

注意， ``@const`` 不一定是常量值，但命名類似 ``CONSTANT_VALUE_CASE`` 的 *一定* 是常量指針。

::

    /**
    * 以毫秒為單位的超時時長
    * @type {number}
    */
    goog.example.TIMEOUT_IN_MILLISECONDS = 60;

1分鐘60秒永遠也不會改變，這是個常量。全部大寫的命名意味其為常量值，所以它不能被重寫。
開源的編譯器允許這個符號被重寫，這是因為 *沒有* ``@const`` 標記。

::

    /**
    * Map of URL to response string.
    * @const
    */
    MyClass.fetchedUrlCache_ = new goog.structs.Map();

在這個例子中，指針沒有變過，但是值卻是可以變化的，所以這裡用了駝峰式的命名，而不是全部大寫的命名。

分號
---------

一定要使用分號。

依靠語句間隱式的分割，可能會造成細微的調試的問題，千萬不要這樣做。

很多時候不寫分號是很危險的：

::

    // 1.
    MyClass.prototype.myMethod = function() {
        return 42;
    }  // 這裡沒有分號.

    (function() {
        // 一些局部作用域中的初始化代碼
    })();

    var x = {
        'i': 1,
        'j': 2
    }  //沒有分號.

    // 2.  試著在IE和firefox下做一樣的事情.
    //沒人會這樣寫代碼，別管他.
    [normalVersion, ffVersion][isIE]();

    var THINGS_TO_EAT = [apples, oysters, sprayOnCheese]  //這裡沒有分號

    // 3. 條件語句
    -1 == resultOfOperation() || die();

發生了什麼？
~~~~~~~~~~~~~

1. js錯誤。返回42的函數運行了，因為後面有一對括號，而且傳入的參數是一個方法，然後返回的42被調用，導致出錯了。

2. 你可能會得到一個「no sush property in undefined」的錯誤，因為在執行的時候，解釋器將會嘗試執行 ``x[normalVersion, ffVersion][isIE]()`` 這個方法。

3.  ``die`` 這個方法只有在 ``resultOfOperation()`` 是 ``NaN`` 的時候執行，並且 ``THINGS_TO_EAT`` 將會被賦值為 ``die()`` 的結果。

為什麼？
~~~~~~~~~~~~

js語句要求以分號結尾，除非能夠正確地推斷分號的位置。在這個例子當中，函數聲明、對像和數組字面量被寫在了一個語句當中。右括號（")"、"}"、"]"）不足以證明這條語句已經結束了，如果下一個字符是運算符或者"("、"{"、"["，js將不會結束語句。

這個錯誤讓人震驚，所以一定要確保用分號結束語句。

澄清：分號和函數
~~~~~~~~~~~~~~

函數表達式後面要分號結束，但是函數聲明就不需要。例如：

::

    var foo = function() {
        return true;
    };  // 這裡要分號

    function foo() {
        return true;
    }  // 這裡不用分號

嵌套函數
-----------------

可以使用。

嵌套函數非常有用，比如在創建持續任務或者隱藏工具方法的時候。可以放心的使用。

塊內涵數聲明
---------------------------

不要使用塊內涵數聲明。

不要這樣做：

::

    if (x) {
        function foo() {}
    }

雖然大多數腳本引擎支持功能區塊內聲明，但ECMAScript並未認可（見 `ECMA-262 <http://www.ecma-international.org/publications/standards/Ecma-262.htm>`_ ，第13條和第14）。若與他人的及EcmaScript所建議的不一致，即可視為不好的實現方式。ECMAScript只允許函數聲明語句列表, 在根語句列表腳本或者函數。相反，使用一個變量初始化函數表達式在塊內定義一個函數塊：

::

    if (x) {
        var foo = function() {}
    }

異常
-------

可以拋出異常。

如果你做一些比較複雜的項目你基本上無法避免異常，比如使用一個應用程序開發框架。可以大膽試一試。

自定義異常
----------

可以自定義異常。

如果沒有自定義異常，返回的錯誤信息來自一個有返回值的函數是難處理的，是不雅的。壞的解決方案包括傳遞引用的類型來保存錯誤信息或總是返回有一個潛在的錯誤成員的對象。這些基本上為原始的異常處理hack。在適當的時候使用自定義的異常。

標準功能
----------

總是優先於非標準功能。

為了最大的可移植性和兼容性，總是使用標準功能而不是非標準功能（例如，採用 `string.charAt(3)` 而非 `string[3]` ，用DOM的功能訪問元素而不是使用特定於一個具體應用的簡寫）。

原始類型的包裝對像
------------------

沒有理由使用原始類型的包裝對象，更何況他們是危險的：

::

    var x = new Boolean(false);
    if (x) {
        alert('hi');  //顯示「hi」。
    }

不要這樣做！

然而類型轉換是可以的。

::

    var x = Boolean(0);
    if (x) {
        alert('hi');  //永遠都不顯示。
    }
    typeof Boolean(0) == 'boolean';
    typeof new Boolean(0) == 'object';

這是非常有用的進行數字、字符串和布爾值轉換的方式。

多重的原型繼承
-------------------

不可取。

多重原型繼承是Javascript實現繼承的方式。如果你有一個以用戶定義的class B作為原型的用戶自定義class D，則得到多重原型繼承。這樣的繼承出現容易但難以正確創造！

出於這個原因，最好是使用 `Closure庫 <https://developers.google.com/closure/library/?csw=1>`_ 中的 ``goog.inherits()`` 或類似的東西。

::

    function D() {
        goog.base(this)
    }
    goog.inherits( D, B );

    D.prototype.method =function() {
        ...
    };

方法和屬性定義
-------------------------

``/**構造函數*/ function SomeConstructor() { this.someProperty = 1; } Foo.prototype.someMethod = function() { ... };``

雖然有多種使用「new」關鍵詞來創建對像方法和屬性的途徑，首選的創建方法的途徑是：

::

    Foo.prototype.bar = function() {
        /* ... */
    };

其他特性的首選創建方式是在構造函數中初始化字段：

::

    /** @constructor */
    function Foo() {
        this.bar = value;
    }

為什麼？
~~~~~~~~~~

當前的JavaScript引擎優化基於一個對象的「形狀」， `給對像添加一個屬性（包括覆蓋原型設置的值）改變了形式，會降低性能 <https://developers.google.com/v8/design#prop_access>`_ 。

刪除
----------

請使用 ``this.foo = null`` 。

::

    o.prototype.dispose = function() {
        this.property_ = null;
    };

而不是：

::

    Foo.prototype.dispose = function() {
        delete his.property_;
    };

在現代的JavaScript引擎中，改變一個對像屬性的數量比重新分配值慢得多。應該避免刪除關鍵字，除非有必要從一個對象的迭代的關鍵字列表刪除一個屬性，或改變 ``if (key in obj)`` 結果。

閉包
-------------

可以使用，但是要小心。

創建閉包可能是JS最有用的和經常被忽視的功能。在 `這裡 <http://jibbering.com/faq/notes/closures/>`_ 很好地描述說明了閉包的工作。

要記住的一件事情，一個閉包的指針指向包含它的範圍。因此，附加一個閉包的DOM元素，可以創建一個循環引用，所以，內存會洩漏。例如，下面的代碼：

::

    function foo(element, a, b) {
        element.onclick = function() { /* 使用 a 和 b */ };
    }

閉包能保持元素a和b的引用即使它從未使用。因為元素還保持對閉包的一個引用，我們有一個循環引用，不會被垃圾收集清理。在這些情況下，代碼的結構可以如下：

::

    function foo(element, a, b) {
        element.onclick = bar(a, b);
    }

    function bar(a, b) {
        return function() { /* 使用 a 和 b */ }
    }

eval()函數
------------------------

只用於反序列化（如評估RPC響應）。

若用於 ``eval()`` 的字符串含有用戶輸入，則 ``eval()`` 會造成混亂的語義，使用它有風險。通常有一個更好
更清晰、更安全的方式來編寫你的代碼，所以一般是不會允許其使用的。然而，eval相對比非eval使反序列化更容易，因此它的使用是可以接受的（例如評估RPC響應）。

反序列化是將一系列字節存到內存中的數據結構轉化過程。例如，你可能會寫的對象是：

::

    users = [
        {
            name: 'Eric',
            id: 37824,
            email: 'jellyvore@myway.com'
        },
        {
            name: 'xtof',
            id: 31337,
            email: 'b4d455h4x0r@google.com'
        },
        ...
    ];

將這些數據讀入內存跟得出文件的字符串表示形式一樣容易。

同樣， ``eval()`` 函數可以簡化解碼RPC的返回值。例如，您可以使用 ``XMLHttpRequest`` 生成RPC，在響應時服務器返回JavaScript：

::

    var userOnline = false;
    var user = 'nusrat';
    var xmlhttp = new XMLHttpRequest();
    xmlhttp.open('GET', 'http://chat.google.com/isUserOnline?user=' + user, false);
    xmlhttp.send('');
    // 服務器返回：
    // userOnline = true;
    if (xmlhttp.status == 200) {
          eval(xmlhttp.responseText);
    }
    // userOnline 現在為 true

with() {}
----------------------

不建議使用。

使用 ``with`` 會影響程序的語義。因為 ``with`` 的目標對象可能會含有和局部變量衝突的屬性，使你程序的語義發生很大的變化。例如，這是做什麼用？

::

    with (foo) {
        var x = 3;
        return x;
    }

答案：什麼都有可能。局部變量 ``x`` 可能會被 ``foo`` 的一個屬性覆蓋，它甚至可能有setter方法，在此情況下將其賦值為3可能會執行很多其他代碼。不要使用 ``with`` 。

this
-------------------

只在構造函數對像、方法，和創建閉包的時候使用。

``this`` 的語義可能會非常詭異。有時它指向全局對像（很多時候）、調用者的作用域鏈（在 ``eval`` 裡）、DOM樹的一個節點（當使用HTML屬性來做為事件句柄時）、新創建的對象（在一個構造函數中）、或者其他的對象（如果函數被 ``call()`` 或 ``apply()`` 方式調用）。

正因為 ``this`` 很容易被弄錯，故將其使用限制在以下必須的地方：

* 在構造函數中

* 在對象的方法中（包括閉包的創建）

for-in 循環
------------------

只使用在對像、映射、哈希的鍵值迭代中。

``for-in`` 循環經常被不正確的用在元素數組的循環中。由於並不是從 ``0`` 到 ``length-1`` 進行循環，而是遍歷對像中和它原型鏈上的所有的鍵，所以很容易出錯。這裡有一些失敗的例子：

::

    function printArray(arr) {
        for (var key in arr) {
            print(arr[key]);
        }
    }

    printArray([0,1,2,3]);  //這樣可以

    var a = new Array(10);
    printArray(a);  //這樣不行

    a = document.getElementsByTagName('*');
    printArray(a);  //這樣不行

    a = [0,1,2,3];
    a.buhu = 'wine';
    printArray(a);  //這樣不行

    a = new Array;
    a[3] = 3;
    printArray(a);  //這樣不行

在數組循環時常用的一般方式：

::

    function printArray(arr) {
        var l = arr.length;
        for (var i = 0; i < l; i++) {
            print(arr[i]);
        }
    }

關聯數組
-----------------------

不要將映射，哈希，關聯數組當作一般數組來使用。

不允許使用關聯數組……確切的說在數組，你不可以使用非數字的索引。如果你需要一個映射或者哈希，在這種情況下你應該使用對像來代替數組，因為在功能上你真正需要的是對象的特性而不是數組的。

數組僅僅是用來拓展對象的（像在JS中你曾經使用過的 ``Date`` 、 ``RegExp`` 和 ``String`` 對像一樣的）。

多行的字符串字面量
------------------------------------

不要使用。

不要這樣做：

::

    var myString = 'A rather long string of English text, an error message \
                  actually that just keeps going and going -- an error \
                  message to make the Energizer bunny blush (right through \
                  those Schwarzenegger shades)! Where was I? Oh yes, \
                  you\'ve got an error and all the extraneous whitespace is \
                  just gravy.  Have a nice day.';

在編譯時每一行頭部的空白符不會被安全地去除掉；斜線後的空格也會導致棘手的問題；雖然大部分腳本引擎都會支持，但是它不是ECMAScript規範的一部分。

使用字符串連接來代替：

::

    var myString = 'A rather long string of English text, an error message ' +
       'actually that just keeps going and going -- an error ' +
       'message to make the Energizer bunny blush (right through ' +
       'those Schwarzenegger shades)! Where was I? Oh yes, ' +
       'you\'ve got an error and all the extraneous whitespace is ' +
       'just gravy.  Have a nice day.';

數組和對像字面量
----------------------------------

建議使用。

使用數組和對像字面量來代替數組和對像構造函數。

數組構造函數容易在參數上出錯。

::

    // 長度為3
    var a1 = new Array(x1, x2, x3);

    // 長度為 2
    var a2 = new Array(x1, x2);

    // If x1 is a number and it is a natural number the length will be x1.
    // If x1 is a number but not a natural number this will throw an exception.
    // Otherwise the array will have one element with x1 as its value.
    var a3 = new Array(x1);

    // 長度為0
    var a4 = new Array();

由此，如果有人將代碼從2個參數變成了一個參數，那麼這個數組就會有一個錯誤的長度。

為了避免這種怪異的情況，永遠使用可讀性更好的數組字面量。

::

    var a = [x1, x2, x3];
    var a2 = [x1, x2];
    var a3 = [x1];
    var a4 = [];

對像構造函數雖然沒有相同的問題，但是對於可讀性和一致性，還是應該使用對像字面量。

::

    var o = new Object();

    var o2 = new Object();
    o2.a = 0;
    o2.b = 1;
    o2.c = 2;
    o2['strange key'] = 3;

應該寫成：

::

    var o = {};

    var o2 = {
        a: 0,
        b: 1,
        c: 2,
        'strange key': 3
    };

修改內置對像原型
--------------------------------

不建議。

強烈禁止修改如 ``Object.prototype`` 和 ``Array.prototype`` 等對象的原型。修改其他內置原型如 ``Function.prototype`` 危險性較小，但在生產環境中還是會引發一些難以調試的問題，也應當避免。

Internet Explorer中的條件註釋
----------------------------------------------------------

不要使用。

不要這樣做：

::

    var f = function () {
        /*@cc_on if (@_jscript) { return 2* @*/  3; /*@ } @*/
    };

條件註釋會在運行時改變JavaScript語法樹，阻礙自動化工具。
