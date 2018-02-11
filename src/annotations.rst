

.. _appendixes.annotations:

=======
アノテーション
=======

アノテーションとはメタデータを表す特別な構文のことで、
プログラミング言語のソースコードに追加することができます。
PHP そのものにはソースコードにアノテーションする専用の仕組みはありませんが、
ドキュメンテーションブロックに ``@アノテーション名 引数``
のようなタグを書くことでアノテーションを表すという記法が
PHP コミュニティ内で一般に使われています。
PHP では、リフレクション API の ``getDocComment()``
メソッドを使えば関数、クラス、メソッド、属性
それぞれのドキュメンテーションブロックにアクセスすることができます。
PHPUnit などのアプリケーションでは、
この情報をもとに実行時の振る舞いを設定するのです。

.. admonition:: Note

   PHP の doc コメントは、``/**`` で始めて
   ``*/`` で終わる必要があります。
   その他の形式のコメントで書いたアノテーションは、無視されます。

本章では、PHPUnit がサポートするすべてのアノテーションについて解説します。

.. _appendixes.annotations.author:

@author
#######

``@author`` アノテーションは
``@group`` アノテーション (:ref:`appendixes.annotations.group` を参照ください) のエイリアスで、
テストの作者にもとづいたフィルタリングができるようになります。

.. _appendixes.annotations.after:

@after
######

``@after`` アノテーションを使うと、
テストケースクラス内の各テストメソッドを実行した後に呼ぶメソッドを指定できます。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures()
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.afterClass:

@afterClass
###########

``@afterClass`` アノテーションを使うと、
テストケースクラス内の各テストメソッドを実行した後に呼ぶ静的メソッドを指定できます。
ここで共有フィクスチャの後始末をします。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

グローバル変数の保存や復元を、テストケースクラスのすべてのテストで完全に無効にすることができます。
このように使います。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        // ...
    }

``@backupGlobals`` アノテーションは、テストメソッドレベルで使うこともできます。
これによって、保存と復元の操作をより細やかに制御できるようになります。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals disabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupGlobals enabled
         */
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

``@backupStaticAttributes`` アノテーションを使うと、
宣言されたクラス内のすべての static プロパティの値をバックアップしてからテストを始め、
テストが終わった後でそれらの値を復元することができます。
テストケースクラス単位、あるいはテストメソッド単位で使えます。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    class MyTest extends TestCase
    {
        /**
         * @backupStaticAttributes disabled
         */
        public function testThatInteractsWithStaticAttributes()
        {
            // ...
        }
    }

.. admonition:: Note

   PHP の内部的な制約のため、
   ``@backupStaticAttributes`` が、
   意図していない static 値を保存してしまい、
   その後のテストに影響してしまうことがあります。

   詳細は :ref:`fixtures.global-state` を参照ください。

.. _appendixes.annotations.before:

@before
#######

``@before`` アノテーションを使うと、
テストケースクラス内の各テストメソッドを実行する前に呼ぶメソッドを指定できます。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures()
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.beforeClass:

@beforeClass
############

``@beforeClass`` アノテーションを使うと、
テストケースクラス内の各テストメソッドを実行する前に呼ぶ静的メソッドを指定できます。
ここで共有フィクスチャの準備をします。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures()
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures()
        {
            // ...
        }
    }

.. _appendixes.annotations.codeCoverageIgnore:

@codeCoverageIgnore*
####################

``@codeCoverageIgnore`` や
``@codeCoverageIgnoreStart``、そして
``@codeCoverageIgnoreEnd`` アノテーションを使うと、
コード内の特定の行をカバレッジ解析の対象外にできます。

利用法は :ref:`code-coverage-analysis.ignoring-code-blocks` を参照ください。

.. _appendixes.annotations.covers:

@covers
#######

``@covers`` アノテーションをテストコードで使うと、
そのテストメソッドがどのメソッドをテストするのかを指定することができます。

.. code-block:: php

    /**
     * @covers BankAccount::getBalance
     */
    public function testBalanceIsInitiallyZero()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

これを指定した場合は、指定したメソッドのみのコードカバレッジ情報を考慮することになります。

:numref:`appendixes.annotations.covers.tables.annotations`
に ``@covers`` アノテーションの構文を示します。

