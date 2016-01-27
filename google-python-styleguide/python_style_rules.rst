Python風格規範
================================

分號
--------------------

.. tip::
    不要在行尾加分號, 也不要用分號將兩條命令放在同一行.

.. _line_length:

行長度
--------------------

.. tip::
    每行不超過80個字符

例外:

#. 長的導入模塊語句
#. 註釋裡的URL

不要使用反斜槓連接行.

Python會將 `圓括號, 中括號和花括號中的行隱式的連接起來 <http://docs.python.org/2/reference/lexical_analysis.html#implicit-line-joining>`_ , 你可以利用這個特點. 如果需要, 你可以在表達式外圍增加一對額外的圓括號.

.. code-block:: python

    Yes: foo_bar(self, width, height, color='black', design=None, x='foo',
                 emphasis=None, highlight=0)

         if (width == 0 and height == 0 and
             color == 'red' and emphasis == 'strong'):

如果一個文本字符串在一行放不下, 可以使用圓括號來實現隱式行連接:

.. code-block:: python

    x = ('This will build a very long long '
         'long long long long long long string')

在註釋中，如果必要，將長的URL放在一行上。

.. code-block:: python

    Yes:  # See details at
          # http://www.example.com/us/developer/documentation/api/content/v2.0/csv_file_name_extension_full_specification.html

.. code-block:: python

    No:  # See details at
         # http://www.example.com/us/developer/documentation/api/content/\
         # v2.0/csv_file_name_extension_full_specification.html

注意上面例子中的元素縮進; 你可以在本文的 :ref:`縮進 <indentation>` 部分找到解釋.

括號
--------------------

.. tip::
    寧缺毋濫的使用括號

除非是用於實現行連接, 否則不要在返回語句或條件語句中使用括號. 不過在元組兩邊使用括號是可以的.

.. code-block:: python

    Yes: if foo:
             bar()
         while x:
             x = bar()
         if x and y:
             bar()
         if not x:
             bar()
         return foo
         for (x, y) in dict.items(): ...

.. code-block:: python

    No:  if (x):
             bar()
         if not(x):
             bar()
         return (foo)

.. _indentation:

縮進
--------------------

.. tip::
    用4個空格來縮進代碼

絕對不要用tab, 也不要tab和空格混用. 對於行連接的情況, 你應該要麼垂直對齊換行的元素(見 :ref:`行長度 <line_length>` 部分的示例), 或者使用4空格的懸掛式縮進(這時第一行不應該有參數):

.. code-block:: python

   Yes:   # Aligned with opening delimiter
          foo = long_function_name(var_one, var_two,
                                   var_three, var_four)

          # Aligned with opening delimiter in a dictionary
          foo = {
              long_dictionary_key: value1 +
                                   value2,
              ...
          }

          # 4-space hanging indent; nothing on first line
          foo = long_function_name(
              var_one, var_two, var_three,
              var_four)

          # 4-space hanging indent in a dictionary
          foo = {
              long_dictionary_key:
                  long_dictionary_value,
              ...
          }

.. code-block:: python

    No:    # Stuff on first line forbidden
          foo = long_function_name(var_one, var_two,
              var_three, var_four)

          # 2-space hanging indent forbidden
          foo = long_function_name(
            var_one, var_two, var_three,
            var_four)

          # No hanging indent in a dictionary
          foo = {
              long_dictionary_key:
                  long_dictionary_value,
                  ...
          }

空行
--------------------

.. tip::
    頂級定義之間空兩行, 方法定義之間空一行

頂級定義之間空兩行, 比如函數或者類定義. 方法定義, 類定義與第一個方法之間, 都應該空一行. 函數或方法中, 某些地方要是你覺得合適, 就空一行.


空格
--------------------

.. tip::
    按照標準的排版規範來使用標點兩邊的空格

括號內不要有空格.

.. code-block:: python

    Yes: spam(ham[1], {eggs: 2}, [])

.. code-block:: python

    No:  spam( ham[ 1 ], { eggs: 2 }, [ ] )

