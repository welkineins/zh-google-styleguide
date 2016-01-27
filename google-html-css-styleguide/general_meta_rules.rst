整體的元數據規則
===================

編碼
---------

使用UTF-8無BOM編碼。

讓你的編輯器使用無字節順序標記的UTF-8編碼。

在HTML模板和文檔中使用 ``<meta charset=」utf-8」>`` 指定編碼。不需要為樣式表指定編碼，它默認是UTF-8。

（想瞭解更多關於應該何時並如何指定編碼，請查看 `Handling character encodings in HTML and CSS <http://www.w3.org/International/tutorials/tutorial-char-enc/>`_）

註釋
--------

在需要時盡可能去解釋你的代碼。

用註釋去解釋你的代碼，包括它的應用範圍、用途、此方案的選擇理由等。

（這一條是可選的，沒必要為每個文件寫上詳細的註釋，會增重HTML/CSS的代碼，主要取決於項目的複雜度。）

處理內容
----------

用TODO標記待辦事宜和處理內容。

只用TODO來標記待辦事宜，不要使用其他格式，例如@@。

在括號裡添加聯繫方式（姓名或郵箱），格式為TODO（聯繫方式）。

在冒號後面添加處理內容，格式為TODO：處理內容。

.. code-block:: html

  {# TODO(john.doe): 重新處理水平居中 #}
  <center>Test</center>

  <!-- TODO: 移除可選的標籤 -->
  <ul>
   <li>Apples</li>
   <li>Oranges</li>
  </ul>
