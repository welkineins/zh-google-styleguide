7. 註解
------------

註解雖然寫起來很痛苦, 但對保證程式碼可讀性至關重要. 下面的規則描述了如何註釋以及在哪兒註釋. 當然也要記住: 註釋固然很重要, 但最好的程式碼本身應該是自文檔化. 有意義的類型名和變數名, 要遠勝過要用註釋解釋的含糊不清的名字.

你寫的註解是給程式碼讀者看的: 下一個需要理解你的程式碼的人. 慷慨些吧, 下一個人可能就是你!

7.1. 註解風格
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    使用 ``//`` 或 ``/* */``, 統一就好.

``//`` 或 ``/* */`` 都可以; 但 ``//`` *更* 常用. 要在如何註解及註釋風格上確保統一.

7.2. 文件註解
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    在每一個文件開頭加入版權公告, 然後是文件內容描述.

法律公告和作者信息:

    每個文件都應該包含以下項, 依次是:

        - 版權宣告 (比如, ``Copyright 2008 Google Inc.``)

        - 許可證. 為專案選擇合適的許可證版本 (比如, Apache 2.0, BSD, LGPL, GPL)

        - 作者: 標識文件的原始作者.

    如果你對原始作者的文件做了重大修改, 將你的信息添加到作者信息裡. 這樣當其他人對該文件有疑問時可以知道該聯繫誰.

文件內容:

    緊接著版權許可和作者信息之後, 每個文件都要用註解描述文件內容.

    通常, ``.h`` 文件要對所宣告的類的功能和用法作簡單說明. ``.cc`` 文件通常包含了更多的實作細節或算法技巧討論,  如果你感覺這些實作細節或算法技巧討論對於理解 ``.h`` 文件有幫助, 可以將該註解挪到 ``.h``, 並在 ``.cc`` 中指出文檔在 ``.h``.

    不要簡單的在 ``.h`` 和 ``.cc`` 間複製註解. 這種偏離了註釋的實際意義.

.. _class-comments:

7.3. 類註解
~~~~~~~~~~~~~~~~~~

.. tip::

    每個類的定義都要附帶一份註解, 描述類的功能和用法.

.. code-block:: c++

    // Iterates over the contents of a GargantuanTable.  Sample usage:
    //    GargantuanTable_Iterator* iter = table->NewIterator();
    //    for (iter->Seek("foo"); !iter->done(); iter->Next()) {
    //      process(iter->key(), iter->value());
    //    }
    //    delete iter;
    class GargantuanTable_Iterator {
        ...
    };

如果你覺得已經在文件頂部詳細描述了該類, 想直接簡單的來上一句 "完整描述見文件頂部" 也不打緊, 但務必確保有這類註解.

如果類有任何同步前提, 文檔說明之. 如果該類的實例可被多線程訪問, 要特別注意文檔說明多線程環境下相關的規則和常數使用.

7.4. 函式註解
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    函式宣告處註解描述函式功能; 定義處描述函式實作.

函式宣告:

    註解位於宣告之前, 對函式功能及用法進行描述. 註釋使用敘述式 ("Opens the file") 而非指令式 ("Open the file"); 註釋只是為了描述函式, 而不是命令函式做什麼. 通常, 註釋不會描述函式如何工作. 那是函式定義部分的事情.

    函式宣告處註解的內容:

        - 函式的輸入輸出.
        - 對類成員函式而言: 函式呼叫期間對象是否需要保持引用參數, 是否會釋放這些參數.
        - 如果函式分配了空間, 需要由呼叫者釋放.
        - 參數是否可以為 ``NULL``.
        - 是否存在函式使用上的性能隱患.
        - 如果函式是可重入的, 其同步前提是什麼?

    舉例如下:

        .. code-block:: c++

            // Returns an iterator for this table.  It is the client's
            // responsibility to delete the iterator when it is done with it,
            // and it must not use the iterator once the GargantuanTable object
            // on which the iterator was created has been deleted.
            //
            // The iterator is initially positioned at the beginning of the table.
            //
            // This method is equivalent to:
            //    Iterator* iter = table->NewIterator();
            //    iter->Seek("");
            //    return iter;
            // If you are going to immediately seek to another place in the
            // returned iterator, it will be faster to use NewIterator()
            // and avoid the extra seek.
            Iterator* GetIterator() const;

    但也要避免囉囉嗦嗦, 或做些顯而易見的說明. 下面的註解就沒有必要加上 "returns false otherwise", 因為已經暗含其中了:

        .. code-block:: c++

            // Returns true if the table cannot hold any more entries.
            bool IsTableFull();

    註解建構/解構子時, 切記讀程式碼的人知道構造/解構子是幹啥的, 所以 "destroys this object" 這樣的註釋是沒有意義的. 註明建構子對參數做了什麼 (例如, 是否取得指標所有權) 以及解構子清理了什麼. 如果都是些無關緊要的內容, 直接省掉註釋. 解構子前沒有註釋是很正常的.

