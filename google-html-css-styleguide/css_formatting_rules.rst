CSS格式化規則
===============

聲明順序
---------

按字母順序排列聲明。

css文件書寫按字母順序排列的方式，容易記憶和維護，以達到一致的代碼。

在排序時忽略瀏覽器特定的前綴。但是，特定CSS屬性的多個瀏覽器前綴應按字母順序排列（如-moz書寫在-webkit前面）。

.. code-block:: css

  background: fuchsia;
  border: 1px solid;
  -moz-border-radius: 4px;
  -webkit-border-radius: 4px;
  border-radius: 4px;
  color: black;
  text-align: center;
  text-indent: 2em;

塊內容的縮進
--------------

縮進塊內容。

將包括嵌套及聲明的 `塊內容 <http://www.w3.org/TR/CSS21/syndata.html#block>`_ 進行縮進，以體現層次並提高可讀性。

.. code-block:: css

  @media screen, projection {

    html {
      background: #fff;
      color: #444;
    }

  }

聲明結束
----------

每個屬性後使用分號結束。

以分號結束每個屬性，提高一致性和可擴展性。

.. code-block:: css

  /* 不推薦 */
  .test {
    display: block;
    height: 100px
  }

  /* 推薦 */
  .test {
    display: block;
    height: 100px;
  }

CSS屬性名結束
---------------

屬性名稱的冒號後有一個空格。

為保證一致性，在屬性名與屬性值之間添加一個空格（但是屬性名和冒號間沒有空格）。

.. code-block:: css

  /* 不推薦 */
  h3 {
    font-weight:bold;
  }

  /* 推薦 */
  h3 {
    font-weight: bold;
  }

聲明塊間隔
--------------

在選擇器和後面的聲明塊之間使用一個空格。

最後一個選擇器與表示 `聲名塊 <http://www.w3.org/TR/CSS21/syndata.html#rule-sets>`_ 開始的左大花括號在同行，中間有一個字符空格。

表示開始的左大花括號和選擇器在同行。

.. code-block:: css

  /* 不推薦：缺少空間 */
  #video{
    margin-top: 1em;
  }


  /* 不推薦：不必要的換行符 */
  #video
  {
    margin-top: 1em;
  }

  /* 推薦 */
  #video {
    margin-top: 1em;
  }


選擇器及聲明分離
-------------------

每個選擇器和聲明獨立成行。

總是讓每個選擇器和聲明單獨成行。

.. code-block:: css

  /* 不推薦 */
  a:focus, a:active {
    position: relative; top: 1px;
  }

  /* 推薦 */
  h1,
  h2,
  h3 {
    font-weight: normal;
    line-height: 1.2;
  }


CSS代碼塊分離
-----------------

使用新空行分離規則。

始終把一個空行（兩個換行符）放在代碼塊規則之間。

.. code-block:: css

  html {
    background: #fff;
  }


  body {
    margin: auto;
    width: 50%;
  }

CSS引號
----------

屬性選擇器和屬性值中使用單引號。

在屬性選擇器及屬性值中使用單引號（''）而不是雙引號（""）。在 ``url（）`` 中不要使用引號。

特例：如果你確實需要定義 ``@charset`` ，由於 `不允許使用單引號 <http://www.w3.org/TR/CSS21/syndata.html#charset>`_ ，故請使用雙引號。

.. code-block:: css

  /* 不推薦 */
  @import url("//www.google.com/css/maia.css");

  html {
    font-family: "open sans", arial, sans-serif;
  }

  /* 推薦 */
  @import url(//www.google.com/css/maia.css);

  html {
    font-family: 'open sans', arial, sans-serif;
  }
