總體排版規則
==============

縮進
---------

每次縮進使用兩個空格。

不使用TAB鍵或者混合使用TAB鍵和空格進行縮進。

.. code-block:: html

  <ul>
   <li>Fantastic
   <li>Great
  </ul>

.. code-block:: css

  .example {
   color: blue;
  }

大小寫
----------

只使用小寫字母。

所有的代碼都使用小寫字母：適用於HTML元素、屬性、屬性值（除了text/CDATA）、CSS選擇器、屬性名以及屬性值（字符串除外）。

.. code-block:: html

  <!-- 不推薦 -->
  <A HREF="/">Home</A>

  <!-- 推薦 -->
  <img src="google.png" alt="Google">

.. code-block:: css

  /* 不推薦 */
  color: #E5E5E5;

  /* 推薦 */
  color: #e5e5e5;

尾部的空格
------------

刪除尾部的空格。

尾部的空格是多餘的，不刪除則形成無意義的文件差異。

.. code-block:: html

  <!-- 不推薦 -->
  <p>What?_

  <!-- 推薦 -->
  <p>Yes please.
