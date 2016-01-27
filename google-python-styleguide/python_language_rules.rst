Python語言規範
================================

Lint
--------------------

.. tip::
    對你的代碼運行pylint

定義:
    pylint是一個在Python源代碼中查找bug的工具. 對於C和C++這樣的不那麼動態的(譯者注: 原文是less dynamic)語言, 這些bug通常由編譯器來捕獲. 由於Python的動態特性, 有些警告可能不對. 不過偽告警應該很少.

優點:
    可以捕獲容易忽視的錯誤, 例如輸入錯誤, 使用未賦值的變量等.

缺點:
    pylint不完美. 要利用其優勢, 我們有時侯需要: a) 圍繞著它來寫代碼 b) 抑制其告警 c) 改進它, 或者d) 忽略它.

結論:
    確保對你的代碼運行pylint.抑制不準確的警告,以便能夠將其他警告暴露出來。

    你可以通過設置一個行註釋來抑制告警. 例如:

    .. code-block:: python

        dict = 'something awful'  # Bad Idea... pylint: disable=redefined-builtin

    pylint警告是以一個數字編號(如 ``C0112`` )和一個符號名(如 ``empty-docstring`` )來標識的. 在編寫新代碼或更新已有代碼時對告警進行醫治, 推薦使用符號名來標識.

    如果警告的符號名不夠見名知意，那麼請對其增加一個詳細解釋。

    採用這種抑制方式的好處是我們可以輕鬆查找抑制並回顧它們.

    你可以使用命令 ``pylint --list-msgs`` 來獲取pylint告警列表. 你可以使用命令 ``pylint --help-msg=C6409`` , 以獲取關於特定消息的更多信息.

    相比較於之前使用的 ``pylint: disable-msg`` , 本文推薦使用 ``pylint: disable`` .

    要抑制"參數未使用"告警, 你可以用"_"作為參數標識符, 或者在參數名前加"unused\_". 遇到不能改變參數名的情況, 你可以通過在函數開頭"提到"它們來消除告警. 例如:

    .. code-block:: python

        def foo(a, unused_b, unused_c, d=None, e=None):
            _ = d, e
            return a


導入
--------------------

.. tip::
    僅對包和模塊使用導入

定義:
    模塊間共享代碼的重用機制.

優點:
    命名空間管理約定十分簡單. 每個標識符的源都用一種一致的方式指示. x.Obj表示Obj對像定義在模塊x中.

缺點:
    模塊名仍可能衝突. 有些模塊名太長, 不太方便.

結論:
    使用 ``import x`` 來導入包和模塊.

    使用 ``from x import y`` , 其中x是包前綴, y是不帶前綴的模塊名.

    使用 ``from x import y as z``, 如果兩個要導入的模塊都叫做z或者y太長了.

    例如, 模塊 ``sound.effects.echo`` 可以用如下方式導入:

    .. code-block:: python

        from sound.effects import echo
        ...
        echo.EchoFilter(input, output, delay=0.7, atten=4)

    導入時不要使用相對名稱. 即使模塊在同一個包中, 也要使用完整包名. 這能幫助你避免無意間導入一個包兩次.

包
--------------------

.. tip::
    使用模塊的全路徑名來導入每個模塊

優點:
    避免模塊名衝突. 查找包更容易.

缺點:
    部署代碼變難, 因為你必須複製包層次.

結論:
    所有的新代碼都應該用完整包名來導入每個模塊.

    應該像下面這樣導入:

    .. code-block:: python

        # Reference in code with complete name.
        import sound.effects.echo

        # Reference in code with just module name (preferred).
        from sound.effects import echo

異常
--------------------

.. tip::
    允許使用異常, 但必須小心

定義:
    異常是一種跳出代碼塊的正常控制流來處理錯誤或者其它異常條件的方式.

優點:
    正常操作代碼的控制流不會和錯誤處理代碼混在一起. 當某種條件發生時, 它也允許控制流跳過多個框架. 例如, 一步跳出N個嵌套的函數, 而不必繼續執行錯誤的代碼.

