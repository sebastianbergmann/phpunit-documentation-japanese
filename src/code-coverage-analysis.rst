

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
や `PCOV <https://github.com/krakjoe/pcov>`_
などのPHP拡張モジュールが提供するコードカバレッジ機能、もしくは
`PHPDBG <https://www.php.net/manual/en/book.phpdbg.php>`_
などが提供するコードカバレッジ機能を利用しています。

.. admonition:: Note

   テストを実行する際に「 no code coverage driver is available 」という WARNING のエラーが出る場合、
   Xdebug や PCOV がインストールされていない、もしくはうまく設定できていない可能性があります。

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

*Branch Coverage*

    *ブランチカバレッジ* は、テストスイートの実行中に、
    制御構造内の boolean 式が ``true`` あるいは ``false``
    のどちらかとして評価されたかどうかを計測します。

*Path Coverage*

    *パスカバレッジ* は、テストスイートの実行中に、
    関数やメソッド内で取りうる実行パスが網羅されたかどうかを計測します。
    実行パスとは、関数やメソッドに入ってから出るまでの間のルート内での分岐のことです。

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

*Change Risk Anti-Patterns (CRAP) Index*

    *Change Risk Anti-Patterns (CRAP) インデックス*
    とは、循環的複雑度と、あるコード単位のコードカバレッジに基づいて算出される指標です。
    複雑度が低く、適切なテストカバレッジが達成されているコードは、CRAPインデックスの値が低くなります。
    CRAPインデックスを下げるには、テストを書くか、
    あるいはリファクタリングでコードの複雑性を下げます。

.. _code-coverage-analysis.including-files:

コードカバレッジレポートに含めるファイルの指定
################################################

フィルターを設定して、
PHPUnit に対してどのソースコードファイルをコードカバレッジレポートに含めるかを指定する必要があります。
フィルターの設定には、コマンドラインオプションの ``--coverage-filter``
:ref:`command line <textui.clioptions>` を使うか、あるいは設定ファイルを使います (
:ref:`appendixes.configuration.whitelisting-files` を参照ください)。

設定項目 ``includeUncoveredFilesInCodeCoverageReport`` および ``processUncoveredFilesForCodeCoverageReport`` で、フィルターの使いかたを設定できます。

- ``includeUncoveredFilesInCodeCoverageReport="false"`` と指定すると、指定したファイルのうち、実行される行が少なくとも一行以上あるファイルだけをコードカバレッジレポートに含めます。

- ``includeUncoveredFilesInCodeCoverageReport="true"`` (デフォルト) と指定すると、実行されるコードが一行もないファイルを含めて、フィルターで指定したすべてのファイルをコードカバレッジレポートに含めます。

- ``processUncoveredFilesForCodeCoverageReport="false"`` (デフォルト) と指定すると、フィルターで指定したファイルのうち、実行される行が一行もないファイルも (``includeUncoveredFilesInCodeCoverageReport="true"`` が指定されている場合) コードカバレッジレポートに追加されますが、PHPUnit には読み込まれず、実行可能な行数の解析は行われません。

- ``processUncoveredFilesForCodeCoverageReport="true"`` と指定すると、フィルターで指定したファイルのうち、実行される行が一行もないファイルも PHPUnit に読み込まれて、実行可能な行数の解析も行われるようになります。

.. admonition:: Note

   ``processUncoveredFilesForCodeCoverageReport="true"``
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    /**
     * @codeCoverageIgnore
     */
    final class Foo
    {
        public function bar(): void
        {
        }
    }

    final class Bar
    {
        /**
         * @codeCoverageIgnore
         */
        public function foo(): void
        {
        }
    }

    if (false) {
        // @codeCoverageIgnoreStart
        print '*';
        // @codeCoverageIgnoreEnd
    }

    exit; // @codeCoverageIgnore

これらのアノテーションを使って無視するよう指定された行は、
もし実行可能なら (たとえ実行されていなくても) 実行されたものとみなされ、
強調表示されません。

