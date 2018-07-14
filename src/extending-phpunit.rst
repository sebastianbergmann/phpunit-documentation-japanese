

.. _extending-phpunit:

===========
PHPUnit の拡張
===========

テストを書きやすくする、あるいはテストの実行結果の表示方法を変更するなど、
PHPUnit はさまざまな方法で拡張することができます。
PHPUnit を拡張するための第一歩をここで説明します。

.. _extending-phpunit.PHPUnit_Framework_TestCase:

PHPUnit\\Framework\\TestCase のサブクラスの作成
######################################

``PHPUnit\Framework\TestCase``
を継承した抽象サブクラスにカスタムアサーションやユーティリティメソッドを書き、
そのクラスをさらに継承してテストクラスを作成します。
これが、PHPUnit を拡張するための一番簡単な方法です。

.. _extending-phpunit.custom-assertions:

カスタムアサーションの作成
#############

カスタムアサーションを作成するときには、PHPUnit 自体のアサーションの実装方法を真似るのがおすすめです。
:numref:`extending-phpunit.examples.Assert.php` を見ればわかるとおり、
``assertTrue()`` メソッドは
``isTrue()`` および ``assertThat()`` メソッドの単なるラッパーに過ぎません。
``isTrue()`` が matcher オブジェクトを作り、それを
``assertThat()`` に渡して評価しています。

.. code-block:: php
    :caption: PHPUnit\Framework\Assert クラスの assertTrue() および isTrue() メソッド
    :name: extending-phpunit.examples.Assert.php

    <?php
    namespace PHPUnit\Framework;

    use PHPUnit\Framework\TestCase;

    abstract class Assert
    {
        // ...

        /**
         * Asserts that a condition is true.
         *
         * @param  boolean $condition
         * @param  string  $message
         * @throws PHPUnit\Framework\AssertionFailedError
         */
        public static function assertTrue($condition, $message = '')
        {
            self::assertThat($condition, self::isTrue(), $message);
        }

        // ...

        /**
         * Returns a PHPUnit\Framework\Constraint\IsTrue matcher object.
         *
         * @return PHPUnit\Framework\Constraint\IsTrue
         * @since  Method available since Release 3.3.0
         */
        public static function isTrue()
        {
            return new PHPUnit\Framework\Constraint\IsTrue;
        }

        // ...
    }?>

:numref:`extending-phpunit.examples.IsTrue.php` は、
``PHPUnit\Framework\Constraint\IsTrue`` が
matcher オブジェクト (あるいは制約) のために抽象クラス
``PHPUnit\Framework\Constraint`` を継承している部分です。

.. code-block:: php
    :caption: PHPUnit\Framework\Constraint\IsTrue クラス
    :name: extending-phpunit.examples.IsTrue.php

    <?php
    namespace PHPUnit\Framework\Constraint;

    use PHPUnit\Framework\Constraint;

    class IsTrue extends Constraint
    {
        /**
         * Evaluates the constraint for parameter $other. Returns true if the
         * constraint is met, false otherwise.
         *
         * @param mixed $other Value or object to evaluate.
         * @return bool
         */
        public function matches($other)
        {
            return $other === true;
        }

        /**
         * Returns a string representation of the constraint.
         *
         * @return string
         */
        public function toString()
        {
            return 'is true';
        }
    }?>

``assertTrue()`` や
``isTrue()`` メソッドの実装を
``PHPUnit\Framework\Constraint\IsTrue`` クラスと同じようにしておけば、
アサーションの評価やタスクの記録 (テストの統計情報に自動的に更新するなど)
を ``assertThat()`` が自動的に行ってくれるようになります。
さらに、モックオブジェクトを設定する際の matcher として ``isTrue()``
メソッドを使えるようにもなります。

.. _extending-phpunit.PHPUnit_Framework_TestListener:

PHPUnit\\Framework\\TestListener の実装
####################################

:numref:`extending-phpunit.examples.SimpleTestListener.php` は、
``PHPUnit\Framework\TestListener``
インターフェイスのシンプルな実装例です。