.. rst-class:: table
.. list-table:: カバーするメソッドを指定するためのアノテーション
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - アノテーション
      - 説明
    * - ``@covers ClassName::methodName``
      - ``そのテストメソッドが指定したメソッドをカバーすることを表します。``
    * - ``@covers ClassName``
      - ``そのテストメソッドが指定したクラスのすべてのメソッドをカバーすることを表します。``
    * - ``@covers ClassName<extended>``
      - ``そのテストメソッドが、指定したクラスとその親クラスおよびインターフェイスのすべてのメソッドをカバーすることを表します。``
    * - ``@covers ClassName::<public>``
      - ``そのテストメソッドが、指定したクラスのすべての public メソッドをカバーすることを表します。``
    * - ``@covers ClassName::<protected>``
      - ``そのテストメソッドが、指定したクラスのすべての protected メソッドをカバーすることを表します。``
    * - ``@covers ClassName::<private>``
      - ``そのテストメソッドが、指定したクラスのすべての private メソッドをカバーすることを表します。``
    * - ``@covers ClassName::<!public>``
      - ``そのテストメソッドが、指定したクラスのすべての非 public メソッドをカバーすることを表します。``
    * - ``@covers ClassName::<!protected>``
      - ``そのテストメソッドが、指定したクラスのすべての非 protected メソッドをカバーすることを表します。``
    * - ``@covers ClassName::<!private>``
      - ``そのテストメソッドが、指定したクラスのすべての非 private メソッドをカバーすることを表します。``
    * - ``@covers ::functionName``
      - ``そのテストメソッドが、指定したグローバル関数をカバーすることを表します。``

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

``@coversDefaultClass`` アノテーションを使うと、
デフォルトの名前空間あるいはクラス名を指定できます。
こうすることで、
``@covers`` アノテーションのたびに長い名前を繰り返す必要がなくなります。
:numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`
を参照ください。

.. code-block:: php
    :caption: @coversDefaultClass を使ったアノテーションの短縮
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething()
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }
    ?>

.. _appendixes.annotations.coversNothing:

@coversNothing
##############

``@coversNothing`` アノテーションをテストコードで使うと、
そのテストケースについてはコードカバレッジ情報を記録しないように指定できます。

これはインテグレーションテストで使えます。例として
:ref:`code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php`
を参照ください。

このメソッドはクラスレベルおよびメソッドレベルで使え、
あらゆる ``@covers`` タグを上書きします。

.. _appendixes.annotations.dataProvider:

@dataProvider
#############

テストメソッドには任意の引数を渡すことができます。
引数は、データプロバイダメソッド
(:ref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php` の
``provider()``) から渡されます。
使用するデータプロバイダメソッドを指定するには
``@dataProvider`` アノテーションを使います。

詳細は :ref:`writing-tests-for-phpunit.data-providers`
を参照ください。

.. _appendixes.annotations.depends:

@depends
########

PHPUnit は、テストメソッド間の依存性の明示的な宣言をサポートしています。
この依存性とは、テストメソッドが実行される順序を定義するものではありません。
プロデューサーがテストフィクスチャを作ってそのインスタンスを返し、
依存するコンシューマーがそれを受け取って利用するというものです。
:ref:`writing-tests-for-phpunit.examples.StackTest2.php`
は、``@depends`` アノテーションを使ってテストメソッドの依存性をあらわす例です。

詳細は :ref:`writing-tests-for-phpunit.test-dependencies`
を参照ください。

.. _appendixes.annotations.doesNotPerformAssertions:

@doesNotPerformAssertions
#########################

アサーションがひとつもないテストを、リスキーであるとみなさないようにします。

.. _appendixes.annotations.expectedException:

@expectedException
##################

:ref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
は、テストするコード内で例外がスローされたかどうかを
``@expectedException`` アノテーションを使用して調べる方法を示すものです。

詳細は :ref:`writing-tests-for-phpunit.exceptions`
を参照ください。

.. _appendixes.annotations.expectedExceptionCode:

@expectedExceptionCode
######################

``@expectedExceptionCode`` アノテーションを
``@expectedException`` と組み合わせて使うと、
スローされた例外のエラーコードについてのアサーションが可能となり、
例外をより狭い範囲に特定できるようになります。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException     MyException
         * @expectedExceptionCode 20
         */
        public function testExceptionHasErrorcode20()
        {
            throw new MyException('Some Message', 20);
        }
    }

テストを実行しやすくし、重複を減らすために、
ショートカットを使ってクラス定数を指定することができます。
``@expectedExceptionCode`` で
"``@expectedExceptionCode ClassName::CONST``" のようにして使います。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
          * @expectedException     MyException
          * @expectedExceptionCode MyClass::ERRORCODE
          */
        public function testExceptionHasErrorcode20()
        {
          throw new MyException('Some Message', 20);
        }
    }
    class MyClass
    {
        const ERRORCODE = 20;
    }