.. _code-coverage-analysis.specifying-covered-parts:

対象とするコードパーツの指定
###############################

テストコードで ``@covers`` アノテーション
(:ref:`annotation documentation <appendixes.annotations.covers.tables.annotations>`
を参照ください) を使用すると、
そのテストクラスやテストメソッドがどのコードパーツをテストしたいのかを指定することができます。
これを指定すると、指定したコードパーツのコードカバレッジ情報のみを考慮します。
:numref:`code-coverage-analysis.specifying-covered-parts.examples.InvoiceTest.php`
に例を示します。


.. admonition:: Note

    ``@covers`` アノテーションでメソッドを指定した場合、そのメソッドがテストによってカバーされているどうかのみが判定されます。このメソッドによって呼び出された他のメソッドがテストによってカバーされたかどうかは判定されません。

    そのため、 ``@covers`` アノテーションをつけたメソッドに対して *Extract Method* (メソッドの抽出) のリファクタリングを行う場合は、新しく抽出したメソッドに適宜 ``@covers`` アノテーションを追加する必要があります。

    これが、このアノテーションをクラス単位で利用することを推奨している理由です。

.. code-block:: php
    :caption: どのクラスを対象とするかを指定したテスト
    :name: code-coverage-analysis.specifying-covered-parts.examples.InvoiceTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;
    /**
     * @covers \Invoice
     * @uses \Money
     */
    final class InvoiceTest extends TestCase
    {
        private $invoice;
        protected function setUp(): void
        {
            $this->invoice = new Invoice;
        }
        public function testAmountInitiallyIsEmpty(): void
        {
            $this->assertEquals(new Money, $this->invoice->getAmount());
        }
    }


.. code-block:: php
    :caption: どのメソッドを対象とするかを指定したテスト
    :name: code-coverage-analysis.specifying-covered-parts.examples.BankAccountTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class BankAccountTest extends TestCase
    {
        private $ba;

        protected function setUp(): void
        {
            $this->ba = new BankAccount;
        }

        /**
         * @covers \BankAccount::getBalance
         */
        public function testBalanceIsInitiallyZero(): void
        {
            $this->assertSame(0, $this->ba->getBalance());
        }

        /**
         * @covers \BankAccount::withdrawMoney
         */
        public function testBalanceCannotBecomeNegative(): void
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
         * @covers \BankAccount::depositMoney
         */
        public function testBalanceCannotBecomeNegative2(): void
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
         * @covers \BankAccount::getBalance
         * @covers \BankAccount::depositMoney
         * @covers \BankAccount::withdrawMoney
         */
        public function testDepositWithdrawMoney(): void
        {
            $this->assertSame(0, $this->ba->getBalance());
            $this->ba->depositMoney(1);
            $this->assertSame(1, $this->ba->getBalance());
            $this->ba->withdrawMoney(1);
            $this->assertSame(0, $this->ba->getBalance());
        }
    }

あるテストが、*一切* メソッドをカバーしてはならないことも指定できます。
そのために使うのが ``@coversNothing`` アノテーションです。
(:ref:`appendixes.annotations.coversNothing` を参照ください)。
これは、インテグレーションテストを書く際に
ユニットテストだけのコードカバレッジを生成させたい場合に便利です。

.. code-block:: php
    :caption: どのメソッドもカバーすべきでないことを指定したテスト
    :name: code-coverage-analysis.specifying-covered-parts.examples.GuestbookIntegrationTest.php

    <?php declare(strict_types=1);
    use PHPUnit\DbUnit\TestCase

    final class GuestbookIntegrationTest extends TestCase
    {
        /**
         * @coversNothing
         */
        public function testAddEntry(): void
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

.. _code-coverage-analysis.edge-cases:

エッジケース
############

この節では、コードカバレッジ情報がわかりにくくなってしまうような、
エッジケースについて紹介します。

.. code-block:: php
    :name: code-coverage-analysis.edge-cases.examples.Sample.php

    <?php declare(strict_types=1);
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
