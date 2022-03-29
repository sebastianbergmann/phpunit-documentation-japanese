

.. _appendixes.annotations:

==============
アノテーション
==============

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @after
         */
        public function tearDownSomeFixtures(): void
        {
            // ...
        }

        /**
         * @after
         */
        public function tearDownSomeOtherFixtures(): void
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @afterClass
         */
        public static function tearDownSomeSharedFixtures(): void
        {
            // ...
        }

        /**
         * @afterClass
         */
        public static function tearDownSomeOtherSharedFixtures(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.backupGlobals:

@backupGlobals
##############

PHPUnit では、グローバル変数やスーパーグローバル変数の値をバックアップしてからテストを始め、
テストが終わった後でそれらの値を復元することができます。

``@backupGlobals enabled`` アノテーションはクラスレベルで使うことができます。
この場合、テストケースクラスのすべてのテストでグローバル変数の保存と復元が行われます。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals enabled
     */
    final class MyTest extends TestCase
    {
        // ...
    }

``@backupGlobals`` アノテーションをテストメソッドレベルで使うこともできます。
これによって、保存と復元の操作をより細やかに制御できるようになります。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @backupGlobals enabled
     */
    final class MyTest extends TestCase
    {
        public function testThatInteractsWithGlobalVariables()
        {
            // ...
        }

        /**
         * @backupGlobals disabled
         */
        public function testThatDoesNotInteractWithGlobalVariables(): void
        {
            // ...
        }
    }

.. _appendixes.annotations.backupStaticAttributes:

@backupStaticAttributes
#######################

PHPUnit では、宣言されたクラス内のすべての static 属性の値をバックアップしてからテストを始め、
テストが終わった後でそれらの値を復元することができます。

``@backupStaticAttributes enabled`` アノテーションはクラスレベルで使うことができます。
この場合、テストケースクラスのすべてのテストで static 属性の保存と復元が行われます。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;
    /**
     * @backupStaticAttributes enabled
     */
    final class MyTest extends TestCase
    {
        // ...
    }

``@backupStaticAttributes`` アノテーションをテストメソッドレベルで使うこともできます。
これによって、保存と復元の操作をより細やかに制御できるようになります。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @backupStaticAttributes enabled
     */
    final class MyTest extends TestCase
    {
        public function testThatInteractsWithStaticAttributes(): void
        {
            // ...
        }

        /**
         * @backupStaticAttributes disabled
         */
        public function testThatDoesNotInteractWithStaticAttributes(): void
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @before
         */
        public function setupSomeFixtures(): void
        {
            // ...
        }

        /**
         * @before
         */
        public function setupSomeOtherFixtures(): void
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @beforeClass
         */
        public static function setUpSomeSharedFixtures(): void
        {
            // ...
        }

        /**
         * @beforeClass
         */
        public static function setUpSomeOtherSharedFixtures(): void
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
     * @covers \BankAccount
     */
    public function testBalanceIsInitiallyZero(): void
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

``@covers`` アノテーションが利用された場合、出力されるコードカバレッジの算出方法が変わります。
アノテーションで自身を指定するテストから実行された場合のみ、「covered」と判定されます。
アノテーションで自身を指定していないテストから間接的に利用されたとしても、「covered」とは判定されません。
これによって、コードカバレッジが実際よりも高く出る false positive を避けることができます。

このアノテーションはテストクラスの docblock に記載することも、個別のテストメソッドの
docblock に記載することもできますが、
テストクラスの docblock に記載することが推奨され、個別のテストメソッドの docblock に記載することは推奨されません。

:ref:`XML 設定ファイル <appendixes.configuration>` の ``forceCoversAnnotation`` オプションを ``true`` に設定した場合、
全てのテストメソッドにおいて ``covers`` アノテーションを設定しなければなりません
(テストクラス単位で設定しても、それぞれのテストメソッドを個別に設定しても構いません)

:numref:`appendixes.annotations.covers.tables.annotations`
に ``@covers`` アノテーションの構文を示します。

:ref:`code-coverage-analysis.specifying-covered-parts` のセクションでは、このアノテーションを使うより詳細な例を紹介しています。

このアノテーションは、完全修飾クラス名を必要とすることに注意してください。
完全修飾クラス名の先頭のバックスラッシュは省略しても動作に問題ありませんが、読み手に完全修飾クラス名であることを伝えるために
先頭にバックスラッシュをつける形式で記載することが推奨されます。

.. rst-class:: table
.. list-table:: カバーするメソッドを指定するためのアノテーション
    :name: appendixes.annotations.covers.tables.annotations
    :header-rows: 1

    * - アノテーション
      - 説明
    * - ``@covers ClassName::methodName`` (not recommended)
      - そのテストメソッドが指定したメソッドをカバーすることを表します。
    * - ``@covers ClassName`` (recommended)
      - そのテストメソッドが指定したクラスのすべてのメソッドをカバーすることを表します。
    * - ``@covers ClassName<extended>`` (not recommended)
      - そのテストメソッドが、指定したクラスとその親クラスおよびインターフェイスのすべてのメソッドをカバーすることを表します。
    * - ``@covers ClassName::<public>`` (not recommended)
      - そのテストメソッドが、指定したクラスのすべての public メソッドをカバーすることを表します。
    * - ``@covers ClassName::<protected>`` (not recommended)
      - そのテストメソッドが、指定したクラスのすべての protected メソッドをカバーすることを表します。
    * - ``@covers ClassName::<private>`` (not recommended)
      - そのテストメソッドが、指定したクラスのすべての private メソッドをカバーすることを表します。
    * - ``@covers ClassName::<!public>`` (not recommended)
      - そのテストメソッドが、指定したクラスのすべての非 public メソッドをカバーすることを表します。
    * - ``@covers ClassName::<!protected>`` (not recommended)
      - そのテストメソッドが、指定したクラスのすべての非 protected メソッドをカバーすることを表します。
    * - ``@covers ClassName::<!private>`` (not recommended)
      - そのテストメソッドが、指定したクラスのすべての非 private メソッドをカバーすることを表します。
    * - ``@covers ::functionName`` (recommended)
      - そのテストメソッドが、指定したグローバル関数をカバーすることを表します。

.. _appendixes.annotations.coversDefaultClass:

@coversDefaultClass
###################

``@coversDefaultClass`` アノテーションを使うと、
デフォルトの名前空間あるいはクラス名を指定できます。
こうすることで、
``@covers`` アノテーションのたびに長い名前を繰り返す必要がなくなります。
:numref:`appendixes.annotations.examples.CoversDefaultClassTest.php`
を参照ください。

このアノテーションは、完全修飾クラス名を必要とすることに注意してください。
完全修飾クラス名の先頭のバックスラッシュは省略しても動作に問題ありませんが、読み手に完全修飾クラス名であることを伝えるために
先頭にバックスラッシュをつける形式で記載することが推奨されます。

.. code-block:: php
    :caption: @coversDefaultClass を使ったアノテーションの短縮
    :name: appendixes.annotations.examples.CoversDefaultClassTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @coversDefaultClass \Foo\CoveredClass
     */
    final class CoversDefaultClassTest extends TestCase
    {
        /**
         * @covers ::publicMethod
         */
        public function testSomething(): void
        {
            $o = new Foo\CoveredClass;
            $o->publicMethod();
        }
    }

.. _appendixes.annotations.coversNothing:

@coversNothing
##############

``@coversNothing`` アノテーションをテストコードで使うと、
そのテストケースについてはコードカバレッジ情報を記録しないように指定できます。

これはインテグレーションテストで使えます。例として
:ref:`code-coverage-analysis.specifying-covered-parts.examples.GuestbookIntegrationTest.php`
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

.. _appendixes.annotations.group:

@group
######

あるテストを、ひとつあるいは複数のグループに属するものとすることができます。
``@group`` アノテーションをこのように使用します。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @group specification
         */
        public function testSomething(): void
        {
        }

        /**
         * @group regression
         * @group bug2204
         */
        public function testSomethingElse(): void
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         * @preserveGlobalState disabled
         */
        public function testInSeparateProcess(): void
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @runTestsInSeparateProcesses
     */
    final class MyTest extends TestCase
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MyTest extends TestCase
    {
        /**
         * @runInSeparateProcess
         */
        public function testInSeparateProcess(): void
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
    public function initialBalanceShouldBe0(): void
    {
        $this->assertSame(0, $this->ba->getBalance());
    }

.. _appendixes.annotations.testdox:

@testdox
########

アジャイルドキュメントを生成する際に使う別の説明を指定します。

``@testdox`` アノテーションは、クラスにもテストメソッドにも指定できます。

.. code-block:: php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @testdox A bank account
     */
    final class BankAccountTest extends TestCase
    {
        /**
         * @testdox has an initial balance of zero
         */
        public function balanceIsInitiallyZero(): void
        {
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

.. admonition:: Note

   PHPUnit 7.0 より前のバージョンでは、アノテーションのパースにバグがあるため、
   ``@testdox`` アノテーションを指定すると自動的に
   ``@test`` アノテーションも指定したものとみなされます。

メソッドレベルで ``@testdox`` アノテーションを ``@dataProvider`` アノテーションと一緒に利用する場合、別の説明の中でメソッドの引数をプレースホルダとして使えます。

.. code-block:: php
    /**
     * @dataProvider additionProvider
     * @testdox Adding $a to $b results in $expected
     */
    public function testAdd($a, $b, $expected)
    {
        $this->assertSame($expected, $a + $b);
    }
    public function additionProvider()
    {
        return [
            [0, 0, 0],
            [0, 1, 1],
            [1, 0, 1],
            [1, 1, 3]
        ];
    }

.. _appendixes.annotations.testWith:

@testWith
#########

テストメソッド ``@dataProvider`` とともに使うメソッドを実装するかわりに、
``@testWith`` アノテーションを使ってデータセットを定義することができます。

データセットには複数の要素を含めることができます。
複数の要素からなるデータセットを定義するには、要素ごとに別の行で定義します。
データセットの要素は、JSONの配列形式でなければいけません。

データセットをテストに渡す方法について、詳しくは :ref:`writing-tests-for-phpunit.data-providers`
を参照ください。

.. code-block:: php

    /**
     * @testWith ["test", 4]
     *           ["longer-string", 13]
     */
    public function testStringLength(string $input, int $expectedLength): void
    {
        $this->assertSame($expectedLength, strlen($input));
    }

JSONのオブジェクト形式で書いた場合は、連想配列として扱われます。

.. code-block:: php

    /**
     * @testWith [{"day": "monday", "conditions": "sunny"}, ["day", "conditions"]]
     */
    public function testArrayKeys(array $array, array $keys): void
    {
        $this->assertSame($keys, array_keys($array));
    }

.. _appendixes.annotations.ticket:

@ticket
#######

``@ticket`` アノテーションは
``@group`` アノテーション (:ref:`appendixes.annotations.group` を参照ください) のエイリアスで、
チケットIDにもとづいたテストのフィルタリングができるようになります。

.. _appendixes.annotations.uses:

@uses
#####

``@uses`` アノテーションは、
テストから実行されてはいるが、そのテストでカバーするつもりはないコードを指定します。
たとえば、コード片をテストするために必要な値オブジェクトなどに使います。

.. code-block:: php

    /**
     * @covers \BankAccount
     * @uses   \Money
     */
    public function testMoneyCanBeDepositedInAccount(): void
    {
        // ...
    }

:numref:`code-coverage-analysis.specifying-covered-parts.examples.InvoiceTest.php`
にて、他の使用例を確認することができます。

このアノテーションはコードを読む人の助けになることに加え、
意図せずコードをカバーしていた場合にテストを失敗させる厳密なカバレッジモードで使うと特に有用です。
厳密なカバレッジモードに関する詳細な情報は
ref:`risky-tests.unintentionally-covered-code`
を参照してください。

このアノテーションは、完全修飾クラス名を必要とすることに注意してください。
完全修飾クラス名の先頭のバックスラッシュは省略しても動作に問題ありませんが、読み手に完全修飾クラス名であることを伝えるために
先頭にバックスラッシュをつける形式で記載することが推奨されます。
