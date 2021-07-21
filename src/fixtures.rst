

.. _fixtures:

============
フィクスチャ
============

テストを記述する際にいちばん時間を食うのは、テストを開始するための事前設定と
テスト終了後の後始末の処理を書くことです。この事前設定は、テストの
*フィクスチャ* と呼ばれます。

:ref:`writing-tests-for-phpunit.examples.StackTest.php`
では、フィクスチャは
``$stack`` という変数に格納された配列だけでした。
しかし、たいていの場合はフィクスチャはこれより複雑なものとなり、
それを準備するにはかなりの量のコードが必要です。本来のテストの内容が、
フィクスチャを設定するためのコードの中に埋もれてしまうことになります。
この問題は、複数のテストで同じようなフィクスチャを設定する場合により顕著になります。
テストフレームワークの助けがなければ、
個々のテストのなかで同じような準備コードを繰り返し書くはめになってしまいます。

PHPUnit は、準備用のコードの共有をサポートしています。
各テストメソッドが実行される前に、``setUp()``
という名前のテンプレートメソッドが実行されます。``setUp()``
は、テスト対象のオブジェクトを生成するような処理に使用します。
テストメソッドの実行が終了すると、それが成功したか否かにかかわらず、
``tearDown()`` という名前の別のテンプレートメソッドが実行されます。
``tearDown()`` では、テスト対象のオブジェクトの後始末などを行います。

:ref:`writing-tests-for-phpunit.examples.StackTest2.php`
producer-consumer の関係を使って複数のテストでフィクスチャを共有しました。
これは常に要求されるというものではありませんし、常に可能だとも限りません。
:numref:`fixtures.examples.StackTest.php` では、
フィクスチャを再利用するのではなくコードで作成する方式で
``StackTest`` にテストを書く方法をごらんいただきましょう。
まずインスタンス変数 ``$stack`` を宣言し、
メソッドローカル変数のかわりにこれを使うことにします。
そして、``array`` の作成を
``setUp()`` メソッドで行います。
最後に、冗長なコードをテストメソッドから削除し、
アサーションメソッド ``assertSame()``
ではメソッド変数 ``$stack`` のかわりに
新たに導入したインスタンス変数 ``$this->stack``
を使うようにします。

.. code-block:: php
    :caption: setUp() を使用して stack フィクスチャを作成する
    :name: fixtures.examples.StackTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StackTest extends TestCase
    {
        private $stack;

        protected function setUp(): void
        {
            $this->stack = [];
        }

        public function testEmpty(): void
        {
            $this->assertTrue(empty($this->stack));
        }

        public function testPush(): void
        {
            array_push($this->stack, 'foo');

            $this->assertSame('foo', $this->stack[count($this->stack)-1]);
            $this->assertFalse(empty($this->stack));
        }

        public function testPop(): void
        {
            array_push($this->stack, 'foo');

            $this->assertSame('foo', array_pop($this->stack));
            $this->assertTrue(empty($this->stack));
        }
    }

テンプレートメソッド ``setUp()`` および ``tearDown()``
は、テストケースクラスのテストメソッドごとに (そして最初にインスタンスを作成したときに)
一度ずつ実行されます。

さらに、テンプレートメソッド ``setUpBeforeClass()`` および
``tearDownAfterClass()`` が存在します。
これらはそれぞれ、テストケースクラスの最初のテストメソッドの実行前と
テストケースクラスの最後のテストの実行後にコールされます。

以下の例は、テストケースクラスで使用できるすべてのテンプレートメソッドを示すものです。

