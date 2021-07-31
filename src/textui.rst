

.. _textui:

==============================
コマンドラインのテストランナー
==============================

:file:`phpunit` コマンドを実行すると、PHPUnit
のコマンドライン版テストランナーが起動します。
コマンドラインのテストランナーを使用したテストの様子を以下に示します。

.. parsed-literal::

    $ phpunit ArrayTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    ..

    Time: 0 seconds

    OK (2 tests, 2 assertions)

このように実行すると、PHPUnit のコマンドラインテストランナーは、
まず現在の作業ディレクトリにあるソースファイル
:file:`ArrayTest.php` を探してそれを読み込み、テストケースクラス
``ArrayTest`` を探します。
そして、そのクラス内のテストを実行します。

テストがひとつ実行されるたびに、PHPUnit
コマンドラインツールはその経過を示す文字を出力します。

``.``

    テストが成功した際に表示されます。

``F``

    テストメソッドの実行中、アサーションに失敗した際に表示されます。

``E``

    テストメソッドの実行中、エラーが発生した際に表示されます。

``R``

    テストが危険だとマークされている場合に表示されます
    (:ref:`risky-tests` を参照ください)。

``S``

    テストが飛ばされた場合に表示されます
    (:ref:`incomplete-and-skipped-tests` を参照ください)。

``I``

    テストが「不完全」あるいは「未実装」とマークされている場合に表示されます
    (:ref:`incomplete-and-skipped-tests` を参照ください)。

PHPUnit は、*失敗 (failures)* と
*エラー (errors)* を区別します。
「失敗」は PHPUnit のアサーションに違反した場合、つまり例えば
``assertSame()`` のコールに失敗した場合などで、
「エラー」は予期せぬ例外や PHP のエラーが発生した場合となります。
この区別は、時に有用です。というのは「エラー」は一般的に「失敗」
より修正しやすい傾向があるからです。
もし大量の問題が発生した場合は、まず「エラー」を最初に片付け、
その後で「失敗」を修正していくのが最良の方法です。

.. _textui.clioptions:

コマンドラインオプション
########################

以下のコードで、コマンドライン版テストランナーのオプションの一覧を見てみましょう。

