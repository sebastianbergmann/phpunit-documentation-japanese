

.. _writing-tests-for-phpunit:

==========================
PHPUnit 用のテストの書き方
==========================

:numref:`writing-tests-for-phpunit.examples.StackTest.php` で、
PHP の配列操作のテストを PHPUnit 用に書く方法を示します。
この例では、PHPUnit を使ったテストを書く際の基本的な決まり事や手順を紹介します。

#.

   ``Class`` という名前のクラスのテストは、``ClassTest`` という名前のクラスに記述します。

#.

   ``ClassTest`` は、(ほとんどの場合) ``PHPUnit\Framework\TestCase`` を継承します。

#.

   テストは、``test*`` という名前のパブリックメソッドとなります。

   あるいは、``@test`` アノテーションをメソッドのコメント部で使用することで、それがテストメソッドであることを示すこともできます。

#.

   テストメソッドの中で ``assertSame()`` のようなアサーションメソッド (:ref:`appendixes.assertions` を参照ください) を使用して、期待される値と実際の値が等しいことを確かめます。

.. code-block:: php
    :caption: PHPUnit での配列操作のテスト
    :name: writing-tests-for-phpunit.examples.StackTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StackTest extends TestCase
    {
        public function testPushAndPop(): void
        {
            $stack = [];
            $this->assertSame(0, count($stack));

            array_push($stack, 'foo');
            $this->assertSame('foo', $stack[count($stack)-1]);
            $this->assertSame(1, count($stack));

            $this->assertSame('foo', array_pop($stack));
            $this->assertSame(0, count($stack));
        }
    }

|
    *Martin Fowler*:

    Whenever you are tempted to type something into a
    ``print`` statement or a debugger expression, write it
    as a test instead.

    何かを ``print`` 文やデバッガの式に書きたくなったときは、
    代わりにその内容をテストに書くようにするんだ。

.. _writing-tests-for-phpunit.test-dependencies:

テストの依存性
##############

    *Adrian Kuhn et. al.*:

    Unit Tests are primarily written as a good practice to help developers
    identify and fix bugs, to refactor code and to serve as documentation
    for a unit of software under test. To achieve these benefits, unit tests
    ideally should cover all the possible paths in a program. One unit test
    usually covers one specific path in one function or method. However a
    test method is not necessarily an encapsulated, independent entity. Often
    there are implicit dependencies between test methods, hidden in the
    implementation scenario of a test.

    ユニットテストを書くそもそもの目的は、バグの発見と修正や
    コードのリファクタリングを開発者がやりやすくすること。
    そしてテスト対象のソフトウェアのドキュメントとしての役割を果たすことだ。
    これらの目的を達成するためには、
    ユニットテストがプログラム内のすべてのルートをカバーしていることが理想である。
    ひとつのユニットテストがカバーするのは、
    通常はひとつの関数やメソッド内の特定のルートだけとなる。
    しかし、テストメソッドは必ずしもカプセル化して独立させる必要はない。
    複数のテストメソッドの間に暗黙の依存性があって、
    隠された実装シナリオがテストの中にあるのもよくあることだ。

PHPUnit は、テストメソッド間の依存性の明示的な宣言をサポートしています。
この依存性とは、テストメソッドが実行される順序を定義するものではありません。
プロデューサーがテストフィクスチャを作ってそのインスタンスを返し、
依存するコンシューマーがそれを受け取って利用するというものです。

-

  プロデューサーとは、返り値としてテスト対象のユニットを生成するテストメソッドのこと。

-

  コンシューマーとは、プロデューサーの返り値に依存するテストメソッドのこと。

:numref:`writing-tests-for-phpunit.examples.StackTest2.php`
は、``@depends`` アノテーションを使ってテストメソッドの依存性をあらわす例です。

