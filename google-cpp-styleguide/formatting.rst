8. 格式
------------

代碼風格和格式確實比較隨意, 但一個項目中所有人遵循同一風格是非常容易的. 個體未必同意下述每一處格式規則, 但整個項目服從統一的編程風格是很重要的, 只有這樣才能讓所有人能很輕鬆的閱讀和理解代碼.

另外, 我們寫了一個 `emacs 配置文件 <http://google-styleguide.googlecode.com/svn/trunk/google-c-style.el>`_ 來幫助你正確的格式化代碼.

.. _line-length:

8.1. 行長度
~~~~~~~~~~~~~~~~~~~~

.. tip::

    每一行代碼字符數不超過 80.

我們也認識到這條規則是有爭議的, 但很多已有代碼都已經遵照這一規則, 我們感覺一致性更重要.

優點:

    提倡該原則的人主張強迫他們調整編輯器窗口大小很野蠻. 很多人同時並排開幾個代碼窗口, 根本沒有多餘空間拉伸窗口. 大家都把窗口最大尺寸加以限定, 並且 80 列寬是傳統標準. 為什麼要改變呢?

缺點:

    反對該原則的人則認為更寬的代碼行更易閱讀. 80 列的限制是上個世紀 60 年代的大型機的古板缺陷; 現代設備具有更寬的顯示屏, 很輕鬆的可以顯示更多代碼.

結論:

    80 個字符是最大值.

    特例:

    - 如果一行註釋包含了超過 80 字符的命令或 URL, 出於複製粘貼的方便允許該行超過 80 字符.
    - 包含長路徑的 ``#include`` 語句可以超出80列. 但應該盡量避免.
    - :ref:`頭文件保護 <define-guard>` 可以無視該原則.

8.2. 非 ASCII 字符
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    盡量不使用非 ASCII 字符, 使用時必須使用 UTF-8 編碼.

即使是英文, 也不應將用戶界面的文本硬編碼到源代碼中, 因此非 ASCII 字符要少用. 特殊情況下可以適當包含此類字符. 如, 代碼分析外部數據文件時, 可以適當硬編碼數據文件中作為分隔符的非 ASCII 字符串; 更常見的是 (不需要本地化的) 單元測試代碼可能包含非 ASCII 字符串. 此類情況下, 應使用 UTF-8 編碼, 因為很多工具都可以理解和處理 UTF-8 編碼.

十六進制編碼也可以, 能增強可讀性的情況下尤其鼓勵 —— 比如 ``"\xEF\xBB\xBF"`` 在 Unicode 中是 *零寬度 無間斷* 的間隔符號, 如果不用十六進制直接放在 UTF-8 格式的源文件中, 是看不到的.

(Yang.Y 注: ``"\xEF\xBB\xBF"`` 通常用作 UTF-8 with BOM 編碼標記)

用 ``u8`` 前綴以把帶 ``uXXXX`` 轉義序列的字符串字面值編碼成 UTF-8. 不要用在本身就帶 UTF-8 字符的字符串字面值上，因為如果編譯器不把源代碼識別成 UTF-8, 輸出就會出錯。

別用 C++11 的 ``char16_t`` 和 ``char32_t``, 它們和 UTF-8 文本沒有關係，``wchar_t`` 同理，除非您寫的代碼要調用 Windows API, 後者有用到 ``wchar_t`` 擴展。

8.3. 空格還是製表位
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    只使用空格, 每次縮進 2 個空格.

我們使用空格縮進. 不要在代碼中使用制符表. 你應該設置編輯器將制符表轉為空格.

8.4. 函數聲明與定義
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    返回類型和函數名在同一行, 參數也盡量放在同一行，如果放不下就對形參分行。

函數看上去像這樣:

    .. code-block:: c++

        ReturnType ClassName::FunctionName(Type par_name1, Type par_name2) {
            DoSomething();
            ...
        }

如果同一行文本太多, 放不下所有參數:

    .. code-block:: c++

        ReturnType ClassName::ReallyLongFunctionName(Type par_name1, Type par_name2,
                                                     Type par_name3) {
            DoSomething();
            ...
        }