.. parsed-literal::

    $ phpunit --help
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    Usage:
      phpunit [options] UnitTest.php
      phpunit [options] <directory>

    Code Coverage Options:
      --coverage-clover <file>    Generate code coverage report in Clover XML format
      --coverage-crap4j <file>    Generate code coverage report in Crap4J XML format
      --coverage-html <dir>       Generate code coverage report in HTML format
      --coverage-php <file>       Export PHP_CodeCoverage object to file
      --coverage-text=<file>      Generate code coverage report in text format [default: standard output]
      --coverage-xml <dir>        Generate code coverage report in PHPUnit XML format
      --coverage-cache <dir>      Cache static analysis results
      --warm-coverage-cache       Warm static analysis cache
      --coverage-filter <dir>     Include <dir> in code coverage analysis
      --path-coverage             Perform path coverage analysis
      --disable-coverage-ignore   Disable annotations for ignoring code coverage
      --no-coverage               Ignore code coverage configuration

    Logging Options:
      --log-junit <file>          Log test execution in JUnit XML format to file
      --log-teamcity <file>       Log test execution in TeamCity format to file
      --testdox-html <file>       Write agile documentation in HTML format to file
      --testdox-text <file>       Write agile documentation in Text format to file
      --testdox-xml <file>        Write agile documentation in XML format to file
      --reverse-list              Print defects in reverse order
      --no-logging                Ignore logging configuration

    Test Selection Options:
      --filter <pattern>          Filter which tests to run
      --testsuite <name>          Filter which testsuite to run
      --group <name>              Only runs tests from the specified group(s)
      --exclude-group <name>      Exclude tests from the specified group(s)
      --list-groups               List available test groups
      --list-suites               List available test suites
      --list-tests                List available tests
      --list-tests-xml <file>     List available tests in XML format
      --test-suffix <suffixes>    Only search for test in files with specified suffix(es). Default: Test.php,.phpt

    Test Execution Options:
      --dont-report-useless-tests Do not report tests that do not test anything
      --strict-coverage           Be strict about @covers annotation usage
      --strict-global-state       Be strict about changes to global state
      --disallow-test-output      Be strict about output during tests
      --disallow-resource-usage   Be strict about resource usage during small tests
      --enforce-time-limit        Enforce time limit based on test size
      --default-time-limit <sec>  Timeout in seconds for tests without @small, @medium or @large
      --disallow-todo-tests       Disallow @todo-annotated tests

      --process-isolation         Run each test in a separate PHP process
      --globals-backup            Backup and restore $GLOBALS for each test
      --static-backup             Backup and restore static attributes for each test

      --colors <flag>             Use colors in output ("never", "auto" or "always")
      --columns <n>               Number of columns to use for progress output
      --columns max               Use maximum number of columns for progress output
      --stderr                    Write to STDERR instead of STDOUT
      --stop-on-defect            Stop execution upon first not-passed test
      --stop-on-error             Stop execution upon first error
      --stop-on-failure           Stop execution upon first error or failure
      --stop-on-warning           Stop execution upon first warning
      --stop-on-risky             Stop execution upon first risky test
      --stop-on-skipped           Stop execution upon first skipped test
      --stop-on-incomplete        Stop execution upon first incomplete test
      --fail-on-incomplete        Treat incomplete tests as failures
      --fail-on-risky             Treat risky tests as failures
      --fail-on-skipped           Treat skipped tests as failures
      --fail-on-warning           Treat tests with warnings as failures
      -v|--verbose                Output more verbose information
      --debug                     Display debugging information

      --repeat <times>            Runs the test(s) repeatedly
      --teamcity                  Report test execution progress in TeamCity format
      --testdox                   Report test execution progress in TestDox format
      --testdox-group             Only include tests from the specified group(s)
      --testdox-exclude-group     Exclude tests from the specified group(s)
      --no-interaction            Disable TestDox progress animation
      --printer <printer>         TestListener implementation to use

      --order-by <order>          Run tests in order: default|defects|duration|no-depends|random|reverse|size
      --random-order-seed <N>     Use a specific random seed <N> for random order
      --cache-result              Write test results to cache file
      --do-not-cache-result       Do not write test results to cache file

    Configuration Options:
      --prepend <file>            A PHP script that is included as early as possible
      --bootstrap <file>          A PHP script that is included before the tests run
      -c|--configuration <file>   Read configuration from XML file
      --no-configuration          Ignore default configuration file (phpunit.xml)
      --extensions <extensions>   A comma separated list of PHPUnit extensions to load
      --no-extensions             Do not load PHPUnit extensions
      --include-path <path(s)>    Prepend PHP's include_path with given path(s)
      -d <key[=value]>            Sets a php.ini value
      --cache-result-file <file>  Specify result cache path and filename
      --generate-configuration    Generate configuration file with suggested settings
      --migrate-configuration     Migrate configuration file to current format

    Miscellaneous Options:
      -h|--help                   Prints this usage information
      --version                   Prints the version and exits
      --atleast-version <min>     Checks that version is greater than min and exits
      --check-version             Check whether PHPUnit is the latest version

``phpunit UnitTest``

    ``UnitTest`` という名前のクラスで定義されている
    テストを実行します。このクラスは、:file:`UnitTest.php`
    という名前のファイルの中に定義されているものとします。

    ``UnitTest`` は、``PHPUnit\Framework\TestCase``
    を継承したクラスであるか、あるいは ``PHPUnit\Framework\Test``
    オブジェクト、例えば ``PHPUnit\Framework\TestSuite``
    のインスタンスを返す ``public static suite()``
    というメソッドを保持するクラスでなければなりません。

