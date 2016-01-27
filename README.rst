Google 開源項目風格指南 (中文版)
================================

* 在線文檔托管在 ReadTheDocs : `在線閱讀最新版本 <http://zh-google-styleguide.readthedocs.org/>`_

* 中文風格指南 GitHub 托管地址：`zh-google-styleguide <https://github.com/zh-google-styleguide/zh-google-styleguide>`_

.. note:: 聲明.

    本項目並非 Google 官方項目, 而是由國內程序員憑熱情創建和維護.

    如果你關注的是 Google 官方英文版, 請移步 `Google Style Guide <https://github.com/google/styleguide>`_

每個較大的開源項目都有自己的風格指南: 關於如何為該項目編寫代碼的一系列約定 (有時候會比較武斷).
當所有代碼均保持一致的風格, 在理解大型代碼庫時更為輕鬆.

"風格" 的含義涵蓋範圍廣, 從 "變量使用駝峰格式 (camelCase)" 到 "決不使用全局變量" 再到 "決不使用異常".
英文版項目維護的是在 Google 使用的編程風格指南. 如果你正在修改的項目源自 Google, 你可能會被引導至
英文版項目頁面, 以瞭解項目所使用的風格.

我們已經發佈了四份 **中文版** 的風格指南:

#. `Google C++ 風格指南 <http://zh-google-styleguide.readthedocs.org/en/latest/google-cpp-styleguide/>`_

#. `Google Objective-C 風格指南 <http://zh-google-styleguide.readthedocs.org/en/latest/google-objc-styleguide/>`_

#. `Google Python 風格指南 <http://zh-google-styleguide.readthedocs.org/en/latest/google-python-styleguide/>`_

#. `Google JSON 風格指南 <https://github.com/darcyliu/google-styleguide/blob/master/JSONStyleGuide.md>`_


中文版項目採用 reStructuredText 純文本標記語法, 並使用 Sphinx 生成 HTML / CHM / PDF 等文檔格式.

* 英文版項目還包含 `cpplint <https://github.com/google/styleguide/tree/gh-pages/cpplint>`_ - 一個用來幫助適應風格準則的工具, 以及 `google-c-style.el <https://raw.githubusercontent.com/google/styleguide/gh-pages/google-c-style.el>`_, Google 風格的 Emacs 配置文件.

* 另外, 招募志願者翻譯 `JavaScript Style Guide <http://google.github.io/styleguide/javascriptguide.xml>`_ 以及 `XML Document Format Style Guide <http://google.github.io/styleguide/xmlstyle.html>`_, 有意者請聯繫 `Yang.Y <https://github.com/yangyubo>`_.
