

.. _organizing-tests:

============
テストの構成
============

PHPUnit の目指すところのひとつに
「自由に組み合わせられる」ということがあります。つまり、
例えば「そのプロジェクトのすべてのテストを実行する」「プロジェクトの中の
ある部品を構成するすべてのクラスについて、すべてのテストを実行する」
「特定のひとつのクラスのテストのみを実行する」など、
数や組み合わせにとらわれずに好きなテストを一緒に実行できるということです。

PHPUnit では、さまざまな方法でテストを組み合わせてテストスイートにまとめることができます。
本章では、その中でもよく使われる手法を説明します。

.. _organizing-tests.filesystem:

ファイルシステムを用いたテストスイートの構成
############################################

おそらく、テストスイートをとりまとめるもっとも簡単な方法は
すべてのテストケースのソースファイルを一つのテストディレクトリにまとめることでしょう。
PHPUnit はテストディレクトリを再帰的に探索し、
テストを自動的に見つけて実行します。

`sebastianbergmann/money <http://github.com/sebastianbergmann/money/>`_
ライブラリのテストスイートを見てみましょう。このプロジェクトのディレクトリ構成を見ると、
テストケースクラスが :file:`tests` ディレクトリにまとめられていることがわかります。
その中のディレクトリの構造は、テスト対象のシステム (SUT) がある
:file:`src` ディレクトリ以下の構造と同じになっています。

.. code-block:: none

    src                                 tests
    `-- Currency.php                    `-- CurrencyTest.php
    `-- IntlFormatter.php               `-- IntlFormatterTest.php
    `-- Money.php                       `-- MoneyTest.php
    `-- autoload.php

PHPUnit のコマンドラインテストランナーに
テストディレクトリの場所を指示することで、
このライブラリのすべてのテストを実行できます。

.. parsed-literal::

    $ phpunit --bootstrap src/autoload.php tests
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    .................................

    Time: 636 ms, Memory: 3.50Mb

    OK (33 tests, 52 assertions)

.. admonition:: Note

   PHPUnit のコマンドラインテストランナーでディレクトリを指定すると、
   その中の :file:`*Test.php` ファイルを見つけて実行します。

:file:`tests/CurrencyTest.php` にあるテストケースクラス
``CurrencyTest`` で宣言されているテストだけを実行するには、
次のコマンドを実行します。

.. parsed-literal::

    $ phpunit --bootstrap src/autoload.php tests/CurrencyTest.php
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ........

    Time: 280 ms, Memory: 2.75Mb

    OK (8 tests, 8 assertions)

実行したいテストをより細かく指示するには
``--filter`` オプションを使います。

.. parsed-literal::

    $ phpunit --bootstrap src/autoload.php --filter testObjectCanBeConstructedForValidConstructorArgument tests
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..

    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

.. admonition:: Note

   この方式の欠点は、テストの実行順を制御できないことです。
   そのため、テストの依存性に関する問題を引き起こすことがあります。
   :ref:`writing-tests-for-phpunit.test-dependencies`
   を参照ください。
   次の節では、テストの実行順序を XML 設定ファイルで明示的に指定する方法を説明します。

.. _organizing-tests.xml-configuration:

XML 設定ファイルを用いたテストスイートの構成
############################################

PHPUnit の XML 設定ファイル (:ref:`appendixes.configuration`)
を使ってテストスイートを構成することもできます。
:numref:`organizing-tests.xml-configuration.examples.phpunit.xml`
に、最小限の :file:`phpunit.xml` ファイルを示します。これは、
:file:`tests` ディレクトリを再帰的に探索して
:file:`*Test.php` というファイルにある
``*Test`` クラスをすべて追加する設定です。

.. code-block:: xml
    :caption: XML 設定ファイルを用いたテストスイートの構成
    :name: organizing-tests.xml-configuration.examples.phpunit.xml

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <directory>tests</directory>
        </testsuite>
      </testsuites>
    </phpunit>

特定のテストスイートを実行したい場合は、``--testsuite`` オプションを利用します。

.. parsed-literal::

    $ phpunit --bootstrap src/autoload.php --testsuite money
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..
    Time: 167 ms, Memory: 3.00Mb

    OK (2 test, 2 assertions)

``--configuration`` が設定
*されていない* 場合は、現在の作業ディレクトリから
:file:`phpunit.xml` あるいは
:file:`phpunit.xml.dist` を (この順に) 探し、
見つかった場合はそれを自動的に読み込みます。

また以下のように、テストの実行順序を XML 設定ファイルで明示的に指定することもできます。

.. code-block:: xml
    :caption: XML 設定ファイルを用いたテストスイートの構成
    :name: organizing-tests.xml-configuration.examples.phpunit.xml2

    <phpunit bootstrap="src/autoload.php">
      <testsuites>
        <testsuite name="money">
          <file>tests/IntlFormatterTest.php</file>
          <file>tests/MoneyTest.php</file>
          <file>tests/CurrencyTest.php</file>
        </testsuite>
      </testsuites>
    </phpunit>