``phpunit UnitTest UnitTest.php``

    ``UnitTest`` という名前のクラスで定義されているテストを実行します。
    このクラスは、指定したファイルの中で定義されているものとします。

``--coverage-clover``

    テスト結果から XML 形式のログファイルを作成し、
    コードカバレッジ情報もそこに含めます。詳細は
    :ref:`code-coverage-analysis` を参照ください。

``--coverage-crap4j``

    コードカバレッジレポートを Crap4j 形式で作成します。詳細は
    :ref:`code-coverage-analysis` を参照ください。

``--coverage-html``

    コードカバレッジレポートを HTML 形式で作成します。詳細は
    :ref:`code-coverage-analysis` を参照ください。

``--coverage-php``

    シリアライズした PHP_CodeCoverage オブジェクトを生成し、
    コードカバレッジ情報もそこに含めます。

``--coverage-text``

    テストを実行したときに、ログファイルあるいはコマンドライン出力で
    可読形式のコードカバレッジ情報を生成します。

``--log-junit``

    JUnit XML フォーマットを使用して、テストの実行結果のログを作成します。

``--testdox-html`` および ``--testdox-text``

    実行したテストについて、HTML あるいはプレーンテキスト形式のドキュメントを生成します
    ( :ref:`textui.testdox` を参照ください)。

``--filter``

    指定した正規表現パターンにマッチする名前のテストのみを実行します。
    パターンがデリミタで囲まれていない場合は、PHPUnit はパターンをデリミタ ``/`` で囲みます。

    マッチするテスト名は、次のいずれかのフォーマットになります。

    ``TestNamespace\TestCaseClass::testMethod``

        デフォルトのテスト名のフォーマットは、テストメソッドの中でマジック定数
        ``__METHOD__`` を使うのと同等です。

    ``TestNamespace\TestCaseClass::testMethod with data set #0``

        テストがデータプロバイダーを持つ場合、データを処理するたびに、
        現在のインデックスをデフォルトのテスト名の後に続けたものを取得します。

    ``TestNamespace\TestCaseClass::testMethod with data set "my named data"``

        テストが持つデータプロバイダーが名前つきセットを使う場合、データを処理するたびに、
        現在の名前をデフォルトのテスト名の後に続けたものを取得します。
        名前つきデータセットの例は
        :numref:`textui.examples.TestCaseClass.php`
        を参照ください。

        .. code-block:: php
            :caption: 名前つきデータセット
            :name: textui.examples.TestCaseClass.php

            <?php
            namespace TestNamespace;

            use PHPUnit\Framework\TestCase;

            class TestCaseClass extends TestCase
            {
                /**
                 * @dataProvider provider
                 */
                public function testMethod($data)
                {
                    $this->assertTrue($data);
                }

                public function provider()
                {
                    return [
                        'my named data' => [true],
                        'my data'       => [true]
                    ];
                }
            }

    ``/path/to/my/test.phpt``

        PHPT のテストのテスト名は、ファイルシステムのパスになります。

    有効なフィルターパターンの例は、:numref:`textui.examples.filter-patterns` を参照ください。

    .. code-block:: shell
        :caption: フィルターパターンの例
        :name: textui.examples.filter-patterns

        --filter 'TestNamespace\\TestCaseClass::testMethod'
        --filter 'TestNamespace\\TestCaseClass'
        --filter TestNamespace
        --filter TestCaseClase
        --filter testMethod
        --filter '/::testMethod .*"my named data"/'
        --filter '/::testMethod .*#5$/'
        --filter '/::testMethod .*#(5|6|7)$/'

    データプロバイダーのマッチングに使えるショートカットは、
    :numref:`textui.examples.filter-shortcuts` を参照ください。

    .. code-block:: shell
        :caption: フィルターのショートカット
        :name: textui.examples.filter-shortcuts

        --filter 'testMethod#2'
        --filter 'testMethod#2-4'
        --filter '#2'
        --filter '#2-4'
        --filter 'testMethod@my named data'
        --filter 'testMethod@my.*data'
        --filter '@my named data'
        --filter '@my.*data'

