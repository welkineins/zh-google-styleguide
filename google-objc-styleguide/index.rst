
Google Objective-C Style Guide 中文版
----------------------------------------

:版本:   2.36

:原作者:
    .. line-block::

         Mike Pinkerton
         Greg Miller
         Dave MacLachlan

:翻譯:
    .. line-block::

        `ewangke <http://ke.indiebros.com/>`_
        `Yang.Y <https://github.com/yangyubo>`_

:項目主頁:
    - `Google Style Guide <http://google-styleguide.googlecode.com>`_
    - `Google 開源項目風格指南 - 中文版 <http://github.com/zh-google-styleguide/zh-google-styleguide>`_


譯者的話
========

ewanke
^^^^^^^^^^^^

一直想翻譯這個 `style guide <http://google-styleguide.googlecode.com/svn/trunk/objcguide.xml>`_ ，終於在週末花了7個小時的時間用vim敲出了HTML。很多術語的翻譯很難，平時看的中文技術類書籍有限，對很多術語的中文譯法不是很清楚，難免有不恰當之處，請讀者指出並幫我改進：王軻 」ewangke at gmail.com」 2011.03.27

Yang.Y
^^^^^^^^^^^^

對 Objective-C 的瞭解有限，憑著感覺和 C/C++ 方面的理解：

* 把指南更新到 2.36 版本
* 調整了一些術語和句子


背景介紹
========

Objective-C 是 C 語言的擴展，增加了動態類型和面對對象的特性。它被設計成具有易讀易用的，支持複雜的面向對像設計的編程語言。它是 Mac OS X 以及 iPhone 的主要開發語言。

Cocoa 是 Mac OS X 上主要的應用程序框架之一。它由一組 Objective-C 類組成，為快速開發出功能齊全的 Mac OS X 應用程序提供支持。

蘋果公司已經有一份非常全面的 Objective-C 編碼指南。Google 為 C++ 也寫了一份類似的編碼指南。而這份 Objective-C 指南則是蘋果和 Google 常規建議的最佳結合。因此，在閱讀本指南之前，請確定你已經閱讀過：

* `Apple』s Cocoa Coding Guidelines <http://developer.apple.com/documentation/Cocoa/Conceptual/CodingGuidelines/index.html>`_

* `Google』s Open Source C++ Style Guide <http://codinn.com/projects/google-cpp-styleguide/>`_

.. note::

    所有在 Google 的 C++ 風格指南中所禁止的事情，如未明確說明，也同樣不能在Objective-C++ 中使用。

本文檔的目的在於為所有的 Mac OS X 的代碼提供編碼指南及實踐。許多準則是在實際的項目和小組中經過長期的演化、驗證的。Google 開發的開源項目遵從本指南的要求。

Google 已經發佈了遵守本指南開源代碼，它們屬於 `Google Toolbox for Mac project <http://code.google.com/p/google-toolbox-for-mac/>`_ 項目（本文以縮寫 GTM 指代）。GTM 代碼庫中的代碼通常為了可以在不同項目中復用。

注意，本指南不是 Objective-C 教程。我們假定讀者對 Objective-C 非常熟悉。如果你剛剛接觸 Objective-C 或者需要溫習，請閱讀 `The Objective-C Programming Language <http://developer.apple.com/documentation/Cocoa/Conceptual/ObjectiveC/index.html>`_ 。

例子
========

都說一個例子頂上一千句話，我們就從一個例子開始，來感受一下編碼的風格、留白以及命名等等。

一個頭文件的例子，展示了在 ``@interface`` 聲明中如何進行正確的註釋以及留白。

.. code-block:: objc

    //  Foo.h
    //  AwesomeProject
    //
    //  Created by Greg Miller on 6/13/08.
    //  Copyright 2008 Google, Inc. All rights reserved.
    //

    #import <Foundation/Foundation.h>

    // A sample class demonstrating good Objective-C style. All interfaces,
    // categories, and protocols (read: all top-level declarations in a header)
    // MUST be commented. Comments must also be adjacent to the object they're
    // documenting.
    //
    // (no blank line between this comment and the interface)
    @interface Foo : NSObject {
     @private
      NSString *bar_;
      NSString *bam_;
    }

    // Returns an autoreleased instance of Foo. See -initWithBar: for details
    // about |bar|.
    + (id)fooWithBar:(NSString *)bar;

    // Designated initializer. |bar| is a thing that represents a thing that
    // does a thing.
    - (id)initWithBar:(NSString *)bar;

    // Gets and sets |bar_|.
    - (NSString *)bar;
    - (void)setBar:(NSString *)bar;

    // Does some work with |blah| and returns YES if the work was completed
    // successfully, and NO otherwise.
    - (BOOL)doWorkWithBlah:(NSString *)blah;

    @end

一個源文件的例子，展示了 ``@implementation`` 部分如何進行正確的註釋、留白。同時也包括了基於引用實現的一些重要方法，如 ``getters`` 、 ``setters`` 、 ``init`` 以及 ``dealloc`` 。

.. code-block:: objc

    //
    //  Foo.m
    //  AwesomeProject
    //
    //  Created by Greg Miller on 6/13/08.
    //  Copyright 2008 Google, Inc. All rights reserved.
    //

    #import "Foo.h"


    @implementation Foo

    + (id)fooWithBar:(NSString *)bar {
      return [[[self alloc] initWithBar:bar] autorelease];
    }

    // Must always override super's designated initializer.
    - (id)init {
      return [self initWithBar:nil];
    }

    - (id)initWithBar:(NSString *)bar {
      if ((self = [super init])) {
        bar_ = [bar copy];
        bam_ = [[NSString alloc] initWithFormat:@"hi %d", 3];
      }
      return self;
    }

    - (void)dealloc {
      [bar_ release];
      [bam_ release];
      [super dealloc];
    }

    - (NSString *)bar {
      return bar_;
    }

    - (void)setBar:(NSString *)bar {
      [bar_ autorelease];
      bar_ = [bar copy];
    }

    - (BOOL)doWorkWithBlah:(NSString *)blah {
      // ...
      return NO;
    }

    @end


不要求在 ``@interface``、``@implementation`` 和 ``@end`` 前後空行。如果你在 ``@interface`` 聲明了實例變量，則須在關括號 ``}`` 之後空一行。

除非接口和實現非常短，比如少量的私有方法或橋接類，空行方有助於可讀性。