缺點:
    可能會導致讓人困惑的控制流. 調用庫時容易錯過錯誤情況.

結論:
    異常必須遵守特定條件:

    #. 像這樣觸發異常: ``raise MyException("Error message")`` 或者 ``raise MyException`` . 不要使用兩個參數的形式( ``raise MyException, "Error message"`` )或者過時的字符串異常( ``raise "Error message"`` ).
    #. 模塊或包應該定義自己的特定域的異常基類, 這個基類應該從內建的Exception類繼承. 模塊的異常基類應該叫做"Error".

        .. code-block:: python

            class Error(Exception):
                pass

    #. 永遠不要使用 ``except:`` 語句來捕獲所有異常, 也不要捕獲 ``Exception`` 或者 ``StandardError`` , 除非你打算重新觸發該異常, 或者你已經在當前線程的最外層(記得還是要打印一條錯誤消息). 在異常這方面, Python非常寬容, ``except:`` 真的會捕獲包括Python語法錯誤在內的任何錯誤. 使用 ``except:`` 很容易隱藏真正的bug.
    #. 盡量減少try/except塊中的代碼量. try塊的體積越大, 期望之外的異常就越容易被觸發. 這種情況下, try/except塊將隱藏真正的錯誤.
    #. 使用finally子句來執行那些無論try塊中有沒有異常都應該被執行的代碼. 這對於清理資源常常很有用, 例如關閉文件.
    #. 當捕獲異常時, 使用 ``as`` 而不要用逗號. 例如

        .. code-block:: python

            try:
                raise Error
            except Error as error:
                pass


全局變量
--------------------

.. tip::
    避免全局變量

定義:
    定義在模塊級的變量.

優點:
    偶爾有用.

缺點:
    導入時可能改變模塊行為, 因為導入模塊時會對模塊級變量賦值.

結論:
    避免使用全局變量, 用類變量來代替. 但也有一些例外:

    #. 腳本的默認選項.
    #. 模塊級常量. 例如:　PI = 3.14159. 常量應該全大寫, 用下劃線連接.
    #. 有時候用全局變量來緩存值或者作為函數返回值很有用.
    #. 如果需要, 全局變量應該僅在模塊內部可用, 並通過模塊級的公共函數來訪問.

嵌套/局部/內部類或函數
------------------------

.. tip::
    鼓勵使用嵌套/本地/內部類或函數

定義:
    類可以定義在方法, 函數或者類中. 函數可以定義在方法或函數中. 封閉區間中定義的變量對嵌套函數是只讀的.

優點:
    允許定義僅用於有效範圍的工具類和函數.

缺點:
    嵌套類或局部類的實例不能序列化(pickled).

結論:
    推薦使用.

列表推導(List Comprehensions)
--------------------------------

.. tip::
    可以在簡單情況下使用

定義:
    列表推導(list comprehensions)與生成器表達式(generator expression)提供了一種簡潔高效的方式來創建列表和迭代器, 而不必借助map(), filter(), 或者lambda.

優點:
    簡單的列表推導可以比其它的列表創建方法更加清晰簡單. 生成器表達式可以十分高效, 因為它們避免了創建整個列表.

缺點:
    複雜的列表推導或者生成器表達式可能難以閱讀.

結論:
    適用於簡單情況. 每個部分應該單獨置於一行: 映射表達式, for語句, 過濾器表達式. 禁止多重for語句或過濾器表達式. 複雜情況下還是使用循環.

    .. code-block:: python

        Yes:
          result = []
          for x in range(10):
              for y in range(5):
                  if x * y > 10:
                      result.append((x, y))

          for x in xrange(5):
              for y in xrange(5):
                  if x != y:
                      for z in xrange(5):
                          if y != z:
                              yield (x, y, z)

          return ((x, complicated_transform(x))
                  for x in long_generator_function(parameter)
                  if x is not None)

          squares = [x * x for x in range(10)]

          eat(jelly_bean for jelly_bean in jelly_beans
              if jelly_bean.color == 'black')

    .. code-block:: python

        No:
          result = [(x, y) for x in range(10) for y in range(5) if x * y > 10]

          return ((x, y, z)
                  for x in xrange(5)
                  for y in xrange(5)
                  if x != y
                  for z in xrange(5)
                  if y != z)