不要在逗號, 分號, 冒號前面加空格, 但應該在它們後面加(除了在行尾).

.. code-block:: python

    Yes: if x == 4:
             print x, y
         x, y = y, x

.. code-block:: python

    No:  if x == 4 :
             print x , y
         x , y = y , x

參數列表, 索引或切片的左括號前不應加空格.

.. code-block:: python

    Yes: spam(1)

.. code-block:: python

    no: spam (1)

.. code-block:: python

    Yes: dict['key'] = list[index]

.. code-block:: python

    No:  dict ['key'] = list [index]

在二元操作符兩邊都加上一個空格, 比如賦值(=), 比較(==, <, >, !=, <>, <=, >=, in, not in, is, is not), 布爾(and, or, not).  至於算術操作符兩邊的空格該如何使用, 需要你自己好好判斷. 不過兩側務必要保持一致.

.. code-block:: python

    Yes: x == 1

.. code-block:: python

    No:  x<1

當'='用於指示關鍵字參數或默認參數值時, 不要在其兩側使用空格.

.. code-block:: python

    Yes: def complex(real, imag=0.0): return magic(r=real, i=imag)

.. code-block:: python

    No:  def complex(real, imag = 0.0): return magic(r = real, i = imag)

不要用空格來垂直對齊多行間的標記, 因為這會成為維護的負擔(適用於:, #, =等):

.. code-block:: python

    Yes:
         foo = 1000  # comment
         long_name = 2  # comment that should not be aligned

         dictionary = {
             "foo": 1,
             "long_name": 2,
             }

.. code-block:: python

    No:
         foo       = 1000  # comment
         long_name = 2     # comment that should not be aligned

         dictionary = {
             "foo"      : 1,
             "long_name": 2,
             }

Shebang
--------------------

.. tip::
    大部分.py文件不必以#!作為文件的開始. 根據 `PEP-394 <http://www.python.org/dev/peps/pep-0394/>`_ , 程序的main文件應該以 #!/usr/bin/python2或者 #!/usr/bin/python3開始.

(譯者注: 在計算機科學中, `Shebang <http://en.wikipedia.org/wiki/Shebang_(Unix)>`_ (也稱為Hashbang)是一個由井號和歎號構成的字符串行(#!), 其出現在文本文件的第一行的前兩個字符. 在文件中存在Shebang的情況下, 類Unix操作系統的程序載入器會分析Shebang後的內容, 將這些內容作為解釋器指令, 並調用該指令, 並將載有Shebang的文件路徑作為該解釋器的參數. 例如, 以指令#!/bin/sh開頭的文件在執行時會實際調用/bin/sh程序.)

#!先用於幫助內核找到Python解釋器, 但是在導入模塊時, 將會被忽略. 因此只有被直接執行的文件中才有必要加入#!.


.. _comments:

註釋
--------------------

.. tip::
    確保對模塊, 函數, 方法和行內註釋使用正確的風格

**文檔字符串**

    Python有一種獨一無二的的註釋方式: 使用文檔字符串. 文檔字符串是包, 模塊, 類或函數里的第一個語句. 這些字符串可以通過對象的__doc__成員被自動提取, 並且被pydoc所用. (你可以在你的模塊上運行pydoc試一把, 看看它長什麼樣). 我們對文檔字符串的慣例是使用三重雙引號"""( `PEP-257 <http://www.python.org/dev/peps/pep-0257/>`_ ). 一個文檔字符串應該這樣組織: 首先是一行以句號, 問號或驚歎號結尾的概述(或者該文檔字符串單純只有一行). 接著是一個空行. 接著是文檔字符串剩下的部分, 它應該與文檔字符串的第一行的第一個引號對齊. 下面有更多文檔字符串的格式化規範.

**模塊**

    每個文件應該包含一個許可樣板. 根據項目使用的許可(例如, Apache 2.0, BSD, LGPL, GPL), 選擇合適的樣板.

**函數和方法**

    下文所指的函數,包括函數, 方法, 以及生成器.

    一個函數必須要有文檔字符串, 除非它滿足以下條件:

    #. 外部不可見
    #. 非常短小
    #. 簡單明瞭

    文檔字符串應該包含函數做什麼, 以及輸入和輸出的詳細描述. 通常, 不應該描述"怎麼做", 除非是一些複雜的算法. 文檔字符串應該提供足夠的信息, 當別人編寫代碼調用該函數時, 他不需要看一行代碼, 只要看文檔字符串就可以了. 對於複雜的代碼, 在代碼旁邊加註釋會比使用文檔字符串更有意義.

    關於函數的幾個方面應該在特定的小節中進行描述記錄， 這幾個方面如下文所述. 每節應該以一個標題行開始. 標題行以冒號結尾. 除標題行外, 節的其他內容應被縮進2個空格.

    Args:
        列出每個參數的名字, 並在名字後使用一個冒號和一個空格, 分隔對該參數的描述.如果描述太長超過了單行80字符,使用2或者4個空格的懸掛縮進(與文件其他部分保持一致).
        描述應該包括所需的類型和含義.
        如果一個函數接受*foo(可變長度參數列表)或者**bar (任意關鍵字參數), 應該詳細列出*foo和**bar.

    Returns: (或者 Yields: 用於生成器)
        描述返回值的類型和語義. 如果函數返回None, 這一部分可以省略.

    Raises:
        列出與接口有關的所有異常.

    .. code-block:: python

        def fetch_bigtable_rows(big_table, keys, other_silly_variable=None):
            """Fetches rows from a Bigtable.

            Retrieves rows pertaining to the given keys from the Table instance
            represented by big_table.  Silly things may happen if
            other_silly_variable is not None.

            Args:
                big_table: An open Bigtable Table instance.
                keys: A sequence of strings representing the key of each table row
                    to fetch.
                other_silly_variable: Another optional variable, that has a much
                    longer name than the other args, and which does nothing.

            Returns:
                A dict mapping keys to the corresponding table row data
                fetched. Each row is represented as a tuple of strings. For
                example:

                {'Serak': ('Rigel VII', 'Preparer'),
                 'Zim': ('Irk', 'Invader'),
                 'Lrrr': ('Omicron Persei 8', 'Emperor')}

                If a key from the keys argument is missing from the dictionary,
                then that row was not found in the table.

            Raises:
                IOError: An error occurred accessing the bigtable.Table object.
            """
            pass

**類**

    類應該在其定義下有一個用於描述該類的文檔字符串. 如果你的類有公共屬性(Attributes), 那麼文檔中應該有一個屬性(Attributes)段. 並且應該遵守和函數參數相同的格式.

    .. code-block:: python

        class SampleClass(object):
            """Summary of class here.

            Longer class information....
            Longer class information....

            Attributes:
                likes_spam: A boolean indicating if we like SPAM or not.
                eggs: An integer count of the eggs we have laid.
            """

            def __init__(self, likes_spam=False):
                """Inits SampleClass with blah."""
                self.likes_spam = likes_spam
                self.eggs = 0

            def public_method(self):
                """Performs operation blah."""



**塊註釋和行註釋**

    最需要寫註釋的是代碼中那些技巧性的部分. 如果你在下次 `代碼審查 <http://en.wikipedia.org/wiki/Code_review>`_ 的時候必須解釋一下, 那麼你應該現在就給它寫註釋. 對於複雜的操作, 應該在其操作開始前寫上若干行註釋. 對於不是一目瞭然的代碼, 應在其行尾添加註釋.

    .. code-block:: python

        # We use a weighted dictionary search to find out where i is in
        # the array.  We extrapolate position based on the largest num
        # in the array and the array size and then do binary search to
        # get the exact number.

        if i & (i-1) == 0:        # true iff i is a power of 2

    為了提高可讀性, 註釋應該至少離開代碼2個空格.

    另一方面, 絕不要描述代碼. 假設閱讀代碼的人比你更懂Python, 他只是不知道你的代碼要做什麼.

    .. code-block:: python

        # BAD COMMENT: Now go through the b array and make sure whenever i occurs
        # the next element is i+1


類
--------------------

.. tip::
    如果一個類不繼承自其它類, 就顯式的從object繼承. 嵌套類也一樣.

.. code-block:: python

    Yes: class SampleClass(object):
             pass


         class OuterClass(object):

             class InnerClass(object):
                 pass


         class ChildClass(ParentClass):
             """Explicitly inherits from another class already."""

.. code-block:: python

    No: class SampleClass:
            pass


        class OuterClass:

            class InnerClass:
                pass

繼承自 ``object`` 是為了使屬性(properties)正常工作, 並且這樣可以保護你的代碼, 使其不受Python 3000的一個特殊的潛在不兼容性影響. 這樣做也定義了一些特殊的方法, 這些方法實現了對象的默認語義, 包括 ``__new__, __init__, __delattr__, __getattribute__, __setattr__, __hash__, __repr__, and __str__`` .

字符串
--------------------

.. tip::
    即使參數都是字符串, 使用%操作符或者格式化方法格式化字符串. 不過也不能一概而論, 你需要在+和%之間好好判定.

.. code-block:: python

    Yes: x = a + b
         x = '%s, %s!' % (imperative, expletive)
         x = '{}, {}!'.format(imperative, expletive)
         x = 'name: %s; score: %d' % (name, n)
         x = 'name: {}; score: {}'.format(name, n)

.. code-block:: python

    No: x = '%s%s' % (a, b)  # use + in this case
        x = '{}{}'.format(a, b)  # use + in this case
        x = imperative + ', ' + expletive + '!'
        x = 'name: ' + name + '; score: ' + str(n)

避免在循環中用+和+=操作符來累加字符串. 由於字符串是不可變的, 這樣做會創建不必要的臨時對像, 並且導致二次方而不是線性的運行時間. 作為替代方案, 你可以將每個子串加入列表, 然後在循環結束後用 ``.join`` 連接列表. (也可以將每個子串寫入一個 ``cStringIO.StringIO`` 緩存中.)

.. code-block:: python

    Yes: items = ['<table>']
         for last_name, first_name in employee_list:
             items.append('<tr><td>%s, %s</td></tr>' % (last_name, first_name))
         items.append('</table>')
         employee_table = ''.join(items)

.. code-block:: python

    No: employee_table = '<table>'
        for last_name, first_name in employee_list:
            employee_table += '<tr><td>%s, %s</td></tr>' % (last_name, first_name)
        employee_table += '</table>'

在同一個文件中, 保持使用字符串引號的一致性. 使用單引號'或者雙引號"之一用以引用字符串, 並在同一文件中沿用. 在字符串內可以使用另外一種引號, 以避免在字符串中使用\. GPyLint已經加入了這一檢查.

(譯者注:GPyLint疑為筆誤, 應為PyLint.)

.. code-block:: python

   Yes:
        Python('Why are you hiding your eyes?')
        Gollum("I'm scared of lint errors.")
        Narrator('"Good!" thought a happy Python reviewer.')

.. code-block:: python

   No:
        Python("Why are you hiding your eyes?")
        Gollum('The lint. It burns. It burns us.')
        Gollum("Always the great lint. Watching. Watching.")

為多行字符串使用三重雙引號"""而非三重單引號'''. 當且僅當項目中使用單引號'來引用字符串時, 才可能會使用三重'''為非文檔字符串的多行字符串來標識引用. 文檔字符串必須使用三重雙引號""". 不過要注意, 通常用隱式行連接更清晰, 因為多行字符串與程序其他部分的縮進方式不一致.

.. code-block:: python

    Yes:
        print ("This is much nicer.\n"
               "Do it this way.\n")

.. code-block:: python

    No:
          print """This is pretty ugly.
      Don't do this.
      """

文件和sockets
--------------------

.. tip::
    在文件和sockets結束時, 顯式的關閉它.

除文件外, sockets或其他類似文件的對象在沒有必要的情況下打開, 會有許多副作用, 例如:

#. 它們可能會消耗有限的系統資源, 如文件描述符. 如果這些資源在使用後沒有及時歸還系統, 那麼用於處理這些對象的代碼會將資源消耗殆盡.
#. 持有文件將會阻止對於文件的其他諸如移動、刪除之類的操作.
#. 僅僅是從邏輯上關閉文件和sockets, 那麼它們仍然可能會被其共享的程序在無意中進行讀或者寫操作. 只有當它們真正被關閉後, 對於它們嘗試進行讀或者寫操作將會跑出異常, 並使得問題快速顯現出來.

而且, 幻想當文件對像析構時, 文件和sockets會自動關閉, 試圖將文件對象的生命週期和文件的狀態綁定在一起的想法, 都是不現實的. 因為有如下原因:

#. 沒有任何方法可以確保運行環境會真正的執行文件的析構. 不同的Python實現採用不同的內存管理技術, 比如延時垃圾處理機制. 延時垃圾處理機制可能會導致對像生命週期被任意無限制的延長.

#. 對於文件意外的引用,會導致對於文件的持有時間超出預期(比如對於異常的跟蹤, 包含有全局變量等).

推薦使用 `"with"語句 <http://docs.python.org/reference/compound_stmts.html#the-with-statement>`_ 以管理文件:

.. code-block:: python

      with open("hello.txt") as hello_file:
          for line in hello_file:
              print line

對於不支持使用"with"語句的類似文件的對象,使用 contextlib.closing():

.. code-block:: python

      import contextlib

      with contextlib.closing(urllib.urlopen("http://www.python.org/")) as front_page:
          for line in front_page:
              print line

Legacy AppEngine 中Python 2.5的代碼如使用"with"語句, 需要添加 "from __future__ import with_statement".


TODO註釋
--------------------

.. tip::
    為臨時代碼使用TODO註釋, 它是一種短期解決方案. 不算完美, 但夠好了.

TODO註釋應該在所有開頭處包含"TODO"字符串, 緊跟著是用括號括起來的你的名字, email地址或其它標識符. 然後是一個可選的冒號. 接著必須有一行註釋, 解釋要做什麼. 主要目的是為了有一個統一的TODO格式, 這樣添加註釋的人就可以搜索到(並可以按需提供更多細節). 寫了TODO註釋並不保證寫的人會親自解決問題. 當你寫了一個TODO, 請注上你的名字.

.. code-block:: python

    # TODO(kl@gmail.com): Use a "*" here for string repetition.
    # TODO(Zeke) Change this to use relations.

如果你的TODO是"將來做某事"的形式, 那麼請確保你包含了一個指定的日期("2009年11月解決")或者一個特定的事件("等到所有的客戶都可以處理XML請求就移除這些代碼").

導入格式
--------------------

.. tip::
    每個導入應該獨佔一行

.. code-block:: python

    Yes: import os
         import sys

.. code-block:: python

    No:  import os, sys

導入總應該放在文件頂部, 位於模塊註釋和文檔字符串之後, 模塊全局變量和常量之前.  導入應該按照從最通用到最不通用的順序分組:

#. 標準庫導入
#. 第三方庫導入
#. 應用程序指定導入

每種分組中,  應該根據每個模塊的完整包路徑按字典序排序, 忽略大小寫.

.. code-block:: python

    import foo
    from foo import bar
    from foo.bar import baz
    from foo.bar import Quux
    from Foob import ar

語句
--------------------

.. tip::
    通常每個語句應該獨佔一行

不過, 如果測試結果與測試語句在一行放得下, 你也可以將它們放在同一行.  如果是if語句, 只有在沒有else時才能這樣做. 特別地, 絕不要對 ``try/except`` 這樣做, 因為try和except不能放在同一行.

.. code-block:: python

    Yes:

      if foo: bar(foo)

.. code-block:: python

    No:

      if foo: bar(foo)
      else:   baz(foo)

      try:               bar(foo)
      except ValueError: baz(foo)

      try:
          bar(foo)
      except ValueError: baz(foo)


訪問控制
--------------------

.. tip::
    在Python中, 對於瑣碎又不太重要的訪問函數, 你應該直接使用公有變量來取代它們, 這樣可以避免額外的函數調用開銷. 當添加更多功能時, 你可以用屬性(property)來保持語法的一致性.

    (譯者注: 重視封裝的面向對像程序員看到這個可能會很反感, 因為他們一直被教育: 所有成員變量都必須是私有的! 其實, 那真的是有點麻煩啊. 試著去接受Pythonic哲學吧)

另一方面, 如果訪問更複雜, 或者變量的訪問開銷很顯著, 那麼你應該使用像 ``get_foo()`` 和 ``set_foo()`` 這樣的函數調用. 如果之前的代碼行為允許通過屬性(property)訪問 , 那麼就不要將新的訪問函數與屬性綁定. 這樣, 任何試圖通過老方法訪問變量的代碼就沒法運行, 使用者也就會意識到複雜性發生了變化.

命名
--------------------

.. tip::
    module_name, package_name, ClassName, method_name, ExceptionName, function_name, GLOBAL_VAR_NAME, instance_var_name, function_parameter_name, local_var_name.

**應該避免的名稱**

    #. 單字符名稱, 除了計數器和迭代器.
    #. 包/模塊名中的連字符(-)
    #. 雙下劃線開頭並結尾的名稱(Python保留, 例如__init__)

**命名約定**

    #. 所謂"內部(Internal)"表示僅模塊內可用, 或者, 在類內是保護或私有的.
    #. 用單下劃線(_)開頭表示模塊變量或函數是protected的(使用import * from時不會包含).
    #. 用雙下劃線(__)開頭的實例變量或方法表示類內私有.
    #. 將相關的類和頂級函數放在同一個模塊裡. 不像Java, 沒必要限制一個類一個模塊.
    #. 對類名使用大寫字母開頭的單詞(如CapWords, 即Pascal風格), 但是模塊名應該用小寫加下劃線的方式(如lower_with_under.py). 儘管已經有很多現存的模塊使用類似於CapWords.py這樣的命名, 但現在已經不鼓勵這樣做, 因為如果模塊名碰巧和類名一致, 這會讓人困擾.

**Python之父Guido推薦的規範**

===========================    ====================    ======================================================================
Type                           Public                  Internal
===========================    ====================    ======================================================================
Modules                        lower_with_under        _lower_with_under
Packages                       lower_with_under
Classes                        CapWords                _CapWords
Exceptions                     CapWords
Functions                      lower_with_under()      _lower_with_under()
Global/Class Constants         CAPS_WITH_UNDER         _CAPS_WITH_UNDER
Global/Class Variables         lower_with_under        _lower_with_under
Instance Variables             lower_with_under        _lower_with_under (protected) or __lower_with_under (private)
Method Names                   lower_with_under()      _lower_with_under() (protected) or __lower_with_under() (private)
Function/Method Parameters     lower_with_under
Local Variables                lower_with_under
===========================    ====================    ======================================================================

.. _main:

Main
--------------------

.. tip::
    即使是一個打算被用作腳本的文件, 也應該是可導入的. 並且簡單的導入不應該導致這個腳本的主功能(main functionality)被執行, 這是一種副作用. 主功能應該放在一個main()函數中.

在Python中, pydoc以及單元測試要求模塊必須是可導入的. 你的代碼應該在執行主程序前總是檢查 ``if __name__ == '__main__'`` , 這樣當模塊被導入時主程序就不會被執行.

.. code-block:: python

    def main():
          ...

    if __name__ == '__main__':
        main()

所有的頂級代碼在模塊導入時都會被執行. 要小心不要去調用函數, 創建對像, 或者執行那些不應該在使用pydoc時執行的操作.
