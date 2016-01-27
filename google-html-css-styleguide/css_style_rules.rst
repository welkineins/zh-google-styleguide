css樣式規則
==================

CSS有效性
----------

盡可能使用有效的CSS。

使用有效的CSS代碼，除非在處理css驗證器bug或者是專有的語法時。

使用諸如 `W3C CSS validator <http://jigsaw.w3.org/css-validator/>`_ 等工具驗證測試。

使用有效的CSS代碼是一個可衡量CSS代碼質量的指標，可幫你找出不起作用可被刪除的CSS代碼，從而確保CSS的合理使用。

id與class的命名
-----------------

使用有意義的或者通用的id和class名稱

用能反映出元素目的或者通用的id、class名稱，代替那些很表象的、難懂的名稱。

如果名稱需要是易懂的，或不容易被修改，應該首選特定的或者能反映出元素目的的名稱。

通用的名稱適用於非特殊元素或與兄弟元素無區別的元素。他們常被稱為「輔助元素」。

使用功能性或者通用的名稱，可減少不必要的文檔或者模板變化。

.. code-block:: css

  /* 不推薦：無意義 */
  #yee-1901 {}


  /* 不推薦：表象 */
  .button-green {}
  .clear {}

  /* 推薦：具體的 */
  #gallery {}
  #login {}
  .video {}

  /* 推薦：通用的 */
  .aux {}
  .alt {}

id與class的命名規範
-----------------------

ID和class命名要盡可能簡短，但必要的話就別怕長。

盡可能簡潔地傳達id或者class名稱的含義。

使用簡潔的id或者class名稱有助於提高可讀性和代碼效率。

.. code-block:: css

  /* 不推薦 */
  #navigation {}
  .atr {}

  /* 推薦 */
  #nav {}
  .author {}

選擇器的類型
--------------

應當避免在id和class前添加類型選擇器。

除了必要情況下（例如輔助的類），不要將元素與id或class名稱結合做為選擇器。

避免不必要的祖先選擇器也是出於 `性能原因 <http://www.stevesouders.com/blog/2009/06/18/simplifying-css-selectors/>`_ 的考慮。

.. code-block:: css

  /* 不推薦 */
  ul#example {}
  div.error {}

  /* 推薦 */
  #example {}
  .error {}

簡寫屬性
------------

盡可能使用簡寫的屬性書寫方式。

CSS提供了多種屬性 `簡寫 <http://www.w3.org/TR/CSS21/about.html#shorthand>`_ 的方式（如 ``font`` ），即使只顯式設置一個值，也應該盡可能地使用。

使用簡寫屬性有助於提高代碼效率及可讀性。

.. code-block:: css

  /* 不推薦 */
  border-top-style: none;
  font-family: palatino, georgia, serif;
  font-size: 100%;
  line-height: 1.6;
  padding-bottom: 2em;
  padding-left: 1em;
  padding-right: 1em;
  padding-top: 0;

  /* 推薦 */
  border-top: 0;
  font: 100%/1.6 palatino, georgia, serif;
  padding: 0 1em 2em;


0與單位
----------

省略「0」後的單位。

除非必需，否則0後不要加單位。

.. code-block:: css

  margin: 0;
  padding: 0;

前導0
-----------

省略前導「0」值。

在-1至1之間的值無需保留整數位的0。

.. code-block:: css

  font-size: .8em;


十六進製表示法
----------------

在可能的情況下使用3個字符的十六進製表示法。

對於可用3字符十六進製表示的顏色值，按此規則書寫更短、更簡潔。

.. code-block:: css

  /* 不推薦 */
  color: #eebbcc;

  /* 推薦 */
  color: #ebc;


前綴選擇器
------------

加特定應用前綴（可選）

大型項目中以及嵌入在其它項目或外部網站上的代碼需要給id和class添加前綴（命名空間）。使用短的、獨特的標識符，並在其後跟一個破折號。
使用命名空間有助於防止命名衝突，可以讓維護變得簡單，例如在搜索和替換操作時。

.. code-block:: css

  .adw-help {} /* AdWords */
  #maia-note {} /* Maia */


id與class名稱分隔符
---------------------

用連字符分隔ID和類名中的單詞。

選擇器中的詞語和縮寫中不要使用除了連字符以外的任何字符（包括空字符），以提高可理解性和可讀性。

.. code-block:: css

  /* 不推薦: 單詞未分開 */
  .demoimage {}

  /* 不推薦：使用下劃線而不是連字符 */
  .error_status {}

  /* 推薦 */
  #video-id {}
  .ads-sample {}

Hacks
------------

請先嘗試其他的方法，避免用戶代理檢測以及CSS的「hacks」。

進行用戶代理檢測或使用特殊的CSS選擇器及hacks看起來是處理樣式差異的捷徑。但為了實現和保持高效性以及代碼的可維護性，這兩種方案應該放到最後考慮。換句話說，用戶代理檢測和使用hacks會增大項目推進的阻力，所以從項目的長遠利益考慮應盡力避免。一旦允許並無顧忌地使用用戶代理檢測和hacks便很容易濫用，最終一發而不可收。