.. code-block:: php
    :caption: ``@depends`` アノテーションを使った依存性の表現
    :name: writing-tests-for-phpunit.examples.StackTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StackTest extends TestCase
    {
        public function testEmpty(): array
        {
            $stack = [];
            $this->assertEmpty($stack);

            return $stack;
        }

        /**
         * @depends testEmpty
         */
        public function testPush(array $stack): array
        {
            array_push($stack, 'foo');
            $this->assertSame('foo', $stack[count($stack)-1]);
            $this->assertNotEmpty($stack);

            return $stack;
        }

        /**
         * @depends testPush
         */
        public function testPop(array $stack): void
        {
            $this->assertSame('foo', array_pop($stack));
            $this->assertEmpty($stack);
        }
    }

上の例では、まず最初のテスト ``testEmpty()``
で新しい配列を作り、それが空であることを確かめます。
このテストは、フィクスチャを返します。
二番目のテスト ``testPush()`` は
``testEmpty()`` に依存しており、
依存するテストの結果を引数として受け取ります。
最後の ``testPop()`` は
``testPush()`` に依存しています。

.. admonition:: Note

   プロデューサーの生成する戻り値は、デフォルトでは「そのままの形式」でコンシューマーに渡されます。
   つまり、プロデューサーがオブジェクトを戻した場合は、そのオブジェクトへの参照がコンシューマーに渡されるということです。
   参照を使う代わりに、 ``@depends clone`` を用いてディープコピーをしたり
   ``@depends shallowClone`` を用いてシャローコピー
   （PHPのキーワード ``clone`` によるコピー）をしたりすることもできます。

問題の局所化を手早く行うには、失敗したテストに目を向けやすくしたいものです。
そのため PHPUnit では、
あるテストが失敗したときにはそのテストに依存する他のテストの実行をスキップします。
テスト間の依存性を活用して問題点を見つけやすくしている例を
:numref:`writing-tests-for-phpunit.examples.DependencyFailureTest.php`
に示します。

.. code-block:: php
    :caption: テストの依存性の活用
    :name: writing-tests-for-phpunit.examples.DependencyFailureTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DependencyFailureTest extends TestCase
    {
        public function testOne(): void
        {
            $this->assertTrue(false);
        }

        /**
         * @depends testOne
         */
        public function testTwo(): void
        {
        }
    }

.. parsed-literal::

    $ phpunit --verbose DependencyFailureTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    FS

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) DependencyFailureTest::testOne
    Failed asserting that false is true.

    /home/sb/DependencyFailureTest.php:6

    There was 1 skipped test:

    1) DependencyFailureTest::testTwo
    This test depends on "DependencyFailureTest::testOne" to pass.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1, Skipped: 1.

ひとつのテストに複数の ``@depends`` アノテーションをつけることもできます。
PHPUnit はテストが実行される順序を変更しないので、
テストが実行されるときに確実に依存性が満たされているようにしておく必要があります。

複数の ``@depends`` アノテーションを持つテストは、
最初のプロデューサーからのフィクスチャを最初の引数、二番目のプロデューサーからのフィクスチャを二番目の引数、……
として受け取ります。
:numref:`writing-tests-for-phpunit.examples.MultipleDependencies.php`
を参照ください。

