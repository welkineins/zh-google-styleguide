6. 命名約定
------------------

最重要的一致性規則是命名管理. 命名風格快速獲知名字代表是什麼東東: 類型? 變數? 函數? 常數? 巨集 ... ? 甚至不需要去查找類型宣告. 我們大腦中的模式匹配引擎可以非常可靠的處理這些命名規則.

命名規則具有一定隨意性, 但相比按個人喜好命名, 一致性更重, 所以不管你怎麼想, 規則總歸是規則.

6.1. 通用命名規則
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    函數命名，變數命名，文件命名要有描述性；少用縮寫。

    盡可能給有描述性的命名，別心疼空間，畢竟讓程式碼易於新讀者理解很重要。不要用只有專案開發者能理解的縮寫，也不要通過砍掉幾個字母來縮寫單詞。

    .. code-block:: c++

        int price_count_reader;    // 無縮寫
        int num_errors;            // 「num」 本來就很常見
        int num_dns_connections;   // 人人都知道 「DNS」 是啥

    .. warning::

        .. code-block:: c++

            int n;                     // 莫名其妙。
            int nerr;                  // 怪縮寫。
            int n_comp_conns;          // 怪縮寫。
            int wgc_connections;       // 只有貴團隊知道是啥意思。
            int pc_reader;             // "pc" 有太多可能的解釋了。
            int cstmr_id;              // 有刪減若干字母。

6.2. 文件命名
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    文件名要全部小寫, 可以包含底線 (``_``) 或連字符 (``-``). 按專案約定來. 如果並沒有專案約定，"_" 更好。

    可接受的文件命名::

        * my_useful_class.cc
        * my-useful-class.cc
        * myusefulclass.cc
        * muusefulclass_test.cc // ``_unittest`` 和 ``_regtest`` 已棄用。

    C++ 文件要以 ``.cc`` 結尾, 標頭檔以 ``.h`` 結尾. 專門插入文本的文件則以 ``.inc`` 結尾，參見:ref:`self-contained headers`。

    不要使用已經存在於 ``/usr/include`` 下的文件名 (Yang.Y 注: 即編譯器搜索系統標頭檔的路徑), 如 ``db.h``.

    通常應盡量讓文件名更加明確. ``http_server_logs.h`` 就比 ``logs.h`` 要好. 定義類時文件名一般成對出現, 如 ``foo_bar.h`` 和 ``foo_bar.cc``, 對應於類 ``FooBar``.

    內聯函數必須放在 ``.h`` 文件中. 如果內聯函數比較短, 就直接放在 ``.h`` 中.

6.3. 類型命名
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    類型名稱的每個單詞首字母均大寫, 不包含底線: ``MyExcitingClass``, ``MyExcitingEnum``.

