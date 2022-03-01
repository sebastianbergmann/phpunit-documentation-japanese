

.. _extending-phpunit:

==============
PHPUnit の拡張
==============

テストを書きやすくする、あるいはテストの実行結果の表示方法を変更するなど、
PHPUnit はさまざまな方法で拡張することができます。
PHPUnit を拡張するための第一歩をここで説明します。

.. _extending-phpunit.PHPUnit_Framework_TestCase:

PHPUnit\\Framework\\TestCase のサブクラスの作成
###############################################

``PHPUnit\Framework\TestCase``
を継承した抽象サブクラスにカスタムアサーションやユーティリティメソッドを書き、
そのクラスをさらに継承してテストクラスを作成します。
これが、PHPUnit を拡張するための一番簡単な方法です。

.. _extending-phpunit.custom-assertions:

カスタムアサーションの作成
##########################

カスタムアサーションを作成するときには、PHPUnit 自体のアサーションの実装方法を真似るのがおすすめです。
:numref:`extending-phpunit.examples.Assert.php` を見ればわかるとおり、
``assertTrue()`` メソッドは
``isTrue()`` および ``assertThat()`` メソッドのラッパーです。
``isTrue()`` が matcher オブジェクトを作り、それを
``assertThat()`` に渡して評価しています。

.. code-block:: php
    :caption: PHPUnit\\Framework\\Assert クラスの assertTrue() および isTrue() メソッド
    :name: extending-phpunit.examples.Assert.php

    <?php declare(strict_types=1);
    namespace PHPUnit\Framework;

    use PHPUnit\Framework\Constraint\IsTrue;

    abstract class Assert
    {
        // ...

        public static function assertTrue($condition, string $message = ''): void
        {
            static::assertThat($condition, static::isTrue(), $message);
        }

        // ...

        public static function isTrue(): IsTrue
        {
            return new IsTrue;
        }

        // ...
    }

:numref:`extending-phpunit.examples.IsTrue.php` は、
``PHPUnit\Framework\Constraint\IsTrue`` が
matcher オブジェクト (あるいは制約) のために抽象クラス
``PHPUnit\Framework\Constraint`` を継承している部分です。

.. code-block:: php
    :caption: PHPUnit\\Framework\\Constraint\\IsTrue クラス
    :name: extending-phpunit.examples.IsTrue.php

    <?php declare(strict_types=1);
    namespace PHPUnit\Framework\Constraint;

    use PHPUnit\Framework\Constraint;

    final class IsTrue extends Constraint
    {
        public function toString(): string
        {
            return 'is true';
        }

        protected function matches($other): bool
        {
            return $other === true;
        }
    }

``assertTrue()`` や
``isTrue()`` メソッドの実装を
``PHPUnit\Framework\Constraint\IsTrue`` クラスと同じようにしておけば、
アサーションの評価やタスクの記録 (テストの統計情報に自動的に更新するなど)
を ``assertThat()`` が自動的に行ってくれるようになります。
さらに、モックオブジェクトを設定する際の matcher として ``isTrue()``
メソッドを使えるようにもなります。

.. _extending-phpunit.TestRunner:

Test Runner の拡張
##################

PHPUnit の Test Runner はオブジェクトを登録することで拡張することができます。
登録されるオブジェクトは以下のインターフェースのうち、少なくともひとつを実装する必要があります。

- ``AfterIncompleteTestHook``
- ``AfterLastTestHook``
- ``AfterRiskyTestHook``
- ``AfterSkippedTestHook``
- ``AfterSuccessfulTestHook``
- ``AfterTestErrorHook``
- ``AfterTestFailureHook``
- ``AfterTestWarningHook``
- ``AfterTestHook``
- ``BeforeFirstTestHook``
- ``BeforeTestHook``

これらそれぞれのインターフェースを 「フック」と呼びます。
PHPUnit でテストを実行した際に発生するイベントを表現したものです。

PHPUnit の XML 設定ファイルを使ってエクステンションを組み込む方法については
:ref:`appendixes.configuration.extensions` を参照ください。

:numref:`extending-phpunit.examples.TestRunnerExtension` に、
``BeforeFirstTestHook`` と ``AfterLastTestHook``
を実装したエクステンションの例を示します。

.. code-block:: php
    :caption: Test Runner エクステンションの例
    :name: extending-phpunit.examples.TestRunnerExtension

    <?php declare(strict_types=1);
    namespace Vendor;

    use PHPUnit\Runner\BeforeFirstTestHook;
    use PHPUnit\Runner\AfterLastTestHook;

    final class MyExtension implements BeforeFirstTestHook, AfterLastTestHook
    {
        public function executeBeforeFirstTest(): void
        {
            // 最初のテストの実行前にコールされます
        }

        public function executeAfterLastTest(): void
        {
            // 最後のテストの実行後にコールされます
        }
    }

エクステンションの設定
-------------------------

エクステンションに設定値を受け渡すことで、エクステンション自体の設定が行なえます。
設定値を受け渡すためには、エクステンションのクラスが設定値を受け入れるようにしておく必要があります。

エクステンションのクラスに ``__constructor()`` を追加し、 設定可能とする例を
:numref:`extending-phpunit.examples.TestRunnerConfigurableExtension` に示します:

.. code-block:: php
    :caption: コンストラクタを追加して Test Runner のエクステンションを設定可能とする例
    :name: extending-phpunit.examples.TestRunnerConfigurableExtension

    <?php declare(strict_types=1);
    namespace Vendor;
    use PHPUnit\Runner\BeforeFirstTestHook;
    use PHPUnit\Runner\AfterLastTestHook;
    final class MyConfigurableExtension implements BeforeFirstTestHook, AfterLastTestHook
    {
        protected $config_value_1 = '';
        protected $config_value_2 = 0;
        public function __construct(string $value1 = '', int $value2 = 0)
        {
            $this->config_value_1 = $config_1;
            $this->config_value_2 = $config_2;
        }
        public function executeBeforeFirstTest(): void
        {
            if (strlen($this->config_value_1) {
                echo 'Testing with configuration value: ' . $this->config_value_1;
            }
        }
        public function executeAfterLastTest(): void
        {
            if ($this->config_value_2 > 10) {
                echo 'Second config value is OK!';
            }
        }
    }

XML から TestRunner の拡張の設定値を受け渡す場合は、 ``extensions`` セクションを利用します。
``extensions`` セクションは
:numref:`extending-phpunit.examples.TestRunnerConfigurableExtensionConfig` の例のように、
設定値を記載する必要があります:

.. code-block:: xml
    :caption: TestRunner Extension configuration
    :name: extending-phpunit.examples.TestRunnerConfigurableExtensionConfig

    <extensions>
        <extension class="Vendor\MyUnconfigurableExtension" />
        <extension class="Vendor\MyConfigurableExtension">
            <arguments>
                <string>Hello world!</string>
                <int>15</int>
            </arguments>
        </extension>
    </extensions>

``arguments`` の設定方法の詳細については、
:ref:`appendixes.configuration.extensions.extension.arguments` を参照してください。

注意: 全ての設定値が必ず設定されるとは限りません。 TestRunner の拡張を作成するときは、設定値が設定されていない場合にデフォルト値を利用して動作する、
もしくは自身を無効化するなど、異常が発生しないように取り計らってください。