.. code-block:: php
    :caption: シンプルなテストリスナー
    :name: extending-phpunit.examples.SimpleTestListener.php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\Framework\TestListener;

    class SimpleTestListener implements TestListener
    {
        public function addError(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("テスト '%s' の実行中にエラーが発生\n", $test->getName());
        }

        public function addFailure(PHPUnit\Framework\Test $test, PHPUnit\Framework\AssertionFailedError $e, $time)
        {
            printf("テスト '%s' に失敗\n", $test->getName());
        }

        public function addIncompleteTest(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("テスト '%s' は未完成\n", $test->getName());
        }

        public function addRiskyTest(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("テスト '%s' は危険\n", $test->getName());
        }

        public function addSkippedTest(PHPUnit\Framework\Test $test, Exception $e, $time)
        {
            printf("テスト '%s' をスキップ\n", $test->getName());
        }

        public function startTest(PHPUnit\Framework\Test $test)
        {
            printf("テスト '%s' が開始\n", $test->getName());
        }

        public function endTest(PHPUnit\Framework\Test $test, $time)
        {
            printf("テスト '%s' が終了\n", $test->getName());
        }

        public function startTestSuite(PHPUnit\Framework\TestSuite $suite)
        {
            printf("テストスイート '%s' が開始\n", $suite->getName());
        }

        public function endTestSuite(PHPUnit\Framework\TestSuite $suite)
        {
            printf("テストスイート '%s' が終了\n", $suite->getName());
        }
    }
    ?>

:numref:`extending-phpunit.examples.BaseTestListener.php`
は、抽象クラス ``PHPUnit\Framework\BaseTestListener``
のサブクラスを作る例です。これは、インターフェイスのメソッドのうち実際に使うものだけを指定し、
他のメソッドについては空の実装を提供します。

.. code-block:: php
    :caption: ベーステストリスナーの利用法
    :name: extending-phpunit.examples.BaseTestListener.php

    <?php
    use PHPUnit\Framework\BaseTestListener;

    class ShortTestListener extends BaseTestListener
    {
        public function endTest(PHPUnit\Framework\Test $test, $time)
        {
            printf("テスト '%s' が終了\n", $test->getName());
        }
    }
    ?>

:ref:`appendixes.configuration.test-listeners`
に、自作のテストリスナーをテスト実行時にアタッチするための
PHPUnit の設定方法についての説明があります。

.. _extending-phpunit.PHPUnit_Framework_Test:

PHPUnit\Framework\Test の実装
##########################

``PHPUnit\Framework\Test`` インターフェイスの機能は限られており、
実装するのは簡単です。``PHPUnit\Framework\Test``
を実装するのは ``PHPUnit\Framework\TestCase`` の実装より単純で、
これを用いて例えば *データ駆動のテスト (data-driven tests)*
などを実行します。

カンマ区切り (CSV) ファイルの値と比較する、データ駆動のテストを
:numref:`extending-phpunit.examples.DataDrivenTest.php`
に示します。このファイルの各行は ``foo;bar``
のような形式になっており (訳注: CSV じゃない……)、
最初の値が期待値で 2 番目の値が実際の値です。

.. code-block:: php
    :caption: データ駆動のテスト
    :name: extending-phpunit.examples.DataDrivenTest.php

    <?php
    use PHPUnit\Framework\TestCase;

    class DataDrivenTest implements PHPUnit\Framework\Test
    {
        private $lines;

        public function __construct($dataFile)
        {
            $this->lines = file($dataFile);
        }

        public function count()
        {
            return 1;
        }

        public function run(PHPUnit\Framework\TestResult $result = null)
        {
            if ($result === null) {
                $result = new PHPUnit\Framework\TestResult;
            }

            foreach ($this->lines as $line) {
                $result->startTest($this);
                PHP_Timer::start();
                $stopTime = null;

                list($expected, $actual) = explode(';', $line);

                try {
                    PHPUnit\Framework\Assert::assertEquals(
                      trim($expected), trim($actual)
                    );
                }

                catch (PHPUnit\Framework\AssertionFailedError $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addFailure($this, $e, $stopTime);
                }

                catch (Exception $e) {
                    $stopTime = PHP_Timer::stop();
                    $result->addError($this, $e, $stopTime);
                }

                if ($stopTime === null) {
                    $stopTime = PHP_Timer::stop();
                }

                $result->endTest($this, $stopTime);
            }

            return $result;
        }
    }

    $test = new DataDrivenTest('data_file.csv');
    $result = PHPUnit\TextUI\TestRunner::run($test);
    ?>

.. code-block:: bash

    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds

    There was 1 failure:

    1) DataDrivenTest
    Failed asserting that two strings are equal.
    expected string <bar>
    difference      <  x>
    got string      <baz>
    /home/sb/DataDrivenTest.php:32
    /home/sb/DataDrivenTest.php:53

    FAILURES!
    Tests: 2, Failures: 1.