所有類型命名 —— 類, 結構體, 類型定義 (``typedef``), 列舉 —— 均使用相同約定. 例如:

    .. code-block:: c++

        // classes and structs
        class UrlTable { ...
        class UrlTableTester { ...
        struct UrlTableProperties { ...

        // typedefs
        typedef hash_map<UrlTableProperties *, string> PropertiesMap;

        // enums
        enum UrlTableErrors { ...

6.4. 變數命名
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    變數名一律小寫, 單詞之間用底線連接. 類的成員變量以下劃線結尾, 但結構體的就不用，如:: ``a_local_variable``, ``a_struct_data_member``, ``a_class_data_member_``.

普通變數命名:

    舉例::

        string table_name;  // 可 - 用底線。
        string tablename;   // 可 - 全小寫。

    .. warning::
        .. code-block:: c++

            string tableName;   // 差 - 混合大小寫。

類數據成員：

    不管是靜態的還是非靜態的，類數據成員都可以和普通變數一樣, 但要接底線。

        .. code-block:: c++

            class TableInfo {
              ...
             private:
              string table_name_;  // 可 - 尾後加底線。
              string tablename_;   // 可。
              static Pool<TableInfo>* pool_;  // 可。
            };

結構體變數:

    不管是靜態的還是非靜態的，結構體數據成員都可以和普通變數一樣, 不用像類那樣接底線:

        .. code-block:: c++

            struct UrlTableProperties {
                string name;
                int num_entries;
            }

    結構體與類的討論參考 :ref:`結構體 vs. 類 <structs_vs_classes>` 一節.

全域變數:

    對全域變數沒有特別要求, 少用就好, 但如果你要用, 可以用 ``g_`` 或其它標誌作為前綴, 以便更好的區分局部變量.

.. _constant-names:

6.5. 常數命名
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    在全域或類裡的常數名稱前加 ``k``: kDaysInAWeek. 且除去開頭的 ``k`` 之外每個單詞開頭字母均大寫。

    所有編譯時常數, 無論是局部的, 全域的還是類中的, 和其他變數稍微區別一下. ``k`` 後接大寫字母開頭的單詞:

        .. code-block:: c++

            const int kDaysInAWeek = 7;

    這規則適用於編譯時的局部作用域常數，不過要按變數規則來命名也可以。

.. _function-names:

6.6. 函數命名
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    常規函數使用大小寫混合, 取值和設值函數則要求與變數名匹配: ``MyExcitingFunction()``, ``MyExcitingMethod()``, ``my_exciting_member_variable()``, ``set_my_exciting_member_variable()``.

常規函數:

    函數名的每個單詞首字母大寫, 沒有底線。

    如果你的某函數出錯時就要直接 crash, 那麼就在函數名加上 OrDie. 但這函數本身必須集成在產品程式碼裡，且平時也可能會出錯。

        .. code-block:: c++

            AddTableEntry()
            DeleteUrl()
            OpenFileOrDie()

取值和設值函數:

    取值（Accessors）和設值（Mutators）函數要與存取的變數名匹配. 這兒摘錄一個類, ``num_entries_`` 是該類的實例變量:

        .. code-block:: c++

            class MyClass {
                public:
                    ...
                    int num_entries() const { return num_entries_; }
                    void set_num_entries(int num_entries) { num_entries_ = num_entries; }

                private:
                    int num_entries_;
            };

    其它非常短小的內聯函數名也可以用小寫字母, 例如. 如果你在循環中呼叫這樣的函數甚至都不用緩存其返回值, 小寫命名就可以接受.

6.7. 命名空間命名
~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    命名空間用小寫字母命名, 並基於專案名稱和目錄結構: ``google_awesome_project``.

關於命名空間的討論和如何命名, 參考 :ref:`名字空間 <namespaces>` 一節.

6.8. 列舉命名
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    列舉的命名應當和 :ref:`常數 <constant-names>` 或 :ref:`巨集 <macro-names>` 一致: ``kEnumName`` 或是 ``ENUM_NAME``.

單獨的列舉值應該優先採用 :ref:`常數 <constant-names>` 的命名方式. 但 :ref:`巨集 <macro-names>` 方式的命名也可以接受. 枚舉名 ``UrlTableErrors`` (以及 ``AlternateUrlTableErrors``) 是類型, 所以要用大小寫混合的方式.
    .. code-block:: c++

        enum UrlTableErrors {
            kOK = 0,
            kErrorOutOfMemory,
            kErrorMalformedInput,
        };
        enum AlternateUrlTableErrors {
            OK = 0,
            OUT_OF_MEMORY = 1,
            MALFORMED_INPUT = 2,
        };

2009 年 1 月之前, 我們一直建議採用 :ref:`巨集 <macro-names>` 的方式命名列舉值. 由於枚舉值和宏之間的命名衝突, 直接導致了很多問題. 由此, 這裡改為優先選擇常數風格的命名方式. 新程式碼應該盡可能優先使用常量風格. 但是老程式碼沒必要切換到常量風格, 除非宏風格確實會產生編譯期問題.

.. _macro-names:

6.9. 巨集命名
~~~~~~~~~~~~~~~~~~

.. tip::

    你並不打算:ref:`使用巨集 <preprocessor-macros>`, 對吧? 如果你一定要用, 像這樣命名: ``MY_MACRO_THAT_SCARES_SMALL_CHILDREN``.

參考:ref:`前處理巨集 <preprocessor-macros>`; 通常 *不應該* 使用宏. 如果不得不用, 其命名像列舉命名一樣全部大寫, 使用底線::

    #define ROUND(x) ...
    #define PI_ROUNDED 3.0

6.10. 命名規則的特例
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    如果你命名的實體與已有 C/C++ 實體相似, 可參考現有命名策略.

``bigopen()``:

    函數名, 參照 ``open()`` 的形式

``uint``:

    ``typedef``

``bigpos``:

    ``struct`` 或 ``class``, 參照 ``pos`` 的形式

``sparse_hash_map``:

    STL 相似實體; 參照 STL 命名約定

``LONGLONG_MAX``:

    常數, 如同 ``INT_MAX``

譯者（acgtyrant）筆記
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. 感覺 Google 的命名約定很高明，比如寫了簡單的類 QueryResult, 接著又可以直接定義一個變數 query_result, 區分度很好；再次，類內變量以底線結尾，那麼就可以直接傳入同名的形參，比如 TextQuery::TextQuery(std::string word) : word_(word) {}, 其中 ``word_`` 自然是類內私有成員。