``--testsuite``

    指定したパターンにマッチする名前のテストスイートのみを実行します。

``--group``

    指定したグループのテストのみを実行します。
    あるテストを特定のグループに所属させるには、
    ``@group`` アノテーションを使用します。

    ``@author`` アノテーションおよび ``@ticket`` アノテーションは
    ``@group`` のエイリアスで、
    それぞれテストの作者およびチケットIDに基づいてテストをフィルタリングします。

``--exclude-group``

    指定したグループをテストの対象外とします。
    あるテストを特定のグループに所属させるには、
    ``@group`` アノテーションを使用します。

``--list-groups``

    使用可能なテストグループの一覧を表示します。

``--test-suffix``

    指定したサフィックスのテストファイルだけを探します。

``--dont-report-useless-tests``

    何もテストをしないテストについて報告しません。
    詳細は :ref:`risky-tests` を参照ください。

``--strict-coverage``

    意図せずカバーしているコードについて厳格にチェックします。
    詳細は :ref:`risky-tests` を参照ください。

``--strict-global-state``

    グローバルな状態の変更について厳格にチェックします。
    詳細は :ref:`risky-tests` を参照ください。

``--disallow-test-output``

    実行中に何かを出力するテストについて厳格にチェックします。
    詳細は :ref:`risky-tests` を参照ください。

``--disallow-todo-tests``

    docblock に ``@todo`` アノテーションが指定されているテストを実行しません。

``--enforce-time-limit``

    テストのサイズに応じて、制限時間を設定します。
    詳細は :ref:`risky-tests` を参照ください。

``--process-isolation``

    各テストを個別の PHP プロセスで実行します。

``--no-globals-backup``

    $GLOBALS のバックアップ・リストアを行いません。
    詳細は :ref:`fixtures.global-state` を参照ください。

``--static-backup``

    ユーザ定義クラスの静的属性のバックアップ・リストアを行います。
    詳細は :ref:`fixtures.global-state` を参照ください。

``--colors``

    出力に色を使用します。
    Windows では、`ANSICON <https://github.com/adoxa/ansicon>`_ あるいは `ConEmu <https://github.com/Maximus5/ConEmu>`_
    を利用します。

    指定できる値は、以下の三つです。

    -

      ``never``: 出力を色分けしません。これは、``--colors`` オプションを省略したときのデフォルトです。

    -

      ``auto``: ターミナルが色をサポートしていない場合や、
      出力をコマンドにパイプしたりファイルにリダイレクトしたりする場合を除き、出力に色を使います。

    -

      ``always``: ターミナルが色をサポートしていない場合や、
      出力をコマンドにパイプしたりファイルにリダイレクトしたりする場合も含めて、常に出力に色を使います。

    ``--colors`` だけを指定して何も値を指定しなかった場合は、``auto`` を選んだものとみなされます。

``--stderr``

    オプションで、出力先を
    ``STDOUT`` ではなく ``STDERR`` にします。

``--stop-on-error``

    最初にエラーが発生した時点で実行を停止します。

``--stop-on-failure``

    最初にエラーあるいは失敗が発生した時点で実行を停止します。

``--stop-on-risky``

    最初に危険なテストがあらわれた時点で実行を停止します。

``--stop-on-skipped``

    最初にテストのスキップが発生した時点で実行を停止します。

``--stop-on-incomplete``

    最初に不完全なテストがあらわれた時点で実行を停止します。

``--verbose``

    より詳細な情報を出力します。例えば、
    未完成のテストや省略したテストの名前が表示されます。

``--debug``

    テスト名などのデバッグ情報を、テストの実行開始時に出力します。

