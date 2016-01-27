HTML樣式規則
==============

文檔類型
---------

使用HTML5。

HTML5（HTML語法）是所有HTML文檔的首選： ``<!DOCTYPE html>`` 。

（推薦使用HTML，即text/html。不要使用XHTML。XHTML，即 `application/xhtml+xml <http://hixie.ch/advocacy/xhtml>`_，缺乏瀏覽器和基礎結構的支持，並且優化的空間比HTML小。）
雖然HTML閉合標籤沒有問題，但是不要自閉合空標籤。即寫 ``<br>`` 而不是 ``<br />`` 。


HTML合法性
------------

在可能的情況下使用合法的HTML。

使用合法的HTML代碼，除非由於文件大小導致的不可達到的性能目標而不能使用。

利用已用工具對合法性進行測試，例如 `W3C HTML validator <http://validator.w3.org/nu/>`_。

使用合法的HTML是一個可度量的基準質量屬性，該屬性有助於瞭解技術需求和約束，從而確保合理的HTML使用。

.. code-block:: html

  <!-- 不推薦 -->
  <title>Test</title>
  <article>This is only a test.

  <!-- 推薦 -->
  <!DOCTYPE html>
  <meta charset="utf-8">
  <title>Test</title>
  <article>This is only a test.</article>

語義化
--------

根據HTML的目的使用它。

根據元素（有時被錯誤的叫做「標籤」）被創造的用途使用他們。比如，對標題使用標題元素，對段落使用 ``p`` 元素，對錨點使用 ``a`` 元素等。

語義化的使用HTML對於可訪問性、復用性和代碼的高效性等因素非常重要。

.. code-block:: html

  <!-- 不推薦 -->
  <div onclick="goToRecommendations();">All recommendations</div>

  <!-- 推薦 -->
  <a href="recommendations/">All recommendations</a>

多媒體降級
------------

為多媒體提供替代內容。

對於圖片、視頻、通過 ``canvas`` 實現的動畫等多媒體來說，確保提供可訪問的替代內容。對於圖片，可提供有意義的替代文本（ ``alt`` ）；對於視頻和音頻，如有條件可提供對白和字幕。

提供替代內容對輔助功能很重要：沒有 ``alt`` ，一位盲人用戶很難知道一張圖片的內容，其他用戶可能不能瞭解視頻和音頻的內容。
（對於 ``alt`` 屬性會引起冗余的圖片和你不打算添加CSS的純粹裝飾性的圖片，不用添加替代文本，寫成 ``alt=""`` 即可。）

.. code-block:: html

  <!-- 不推薦 -->
  <img src="spreadsheet.png">

  <!-- 推薦 -->
  <img src="spreadsheet.png" alt="Spreadsheet screenshot.">

關注點分離
-----------

將結構、表現、行為分離。

嚴格保持結構（標識），表現（樣式），行為（腳本）分離，盡量使三者之間的相互影響最小。

就是說，確保文檔和模板只包含HTML，並且HTML只用來表現結構。把任何表現性的東西都移到樣式表，任何行為性的東西都移到腳本中。

此外，盡可能少的從文檔和模板中引用樣式表和腳本來減少三者的相互影響。

結構、表現、行為分離對維護非常重要。更改HTML文檔和模板總是比更新樣式表和腳本成本更高。

.. code-block:: html

  <!-- 不推薦 -->
  <!DOCTYPE html>
  <title>HTML sucks</title>
  <link rel="stylesheet" href="base.css" media="screen">
  <link rel="stylesheet" href="grid.css" media="screen">
  <link rel="stylesheet" href="print.css" media="print">
  <h1 style="font-size: 1em;">HTML sucks</h1>
  <p>I』ve read about this on a few sites but now I』m sure:
    <u>HTML is stupid!!1</u>
  <center>I can』t believe there』s no way to control the styling of
    my website without doing everything all over again!</center>

  <!-- 推薦 -->
  <!DOCTYPE html>
  <title>My first CSS-only redesign</title>
  <link rel="stylesheet" href="default.css">
  <h1>My first CSS-only redesign</h1>
  <p>I』ve read about this on a few sites but today I』m actually
    doing it: separating concerns and avoiding anything in the HTML of
    my website that is presentational.
  <p>It』s awesome!

實體引用
-----------

不要使用實體引用。

假設文件、編輯器和團隊之間使用相同的編碼（UTF-8），則沒有必要使用例如 ``&mdash;`` 、 ``&rdquo;`` 或 ``☺`` 這樣的實體引用。

唯一的例外適用於HTML中具有特殊意義的字符（比如<和&），和控制或者隱藏的字符（比如不換行空格）。

.. code-block:: html

  <!-- 不推薦 -->
  The currency symbol for the Euro is &ldquo;&eur;&rdquo;.

  <!-- 推薦 -->
  The currency symbol for the Euro is "".

可選的標籤
------------

省略可選的標籤（可選）。

為了優化文件大小和可掃瞄，考慮省略可選標籤。 `HTML5規範 <http://www.whatwg.org/specs/web-apps/current-work/multipage/syntax.html#syntax-tag-omission>`_ 定義了哪些標籤可以被省略。

（這種方法可能要求一段寬限期去建立一個更加廣泛的準則，因為它和Web開發人員通常所瞭解的有著顯著不同。考慮到一致性和簡單性，最好省略所有可選標籤。）

.. code-block:: html

  <!-- 不推薦 -->
  <!DOCTYPE html>
  <html>
   <head>
     <title>Spending money, spending bytes</title>
   </head>
   <body>
     <p>Sic.</p>
   </body>
  </html>

  <!-- 推薦 -->
  <!DOCTYPE html>
  <title>Saving money, saving bytes</title>
  <p>Qed.

type屬性
---------

為樣式表和腳本省略 ``type`` 屬性。

引用樣式表（除非不是使用CSS）和腳本（除非不是使用JavaScript）不要使用type屬性。

HTML5將 `text/css <http://www.whatwg.org/specs/web-apps/current-work/multipage/semantics.html#attr-style-type>`_ 和 `text/javascript <http://www.whatwg.org/specs/web-apps/current-work/multipage/scripting-1.html#attr-script-type>`_ 設置為默認值，在這種情況下指定type屬性並不必要。甚至同樣兼容老版本的瀏覽器。

.. code-block:: html
  <!-- 不推薦 -->
  <link rel="stylesheet" href="//www.google.com/css/maia.css" type="text/css">

  <!-- 推薦 -->
  <link rel="stylesheet" href="//www.google.com/css/maia.css">

  <!-- 不推薦 -->
  <script src="//www.google.com/js/gweb/analytics/autotrack.js" type="text/javascript"></script>

  <!-- 推薦 -->
  <script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>