.. code-block:: php
    :caption: 複数の依存性を持つテスト
    :name: writing-tests-for-phpunit.examples.MultipleDependencies.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class MultipleDependenciesTest extends TestCase
    {
        public function testProducerFirst(): string
        {
            $this->assertTrue(true);

            return 'first';
        }

        public function testProducerSecond(): string
        {
            $this->assertTrue(true);

            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         */
        public function testConsumer(string $a, string $b): void
        {
            $this->assertSame('first', $a);
            $this->assertSame('second', $b);
        }
    }

.. parsed-literal::

    $ phpunit --verbose MultipleDependenciesTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...

    Time: 0 seconds, Memory: 3.25Mb

    OK (3 tests, 4 assertions)

.. _writing-tests-for-phpunit.data-providers:

データプロバイダ
################

テストメソッドには任意の引数を渡すことができます。
この引数は、データプロバイダメソッド
(:numref:`writing-tests-for-phpunit.data-providers.examples.DataTest.php`
の ``additionProvider()``)
で指定します。使用するデータプロバイダメソッドを指定するには
``@dataProvider`` アノテーションを使用します。

データプロバイダメソッドは、``public``
でなければなりません。また、
メソッドの返り値の型は、配列の配列あるいはオブジェクト
(``Iterator`` インターフェイスを実装しており、
反復処理の際に配列を返すもの) である必要があります。
この返り値の各要素に対して、その配列の中身を引数としてテストメソッドがコールされます。

.. code-block:: php
    :caption: 配列の配列を返すデータプロバイダの使用
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider(): array
        {
            return [
                [0, 0, 0],
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }
    }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

大量のデータセットを使う場合は、デフォルトの数字を使うのではなく、各データセットに文字列の名前をつけておくと便利です。
出力もよりわかりやすくなり、テストを失敗させたデータセットの名前もわかるようになります。

.. code-block:: php
    :caption: データプロバイダでの名前つきデータセットの使用
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest1.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider(): array
        {
            return [
                'adding zeros'  => [0, 0, 0],
                'zero plus one' => [0, 1, 1],
                'one plus zero' => [1, 0, 1],
                'one plus one'  => [1, 1, 3]
            ];
        }
    }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit 4.6.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set "one plus one" (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:9

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: Iterator オブジェクトを返すデータプロバイダの使用
    :name: writing-tests-for-phpunit.data-providers.examples.DataTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionProvider(): CsvFileIterator
        {
            return new CsvFileIterator('data.csv');
        }
    }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 ('1', '1', '3')
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:11

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: CsvFileIterator クラス
    :name: writing-tests-for-phpunit.data-providers.examples.CsvFileIterator.php

    <?php
    use PHPUnit\Framework\TestCase;

    final class CsvFileIterator implements Iterator
    {
        private $file;
        private $key = 0;
        private $current;

        public function __construct(string $file)
        {
            $this->file = fopen($file, 'r');
        }

        public function __destruct()
        {
            fclose($this->file);
        }

        public function rewind(): void
        {
            rewind($this->file);

            $this->current = fgetcsv($this->file);
            $this->key = 0;
        }

        public function valid(): bool
        {
            return !feof($this->file);
        }

        public function key(): int
        {
            return $this->key;
        }

        public function current(): array
        {
            return $this->current;
        }

        public function next(): void
        {
            $this->current = fgetcsv($this->file);

            $this->key++;
        }
    }

``@dataProvider`` で指定したメソッドと
``@depends`` で指定したテストの両方からの入力を受け取るテストの場合、
データプロバイダからの引数のほうが依存するテストからの引数より先にきます。
依存するテストからの引数は、どちらのデータセットに対しても同じになります。
:numref:`writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php`
を参照ください。

.. code-block:: php
    :caption: 同じテストでの @depends と @dataProvider の組み合わせ
    :name: writing-tests-for-phpunit.data-providers.examples.DependencyAndDataProviderCombo.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DependencyAndDataProviderComboTest extends TestCase
    {
        public function provider(): array
        {
            return [['provider1'], ['provider2']];
        }

        public function testProducerFirst(): string
        {
            $this->assertTrue(true);

            return 'first';
        }

        public function testProducerSecond(): string
        {
            $this->assertTrue(true);

            return 'second';
        }

        /**
         * @depends testProducerFirst
         * @depends testProducerSecond
         * @dataProvider provider
         */
        public function testConsumer(): void
        {
            $this->assertSame(
                ['provider1', 'first', 'second'],
                func_get_args()
            );
        }
    }

.. parsed-literal::

    $ phpunit --verbose DependencyAndDataProviderComboTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ...F

    Time: 0 seconds, Memory: 3.50Mb

    There was 1 failure:

    1) DependencyAndDataProviderComboTest::testConsumer with data set #1 ('provider2')
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
    Array &0 (
    -    0 => 'provider1'
    +    0 => 'provider2'
         1 => 'first'
         2 => 'second'
    )

    /home/sb/DependencyAndDataProviderComboTest.php:31

    FAILURES!
    Tests: 4, Assertions: 4, Failures: 1.