``--loader``

    ``PHPUnit\Runner\TestSuiteLoader`` を実装したクラスのうち、
    実際に使用するものを指定します。

    標準のテストスイートローダーは、現在の作業ディレクトリおよび PHP
    の設定項目 ``include_path``
    で指定されているディレクトリからソースファイルを探します。
    ``Project_Package_Class``
    クラスがソースファイル :file:`Project/Package/Class.php`
    に対応します。

``--repeat``

    指定された回数だけ、繰り返しテストを実行します。

``--testdox``

    テストの進行状況を、アジャイルな文書として報告します。
    ( :ref:`textui.testdox`) を参照ください。

``--printer``

    結果を表示するために使うプリンタクラスを指定します。このプリンタクラスは
    ``PHPUnit\Util\Printer`` を継承し、かつ
    ``PHPUnit\Framework\TestListener``
    インターフェイスを実装したものでなければなりません。

``--bootstrap``

    テストの前に実行される "ブートストラップ" PHP ファイルを指定します。

``--configuration``, ``-c``

    設定を XML ファイルから読み込みます。
    詳細は :ref:`appendixes.configuration` を参照ください。

    :file:`phpunit.xml` あるいは
    :file:`phpunit.xml.dist` (この順番で使用します)
    が現在の作業ディレクトリに存在しており、かつ ``--configuration``
    が使われて *いない* 場合、設定が自動的にそのファイルから読み込まれます。

    指定されているのがディレクトリで、
    :file:`phpunit.xml` あるいは
    :file:`phpunit.xml.dist` (この順番で使用します)
    がそのディレクトリ内に存在する場合、設定が自動的にそのファイルから読み込まれます。

``--no-configuration``

    現在の作業ディレクトリにある :file:`phpunit.xml` および
    :file:`phpunit.xml.dist` を無視します。

``--include-path``

    PHP の ``include_path`` の先頭に、指定したパスを追加します。

``-d``

    指定した PHP 設定オプションの値を設定します。

.. admonition:: Note

   これらのオプションは、引数の後に指定することもできます。

.. _textui.testdox:

TestDox
#######

PHPUnit の TestDox 機能は、テストクラス内のすべてのテストメソッドの名前を抽出し、
それを PHP 風のキャメルケースから通常の文に変換します。つまり
``testBalanceIsInitiallyZero()`` が "Balance is initially zero"
のようになるわけです。最後のほうの数字のみが違うメソッド、例えば
``testBalanceCannotBecomeNegative()`` と
``testBalanceCannotBecomeNegative2()`` のようなものが存在した場合は、
文 "Balance cannot become negative" は一度のみ表示され、
全てのテストが成功したことを表します。

``BankAccount`` クラスのアジャイルな文書を見てみましょう。

.. parsed-literal::

    $ phpunit --testdox BankAccountTest.php
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    BankAccount
     ✔ Balance is initially zero
     ✔ Balance cannot become negative

また、アジャイルな文書を HTML あるいはプレーンテキスト形式で作成してファイルに書き出すこともできます。
この場合は、引数 ``--testdox-html``
あるいは ``--testdox-text`` を使用します。

アジャイルな文書は、プロジェクト内であなたが作成しようとしている外部パッケージについて、
このように動作するであるという期待をまとめた文書にもなります。
外部のパッケージを使用するときには、
そのパッケージが期待通りに動作しなくなるというリスクに常にさらされています。
パッケージのバージョンアップにより知らないうちに挙動が変わってしまい、
あなたのコードが動作しなくなる可能性もあります。そのようなことを避けるため、
「このパッケージはこのように動作するはず」
ということを常にテストケースで記述しておくようにします。テストが成功すれば、
期待通りに動作していることがわかります。もし動作仕様をすべてテストで記述できているのなら、
外部パッケージが将来バージョンアップされたとしても何の心配もいりません。
テストをクリアしたということは、システムは期待通りに動作するということだからです。
