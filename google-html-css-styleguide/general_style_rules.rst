整體樣式規則
================

協議
---------------

嵌入式資源省略協議頭。

省略圖片、媒體文件、樣式表以及腳本的URL協議頭部分（http:、https:），不使用這兩個協議的文件則不省略。
省略協議頭，即讓URL變成相對地址，可以避免協議混合及小文件重複下載。

.. code-block:: html

  <!-- 不推薦 -->
  <script src="http://www.google.com/js/gweb/analytics/autotrack.js"></script>

  <!-- 推薦 -->
  <script src="//www.google.com/js/gweb/analytics/autotrack.js"></script>

.. code-block:: css

  /* 不推薦 */
  .example {
   background: url(http://www.google.com/images/example);
  }

  /* 推薦 */
  .example {
    background: url(//www.google.com/images/example);
  }