.. code-block:: php
    :caption: ひとつのテストでの複数のデータプロバイダの使用
    :name: writing-tests-for-phpunit.data-providers.examples2.DataTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DataTest extends TestCase
    {
        /**
         * @dataProvider additionWithNonNegativeNumbersProvider
         * @dataProvider additionWithNegativeNumbersProvider
         */
        public function testAdd(int $a, int $b, int $expected): void
        {
            $this->assertSame($expected, $a + $b);
        }

        public function additionWithNonNegativeNumbersProvider(): array
        {
            return [
                [0, 1, 1],
                [1, 0, 1],
                [1, 1, 3]
            ];
        }

        public function additionWithNegativeNumbersProvider(): array
        {
            return [
                [-1, 1, 0],
                [-1, -1, -2],
                [1, -1, 0]
            ];
        }
     }

.. parsed-literal::

    $ phpunit DataTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..F...                                                              6 / 6 (100%)

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) DataTest::testAdd with data set #3 (1, 1, 3)
    Failed asserting that 2 is identical to 3.

    /home/sb/DataTest.php:12

    FAILURES!
    Tests: 6, Assertions: 6, Failures: 1.

.. admonition:: Note

   あるテストがデータプロバイダを使う別のテストに依存している場合、
   別のテストで少なくともひとつのデータセットに対するテストが成功すれば
   そのテストも実行されます。
   データプロバイダを使ったテストの結果をそのテストに注入することはできません。

.. admonition:: Note

   すべてのデータプロバイダを実行してから、
   静的メソッド ``setUpBeforeClass()``
   や ``setUp()`` メソッドの最初の呼び出しが発生します。そのため、
   これらのメソッドで作った変数にデータプロバイダ内からアクセスすることはできません。
   そうなっている理由は、PHPUnit がテストの総数を算出できるようにするためです。

.. _writing-tests-for-phpunit.exceptions:

例外のテスト
############

:numref:`writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php`
は、テストするコード内で例外がスローされたかどうかを
``expectException()`` メソッドを使用して調べる方法を示すものです。

.. code-block:: php
    :caption: expectException() メソッドの使用法
    :name: writing-tests-for-phpunit.exceptions.examples.ExceptionTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ExceptionTest extends TestCase
    {
        public function testException(): void
        {
            $this->expectException(InvalidArgumentException::class);
        }
    }

.. parsed-literal::

    $ phpunit ExceptionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ExceptionTest::testException
    Failed asserting that exception of type "InvalidArgumentException" is thrown.

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``expectException()`` メソッドのほかにも
``expectExceptionCode()``、
``expectExceptionMessage()``、
``expectExceptionMessageMatches()`` といったメソッドで、
テスト対象のコードで発生するであろう例外をテストできます。

.. admonition:: Note

   ``expectExceptionMessage()`` は ``$actual`` の中に
   ``$expected`` のメッセージが含まれるかどうかを確かめるだけのものであり、
   完全一致するかどうかを確かめるわけではないことに注意しましょう。

.. _writing-tests-for-phpunit.errors:

PHP のエラーのテスト
####################

デフォルトでは、PHPUnit はテストの実行中に発生した PHP のエラーや警告そして notice
を例外に変換します。これらの例外を用いて、たとえば
:numref:`writing-tests-for-phpunit.exceptions.examples.ErrorTest.php`
のように PHP のエラーが発生することをテストできます。

.. admonition:: Note

   PHP の実行時設定 ``error_reporting`` を使うと、
   PHPUnit がどのエラーを例外に変換するのかを制限できます。
   この機能に関して何か問題がでた場合は、PHP の設定を見直し、
   調べたいと思っているエラーを抑制するようになっていないかどうか確認しましょう。