函式定義:

    每個函式定義時要用註解說明函式功能和實作要點. 比如說說你用的程式撰寫技巧, 實作的大致步驟, 或解釋如此實作的理由, 為什麼前半部分要加鎖而後半部分不需要.

    *不要* 從 ``.h`` 文件或其他地方的函式宣告處直接複製註解. 簡要重述函式功能是可以的, 但註釋重點要放在如何實作上.

7.5. 變數註解
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    通常變數名本身足以很好說明變量用途. 某些情況下, 也需要額外的註解說明.

類數據成員:

    每個類數據成員 (也叫實例變數或成員變量) 都應該用註解說明用途. 如果變量可以接受 ``NULL`` 或 ``-1`` 等警戒值, 須加以說明. 比如:

        .. code-block:: c++

            private:
                // Keeps track of the total number of entries in the table.
                // Used to ensure we do not go over the limit. -1 means
                // that we don't yet know how many entries the table has.
                int num_total_entries_;


全域變數:

    和數據成員一樣, 所有全域變數也要註解說明含義及用途. 比如:

        .. code-block:: c++

            // The total number of tests cases that we run through in this regression test.
            const int kNumTestCases = 6;

7.6. 實作註解
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    對於程式碼中巧妙的, 晦澀的, 有趣的, 重要的地方加以註解.

程式碼前註解:

    巧妙或複雜的程式碼段前要加註解. 比如:

        .. code-block:: c++

            // Divide result by two, taking into account that x
            // contains the carry from the add.
            for (int i = 0; i < result->size(); i++) {
                x = (x << 8) + (*result)[i];
                (*result)[i] = x >> 1;
                x &= 1;
            }

行註解:

    比較隱晦的地方要在行尾加入註解. 在行尾空兩格進行註釋. 比如:

        .. code-block:: c++

            // If we have enough memory, mmap the data portion too.
            mmap_budget = max<int64>(0, mmap_budget - index_->length());
            if (mmap_budget >= data_size_ && !MmapData(mmap_chunk_bytes, mlock))
                return;  // Error already logged.

    注意, 這裡用了兩段註解分別描述這段程式碼的作用, 和提示函式返回時錯誤已經被記入日誌.

    如果你需要連續進行多行註解, 可以使之對齊獲得更好的可讀性:

        .. code-block:: c++

            DoSomething();                  // Comment here so the comments line up.
            DoSomethingElseThatIsLonger();  // Comment here so there are two spaces between
                                            // the code and the comment.
            { // One space before comment when opening a new scope is allowed,
              // thus the comment lines up with the following comments and code.
              DoSomethingElse();  // Two spaces before line comments normally.
            }

NULL, true/false, 1, 2, 3...:

    向函式傳入 ``NULL``, 布爾值或整數時, 要註解說明含義, 或使用常數讓程式碼望文知意. 例如, 對比:

        .. warning::
            .. code-block:: c++

                bool success = CalculateSomething(interesting_value,
                                                  10,
                                                  false,
                                                  NULL);  // What are these arguments??


    和:

        .. code-block:: c++

            bool success = CalculateSomething(interesting_value,
                                              10,     // Default base value.
                                              false,  // Not the first time we're calling this.
                                              NULL);  // No callback.

    或使用常數或描述性變數:

        .. code-block:: c++

            const int kDefaultBaseValue = 10;
            const bool kFirstTimeCalling = false;
            Callback *null_callback = NULL;
            bool success = CalculateSomething(interesting_value,
                                              kDefaultBaseValue,
                                              kFirstTimeCalling,
                                              null_callback);

