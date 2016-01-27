HTML格式規則
==============

常規格式化
------------

對每個塊、列表、表格元素都另起一行，每個子元素都縮進。

每個塊元素、列表元素或表格元素另起一行，而不必考慮元素的樣式（因CSS可以改變元素的 ``display`` 屬性）。

同樣的，如果他們是塊、列表或者表格元素的子元素，則將之縮進。

（如果你遇到列表項之間有空白的問題，可以把所有 ``li`` 元素放到一行。Linter鼓勵拋出警告而不是錯誤。）

.. code-block:: html

  <blockquote>
    <p><em>Space</em>, the final frontier.</p>
  </blockquote>

  <ul>
    <li>Moe
    <li>Larry
    <li>Curly
  </ul>

  <table>
    <thead>
      <tr>
        <th scope="col">Income
        <th scope="col">Taxes
    <tbody>
      <tr>
        <td>$ 5.00
        <td>$ 4.50
  </table>

HTML引號
-----------

當引用屬性值時，使用雙引號。

使用雙引號而不是單引號來包裹屬性值。

.. code-block:: html

  <!-- 不推薦 -->
  <a class='maia-button maia-button-secondary'>Sign in</a>

  <!-- 推薦 -->
  <a class="maia-button maia-button-secondary">Sign in</a>
