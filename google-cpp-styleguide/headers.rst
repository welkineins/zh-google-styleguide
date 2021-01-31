1. 標頭檔 (Header Files)
----------------

通常每一個 ``.cc`` 文件都有一個對應的 ``.h`` 文件。也有一些常見例外, 如單元測試程式碼和只引入 ``main()`` 函式的 ``.cc`` 文件。

正確使用標頭檔可令程式碼在可讀性、文件大小和編譯性能上大為改觀。

下面的規則將引導你規避使用標頭檔時的各種陷阱。

.. _self-contained headers:

1.1. 自足的標頭檔
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    標頭檔應該能夠自給自足（self-contained），以 ``.h`` 結尾。至於用來插入其他檔案的文件，說到底它們並不是標頭檔，所以應以 ``.inc`` 結尾。但請不要以 ``-inl.h`` 的方式來撰寫標頭檔。

所有標頭檔要能夠自給自足。換言之，使用者和重構工具不需要為了使用一個標頭檔而引入額外更多的標頭檔。特別是，一個標頭檔應該要有 :ref:`define-guard`、引入所有它所需要的其它標頭檔，並且不需要任何特別的 symbols 在 include 它前需要被定義。

只有少數的例外，一個標頭檔不是自我滿足的而是用來安插到程式碼某處裡。例如某些文件會被重複的 include 或是文件內容實際上是特定平台（platform-specific）擴展部分。這些文件就要用 ``.inc`` 文件擴展名。

如果 ``.h`` 文件宣告了一個樣板或內聯 (inline) 函式，同時也在該文件加以定義。凡是有用到這些的 ``.cc`` 文件，就得統統引入該標頭檔，否則程式可能會在構建中連結失敗。現在不要把這些定義放到分離的 ``-inl.h`` 文件裡了（譯者註：過去該規範曾提倡把定義放到 -inl.h 裡過）。

有個例外：如果某函式樣板為所有相關模板參數顯式實例化，或本身就是類別的一個私有成員，那麼它就只能定義在實例化該模板的 ``.cc`` 文件裡。

.. _define-guard:

1.2. #define 保護 (#define Guard)
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    所有標頭檔都應該使用 ``#define`` 防止標頭檔被多次引入。建議的命名格式為 ``<PROJECT>_<PATH>_<FILE>_H_``

為保證唯一性，標頭檔的命名應該依據所在專案的完整路徑。例如：專案 foo 中的標頭檔 ``foo/src/bar/baz.h`` 可按如下方式保護：

.. code-block:: c++

    #ifndef FOO_BAR_BAZ_H_
    #define FOO_BAR_BAZ_H_
    …
    #endif // FOO_BAR_BAZ_H_

.. _forward-declarations:

1.3. 前置宣告（Forward Declaration）
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    避免使用前置宣告，直接引入需要的標頭檔即可。

定義：
    前置宣告是不提供與之關連的定義下，宣告一個類別、函式或是樣板。

.. code-block:: c++

    // In a C++ source file:
    class B;
    void FuncInB();
    extern int variable_in_b;
    ABSL_DECLARE_FLAG(flag_in_b);

優點：

	* 由於 ``#include`` 會強制編譯器開啟更多的檔案與處理更多的輸入，利用前置宣告減少 ``#include`` 可以減少編譯時間。
	* 越多的 ``#include`` 代表程式碼更可能因為相依的標頭檔更動而被重新編譯，使用前置宣告可以節省不必要的重新編譯。

缺點：

	* 前置宣告可能隱藏掉與標頭檔間的相依關係，導致當標頭檔改變時，相依的程式碼沒有被重新編譯。
	* 前置宣告可能在函式庫進行可向下相容的 API 改動時發生編譯錯誤。例如函式庫開發者放寬了某個參數類型、替樣板增加預設參數或更改命名空間等等。
	* 前置宣告來自 ``std::`` 命名空間的 symbols 會導致未定義行為 (undefined behavior)。
	* 難以抉擇是要使用前置宣告或是引入完整的標頭檔。在某些狀況下，使用前置宣告替換掉 ``#include`` 可能意外的修改了程式碼的意圖。如以下程式碼，若 ``#include`` 被替換成 B 和 D 的前置宣告，``test()`` 會呼叫到 ``f(void*)``。
	    
	    .. code-block:: c++
        
                // b.h:
                struct B {};
                struct D : B {};
            
                // good_user.cc:
                #include "b.h"
                void f(B*);
                void f(void*);
                void test(D* x) { f(x); }  // calls f(B*)            

	* 使用前置宣告多個 symbols 可能暴露了比直接引入標頭檔更多的訊息。
	* 為了使用前置宣告而修改程式碼（例如：使用指標成員而不是物件成員) 可能會導致程式運作較為緩慢或是更加的複雜。

結論：

	* 在任何狀況下避免使用前置宣告。
	* 當在標頭檔內使用到函式宣告時，總是引入對應的標頭檔。
	* 當使用類別樣板時，建議引入對應得標頭檔。


至於什麼時候引入標頭檔，參見 :ref:`name-and-order-of-includes`。

.. _inline-functions:

1.4. 內聯函式 (Inline Functions)
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    只有當函式非常的短，例如只有 10 行甚至更少的時候，才將其定義為內聯函式。