默認迭代器和操作符
--------------------

.. tip::
    如果類型支持, 就使用默認迭代器和操作符. 比如列表, 字典及文件等.

定義:
    容器類型, 像字典和列表, 定義了默認的迭代器和關係測試操作符(in和not in)

優點:
    默認操作符和迭代器簡單高效, 它們直接表達了操作, 沒有額外的方法調用. 使用默認操作符的函數是通用的. 它可以用於支持該操作的任何類型.

缺點:
    你沒法通過閱讀方法名來區分對象的類型(例如, has_key()意味著字典). 不過這也是優點.

結論:
    如果類型支持, 就使用默認迭代器和操作符, 例如列表, 字典和文件. 內建類型也定義了迭代器方法. 優先考慮這些方法, 而不是那些返回列表的方法. 當然，這樣遍歷容器時，你將不能修改容器.

    .. code-block:: python

        Yes:  for key in adict: ...
              if key not in adict: ...
              if obj in alist: ...
              for line in afile: ...
              for k, v in dict.iteritems(): ...

    .. code-block:: python

        No:   for key in adict.keys(): ...
              if not adict.has_key(key): ...
              for line in afile.readlines(): ...

生成器
--------------------

.. tip::
    按需使用生成器.

定義:
    所謂生成器函數, 就是每當它執行一次生成(yield)語句, 它就返回一個迭代器, 這個迭代器生成一個值. 生成值後, 生成器函數的運行狀態將被掛起, 直到下一次生成.

優點:
    簡化代碼, 因為每次調用時, 局部變量和控制流的狀態都會被保存. 比起一次創建一系列值的函數, 生成器使用的內存更少.

缺點:
    沒有.

結論:
    鼓勵使用. 注意在生成器函數的文檔字符串中使用"Yields:"而不是"Returns:".

    (譯者注: 參看 :ref:`註釋<comments>` )


Lambda函數
--------------------

.. tip::
    適用於單行函數

定義:
    與語句相反, lambda在一個表達式中定義匿名函數. 常用於為 ``map()`` 和 ``filter()`` 之類的高階函數定義回調函數或者操作符.

優點:
    方便.

缺點:
    比本地函數更難閱讀和調試. 沒有函數名意味著堆棧跟蹤更難理解. 由於lambda函數通常只包含一個表達式, 因此其表達能力有限.

結論:
    適用於單行函數. 如果代碼超過60-80個字符, 最好還是定義成常規(嵌套)函數.

    對於常見的操作符，例如乘法操作符，使用 ``operator`` 模塊中的函數以代替lambda函數. 例如, 推薦使用 ``operator.mul`` , 而不是 ``lambda x, y: x * y`` .

條件表達式
--------------------

.. tip::
    適用於單行函數

定義:
    條件表達式是對於if語句的一種更為簡短的句法規則. 例如: ``x = 1 if cond else 2`` .

優點:
    比if語句更加簡短和方便.

缺點:
    比if語句難於閱讀. 如果表達式很長， 難於定位條件.

結論:
    適用於單行函數. 在其他情況下，推薦使用完整的if語句.

默認參數值
--------------------

.. tip::
    適用於大部分情況.

定義:
    你可以在函數參數列表的最後指定變量的值, 例如, ``def foo(a, b = 0):`` . 如果調用foo時只帶一個參數, 則b被設為0. 如果帶兩個參數, 則b的值等於第二個參數.