甚至連第一個參數都放不下:

    .. code-block:: c++

        ReturnType LongClassName::ReallyReallyReallyLongFunctionName(
                Type par_name1,  // 4 空格縮進
                Type par_name2,
                Type par_name3) {
            DoSomething();  // 2 空格縮進
            ...
        }

注意以下幾點:

    - 如果返回類型和函數名在一行放不下，分行。

    - 如果返回類型那個與函數聲明或定義分行了，不要縮進。

    - 左圓括號總是和函數名在同一行;

    - 函數名和左圓括號間沒有空格;

    - 圓括號與參數間沒有空格;

    - 左大括號總在最後一個參數同一行的末尾處;

    - 如果其它風格規則允許的話，右大括號總是單獨位於函數最後一行，或者與左大括號同一行。

    - 右大括號和左大括號間總是有一個空格;

    - 函數聲明和定義中的所有形參必須有命名且一致;

    - 所有形參應盡可能對齊;

    - 缺省縮進為 2 個空格;

    - 換行後的參數保持 4 個空格的縮進;

如果有些參數沒有用到, 在函數定義處將參數名註釋起來:

    .. code-block:: c++

        // 接口中形參恆有命名。
        class Shape {
         public:
          virtual void Rotate(double radians) = 0;
        }

        // 聲明中形參恆有命名。
        class Circle : public Shape {
         public:
          virtual void Rotate(double radians);
        }

        // 定義中註釋掉無用變量。
        void Circle::Rotate(double /*radians*/) {}

    .. warning::

        .. code-block:: c++

            // 差 - 如果將來有人要實現，很難猜出變量是幹什麼用的。
            void Circle::Rotate(double) {}

8.5. Lambda 表達式
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    其它函數怎麼格式化形參和函數體，Lambda 表達式就怎麼格式化；捕獲列表同理。

    若用引用捕獲，在變量名和 ``&`` 之間不留空格。

    .. code-block:: c++

        int x = 0;
        auto add_to_x = [&x](int n) { x += n; };

    短 lambda 就寫得和內聯函數一樣。

    .. code-block:: c++

        std::set<int> blacklist = {7, 8, 9};
        std::vector<int> digits = {3, 9, 1, 8, 4, 7, 1};
        digits.erase(std::remove_if(digits.begin(), digits.end(), [&blacklist](int i) {
                        return blacklist.find(i) != blacklist.end();
                    }),
                    digits.end());