.. code-block:: php
    :caption: PHPのエラーが発生することのテスト
    :name: writing-tests-for-phpunit.exceptions.examples.ErrorTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;
    final class ErrorTest extends TestCase
    {
        public function testDeprecationCanBeExpected(): void
        {
            $this->expectDeprecation();
            // Optionally test that the message is equal to a string
            $this->expectDeprecationMessage('foo');
            // Or optionally test that the message matches a regular expression
            $this->expectDeprecationMessageMatches('/foo/');
            \trigger_error('foo', \E_USER_DEPRECATED);
        }
        public function testNoticeCanBeExpected(): void
        {
            $this->expectNotice();
            // Optionally test that the message is equal to a string
            $this->expectNoticeMessage('foo');
            // Or optionally test that the message matches a regular expression
            $this->expectNoticeMessageMatches('/foo/');
            \trigger_error('foo', \E_USER_NOTICE);
        }
        public function testWarningCanBeExpected(): void
        {
            $this->expectWarning();
            // Optionally test that the message is equal to a string
            $this->expectWarningMessage('foo');
            // Or optionally test that the message matches a regular expression
            $this->expectWarningMessageMatches('/foo/');
            \trigger_error('foo', \E_USER_WARNING);
        }
        public function testErrorCanBeExpected(): void
        {
            $this->expectError();
            // Optionally test that the message is equal to a string
            $this->expectErrorMessage('foo');
            // Or optionally test that the message matches a regular expression
            $this->expectErrorMessageMatches('/foo/');
            \trigger_error('foo', \E_USER_ERROR);
        }
    }

エラーを引き起こすような PHP の関数、たとえば ``fopen``
などに依存するテストを行うときには、テスト中にエラーを抑制できれば便利なことがあります。
そうすれば、notice のせいで
``PHPUnit\Framework\Error\Notice``
が出てしまうことなく、返り値だけをチェックできるようになります。

.. code-block:: php
    :caption: PHP のエラーが発生するコードの返り値のテスト
    :name: writing-tests-for-phpunit.exceptions.examples.TriggerErrorReturnValue.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ErrorSuppressionTest extends TestCase
    {
        public function testFileWriting(): void
        {
            $writer = new FileWriter;

            $this->assertFalse(@$writer->write('/is-not-writeable/file', 'stuff'));
        }
    }

    final class FileWriter
    {
        public function write($file, $content)
        {
            $file = fopen($file, 'w');

            if ($file === false) {
                return false;
            }

            // ...
        }
    }

.. parsed-literal::

    $ phpunit ErrorSuppressionTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .

    Time: 1 seconds, Memory: 5.25Mb

    OK (1 test, 1 assertion)

もしエラーを抑制しなければ、このテストは失敗して
``fopen(/is-not-writeable/file): failed to open stream: No such file or directory`` となります。

.. _writing-tests-for-phpunit.output:

出力内容のテスト
################

メソッドの実行結果を確かめる方法として、(``echo`` や
``print`` などによる)
出力が期待通りのものかを調べたいこともあるでしょう。
``PHPUnit\Framework\TestCase`` クラスは、PHP の
`出力バッファリング <http://www.php.net/manual/ja/ref.outcontrol.php>`_ 機能を使用してこの仕組みを提供します。

:numref:`writing-tests-for-phpunit.output.examples.OutputTest.php`
では、期待する出力内容を ``expectOutputString()``
メソッドで設定する方法を示します。
期待通りの出力が得られなかった場合は、そのテストは失敗という扱いになります。

.. code-block:: php
    :caption: 関数やメソッドの出力内容のテスト
    :name: writing-tests-for-phpunit.output.examples.OutputTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class OutputTest extends TestCase
    {
        public function testExpectFooActualFoo(): void
        {
            $this->expectOutputString('foo');

            print 'foo';
        }

        public function testExpectBarActualBaz(): void
        {
            $this->expectOutputString('bar');

            print 'baz';
        }
    }

.. parsed-literal::

    $ phpunit OutputTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) OutputTest::testExpectBarActualBaz
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

:numref:`writing-tests-for-phpunit.output.tables.api` は、
テストの出力用に提供するメソッドをまとめたものです。