優點:
    你經常會碰到一些使用大量默認值的函數, 但偶爾(比較少見)你想要覆蓋這些默認值. 默認參數值提供了一種簡單的方法來完成這件事, 你不需要為這些罕見的例外定義大量函數. 同時, Python也不支持重載方法和函數, 默認參數是一種"仿造"重載行為的簡單方式.

缺點:
    默認參數只在模塊加載時求值一次. 如果參數是列表或字典之類的可變類型, 這可能會導致問題. 如果函數修改了對象(例如向列表追加項), 默認值就被修改了.

結論:
    鼓勵使用, 不過有如下注意事項:

    不要在函數或方法定義中使用可變對像作為默認值.

    .. code-block:: python

        Yes: def foo(a, b=None):
                 if b is None:
                     b = []

    .. code-block:: python

        No:  def foo(a, b=[]):
                 ...
        No:  def foo(a, b=time.time()):  # The time the module was loaded???
                 ...
        No:  def foo(a, b=FLAGS.my_thing):  # sys.argv has not yet been parsed...
                 ...


屬性(properties)
--------------------

.. tip::
    訪問和設置數據成員時, 你通常會使用簡單, 輕量級的訪問和設置函數. 建議用屬性（properties）來代替它們.

定義:
    一種用於包裝方法調用的方式. 當運算量不大, 它是獲取和設置屬性(attribute)的標準方式.

優點:
    通過消除簡單的屬性(attribute)訪問時顯式的get和set方法調用, 可讀性提高了. 允許懶惰的計算. 用Pythonic的方式來維護類的接口. 就性能而言, 當直接訪問變量是合理的, 添加訪問方法就顯得瑣碎而無意義. 使用屬性(properties)可以繞過這個問題. 將來也可以在不破壞接口的情況下將訪問方法加上.

缺點:
    屬性(properties)是在get和set方法聲明後指定, 這需要使用者在接下來的代碼中注意: set和get是用於屬性(properties)的(除了用 ``@property`` 裝飾器創建的只讀屬性).  必須繼承自object類. 可能隱藏比如操作符重載之類的副作用. 繼承時可能會讓人困惑.

結論:
    你通常習慣於使用訪問或設置方法來訪問或設置數據, 它們簡單而輕量. 不過我們建議你在新的代碼中使用屬性. 只讀屬性應該用 ``@property`` `裝飾器 <http://google-styleguide.googlecode.com/svn/trunk/pyguide.html#Function_and_Method_Decorators>`_ 來創建.

    如果子類沒有覆蓋屬性, 那麼屬性的繼承可能看上去不明顯. 因此使用者必須確保訪問方法間接被調用, 以保證子類中的重載方法被屬性調用(使用模板方法設計模式).

    .. code-block:: python

        Yes: import math

             class Square(object):
                 """A square with two properties: a writable area and a read-only perimeter.

                 To use:
                 >>> sq = Square(3)
                 >>> sq.area
                 9
                 >>> sq.perimeter
                 12
                 >>> sq.area = 16
                 >>> sq.side
                 4
                 >>> sq.perimeter
                 16
                 """

                 def __init__(self, side):
                     self.side = side

                 def __get_area(self):
                     """Calculates the 'area' property."""
                     return self.side ** 2

                 def ___get_area(self):
                     """Indirect accessor for 'area' property."""
                     return self.__get_area()

                 def __set_area(self, area):
                     """Sets the 'area' property."""
                     self.side = math.sqrt(area)

                 def ___set_area(self, area):
                     """Indirect setter for 'area' property."""
                     self._SetArea(area)

                 area = property(___get_area, ___set_area,
                                 doc="""Gets or sets the area of the square.""")

                 @property
                 def perimeter(self):
                     return self.side * 4


    (譯者注: 老實說, 我覺得這段示例代碼很不恰當, 有必要這麼蛋疼嗎?)

True/False的求值
--------------------

.. tip::
    盡可能使用隱式false