.. _appendixes.annotations.expectedExceptionMessage:

@expectedExceptionMessage
#########################

``@expectedExceptionMessage`` アノテーションは
``@expectedExceptionCode`` と似ており、
例外のエラーメッセージに関するアサーションを行います。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @expectedException        MyException
         * @expectedExceptionMessage Some Message
         */
        public function testExceptionHasRightMessage()
        {
            throw new MyException('Some Message', 20);
        }
    }

期待するメッセージを、例外メッセージの一部にすることもできます。
これは、特定の名前や渡したパラメータが例外に表示されることを確かめたいけれども
例外メッセージ全体は固定していない場合に便利です。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException        MyException
          * @expectedExceptionMessage broken
          */
         public function testExceptionHasRightMessage()
         {
             $param = "broken";
             throw new MyException('Invalid parameter "'.$param.'".', 20);
         }
    }

テストを実行しやすくし、重複を減らすために、
ショートカットを使ってクラス定数を指定することができます。
``@expectedExceptionMessage`` で
"``@expectedExceptionMessage ClassName::CONST``" のようにして使います。
サンプルコードは :ref:`appendixes.annotations.expectedExceptionCode` を参照ください。

.. _appendixes.annotations.expectedExceptionMessageRegExp:

@expectedExceptionMessageRegExp
###############################

期待するメッセージを、``@expectedExceptionMessageRegExp``
アノテーションを使って正規表現で指定することもできます。
これは、部分文字列だけでは指定したメッセージとのマッチングが不十分なときに便利です。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
         /**
          * @expectedException              MyException
          * @expectedExceptionMessageRegExp /Argument \d+ can not be an? \w+/
          */
         public function testExceptionHasRightMessage()
         {
             throw new MyException('Argument 2 can not be an integer');
         }
    }

.. _appendixes.annotations.group:

@group
######

あるテストを、ひとつあるいは複数のグループに属するものとすることができます。
``@group`` アノテーションをこのように使用します。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething()
        {
        }

        /**
         * @group regresssion
         * @group bug2204
         */
        public function testSomethingElse()
        {
        }
    }

``@group`` アノテーションはテストクラスにも指定できます。
指定すると、そのテストクラスのすべてのメソッドにアノテーションが「継承」されます。

特定のグループに属するテストのみを選んで実行するには、
コマンドラインのテストランナーの場合は
``--group`` オプションあるいは ``--exclude-group``
オプションを指定します。XML 設定ファイルの場合は、
それぞれ対応するディレクティブを指定します。

.. _appendixes.annotations.large:

@large
######

``@large`` アノテーションは、
``@group large`` のエイリアスです。

``PHP_Invoker`` パッケージがインストールされていて strict モードが有効な場合に、
large テストは実行時間が 60 秒を超えたら失敗します。
このタイムアウト時間は、XML 設定ファイルの
``timeoutForLargeTests`` 属性で変更できます。

.. _appendixes.annotations.medium:

@medium
#######

``@medium`` アノテーションは
``@group medium`` のエイリアスです。
medium テストは、``@large`` とマークしたテストに依存してはいけません。

``PHP_Invoker`` パッケージがインストールされていて strict モードが有効な場合に、
medium テストは実行時間が 10 秒を超えたら失敗します。
このタイムアウト時間は、XML 設定ファイルの
``timeoutForMediumTests`` 属性で変更できます。

.. _appendixes.annotations.preserveGlobalState:

@preserveGlobalState
####################

テストを別プロセスで実行するときに、
PHPUnit は親プロセスのグローバルな状態を保存しようと試みます。
親プロセスのすべてのグローバル状態をシリアライズし、
子プロセス内で最後にそれをアンシリアライズするのです。
しかし、親プロセスのグローバル状態の中にもしシリアライズできないものがあれば、
問題が発生します。この問題に対応するために、グローバル状態の保存を無効にすることができます。
そのために使うのが ``@preserveGlobalState`` アノテーションです。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

.. _appendixes.annotations.requires:

@requires
#########

``@requires`` アノテーションを使うと、共通の事前条件
(たとえば PHP のバージョンや拡張モジュールのインストール状況)
を満たさないときにテストをスキップできます。

条件に指定できる内容やその例については
:ref:`incomplete-and-skipped-tests.requires.tables.api`
を参照ください。

.. _appendixes.annotations.runTestsInSeparateProcesses:

