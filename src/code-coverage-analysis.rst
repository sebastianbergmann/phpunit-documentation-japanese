

.. _code-coverage-analysis:

====================
コードカバレッジ解析
====================

    *Wikipedia*:

    In computer science, code coverage is a measure used to describe the
    degree to which the source code of a program is tested by a particular
    test suite. A program with high code coverage has been more thoroughly
    tested and has a lower chance of containing software bugs than a program
    with low code coverage.

この章では、PHPUnit のコードカバレッジ機能について学びます。
これは、テストを実行したときに、実装コードのどの部分が実行されたかを調べるものです。
PHPUnit のコードカバレッジ解析では
`php-code-coverage <https://github.com/sebastianbergmann/php-code-coverage>`_
コンポーネントを使っています。このコンポーネントは、
`Xdebug <https://www.xdebug.org/>`_
拡張モジュールが提供するステートメントカバレッジ機能を利用しています。

.. admonition:: Note

   Xdebug は PHPUnit 本体には組み込まれていません。
   テストを実行したときに no code coverage driver is available という notice が出る場合は、
   Xdebug がインストールされていないかあるいはうまく設定できていないのでしょう。
   PHPUnit のコードカバレッジ機能を使う前に、まずは
   `Xdebug のインストールガイド <https://xdebug.org/docs/install>`_
   を読んでみましょう。

   php-code-coverage は `phpdbg <https://phpdbg.room11.org/introduction.html>`_
   もコードカバレッジデータのソースとしてサポートしています。

PHPUnit は、HTML ベースのコードカバレッジレポートを生成するだけでなく、
XML ベースのログファイルにコードカバレッジ情報を出力することもできます。
Clover、Crap4J、PHPUnit など、さまざまな形式に対応しています。
また、コードカバレッジ情報をテキスト形式で出力 (そして、標準出力に表示)
したり、PHP のコードとして出力して後処理をしたりすることもできます。

コードカバレッジ機能を制御するための
コマンドラインスイッチの一覧は、:ref:`textui` を参照ください。
また、設定項目については :ref:`appendixes.configuration.logging` を参照ください。

.. _code-coverage-analysis.metrics:

コードカバレッジの指標
######################

コードカバレッジを計測するための指標には、さまざまなものがあります。

*Line Coverage*

    *ラインカバレッジ* は、
    実行可能な行が実行されたかどうかを計測します。

*Function and Method Coverage*

    *関数・メソッドカバレッジ* は、
    関数やメソッドが実行されたかどうかを計測します。
    php-code-coverage は、その関数やメソッド内の実行可能な行がすべて実行された場合にのみ、
    その関数やメソッドが実行されたとみなします。

*クラス・トレイトカバレッジ*

    *クラス・トレイトカバレッジ* は、
    クラスやトレイトがカバーされたかどうかを計測します。
    php-code-coverage は、クラスやトレイト内のすべてのメソッドがカバーされている場合にのみ、
    そのクラスやトレイトがカバーされたとみなします。

*Opcode Coverage*

    *オペコードカバレッジ* は、関数やメソッドのオペコードが、
    テストスイートの実行中に実行されたかどうかを計測します。
    通常は、1 行のコードをコンパイルすると、複数のオペコードになります。
    ラインカバレッジは、複数のオペコードのうち少なくともひとつが実行された時点で、
    その行が実行されたとみなします。

*Branch Coverage*

    *ブランチカバレッジ* は、テストスイートの実行中に、
    制御構造内の boolean 式が ``true`` あるいは ``false``
    のどちらかとして評価されたかどうかを計測します。

*Path Coverage*

    *パスカバレッジ* は、テストスイートの実行中に、
    関数やメソッド内で取りうる実行パスが網羅されたかどうかを計測します。
    実行パスとは、関数やメソッドに入ってから出るまでの間のルート内での分岐のことです。

*Change Risk Anti-Patterns (CRAP) Index*

    *Change Risk Anti-Patterns (CRAP) インデックス*
    とは、循環的複雑度と、あるコード単位のコードカバレッジに基づいて算出される指標です。
    複雑度が低く、適切なテストカバレッジが達成されているコードは、CRAPインデックスの値が低くなります。
    CRAPインデックスを下げるには、テストを書くか、
    あるいはリファクタリングでコードの複雑性を下げます。

.. admonition:: Note

   *オペコードカバレッジ*、
   *ブランチカバレッジ*、
   *パスカバレッジ* については、
   php-code-coverage ではまだサポートしていません。

.. _code-coverage-analysis.whitelisting-files:

ファイルのホワイトリスト
########################

*ホワイトリスト* を設定して、
PHPUnit に対してどのソースコードファイルをコードカバレッジレポートに含めるかを指定する必要があります。
ホワイトリストの設定には、コマンドラインオプションの ``--whitelist``
を使うか、あるいは設定ファイルを使います (:ref:`appendixes.configuration.whitelisting-files` を参照ください)。

設定項目 ``addUncoveredFilesFromWhitelist`` および ``processUncoveredFilesFromWhitelist`` で、ホワイトリストの使いかたを設定できます。

