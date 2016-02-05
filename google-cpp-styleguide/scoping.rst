2. 作用域
----------------

.. _namespaces:

2.1. 命名空間 (Namespaces)
~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    除了少數的例外，都建議使用把程式碼放在命名空間內。一個具名的命名空間應該擁有唯一的名字，其名稱可基於專案名稱，甚至是相對路徑。而在 ``.cc`` 文件內，使用匿名的命名空間是推薦的，但禁止使用 using 指示（using-directives）和內聯命名空間（inline namespaces）。

定義:

    命名空間將全域作用域細分為獨立的，具名的作用域可有效防止全域作用域的命名衝突。

優點:

    命名空間可以在大型專案內避免名稱衝突，同時又可以讓多數的程式碼有合理簡短的名稱。

    舉例來說, 兩個不同專案的全域作用域都有一個類別 ``Foo``，這樣在編譯或運行時期會造成衝突。如果每個專案將程式碼置於不同命名空間中，``project1::Foo`` 和 ``project2::Foo`` 在專案中就可以被視為不同的 symbols 而不會發生衝突。兩個類別在各自的命名空間中，也可以繼續使用 ``Foo`` 而不需要前綴命名空間。

    內聯命名空間會自動把內部的標識符放到外層作用域，比如：

    .. code-block:: c++

        namespace X {
        inline namespace Y {
        void foo();
        }
        }

    ``X::Y::foo()`` 與 ``X::foo()`` 彼此可以互換使用。內聯命名空間主要用來保持跨版本的 ABI 相容性。

缺點:

    命名空間可能造成疑惑，因為它增加了識別一個名稱所代表的意涵的難度。例如： ``Foo`` 是命名空間或是一個類別。

    內聯命名空間更是容易令人疑惑，因為它並不完全符合命名空間的定義；內聯命名空間只在大型版本控制裡會被使用到。

    在標頭檔中使用匿名命名空間容易導致違背 C++ 的唯一定義原則 (One Definition Rule (ODR))。
    
    在某些狀況中，經常會需要重複的使用完整 (fully-qualified) 的名稱來參考某些 symbols。對於多層巢狀的命名空間，這會增加許多混亂。

結論:

    根據下文將要提到的策略合理使用命名空間。記得在命名空間的結尾使用註解進行標示，例如下一節的範例程式一樣。

2.1.1. 匿名命名空間 (Unnamed Namespaces)
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

- 在 ``.cc`` 文件中, 可以使用匿名命名空間，甚至鼓勵使用來避免連結時期 (link time) 的命名衝突：

    .. code-block:: c++

        namespace {                             // .cc 文件中

        // 命名空間的內容無需縮排
        
        // 此函式產生出來的 symbol 保證不會和連結時期的其他 symbol 相撞。且此 symbol
        // 只能在這個 .cc 文件中被看到。
        bool UpdateInternals(Frobber* f, int newval) {
          ...
        }

        } // namespace

- 不要在 ``.h`` 文件中使用匿名命名空間。

2.1.2. 具名的命名空間
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

具名的命名空間使用方式如下：

    - 在 ``include``、`gflags <http://code.google.com/p/google-gflags/>`_ 的宣告/定義, 以及類別的前置宣告後，把整個原始碼文件剩下部分放置在命名空間內，以區別於其它命名空間：

        .. code-block:: c++

            // .h 文件
            namespace mynamespace {

            // 所有宣告都置於命名空間中
            // 注意不要使用縮排
            class MyClass {
                public:
                …
                void Foo();
            };

            } // namespace mynamespace

        .. code-block:: c++

            // .cc 文件
            namespace mynamespace {

            // 函式定義都置於命名空間中
            void MyClass::Foo() {
                …
            }

            } // namespace mynamespace

        通常的 ``.cc`` 文件包含更多，更複雜的細節，例如 flags 或 using-declarations。

        .. code-block:: c++

            #include "a.h"

            DEFINE_bool(someflag, false, "dummy flag");
            
            using ::foo::bar;

            namespace a {

            …code for a…

            } // namespace a


    - 不要在命名空間 ``std`` 內宣告任何東西，包括標準函式庫的類別前置宣告。在 ``std`` 命名空間宣告實體 (entities) 會導致不確定行為 (undefined behavior)。比如不可移植。要宣告標準函式庫內的實體，直接 ``include ``對應的標頭檔。

    - 最好不要使用 using-directive 來導出一個命名空間下的所有名稱。

        .. code-block:: c++

            // 禁止 —— 這會污染命名空間
            using namespace foo;
    
    - 不要在標頭檔中的一個命名空間的作用域內使用命名空間別名 (Namespace aliases)，除非該命名空間僅限於內部使用 (internal-only)。因為此操作會導致這些外部引入的東西變成此標頭檔公開的 API 的一部份。
    
        .. code-block:: c++

            // 在 .cc 文件裡可以用來縮短常使用的名稱
            namespace fbz = ::foo::bar::baz;

            // 在 .h 文件裡可以用來縮短常使用的名稱
            namespace librarian {
            namespace impl { // 內部使用，不公開於 API
            namespace sidetable = ::pipeline_diagnostics::sidetable;
            } // namespace impl

            inline void my_inline_function() {
              // 命名空間別名到一個函式或方法
              namespace fbz = ::foo::bar::baz;
              ...
            }
            }  // namespace librarian

    - 禁止用內聯命名空間

2.2. 非成員函式、靜態成員函式和全域函式
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    建議將非成員函式放置在命名空間中，盡量不要使用完全的全域函示。建議利用命名空間來放置相關的多個函式，而不是放置在把類別當作命名空間來使用。類別的靜態方法一般來說要和類別的實例或類別的靜態資料有緊密的關連。