@runTestsInSeparateProcesses
############################

テストクラス内のすべてのテストケースを、個別の PHP プロセスで実行するように指示します。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    class MyTest extends TestCase
    {
        // ...
    }

*注意:*
デフォルトで、PHPUnit は親プロセスのグローバルな状態を保存しようと試みます。
親プロセスのすべてのグローバル状態をシリアライズし、
子プロセス内で最後にそれをアンシリアライズするのです。
しかし、親プロセスのグローバル状態の中にもしシリアライズできないものがあれば、
問題が発生します。この問題に対応するために、グローバル状態の保存を無効にすることができます。
この問題の対処法については :ref:`appendixes.annotations.preserveGlobalState`
を参照ください。

.. _appendixes.annotations.runInSeparateProcess:

@runInSeparateProcess
#####################

そのテストを個別の PHP プロセスで実行するように指示します。

.. code-block:: php

    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess()
        {
            // ...
        }
    }

*注意:*
デフォルトで、PHPUnit は親プロセスのグローバルな状態を保存しようと試みます。
親プロセスのすべてのグローバル状態をシリアライズし、
子プロセス内で最後にそれをアンシリアライズするのです。
しかし、親プロセスのグローバル状態の中にもしシリアライズできないものがあれば、
問題が発生します。この問題に対応するために、グローバル状態の保存を無効にすることができます。
この問題の対処法については :ref:`appendixes.annotations.preserveGlobalState`
を参照ください。

.. _appendixes.annotations.small:

@small
######

``@small`` アノテーションは
``@group small`` のエイリアスです。
small テストは、``@medium`` や ``@large`` とマークしたテストに依存してはいけません。

``PHP_Invoker`` パッケージがインストールされていて strict モードが有効な場合に、
small テストは実行時間が 1 秒を超えたら失敗します。
このタイムアウト時間は、XML 設定ファイルの
``timeoutForSmallTests`` 属性で変更できます。

.. admonition:: Note

   テストの実行時間の制限を有効にするには、``@small``、
   ``@medium``、``@large``
   のいずれかのアノテーションで明示的に指定する必要があります。

.. _appendixes.annotations.test:

@test
#####

テストメソッド名の先頭に ``test``
をつけるかわりに、メソッドのドキュメンテーションブロックで ``@test``
アノテーションを使ってそのメソッドがテストメソッドであることを指定することができます。

.. code-block:: php

    /**
     * @test
     */
    public function initialBalanceShouldBe0()
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

アジャイルドキュメントを生成する際に使う別の説明を指定します
(:ref:`other-uses-for-tests.agile-documentation` を参照ください)。
``@testdox`` アノテーションは、クラスにもテストメソッドにも指定できます。

.. code-block:: php

    /**
     * @testdox A bank account
     */
    class BankAccountTest extends TestCase
    {
        /**
         * @testdox has an initial balance of zero
         */
        public function balanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

.. admonition:: Note

   PHPUnit 7.0 より前のバージョンでは、アノテーションのパースにバグがあるため、
   ``@testdox`` アノテーションを指定すると自動的に
   ``@test`` アノテーションも指定したものとみなされます。

.. code-block:: php

.. _appendixes.annotations.testWith:

@testWith
#########

テストメソッド ``@dataProvider`` とともに使うメソッドを実装するかわりに、
``@testWith`` アノテーションを使ってデータセットを定義することができます。

.. code-block:: php

    /**
     * @param string    $input
     * @param int       $expectedLength
     *
     * @testWith        ["test", 4]
     *                  ["longer-string", 13]
     */
    public function testStringLength(string $input, int $expectedLength)
    {
        $this->assertSame($expectedLength, strlen($input));
    }

.. _appendixes.annotations.ticket:

@ticket
#######

.. code-block:: php

.. _appendixes.annotations.uses:

@uses
#####

``@uses`` アノテーションは、
テストから実行されてはいるが、そのテストでカバーするつもりはないコードを指定します。
たとえば、コード片をテストするために必要な値オブジェクトなどに使います。

.. code-block:: php

    /**
     * @covers BankAccount::deposit
     * @uses   Money
     */
    public function testMoneyCanBeDepositedInAccount()
    {
        // ...
    }

このアノテーションは、厳密なカバレッジモードで使うと特に有用です。
このモードの場合、意図せずカバーしてしまったコードがテストを失敗させてしまうことがあるからです。
厳密なカバレッジモードに関する詳細な情報は
:ref:`risky-tests.unintentionally-covered-code`
を参照ください。