- ``addUncoveredFilesFromWhitelist="false"`` と指定すると、ホワイトリストの追加したファイルのうち、実行される行が少なくとも一行以上あるファイルだけをコードカバレッジレポートに含めます。

- ``addUncoveredFilesFromWhitelist="true"`` (デフォルト) と指定すると、実行されるコードが一行もないファイルを含めてホワイトリスト上のすべてのファイルをコードカバレッジレポートに含めます。

- ``processUncoveredFilesFromWhitelist="false"`` (デフォルト) と指定すると、実行される行が一行もないホワイトリスト上のファイルも (``addUncoveredFilesFromWhitelist="true"`` が指定されている場合) コードカバレッジレポートに追加されますが、PHPUnit には読み込まれず、実行可能な行数の解析は行われません。

- ``processUncoveredFilesFromWhitelist="true"`` と指定すると、実行される行が一行もないホワイトリスト上のファイルが PHPUnit に読み込まれて、実行可能な行数の解析も行われるようになります。

.. admonition:: Note

   ``processUncoveredFilesFromWhitelist="true"``
   が設定されている場合のソースコードファイルの読み込みでは、
   もしクラスや関数のスコープから外れるコードが含まれていたときに問題が起こる可能性があります。

.. _code-coverage-analysis.ignoring-code-blocks:

コードブロックの無視
####################

どうしてもテストができないコードブロックなどを、
コードカバレッジ解析時に無視させたいこともあるでしょう。
PHPUnit でこれを実現するには、
``@codeCoverageIgnore``、
``@codeCoverageIgnoreStart`` および
``@codeCoverageIgnoreEnd`` アノテーションを
:numref:`code-coverage-analysis.ignoring-code-blocks.examples.Sample.php`
のように使用します。

.. code-block:: php
    :caption: ``@codeCoverageIgnore``、``@codeCoverageIgnoreStart`` および ``@codeCoverageIgnoreEnd`` アノテーションの使用法
    :name: code-coverage-analysis.ignoring-code-blocks.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    class Foo
    {
        public function bar()
        {
        }
    }

    class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo()
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore
    ?>

これらのアノテーションを使って無視するよう指定された行は、
もし実行可能なら (たとえ実行されていなくても) 実行されたものとみなされ、
強調表示されません。

.. _code-coverage-analysis.specifying-covered-methods:

カバーするメソッドの指定
########################

テストコードで ``@covers`` アノテーション
(:ref:`appendixes.annotations.covers.tables.annotations`)
を参照ください) を使用すると、
そのテストメソッドがどのメソッドをテストしたいのかを指定することができます。
これを指定すると、指定したメソッドのコードカバレッジ情報のみを考慮します。
:numref:`code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php`
に例を示します。

.. code-block:: php
    :caption: どのメソッドを対象とするかを指定したテスト
    :name: code-coverage-analysis.specifying-covered-methods.examples.BankAccountTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class BankAccountTest extends TestCase
    {
        protected $ba;

        protected function setUp()
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero()
        {
            $this->assertSame(0, $this->ba->getBalance());
        }

        /**
         * @covers BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative()
        {
            try {
                $this->ba->withdrawMoney(1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2()
        {
            try {
                $this->ba->depositMoney(-1);
            }

            catch (BankAccountException $e) {
                $this->assertSame(0, $this->ba->getBalance());

                return;
            }

            $this->fail();
        }

        /**
         * @covers BankAccount::getBalance
         * @covers BankAccount::depositMoney
         * @covers BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney()
        {
            $this->assertSame(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertSame(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertSame(0, $this->ba->getBalance());
        }
    }
    ?>

あるテストが、*一切*メソッドをカバーしてはならないことも指定できます。
そのために使うのが ``@coversNothing`` アノテーションです。
(:ref:`appendixes.annotations.coversNothing` を参照ください)。
これは、インテグレーションテストを書く際に
ユニットテストだけのコードカバレッジを生成させたい場合に便利です。

.. code-block:: php
    :caption: どのメソッドもカバーすべきでないことを指定したテスト
    :name: code-coverage-analysis.specifying-covered-methods.examples.GuestbookIntegrationTest.php

    <?php
    use PHPUnit\DbUnit\TestCase

    class GuestbookIntegrationTest extends TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );

            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");

            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }
    ?>

.. _code-coverage-analysis.edge-cases:

エッジケース
############

この節では、コードカバレッジ情報がわかりにくくなってしまうような、
エッジケースについて紹介します。

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php
    use PHPUnit\Framework\TestCase;

    // カバレッジは「行単位」であって文単位ではないので、
    // 一行にまとめられた行はひとつのカバレッジ状態しか持ちません
    if (false) this_function_call_shows_up_as_covered();

    // コードカバレッジの内部動作上、これら 2 行は特別です。
    // 次の行は「実行されていない」となります
    if (false)
        // 次の行は「実行されている」となります
        // 実際のところ、ひとつ上の if 文のカバレッジ情報がここに表示されることになるからです!
        will_also_show_up_as_covered();

    // これを避けるには、必ず波括弧を使わなければなりません
    if (false) {
        this_call_will_never_show_up_as_covered();
    }
    ?>