.. code-block:: php
    :caption: 利用可能なすべてのテンプレートメソッド
    :name: fixtures.examples.TemplateMethodsTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class TemplateMethodsTest extends TestCase
    {
        public static function setUpBeforeClass(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function setUp(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function assertPreConditions(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public function testOne(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(true);
        }

        public function testTwo(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            $this->assertTrue(false);
        }

        protected function assertPostConditions(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function tearDown(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        public static function tearDownAfterClass(): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
        }

        protected function onNotSuccessfulTest(Throwable $t): void
        {
            fwrite(STDOUT, __METHOD__ . "\n");
            throw $t;
        }
    }

.. parsed-literal::

    $ phpunit TemplateMethodsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    TemplateMethodsTest::setUpBeforeClass
    TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testOne
    TemplateMethodsTest::assertPostConditions
    TemplateMethodsTest::tearDown
    .TemplateMethodsTest::setUp
    TemplateMethodsTest::assertPreConditions
    TemplateMethodsTest::testTwo
    TemplateMethodsTest::tearDown
    TemplateMethodsTest::onNotSuccessfulTest
    FTemplateMethodsTest::tearDownAfterClass

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) TemplateMethodsTest::testTwo
    Failed asserting that <boolean:false> is true.
    /home/sb/TemplateMethodsTest.php:30

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _fixtures.more-setup-than-teardown:

tearDown() よりも setUp()
#########################

``setUp()`` と ``tearDown()``
は理屈上では対称的になるはずですが、実際にはそうではありません。実際には、
``tearDown()`` を実装する必要があるのは ``setUp()``
で外部リソース (ファイルやソケットなど) を割り当てた場合のみです。もし
``setUp()`` で単に PHP オブジェクトを作成しただけの場合は、
一般には ``tearDown()`` は必要ありません。しかし、もし
``setUp()`` で大量のオブジェクトを作成した場合には、
それらの後始末をするために ``tearDown()`` で変数を
``unset()`` したくなることもあるでしょう。
テストケースオブジェクト自体のガベージコレクションにはあまり意味がありません。

.. _fixtures.variations:

バリエーション
##############

ふたつのテストがあって、それぞれの setup がほんの少しだけ違う場合にはどうなるでしょう?
このような場合は、二種類の可能性が考えられます。

-

  もし ``setUp()`` の違いがごくわずかなものなら、
  その違う部分を ``setUp()``
  からテストメソッドのほうに移動させます。

-

  ``setUp()`` の違いが大きければ、
  テストケースクラスを別に分ける必要があります。それぞれのクラスには、
  setup の違いを表す名前をつけます。

.. _fixtures.sharing-fixture:

フィクスチャの共有
##################

複数のテストの間でフィクスチャを共有する利点は、ほとんどありません。
しかし、設計上の問題などでどうしても
フィクスチャを共有しなければならないこともあるでしょう。

複数のテスト間で共有する意味のあるフィクスチャの例として意味のあるものといえば、
データベースとの接続でしょう。テストのたびに新しいデータベース接続を毎回作成するのではなく、
最初にログインした状態を再利用するということです。こうすることで、
テストの実行時間を短縮できます。

:numref:`fixtures.sharing-fixture.examples.DatabaseTest.php`
では、テンプレートメソッド ``setUpBeforeClass()`` および
``tearDownAfterClass()`` を用いて、
テストケースクラス内の最初のテストを実行する前にデータベースに接続し、
最後のテストが終わってから接続を切断するようにしています。

.. code-block:: php
    :caption: テストスイートの複数テスト間でのフィクスチャの共有
    :name: fixtures.sharing-fixture.examples.DatabaseTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DatabaseTest extends TestCase
    {
        private static $dbh;

        public static function setUpBeforeClass(): void
        {
            self::$dbh = new PDO('sqlite::memory:');
        }

        public static function tearDownAfterClass(): void
        {
            self::$dbh = null;
        }
    }

このようにフィクスチャを共有することがテストの価値を下げてしまうということを、
まだうまく伝え切れていないかもしれません。問題なのは、
各オブジェクトが疎結合になっていないという設計なのです。
複数が連携しているようなテストを作って設計上の問題から目をそらしてしまうのではなく、
きちんと設計しなおした上で、スタブ (:ref:`test-doubles` を参照ください)
を使用するテストを書くことをお勧めします。

.. _fixtures.global-state:

グローバルな状態
################

`singleton
を使用するコードをテストするのはたいへんです <http://googletesting.blogspot.com/2008/05/tott-using-dependancy-injection-to.html>`_。
同様に、グローバル変数を使うコードのテストもまたたいへんです。
一般に、テスト対象のコードはグローバル変数と密接に関連しており、
グローバル変数の内容を制御することはできません。
さらに別の問題もあって、あるテストの中でグローバル変数を変更してしまうと
別のテストがうまく動かなくなる可能性があります。

PHP では、グローバル変数は次のような動きをします。

-

  グローバル変数 ``$foo = 'bar';`` は、``$GLOBALS['foo'] = 'bar';`` として格納される。

-

  ``$GLOBALS`` は*スーパーグローバル*変数と呼ばれる。

-

  スーパーグローバル変数は組み込みの変数で、すべてのスコープで常に利用できる。

-

  関数やメソッドのスコープでグローバル変数 ``$foo`` にアクセスするには、直接 ``$GLOBALS['foo']`` にアクセスするか、あるいは ``global $foo;`` を用いて (グローバル変数を参照する) ローカル変数を作成する。

グローバル変数のほかに、クラスの静的属性もグローバル状態となります。

PHPUnit 6より前のバージョンのデフォルトでは、PHPUnit がテストを実行する際には、
グローバル変数やスーパーグローバル変数 (``$GLOBALS``,
``$_ENV``, ``$_POST``,
``$_GET``, ``$_COOKIE``,
``$_SERVER``, ``$_FILES``,
``$_REQUEST``) への変更が他のテストへの影響を及ぼさないようにしました。

PHPUnit 6以降は、グローバル変数やスーパーグローバル変数のバックアップとリストアをデフォルトでは行わなくなりました。
この機能を使いたい場合は、``--globals-backup`` オプションを指定するか、
XML設定ファイルで ``backupGlobals="true"`` を指定します。

.. admonition:: Note

   グローバル変数やクラスの静的属性のバックアップ・リストアには
   ``serialize()`` および
   ``unserialize()`` を使用しています。

   PHP 組み込みの一部のクラス、たとえば ``PDO``
   のオブジェクトはシリアライズできないため、そのようなオブジェクトが
   ``$GLOBALS`` 配列に格納されている場合はバックアップ操作が失敗します。

:ref:`appendixes.annotations.backupGlobals` で説明している
``@backupGlobals`` アノテーションを使用すると、
グローバル変数のバックアップ・リストア操作を制御することができます。
あるいは、グローバル変数のリストを指定して、
その変数だけはバックアップ・リストアの対象から除外することもできます。

.. code-block:: php

    final class MyTest extends TestCase
    {
        protected $backupGlobalsExcludeList = ['globalVariable'];

        // ...
    }

.. admonition:: Note

   ``$backupGlobalsExcludeList``
   プロパティをたとえば ``setUp()``
   メソッド内で設定しても効果が及びません。

:ref:`appendixes.annotations.backupStaticAttributes`
で説明する ``@backupStaticAttributes`` アノテーションを使うと、
宣言されたクラス内のすべての static プロパティの値をバックアップしてからテストを始め、
テストが終わった後でそれらの値を復元することができます。

テストが始まる際に、宣言されたすべてのクラスについて処理を行います。テストクラス自身だけではありません。
処理するのは、クラスの static プロパティだけです。関数の内部の static 変数は対象外です。

.. admonition:: Note

   ``@backupStaticAttributes`` の操作は、テストメソッドの前に実行されます。
   ただし、有効になっている場合だけです。
   先に実行されたテストメソッドの中で static プロパティの値が変更されており、かつそのメソッドでは
   ``@backupStaticAttributes`` が有効になっていなかった場合、
   バックアップ (そしてリストア) されるのは、先に実行されたメソッドで変更後の値になります。
   もともと宣言されていたデフォルト値ではありません。
   PHP は、static 変数が宣言された当時のデフォルト値を、どこにも記録していないのです。

   これは、テストの内部で新しく読み込んだ (宣言した) クラスの static プロパティについても同様です。
   この場合も、もともと宣言されていたデフォルト値を、テストの後に復元することはできません。デフォルト値が残っていないからです。
   テスト内で設定された値が、それ以降のテストに持ち越されます。

   ユニットテストでは、テスト対象の static プロパティの値は、``setUp()``
   で明示的にリセットしておくことを推奨します
   (そして、``tearDown()`` でもリセットしておけば、それ以降のテストに影響を及ぼすこともなくなります)。

static 属性のリストを渡せば、保存と復元の対象からそれらを除外することもできます。

.. code-block:: php

    final class MyTest extends TestCase
    {
        protected $backupStaticAttributesExcludeList = [
            'className' => ['attributeName']
        ];

        // ...
    }

.. admonition:: Note

   ``$backupStaticAttributesBlacklist``
   プロパティをたとえば ``setUp()``
   メソッド内で設定しても効果が及びません。