定義:
    Python在布爾上下文中會將某些值求值為false. 按簡單的直覺來講, 就是所有的"空"值都被認為是false. 因此0， None, [], {}, "" 都被認為是false.

優點:
    使用Python布爾值的條件語句更易讀也更不易犯錯. 大部分情況下, 也更快.

缺點:
    對C/C++開發人員來說, 可能看起來有點怪.

結論:
    盡可能使用隱式的false, 例如: 使用 ``if foo:`` 而不是 ``if foo != []:`` . 不過還是有一些注意事項需要你銘記在心:

    #. 永遠不要用==或者!=來比較單件, 比如None. 使用is或者is not.
    #. 注意: 當你寫下 ``if x:`` 時, 你其實表示的是 ``if x is not None`` . 例如: 當你要測試一個默認值是None的變量或參數是否被設為其它值. 這個值在布爾語義下可能是false!
    #. 永遠不要用==將一個布爾量與false相比較. 使用 ``if not x:`` 代替. 如果你需要區分false和None, 你應該用像 ``if not x and x is not None:`` 這樣的語句.
    #. 對於序列(字符串, 列表, 元組), 要注意空序列是false. 因此 ``if not seq:`` 或者 ``if seq:`` 比 ``if len(seq):`` 或 ``if not len(seq):`` 要更好.
    #. 處理整數時, 使用隱式false可能會得不償失(即不小心將None當做0來處理). 你可以將一個已知是整型(且不是len()的返回結果)的值與0比較.

        .. code-block:: python

            Yes: if not users:
                     print 'no users'

                 if foo == 0:
                     self.handle_zero()

                 if i % 10 == 0:
                     self.handle_multiple_of_ten()

        .. code-block:: python

            No:  if len(users) == 0:
                     print 'no users'

                 if foo is not None and not foo:
                     self.handle_zero()

                 if not i % 10:
                     self.handle_multiple_of_ten()

    #. 注意'0'(字符串)會被當做true.

過時的語言特性
--------------------

.. tip::
    盡可能使用字符串方法取代字符串模塊. 使用函數調用語法取代apply(). 使用列表推導, for循環取代filter(), map()以及reduce().

定義:
    當前版本的Python提供了大家通常更喜歡的替代品.

結論:
    我們不使用不支持這些特性的Python版本, 所以沒理由不用新的方式.

    .. code-block:: python

        Yes: words = foo.split(':')

             [x[1] for x in my_list if x[2] == 5]

             map(math.sqrt, data)    # Ok. No inlined lambda expression.

             fn(*args, **kwargs)

    .. code-block:: python

        No:  words = string.split(foo, ':')

             map(lambda x: x[1], filter(lambda x: x[2] == 5, my_list))

             apply(fn, args, kwargs)

詞法作用域(Lexical Scoping)
-----------------------------

.. tip::
    推薦使用

定義:
    嵌套的Python函數可以引用外層函數中定義的變量, 但是不能夠對它們賦值. 變量綁定的解析是使用詞法作用域, 也就是基於靜態的程序文本. 對一個塊中的某個名稱的任何賦值都會導致Python將對該名稱的全部引用當做局部變量, 甚至是賦值前的處理. 如果碰到global聲明, 該名稱就會被視作全局變量.

    一個使用這個特性的例子:

    .. code-block:: python

        def get_adder(summand1):
            """Returns a function that adds numbers to a given number."""
            def adder(summand2):
                return summand1 + summand2

            return adder

    (譯者注: 這個例子有點詭異, 你應該這樣使用這個函數: ``sum = get_adder(summand1)(summand2)`` )

優點:
    通常可以帶來更加清晰, 優雅的代碼. 尤其會讓有經驗的Lisp和Scheme(還有Haskell, ML等)程序員感到欣慰.