不允許:

    注意 *永遠不要* 用自然語言翻譯程式碼作為註解. 要假設讀程式碼的人 C++ 水平比你高, 即便他/她可能不知道你的用意:

    .. warning::

        .. code-block:: c++

            // 現在, 檢查 b 陣列並確保 i 是否存在,
            // 下一個元素是 i+1.
            ...        // 天哪. 令人崩潰的註解.

7.7. 標點, 拼寫和語法
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    注意標點, 拼寫和語法; 寫的好的註解比差的要易讀的多.

註解的通常寫法是包含正確大小寫和結尾句號的完整語句. 短一點的註釋 (如程式碼行尾註釋) 可以隨意點, 依然要注意風格的一致性. 完整的語句可讀性更好, 也可以說明該註釋是完整的, 而不是一些不成熟的想法.

雖然被別人指出該用分號時卻用了逗號多少有些尷尬, 但清晰易讀的程式碼還是很重要的. 正確的標點, 拼寫和語法對此會有所幫助.

7.8. TODO 註解
~~~~~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    對那些臨時的, 短期的解決方案, 或已經夠好但仍不完美的程式碼使用 ``TODO`` 註解.

``TODO`` 註解要使用全大寫的字符串 ``TODO``, 在隨後的圓括號裡寫上你的大名, 郵件地址, 或其它身份標識. 冒號是可選的. 主要目的是讓添加註釋的人 (也是可以請求提供更多細節的人) 可根據規範的 ``TODO`` 格式進行查找. 添加 ``TODO`` 註釋並不意味著你要自己來修正.

    .. code-block:: c++

        // TODO(kl@gmail.com): Use a "*" here for concatenation operator.
        // TODO(Zeke) change this to use relations.

如果加 ``TODO`` 是為了在 "將來某一天做某事", 可以附上一個非常明確的時間 "Fix by November 2005"), 或者一個明確的事項 ("Remove this code when all clients can handle XML responses.").

7.9. 棄用註解
~~~~~~~~~~~~~~~~~~~~~~

.. tip::

    通過棄用註解（``DEPRECATED`` comments）以標記某介面點（interface points）已棄用。

你可以寫上包含全大寫的 ``DEPRECATED`` 的註解，以標記某介面為棄用狀態。註釋可以放在接口宣告前，或者同一行。

在 ``DEPRECATED`` 一詞後，留下你的名字，郵箱地址以及括號補充。

僅僅標記介面為 ``DEPRECATED`` 並不會讓大家不約而同地棄用，你還得親自主動修正呼叫點（callsites），或是找個幫手。

修正好的程式碼應該不會再涉及棄用介面點了，著實改用新接口點。如果你不知從何下手，可以找標記棄用註解的當事人一起商量。

譯者 (YuleFox) 筆記
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

#. 關於註解風格，很多 C++ 的 coders 更喜歡行註釋, C coders 或許對塊註釋依然情有獨鍾, 或者在文件頭大段大段的註釋時使用塊註釋;
#. 文件註解可以炫耀你的成就, 也是為了捅了簍子別人可以找你;
#. 註解要言簡意賅, 不要拖沓冗余, 複雜的東西簡單化和簡單的東西複雜化都是要被鄙視的;
#. 對於 Chinese coders 來說, 用英文註解還是用中文註釋, it is a problem, 但不管怎樣, 註釋是為了讓別人看懂, 難道是為了炫耀程式撰寫語言之外的你的母語或外語水平嗎；
#. 註解不要太亂, 適當的縮排才會讓人樂意看. 但也沒有必要規定註釋從第幾列開始 (我自己寫程式碼的時候總喜歡這樣), UNIX/LINUX 下還可以約定是使用 tab 還是 space, 個人傾向於 space;
#. TODO 很不錯, 有時候, 註解確實是為了標記一些未完成的或完成的不盡如人意的地方, 這樣一搜索, 就知道還有哪些活要干, 日誌都省了.