8.6. 函數調用
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    要麼一行寫完函數調用，要麼在圓括號裡對參數分行，要麼參數另起一行且縮進四格。如果沒有其它顧慮的話，盡可能精簡行數，比如把多個參數適當地放在同一行裡。

    函數調用遵循如下形式：

    .. code-block:: c++

        bool retval = DoSomething(argument1, argument2, argument3);

    如果同一行放不下，可斷為多行，後面每一行都和第一個實參對齊，左圓括號後和右圓括號前不要留空格：

    .. code-block:: c++

        bool retval = DoSomething(averyveryveryverylongargument1,
                                  argument2, argument3);

    參數也可以放在次行，縮進四格：

        .. code-block:: c++

            if (...) {
              ...
              ...
              if (...) {
                DoSomething(
                    argument1, argument2,  // 4 空格縮進
                    argument3, argument4);
              }

    把多個參數放在同一行，是為了減少函數調用所需的行數，除非影響到可讀性。有人認為把每個參數都獨立成行，不僅更好讀，而且方便編輯參數。不過，比起所謂的參數編輯，我們更看重可讀性，且後者比較好辦：

    如果一些參數本身就是略複雜的表達式，且降低了可讀性。那麼可以直接創建臨時變量描述該表達式，並傳遞給函數：

    .. code-block:: c++

        int my_heuristic = scores[x] * y + bases[x];
        bool retval = DoSomething(my_heuristic, x, y, z);

    或者放著不管，補充上註釋：

    .. code-block:: c++

        bool retval = DoSomething(scores[x] * y + bases[x],  // Score heuristic.
                                  x, y, z);

    如果某參數獨立成行，對可讀性更有幫助的話，就這麼辦。

    此外，如果一系列參數本身就有一定的結構，可以酌情地按其結構來決定參數格式：

    .. code-block:: c++

        // 通過 3x3 矩陣轉換 widget.
        my_widget.Transform(x1, x2, x3,
                            y1, y2, y3,
                            z1, z2, z3);

.. _braced-initializer-list-format

8.7. 列表初始化格式
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    您平時怎麼格式化函數調用，就怎麼格式化:ref:`braced_initializer_list`。

    如果列表初始化伴隨著名字，比如類型或變量名，您可以當名字是函數、{} 是函數調用的括號來格式化它。反之，就當它有個長度為零的名字。

    .. code-block:: c++

        // 一行列表初始化示範。
        return {foo, bar};
        functioncall({foo, bar});
        pair<int, int> p{foo, bar};

        // 當不得不斷行時。
        SomeFunction(
            {"assume a zero-length name before {"},
            some_other_function_parameter);
        SomeType variable{
            some, other, values,
            {"assume a zero-length name before {"},
            SomeOtherType{
                "Very long string requiring the surrounding breaks.",
                some, other values},
            SomeOtherType{"Slightly shorter string",
                          some, other, values}};
        SomeType variable{
            "This is too long to fit all in one line"};
        MyType m = {  // 注意了，您可以在 { 前斷行。
            superlongvariablename1,
            superlongvariablename2,
            {short, interior, list},
            {interiorwrappinglist,
             interiorwrappinglist2}};

8.8. 條件語句
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    傾向於不在圓括號內使用空格. 關鍵字 ``if`` 和 ``else`` 另起一行.

對基本條件語句有兩種可以接受的格式. 一種在圓括號和條件之間有空格, 另一種沒有.

最常見的是沒有空格的格式. 哪種都可以, 但 *保持一致性*. 如果你是在修改一個文件, 參考當前已有格式. 如果是寫新的代碼, 參考目錄下或項目中其它文件. 還在徘徊的話, 就不要加空格了.

    .. code-block:: c++

        if (condition) {  圓括號裡沒空格緊鄰。
          ...  // 2 空格縮進。
        } else {  // else 與 if 的右括號同一行。
          ...
        }

如果你更喜歡在圓括號內部加空格:

    .. code-block:: c++

        if ( condition ) {  // 圓括號與空格緊鄰 - 不常見
          ...  // 2 空格縮進。
        } else {  // else 與 if 的右括號同一行。
          ...
        }

注意所有情況下 ``if`` 和左圓括號間都有個空格. 右圓括號和左大括號之間也要有個空格:

    .. warning::

        .. code-block:: c++

            if(condition)     // 差 - IF 後面沒空格。
            if (condition){   // 差 - { 前面沒空格。
            if(condition){    // 變本加厲地差。

    .. code-block:: c++

        if (condition) {  // 可 - IF 和 { 都與空格緊鄰。

如果能增強可讀性, 簡短的條件語句允許寫在同一行. 只有當語句簡單並且沒有使用 ``else`` 子句時使用:

    .. code-block:: c++

        if (x == kFoo) return new Foo();
        if (x == kBar) return new Bar();

如果語句有 ``else`` 分支則不允許:

    .. warning::

        .. code-block:: c++

            // 不可以這樣子 - 當有 ELSE 分支時 IF 塊卻只有一行
            if (x) DoThis();
            else DoThat();

通常, 單行語句不需要使用大括號, 如果你喜歡用也沒問題; 複雜的條件或循環語句用大括號可讀性會更好. 也有一些項目要求 ``if`` 必須總是使用大括號:

    .. code-block:: c++

        if (condition)
          DoSomething();  // 2 空格縮進。

        if (condition) {
          DoSomething();  // 2 空格縮進。
        }

但如果語句中某個 ``if-else`` 分支使用了大括號的話, 其它分支也必須使用:

    .. warning::

        .. code-block:: c++

            // 不可以這樣子 - IF 有大括號 ELSE 卻沒有。
            if (condition) {
                foo;
            } else
                bar;

            // 不可以這樣子 - ELSE 有大括號 IF 卻沒有。
            if (condition)
                foo;
            else {
                bar;
            }


    .. code-block:: c++

        // 只要其中一個分支用了大括號，兩個分支都要用上大括號。
        if (condition) {
          foo;
        } else {
          bar;
        }

8.9. 循環和開關選擇語句
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    ``switch`` 語句可以使用大括號分段，以表明 cases 之間不是連在一起的。在單語句循環裡，括號可用可不用。空循環體應使用 ``{}`` 或 ``continue``.

``switch`` 語句中的 ``case`` 塊可以使用大括號也可以不用, 取決於你的個人喜好. 如果用的話, 要按照下文所述的方法.

如果有不滿足 ``case`` 條件的枚舉值, ``switch`` 應該總是包含一個 ``default`` 匹配 (如果有輸入值沒有 case 去處理, 編譯器將報警). 如果 ``default`` 應該永遠執行不到, 簡單的加條 ``assert``:

    .. code-block:: c++

        switch (var) {
          case 0: {  // 2 空格縮進
            ...      // 4 空格縮進
            break;
          }
          case 1: {
            ...
            break;
          }
          default: {
            assert(false);
          }
        }

在單語句循環裡，括號可用可不用：

    .. code-block:: c++

        for (int i = 0; i < kSomeNumber; ++i)
            printf("I love you\n");

        for (int i = 0; i < kSomeNumber; ++i) {
            printf("I take it back\n");
        }

空循環體應使用 ``{}`` 或 ``continue``, 而不是一個簡單的分號.

    .. code-block:: c++

        while (condition) {
          // 反覆循環直到條件失效。
        }
        for (int i = 0; i < kSomeNumber; ++i) {}  // 可 - 空循環體。
        while (condition) continue;  // 可 - contunue 表明沒有邏輯。

    .. warning::

        .. code-block:: c++

            while (condition);  // 差 - 看起來僅僅只是 while/loop 的部分之一。

8.10. 指針和引用表達式
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    句點或箭頭前後不要有空格. 指針/地址操作符 (``*, &``) 之後不能有空格.

下面是指針和引用表達式的正確使用範例:

    .. code-block:: c++

        x = *p;
        p = &x;
        x = r.y;
        x = r->y;

注意:啊
    - 在訪問成員時, 句點或箭頭前後沒有空格.
    - 指針操作符 ``*`` 或 ``&`` 後沒有空格.

在聲明指針變量或參數時, 星號與類型或變量名緊挨都可以:

    .. code-block:: c++

        // 好樣的，空格前置。
        char *c;
        const string &str;

        // 好樣的，空格後置。
        char* c;    // 但別忘了 "char* c, *d, *e, ...;"!
        const string& str;

    .. warning::

        .. code-block:: c++

            char * c;  // 差 - * 兩邊都有空格
            const string & str;  // 差 - & 兩邊都有空格。

在單個文件內要保持風格一致, 所以, 如果是修改現有文件, 要遵照該文件的風格.

8.11. 布爾表達式
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    如果一個布爾表達式超過 :ref:`標準行寬 <line-length>`, 斷行方式要統一一下.

下例中, 邏輯與 (``&&``) 操作符總位於行尾:

    .. code-block:: c++

        if (this_one_thing > this_other_thing &&
            a_third_thing == a_fourth_thing &&
            yet_another & last_one) {
          ...
        }

注意, 上例的邏輯與 (``&&``) 操作符均位於行尾. 這格式在 Google 裡很常見，您要把所有操作符放在開頭也可以。可以考慮額外插入圓括號, 合理使用的話對增強可讀性是很有幫助的. 此外直接用符號形式的操作符，比如 ``&&`` 和 ``~``, 不要用詞語形式的 ``and`` 和 ``compl``.

8.12. 函數返回值
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    ``return`` 表達式裡時沒必要都用圓括號。

假如您寫 ``x = epr`` 時本來就會加上括號，那 ``return expr;`` 也可如法炮製。

函數返回時不要使用圓括號:

    .. code-block:: c++

        return result;                  // 返回值很簡單，沒有圓括號。
        // 可以用圓括號把複雜表達式圈起來，改善可讀性。
        return (some_long_condition &&
                another_condition);

    .. warning::

        .. code-block:: c++

            return (value);                // 畢竟您從來不會寫 var = (value);
            return(result);                // return 可不是函數！

8.13. 變量及數組初始化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    用 ``=``, ``()`` 和 ``{}`` 均可.

您可以用 ``=``, ``()`` 和 ``{}``, 以下都對：

    .. code-block:: c++

        int x = 3;
        int x(3);
        int x{3};
        string name("Some Name");
        string name = "Some Name";
        string name{"Some Name"};

請務必小心列表初始化 {...} 用 ``std::initializer_list`` 構造函數初始化出的類型。非空列表初始化就會優先調用 ``std::initializer_list``, 不過空列表初始化除外，後者原則上會調用默認構造函數。為了強制禁用 ``std::initializer_list`` 構造函數，請改用括號。

    .. code-block:: c++

        vector<int> v(100, 1);  // A vector of 100 1s.
        vector<int> v{100, 1};  // A vector of 100, 1.

此外，列表初始化不允許整型類型的四捨五入，這可以用來避免一些類型上的編程失誤。

    .. code-block:: c++

        int pi(3.14);  // 可 -- pi == 3.
        int pi{3.14};  // Compile error: narrowing conversion.

8.14. 預處理指令
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    預處理指令不要縮進, 從行首開始.

即使預處理指令位於縮進代碼塊中, 指令也應從行首開始.

    .. code-block:: c++

        // 可 - directives at beginning of line
          if (lopsided_score) {
        #if DISASTER_PENDING      // 正確 -- 行開頭起。
            DropEverything();
        #endif
            BackToNormal();
          }

    .. warning::

        .. code-block:: c++

            // 差 - indented directives
              if (lopsided_score) {
                #if DISASTER_PENDING  // 錯了！ "#if" 應該放在行開頭
                DropEverything();
                #endif                // 錯了！ "#endif" 不要縮進
                BackToNormal();
              }

8.15. 類格式
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    訪問控制塊的聲明依次序是 ``public:``, ``protected:``, ``private:``, 每次縮進 1 個空格.

類聲明 (對類註釋不瞭解的話, 參考 :ref:`類註釋 <class-comments>`) 的基本格式如下:

    .. code-block:: c++

        class MyClass : public OtherClass {
         public:      // 注意有 1 空格縮進!
          MyClass();  // 照常，2 空格縮進。
          explicit MyClass(int var);
          ~MyClass() {}

          void SomeFunction();
          void SomeFunctionThatDoesNothing() {
          }

          void set_some_var(int var) { some_var_ = var; }
          int some_var() const { return some_var_; }

         private:
          bool SomeInternalFunction();

          int some_var_;
          int some_other_var_;
          DISALLOW_COPY_AND_ASSIGN(MyClass);
        };

注意事項:

    - 所有基類名應在 80 列限制下盡量與子類名放在同一行.

    - 關鍵詞 ``public:``, ``protected:``, ``private:`` 要縮進 1 個空格.

    - 除第一個關鍵詞 (一般是 ``public``) 外, 其他關鍵詞前要空一行. 如果類比較小的話也可以不空.

    - 這些關鍵詞後不要保留空行.

    - ``public`` 放在最前面, 然後是 ``protected``, 最後是 ``private``.

    - 關於聲明順序的規則請參考 :ref:`聲明順序 <declaration-order>` 一節.

8.16. 構造函數初始值列表
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    構造函數初始值列表放在同一行或按四格縮進並排幾行.

下面兩種初始值列表方式都可以接受:

    .. code-block:: c++

        // 當全放在一行合適時：
        MyClass::MyClass(int var) : some_var_(var), some_other_var_(var + 1) {

或

    .. code-block:: c++

        // 如果要斷成多行，縮進四格，冒號放在第一行初始化句：
        MyClass::MyClass(int var)
            : some_var_(var),             // 4 空格縮進
              some_other_var_(var + 1) {  // 對準
          ...
          DoSomething();
          ...
        }

8.17. 名字空間格式化
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    名字空間內容不縮進.

:ref:`名字空間 <namespaces>` 不要增加額外的縮進層次, 例如:

    .. code-block:: c++

        namespace {

        void foo() {  // 正確。命名空間內沒有額外的縮進。
          ...
        }

        }  // namespace

不要縮進名字空間:

    .. warning::

        .. code-block:: c++

            namespace {

              // 錯，縮進多餘了。
              void foo() {
                ...
              }

            }  // namespace

聲明嵌套命名空間時，每命名空間都獨立成行。

    .. code-block:: c++

        namespace foo {
        namespace bar {

8.18. 水平留白
~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    水平留白的使用因地制宜. 永遠不要在行尾添加沒意義的留白.

常規:

    .. code-block:: c++

        void f(bool b) {  // 左大括號前恆有空格。
          ...
        int i = 0;  // 分號前不加空格。
        int x[] = { 0 };  // 大括號內部可與空格緊鄰也不可，不過兩邊都要加上。
        int x[] = {0};
        // 繼承與初始化列表中的冒號前後恆有空格。
        class Foo : public Bar {
         public:
          // 至於內聯函數實現，在大括號內部加上空格並編寫實現。
          Foo(int b) : Bar(), baz_(b) {}  // 大括號裡面是空的話，不加空格。
          void Reset() { baz_ = 0; }  // 用括號把大括號與實現分開。
          ...

    添加冗余的留白會給其他人編輯時造成額外負擔. 因此, 行尾不要留空格. 如果確定一行代碼已經修改完畢, 將多餘的空格去掉; 或者在專門清理空格時去掉（確信沒有其他人在處理). (Yang.Y 注: 現在大部分代碼編輯器稍加設置後, 都支持自動刪除行首/行尾空格, 如果不支持, 考慮換一款編輯器或 IDE)

循環和條件語句:

    .. code-block:: c++

        if (b) {          // if 條件語句和循環語句關鍵字後均有空格。
        } else {          // else 前後有空格。
        }
        while (test) {}   // 圓括號內部不緊鄰空格。
        switch (i) {
        for (int i = 0; i < 5; ++i) {
        switch ( i ) {    // 循環和條件語句的圓括號裡可以與空格緊鄰。
        if ( test ) {     // 圓括號，但這很少見。總之要一致。
        for ( int i = 0; i < 5; ++i ) {
        for ( ; i < 5 ; ++i) {  // 循環裡內 ; 後恆有空格，； 前可以加個空格。
        switch (i) {
          case 1:         // switch case 的冒號前無空格。
            ...
          case 2: break;  // 如果冒號有代碼，加個空格。

操作符:

    .. code-block:: c++

        // 賦值操作系統前後恆有空格。
        x = 0;

        // 其它二元操作符也前後恆有空格，不過對 factors 前後不加空格也可以。
        // 圓括號內部不緊鄰空格。
        v = w * x + y / z;
        v = w*x + y/z;
        v = w * (x + z);

        // 在參數和一元操作符之間不加空格。
        x = -5;
        ++x;
        if (x && !y)
          ...

模板和轉換:

    .. code-block:: c++

        // 尖叫括號(< and >) 不與空格緊鄰，< 前沒有空格，>( 之間也沒有。
        vector<string> x;
        y = static_cast<char*>(x);

        // 在類型與指針操作符之間留空格也可以，但要保持一致。
        vector<char *> x;
        set<list<string>> x;        // 在 C++11 代碼裡可以這樣用了。
        set<list<string> > x;       // C++03 中要在 > > 裡留個空格。

        // 您或許可以在 < < 裡加上一對對稱的空格。
        set< list<string> > x;

8.19. 垂直留白
~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    垂直留白越少越好.

這不僅僅是規則而是原則問題了: 不在萬不得已, 不要使用空行. 尤其是: 兩個函數定義之間的空行不要超過 2 行, 函數體首尾不要留空行, 函數體中也不要隨意添加空行.

基本原則是: 同一屏可以顯示的代碼越多, 越容易理解程序的控制流. 當然, 過於密集的代碼塊和過於疏鬆的代碼塊同樣難看, 取決於你的判斷. 但通常是垂直留白越少越好.

空行心得如下：

* 函數體內開頭或結尾的空行可讀性微乎其微。
* 在多重 if-else 塊裡加空行或許有點可讀性。

譯者 (YuleFox) 筆記
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. 對於代碼格式, 因人, 系統而異各有優缺點, 但同一個項目中遵循同一標準還是有必要的;
#. 行寬原則上不超過 80 列, 把 22 寸的顯示屏都占完, 怎麼也說不過去;
#. 盡量不使用非 ASCII 字符, 如果使用的話, 參考 UTF-8 格式 (尤其是 UNIX/Linux 下, Windows 下可以考慮寬字符), 盡量不將字符串常量耦合到代碼中, 比如獨立出資源文件, 這不僅僅是風格問題了;
#. UNIX/Linux 下無條件使用空格, MSVC 的話使用 Tab 也無可厚非;
#. 函數參數, 邏輯條件, 初始化列表: 要麼所有參數和函數名放在同一行, 要麼所有參數並排分行;
#. 除函數定義的左大括號可以置於行首外, 包括函數/類/結構體/枚舉聲明, 各種語句的左大括號置於行尾, 所有右大括號獨立成行;
#. ``.``/``->`` 操作符前後不留空格, ``*``/``&`` 不要前後都留, 一個就可, 靠左靠右依各人喜好;
#. 預處理指令/命名空間不使用額外縮進, 類/結構體/枚舉/函數/語句使用縮進;
#. 初始化用 ``=`` 還是 ``()`` 依個人喜好, 統一就好;
#. ``return`` 不要加 ``()``;
#. 水平/垂直留白不要濫用, 怎麼易讀怎麼來.
#. 關於 UNIX/Linux 風格為什麼要把左大括號置於行尾 (``.cc`` 文件的函數實現處, 左大括號位於行首), 我的理解是代碼看上去比較簡約, 想想行首除了函數體被一對大括號封在一起之外, 只有右大括號的代碼看上去確實也舒服; Windows 風格將左大括號置於行首的優點是匹配情況一目瞭然.

譯者（acgtyrant）筆記
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. 80 行限制事實上有助於避免代碼可讀性失控，比如超多重嵌套塊，超多重函數調用等等。
#. Linux 上設置好了 Locale 就幾乎一勞永逸設置好所有開發環境的編碼，不像奇葩的 Windows.
#. Google 強調有一對 if-else 時，不論有沒有嵌套，都要有大括號。Apple 正好`有栽過跟頭 <http://coolshell.cn/articles/11112.html>`_.
#. 其實我主張指針／地址操作符與變量名緊鄰，``int* a, b`` vs ``int *a, b``, 新手會誤以為前者的 ``b`` 是 ``int *`` 變量，但後者就不一樣了，高下立判。
#. 在這風格指南裡我才剛知道 C++ 原來還有所謂的 `Alternative operator representations <http://en.cppreference.com/w/cpp/language/operator_alternative>`_, 大概沒人用吧。
#. 注意構造函數初始值列表（Constructer Initializer List）與列表初始化（Initializer List）是兩碼事，我就差點混淆了它們的翻譯。
#. 事實上，如果您熟悉英語本身的書寫規則，就會發現該風格指南在格式上的規定與英語語法相當一脈相承。比如普通標點符號和單詞後面還有文本的話，總會留一個空格；特殊符號與單詞之間就不用留了，比如 ``if (true)`` 中的圓括號與 ``true``.
#. 本風格指南沒有明確規定 void 函數里要不要用 return 語句，不過就 Google 開源項目 leveldb 並沒有寫；此外從 `Is a blank return statement at the end of a function whos return type is void necessary? <http://stackoverflow.com/questions/9316717/is-a-blank-return-statement-at-the-end-of-a-function-whos-return-type-is-void-ne>`_ 來看，``return;`` 比 ``return ;`` 更約定俗成（事實上 cpplint 會對後者報錯，指出分號前有多餘的空格），且可用來提前跳出函數棧。