優點:

    某些情況下，非成員函式和靜態成員函式是非常有用的。將非成員函式放在命名空間內可避免對於全域作用域污染。

缺點:

    為非成員函式和靜態成員函式準備一個新的類別可能更有意義，特別是它們需要存取外部資源或式有大量的相依性關係時。

結論:

    有時候定義一個不綁定特定類別實例的函式是有用的，甚至是必要的。這樣的函式可以被定義成靜態成員或是非成員函式。非成員函式不應該依賴於外部變數，且應該總是放置於某個命名空間內。相比單純為了封裝不共享任何靜態數據的靜態成員函式而創建一個類別，不如之直接使用 :ref:`namespaces`。例如對於 myproject/foo_bar.h 標頭擋來說，可以這樣寫。
    
    
.. code-block:: c++

   namespace myproject {
   namespace foo_bar {
   void Function1();
   void Function2();
   }
   }
   
而不是

.. code-block:: c++

   namespace myproject {
   class FooBar {
    public:
     static void Function1();
     static void Function2();
   };
   }


如果你必須定義非成員函式，又只是在 ``.cc`` 文件中使用它，則可使用匿名:ref:`namespaces`或 ``static`` 連結關鍵字 (例如： ``static int Foo() {...}``) 限定其作用域。

2.3. 區域變數
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    盡可能將函式內的變數的作用域最小化，並在變量宣告時進行初始化。

C++ 允許在函式內的任何位置宣告變數。我們鼓勵在盡可能小的作用域中宣告變量，並且離第一次使用的地方越近越好。這會讓閱讀者更容易找到變數宣告的位置、宣告的類型和初始值。要注意，應該該宣告時直接初始化變數，而不要先代宣告再後賦值, 例如：

    .. code-block:: c++

        int i;
        i = f(); // 不推薦 -- 初始化和宣告分離

    .. code-block:: c++
    
        int j = g(); // 推薦 -- 宣告時初始化

    .. code-block:: c++
    
        vector<int> v;
        v.push_back(1); // 用下面的括號初始化法會更好
        v.push_back(2);

    .. code-block:: c++
    
        vector<int> v = {1, 2}; // 推薦 v 在宣告時初始化

在 ``if``、``while``和``for``陳述句需要的變數一般都會宣告在這些陳述句中，也就是這些變數會存活於這些作用域內。例如：

    .. code-block:: c++

        while (const char* p = strchr(str, '/')) str = p + 1;

一個特例：如果變數是一個物件，每次進入作用域時其建構子都會被呼叫，每次離開作用域時其解構子都會被呼叫。

    .. code-block:: c++

        // 沒效率的實作
        for (int i = 0; i < 1000000; ++i) {
            Foo f; // 建構子和解構子分別呼叫 1000000 次。
            f.DoSomething(i);
        }

在循環作用域外面宣告這類型的變數可能更加的有效率。

    .. code-block:: c++

        Foo f; // 建構子和解構子只呼叫 1 次
        for (int i = 0; i < 1000000; ++i) {
            f.DoSomething(i);
        }

2.4. 靜態和全域變數
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

禁止使用具有 `靜態生存週期 (static strage duration) <http://en.cppreference.com/w/cpp/language/storage_duration#Storage_duration>`__ 的類別函式：它們會因為不確定的建構和解構子呼叫順序而產生難以發現的臭蟲。不過 ``constexpr`` 變數除外，因為它們不牽涉到動態初始化或解構。

全域變數、靜態變數、靜態類別成員變數和函式內靜態變數等都具有靜態生存週期，都必須是原始資料類型 (POD : Plain Old Data)：即 int、chars 、floats 或前三者的指標、陣列和結構體。

靜態變數的建構子、解構子和初始化的順序在 C++ 中規範並不完整，甚至可能每次建置都不同，進而導致難以發現的臭蟲。所以除了禁用類別的全域變量外，也不允許使用函式的返回值來初始化靜態變數，除非該函式（比如 getenv() 或 getpid()）不涉及任何全域變數。但是函式作用域裡的 POD 變數則可以使用函式返回值來初始化，畢竟它的初始化順序是有明確定義的，而且只會在程式流程執行到它的宣告時才會發生。）

同理，全域和靜態變數在程式結束時會被解構，無論所謂結束是從 ``main()`` 返回還是呼叫了 ``exit()``。解構的順序被定義為其建構子呼叫順序的反序。但既然建構順序未定義，那麼解構順序當然也就不確定了。例如，在程式結束時某個靜態變數已經被解構了，但程式還在運行，這時可能有另一個執行緒嘗試要存取這個變數卻失敗了；另一個例子，一個靜態的 string 變量也許會在一個參考它的的其它變數被解構之前被解構掉。

解決以上解構順序問題的方法之一是用 ``quick_exit()`` 來代替 ``exit()`` 來結束程式。它們的不同之處是前者不會執行任何解構子，也不會執行 ``atexit()`` 所註冊的任何 handlers。如果在使用 ``quick_exit()`` 來結束成式時仍然想要執行 handler（例如 flush log），你可以使用 ``_at_quick_exit()`` 來註冊 handler。(如果你想在 ``exit()`` 和 ``quick_exit()`` 都用使用該 handler，則可以將其註冊到這兩個函式。

綜上所述，我們只允許 POD 類型的靜態變數，這代表完全禁用 ``vector`` (使用 C 陣列替代) 和 ``string`` (使用 ``const char []``)。

如果你真的需要一個 ``class`` 類型的靜態或全域變數，可以考慮在 ``main()`` 函式或 ``pthread_once()`` 內初始化一個指標且永不回收 (free)。注意只能用 raw 指標，別用智慧指標，因為後者的解構子涉及到上文指出的不定順序問題。
