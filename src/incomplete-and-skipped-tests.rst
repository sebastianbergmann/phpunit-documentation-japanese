

.. _incomplete-and-skipped-tests:

============================
不完全なテスト・テストの省略
============================

.. _incomplete-and-skipped-tests.incomplete-tests:

不完全なテスト
##############

新しいテストケースクラスを作成する際には、
これから書くべきテストの内容をはっきりさせるために、
まず最初は以下のような空のテストメソッドを書きたくなることでしょう。

.. code-block:: php

    public function testSomething()
    {
    }

しかし、PHPUnit フレームワークでは空のメソッドを「成功した」
と判断してしまうという問題があります。このような解釈ミスがあると、
テスト結果のレポートが無意味になってしまいます。
そのテストがほんとうに成功したのか、
それともまだテストが実装されていないのかが判断できないからです。
実装していないテストメソッドの中で ``$this->fail()``
をコールするようにしたところで事態は何も変わりません。
こうすると、テストが「失敗した」と判断されてしまいます。
これは未実装のテストが「成功」と判断されてしまうのと同じくらいまずいことです
(訳注: レポートを見ても、そのテストがほんとうに失敗したのか、
まだ実装されていないだけなのかがわかりません)。

テストの成功を青信号、失敗を赤信号と考えるなら、
テストが未完成あるいは未実装であることを表すための黄信号が必要です。
そのような場合に使用するインターフェイスが
``PHPUnit\Framework\IncompleteTest`` で、
これは未完成あるいは未実装のテストメソッドで発生する例外を表すものです。
このインターフェイスの標準的な実装が
``PHPUnit\Framework\IncompleteTestError`` です。

:numref:`incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php`
では ``SampleTest`` というテストケースクラスを定義しています。
便利なメソッド ``markTestIncomplete()``
(これは、自動的に ``PHPUnit\Framework\IncompleteTestError``
を発生させます) をテストメソッド内でコールすることで、
このメソッドがまだ完成していないことをはっきりさせます。

.. code-block:: php
    :caption: テストに未完成の印をつける
    :name: incomplete-and-skipped-tests.incomplete-tests.examples.SampleTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class SampleTest extends TestCase
    {
        public function testSomething()
        {
            // オプション: お望みなら、ここで何かのテストをしてください。
            $this->assertTrue(true, 'これは動いているはずです。');

            // ここで処理を止め、テストが未完成であるという印をつけます。
            $this->markTestIncomplete(
              'このテストは、まだ実装されていません。'
            );
        }
    }
    ?>

未完成のテストは、PHPUnit のコマンドライン版テストランナーでは以下のように
``I`` で表されます。

.. code-block:: bash

    $ phpunit --verbose SampleTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    I

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 incomplete test:

    1) SampleTest::testSomething
    このテストは、まだ実装されていません。

    /home/sb/SampleTest.php:12
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 1, Incomplete: 1.

:numref:`incomplete-and-skipped-tests.incomplete-tests.tables.api`
に、テストを未完成扱いにするための API を示します。

.. rst-class:: table
.. list-table:: 未完成のテスト用の API
    :name: incomplete-and-skipped-tests.incomplete-tests.tables.api
    :header-rows: 1

    * - メソッド
      - 意味
    * - ``void markTestIncomplete()``
      - 現在のテストを未完成扱いにします。
    * - ``void markTestIncomplete(string $message)``
      - 現在のテストを未完成扱いにします。それを説明する文字列として ``$message`` を使用します。

.. _incomplete-and-skipped-tests.skipping-tests:

テストの省略
############

すべてのテストがあらゆる環境で実行できるわけではありません。
考えてみましょう。たとえば、データベースの抽象化レイヤーを使用しており、
それがさまざまなドライバを使用してさまざまなデータベースシステムを
サポートしているとします。MySQL ドライバのテストができるのは、
当然 MySQL サーバが使用できる環境だけです。