定義:

    當函式被宣告為內聯函式之後，代表你允許編譯器將其展開在該函式被呼叫的位置，而不是原來的函式呼叫機制進行。

優點:

    當函式主體比較小的時候，內聯該函式可以產生更有效率目標程式碼 (object code)。對於存取函式 (accessors)、賦值函式 (mutators) 以及其它函式體比較短或性能關鍵的函式，可以依據需求將其轉為內聯函式。

缺點:

    濫用內聯反而會導致程式變慢。內聯可能使目標程式碼變大或變小，這取決於內聯函式主體的大小。一個非常短小的存取函式被內聯通常會減少目標程式碼的大小， 但內聯一個相當大的函式將戲劇性的增加目標程式碼大小。現代的處理器 (CPU) 具備有指令緩存 (instruction cache)，執行小巧的程式碼往往執行更快。

結論:

    一個較為合理的經驗準則是，不要內聯超過 10 行的函式。謹慎對待解構子, 解構子往往比其表面看起來要更長, 因為有隱含的成員和父類別解構子被呼叫！

    另一個實用的經驗準則: 內聯那些包含循環或 ``switch`` 語句的函式常常是得不償失的 (除非在大多數情況下, 這些循環或 ``switch`` 語句從不被執行)。

    要注意的是，既使函式即使宣告為內聯，也不一定會被編譯器內聯。例如虛函式 (virtual) 和遞迴函式 (recursive) 就不會被正常內聯。通常, 遞迴函式不應該宣告成內聯函式。（譯註： 遞迴呼叫堆棧的展開並不像循環那麼簡單，例如遞迴層數在編譯時可能是未知的，大多數編譯器都不支持內聯遞迴函式)。虛函式內聯的主要原因則是想把它的函式主體放在類別的定義內， 可能式為了方便，或是當作文件描述其行為。例如存取函式或賦值函式。

.. _name-and-order-of-includes

1.5. ``#include`` 的路徑及順序
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::
    使用以下標準的標頭檔引入順序可增強可讀性，同時避免隱藏相依性：相關標頭檔 > C 函式庫 > C++ 函式庫 > 其他函式庫的 `.h` > 專案內的 `.h`。

專案內的標頭檔應按照專案目錄樹結構排列，避免使用 UNIX 特殊的目錄捷徑 ``.`` (當前目錄) 或 ``..`` (上層目錄)。例如：``google-awesome-project/src/base/logging.h`` 應該按如下方式引入:

    .. code-block:: c++

        #include "base/logging.h"

另一個例子是，若 ``dir/foo.cc`` 或 ``dir/foo_test.cc`` 的主要作用是實作或測試 ``dir2/foo2.h`` 的功能，``foo.cc`` 中引入標頭檔的次序應如下：

    #. ``dir2/foo2.h``
    #. C 系統文件
    #. C++ 系統文件
    #. 其他函式庫的 ``.h`` 文件
    #. 此專案內 ``.h`` 文件

使用這種排序方式，若 ``dir2/foo2.h`` 忽略了任何需要的標頭檔，在編譯 ``dir/foo.cc`` 或 ``dir/foo_test.cc`` 就會發生錯誤。因此這個規則可以確這些功能的保開發者可以在第一時間就發現錯誤，而不是波擊到無辜的人或是其他專案。

``dir/foo.cc`` 和 ``dir2/foo2.h`` 通常位於同一目錄下 (如 ``base/basictypes_test.cc`` 和 ``base/basictypes.h``)，但也可以放在不同目錄下。

標頭檔的順序在依照類別分類後，同類別的引入順序則應該依照按字母順序排列。若現有程式碼不是按照這個規則，應該在有空閒的時間將其修正。

你所依賴的 symbols 被哪些標頭檔所定義，你就應該引入（include）那些標頭檔，較為罕見的 :ref:`forward-declaration` 情況除外。例如你要用到 ``bar.h`` 中的某個 symbol，哪怕你所引入的 ``foo.h`` 已經引入了 ``bar.h``，你也應顯示的引入 ``bar.h``，除非 ``foo.h`` 有明確說明它會向你提供 ``bar.h`` 中的 symbol。不過，cc 文件所對應的標頭檔引入的其他標頭檔，就不需要再重複引入進其 cc 文件裡面了。例如 ``foo.cc`` 只引入 ``foo.h`` 就夠了，不用再引入 ``foo.h`` 中的其它內容。

舉例來說，``google-awesome-project/src/foo/internal/fooserver.cc`` 的引入次序如下：

	.. code-block:: c++

		#include "foo/public/fooserver.h"

		#include <sys/types.h>
		#include <unistd.h>
		#include <hash_map>
		#include <vector>

		#include "base/basictypes.h"
		#include "base/commandlineflags.h"
		#include "foo/public/bar.h"

例外：

有時，平台特定（system-specific）的程式碼需要依據條件被引入（conditional includes），這些程式碼可以放到其它的 includes 之後。當然，盡量讓你的平台特定程式碼小 (small) 且集中 (localized)，例如：

	.. code-block:: c++

		#include "foo/public/fooserver.h"

		#include "base/port.h"  // For LANG_CXX11.

		#ifdef LANG_CXX11
		#include <initializer_list>
		#endif  // LANG_CXX11