缺點:
    可能導致讓人迷惑的bug. 例如下面這個依據 `PEP-0227 <http://www.python.org/dev/peps/pep-0227/>`_ 的例子:

    .. code-block:: python

        i = 4
        def foo(x):
            def bar():
                print i,
            # ...
            # A bunch of code here
            # ...
            for i in x:  # Ah, i *is* local to Foo, so this is what Bar sees
                print i,
            bar()

    因此 ``foo([1, 2, 3])`` 會打印 ``1 2 3 3`` , 不是 ``1 2 3 4`` .

    (譯者注: x是一個列表, for循環其實是將x中的值依次賦給i.這樣對i的賦值就隱式的發生了, 整個foo函數體中的i都會被當做局部變量, 包括bar()中的那個. 這一點與C++之類的靜態語言還是有很大差別的.)

結論:
    鼓勵使用.

函數與方法裝飾器
--------------------

.. tip::
    如果好處很顯然, 就明智而謹慎的使用裝飾器

定義:
    `用於函數及方法的裝飾器 <http://www.python.org/doc/2.4.3/whatsnew/node6.html>`_ (也就是@標記). 最常見的裝飾器是@classmethod 和@staticmethod, 用於將常規函數轉換成類方法或靜態方法. 不過, 裝飾器語法也允許用戶自定義裝飾器. 特別地, 對於某個函數 ``my_decorator`` , 下面的兩段代碼是等效的:

    .. code-block:: python

         class C(object):
            @my_decorator
            def method(self):
                # method body ...

    .. code-block:: python

        class C(object):
            def method(self):
                # method body ...
            method = my_decorator(method)


優點:
    優雅的在函數上指定一些轉換. 該轉換可能減少一些重複代碼, 保持已有函數不變(enforce invariants), 等.

缺點:
    裝飾器可以在函數的參數或返回值上執行任何操作, 這可能導致讓人驚異的隱藏行為. 而且, 裝飾器在導入時執行. 從裝飾器代碼的失敗中恢復更加不可能.

結論:
    如果好處很顯然, 就明智而謹慎的使用裝飾器. 裝飾器應該遵守和函數一樣的導入和命名規則. 裝飾器的python文檔應該清晰的說明該函數是一個裝飾器. 請為裝飾器編寫單元測試.

    避免裝飾器自身對外界的依賴(即不要依賴於文件, socket, 數據庫連接等), 因為裝飾器運行時這些資源可能不可用(由 ``pydoc`` 或其它工具導入). 應該保證一個用有效參數調用的裝飾器在所有情況下都是成功的.

    裝飾器是一種特殊形式的"頂級代碼". 參考後面關於 :ref:`Main <main>` 的話題.

線程
--------------------

.. tip::
    不要依賴內建類型的原子性.

雖然Python的內建類型例如字典看上去擁有原子操作, 但是在某些情形下它們仍然不是原子的(即: 如果__hash__或__eq__被實現為Python方法)且它們的原子性是靠不住的. 你也不能指望原子變量賦值(因為這個反過來依賴字典).

優先使用Queue模塊的 ``Queue`` 數據類型作為線程間的數據通信方式. 另外, 使用threading模塊及其鎖原語(locking primitives). 瞭解條件變量的合適使用方式, 這樣你就可以使用 ``threading.Condition`` 來取代低級別的鎖了.

威力過大的特性
--------------------

.. tip::
    避免使用這些特性

定義:
    Python是一種異常靈活的語言, 它為你提供了很多花哨的特性, 諸如元類(metaclasses), 字節碼訪問, 任意編譯(on-the-fly compilation), 動態繼承, 對像父類重定義(object reparenting), 導入黑客(import hacks), 反射, 系統內修改(modification of system internals), 等等.

優點:
    強大的語言特性, 能讓你的代碼更緊湊.

缺點:
    使用這些很"酷"的特性十分誘人, 但不是絕對必要. 使用奇技淫巧的代碼將更加難以閱讀和調試. 開始可能還好(對原作者而言), 但當你回顧代碼, 它們可能會比那些稍長一點但是很直接的代碼更加難以理解.

結論:
    在你的代碼中避免這些特性.