:numref:`incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php`
に示すテストケースクラス ``DatabaseTest`` には、
テストメソッド ``testConnection()`` が含まれています。
このクラスのテンプレートメソッド ``setUp()`` では、
MySQLi 拡張モジュールが使用可能かを調べたうえで、もし使用できない場合は
``markTestSkipped()`` メソッドでテストを省略するようにしています。

.. code-block:: php
    :caption: テストを省略する
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DatabaseTest extends TestCase
    {
        protected function setUp()
        {
            if (!extension_loaded('mysqli')) {
                $this->markTestSkipped(
                  'MySQLi 拡張モジュールが使用できません。'
                );
            }
        }

        public function testConnection()
        {
            // ...
        }
    }
    ?>

飛ばされたテストは、PHPUnit のコマンドライン版テストランナーでは以下のように
``S`` で表されます。

.. code-block:: bash

    $ phpunit --verbose DatabaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    S

    Time: 0 seconds, Memory: 3.95Mb

    There was 1 skipped test:

    1) DatabaseTest::testConnection
    MySQLi 拡張モジュールが使用できません。

    /home/sb/DatabaseTest.php:9
    OK, but incomplete or skipped tests!
    Tests: 1, Assertions: 0, Skipped: 1.

:numref:`incomplete-and-skipped-tests.skipped-tests.tables.api`
に、テストを省略するための API を示します。

.. rst-class:: table
.. list-table:: テストを省略するための API
    :name: incomplete-and-skipped-tests.skipped-tests.tables.api
    :header-rows: 1

    * - メソッド
      - 意味
    * - ``void markTestSkipped()``
      - 現在のテストを省略扱いにします。
    * - ``void markTestSkipped(string $message)``
      - 現在のテストを省略扱いにします。それを説明する文字列として ``$message`` を使用します。

.. _incomplete-and-skipped-tests.skipping-tests-using-requires:

@requires によるテストのスキップ
################################

ここまでに示したメソッドに加えて、
``@requires`` アノテーションを使って共通の事前条件を記述することもできます。

.. rst-class:: table
.. list-table:: @requires の例用例
    :name: incomplete-and-skipped-tests.requires.tables.api
    :header-rows: 1

    * - 型
      - 取り得る値
      - 例
      - 別の例
    * - ``PHP``
      - PHP のバージョン
      - @requires PHP 5.3.3
      - @requires PHP 7.1-dev
    * - ``PHPUnit``
      - PHPUnit のバージョン
      - @requires PHPUnit 3.6.3
      - @requires PHPUnit 4.6
    * - ``OS``
      - `PHP_OS <http://php.net/manual/ja/reserved.constants.php#constant.php-os>`_ にマッチする正規表現
      - @requires OS Linux
      - @requires OS WIN32|WINNT
    * - ``OSFAMILY``
      - 任意の `OS ファミリー <http://php.net/manual/ja/reserved.constants.php#constant.php-os-family>`_
      - @requires OSFAMILY Solaris
      - @requires OSFAMILY Windows
    * - ``function``
      - `function_exists <http://php.net/function_exists>`_ に渡せるパラメータ
      - @requires function imap_open
      - @requires function ReflectionMethod::setAccessible
    * - ``extension``
      - 拡張モジュール名 (オプションでバージョンも指定できる)
      - @requires extension mysqli
      - @requires extension redis 2.2.0

.. code-block:: php
    :caption: @requires を使ったテストケースのスキップ
    :name: incomplete-and-skipped-tests.skipping-tests.examples.DatabaseClassSkippingTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @requires extension mysqli
     */
    class DatabaseTest extends TestCase
    {
        /**
         * @requires PHP 5.3
         */
        public function testConnection()
        {
            // このテストには mysqli 拡張モジュールと PHP 5.3 以降が必須です
        }

        // ... その他のすべてのテストには mysqli 拡張モジュールが必須です
    }
    ?>

特定のバージョンの PHP でしか使えない構文を利用する場合は、
:ref:`appendixes.configuration.testsuites`
にあるように XML 設定ファイルでのバージョン依存のインクルードを検討しましょう。