.. rst-class:: table
.. list-table:: テストの出力用のメソッド
    :name: writing-tests-for-phpunit.output.tables.api
    :header-rows: 1

    * - メソッド
      - 意味
    * - ``void expectOutputRegex(string $regularExpression)``
      - 出力が正規表現 ``$regularExpression`` にマッチするであろうという予測を設定します。
    * - ``void expectOutputString(string $expectedString)``
      - 出力が文字列 ``$expectedString`` と等しくなるであろうという予測を設定します。
    * - ``bool setOutputCallback(callable $callback)``
      - たとえば出力時の正規化などに使用するコールバック関数を設定します。
    * - ``string getActualOutput()``
      - 実際の出力を取得します。

.. admonition:: Note

   strict モードでは、出力を発生させるテストは失敗します。

.. _writing-tests-for-phpunit.error-output:

エラー出力
##########

テストが失敗した場合、PHPUnit は、状況を可能な限り詳細に報告します。
これが、何が問題だったのかを調べるのに役立つでしょう。

.. code-block:: php
    :caption: 配列の比較に失敗したときのエラー出力
    :name: writing-tests-for-phpunit.error-output.examples.ArrayDiffTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ArrayDiffTest extends TestCase
    {
        public function testEquality(): void
        {
            $this->assertSame(
                [1, 2,  3, 4, 5, 6],
                [1, 2, 33, 4, 5, 6]
            );
        }
    }

.. parsed-literal::

    $ phpunit ArrayDiffTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayDiffTest::testEquality
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 1
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

この例では配列の要素のうちひとつだけが異なっています。
それ以外の値も表示することで、どこが悪かったのかをわかりやすくしています。

出力が長すぎる場合は PHPUnit が出力を分割し、違っている部分の前後数行だけを出力します。

.. code-block:: php
    :caption: 要素数の多い配列の比較に失敗したときのエラー出力
    :name: writing-tests-for-phpunit.error-output.examples.LongArrayDiffTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class LongArrayDiffTest extends TestCase
    {
        public function testEquality(): void
        {
            $this->assertSame(
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2,  3, 4, 5, 6],
                [0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 2, 33, 4, 5, 6]
            );
        }
    }

.. parsed-literal::

    $ phpunit LongArrayDiffTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LongArrayDiffTest::testEquality
    Failed asserting that two arrays are identical.
    --- Expected
    +++ Actual
    @@ @@
         13 => 2
    -    14 => 3
    +    14 => 33
         15 => 4
         16 => 5
         17 => 6
     )

    /home/sb/LongArrayDiffTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _writing-tests-for-phpunit.error-output.edge-cases:

エッジケース
============

比較に失敗したときに、PHPUnit は入力値をテキスト形式にしてこれを比較します。
この実装が原因で、実際の違う箇所よりも多くの問題を報告してしまうことがあります。

この問題が発生するのは、
``assertEquals()`` などの「緩い」比較の関数を、配列やオブジェクトに対して使った場合だけです。

.. code-block:: php
    :caption: 緩い比較を使った場合の diff の生成のエッジケース
    :name: writing-tests-for-phpunit.error-output.edge-cases.examples.ArrayWeakComparisonTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ArrayWeakComparisonTest extends TestCase
    {
        public function testEquality(): void
        {
            $this->assertEquals(
                [1, 2, 3, 4, 5, 6],
                ['1', 2, 33, 4, 5, 6]
            );
        }
    }

.. parsed-literal::

    $ phpunit ArrayWeakComparisonTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) ArrayWeakComparisonTest::testEquality
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    +    0 => '1'
         1 => 2
    -    2 => 3
    +    2 => 33
         3 => 4
         4 => 5
         5 => 6
     )

    /home/sb/ArrayWeakComparisonTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

この例では、最初のインデックスの
``1`` と ``'1'``
がエラー報告されていますが、 ``assertEquals()`` ではこれらを等しいとみなしているはずです。


