

.. _appendixes.configuration:

==========
XML 設定ファイル
==========

.. _appendixes.configuration.phpunit:

PHPUnit
#######

``<phpunit>`` 要素の属性を使って
PHPUnit のコア機能を設定します。

.. code-block:: bash

    <phpunit
             xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
             xsi:noNamespaceSchemaLocation="https://schema.phpunit.de/6.3/phpunit.xsd"
             backupGlobals="true"
             backupStaticAttributes="false"
             <!--bootstrap="/path/to/bootstrap.php"-->
             cacheTokens="false"
             colors="false"
             convertErrorsToExceptions="true"
             convertNoticesToExceptions="true"
             convertWarningsToExceptions="true"
             forceCoversAnnotation="false"
             mapTestClassNameToCoveredClassName="false"
             printerClass="PHPUnit\TextUI\ResultPrinter"
             <!--printerFile="/path/to/ResultPrinter.php"-->
             processIsolation="false"
             stopOnError="false"
             stopOnFailure="false"
             stopOnIncomplete="false"
             stopOnSkipped="false"
             stopOnRisky="false"
             testSuiteLoaderClass="PHPUnit\Runner\StandardTestSuiteLoader"
             <!--testSuiteLoaderFile="/path/to/StandardTestSuiteLoader.php"-->
             timeoutForSmallTests="1"
             timeoutForMediumTests="10"
             timeoutForLargeTests="60"
             verbose="false">
      <!-- ... -->
    </phpunit>

上の XML 設定ファイルは、TextUI テストランナーをデフォルトの設定で起動します。
その詳細は :ref:`textui.clioptions` で説明します。

その他、コマンドラインからは設定できないオプションもあります。

``convertErrorsToExceptions``

    ``false`` にすると、
    すべての PHP のエラーを例外に変換するエラーハンドラをインストールしません。

``convertNoticesToExceptions``

    ``false`` にすると、
    ``convertErrorsToExceptions`` でインストールしたエラーハンドラが
    ``E_NOTICE`` や ``E_USER_NOTICE`` そして
    ``E_STRICT`` を例外に変換しなくなります。

``convertWarningsToExceptions``

    ``false`` にすると、
    ``convertErrorsToExceptions`` でインストールしたエラーハンドラが
    ``E_WARNING`` や ``E_USER_WARNING``
    を例外に変換しなくなります。

``forceCoversAnnotation``

    コードカバレッジの記録を、
    ``@covers`` アノテーションを使っている関数だけに限定します。
    このアノテーションについては
    :ref:`appendixes.annotations.covers` で説明します。

``timeoutForLargeTests``

    テストのサイズによるタイムアウト値を制限する場合に、
    ``@large`` とマークされたすべてのテストのタイムアウトをこの属性で設定します。
    ここで指定した時間内にテストが完了しなかった場合、テストは失敗します。

``timeoutForMediumTests``

    テストのサイズによるタイムアウト値を制限する場合に、
    ``@medium`` とマークされたすべてのテストのタイムアウトをこの属性で設定します。
    ここで指定した時間内にテストが完了しなかった場合、テストは失敗します。

``timeoutForSmallTests``

    テストのサイズによるタイムアウト値を制限する場合に、
    ``@medium`` あるいは ``@large``
    のいずれのマークもついていないすべてのテストのタイムアウトをこの属性で設定します。
    ここで指定した時間内にテストが完了しなかった場合、テストは失敗します。

.. _appendixes.configuration.testsuites:

テストスイート
#######

``<testsuites>`` 要素とその子要素である
``<testsuite>`` を使って、
テストスイート群やテストケース群の中からテストスイートを構成します。

.. code-block:: bash

    <testsuites>
      <testsuite name="My Test Suite">
        <directory>/path/to/*Test.php files</directory>
        <file>/path/to/MyTest.php</file>
        <exclude>/path/to/exclude</exclude>
      </testsuite>
    </testsuites>

``phpVersion`` および
``phpVersionOperator`` 属性を使うと、必要な PHP
のバージョンを指定できます。次の例は、PHP のバージョンが 5.3.0 以降である場合にのみ
:file:`/path/to/\*Test.php` と
:file:`/path/to/MyTest.php` を追加します。

.. code-block:: bash

      <testsuites>
        <testsuite name="My Test Suite">
          <directory suffix="Test.php" phpVersion="5.3.0" phpVersionOperator=">=">/path/to/files</directory>
          <file phpVersion="5.3.0" phpVersionOperator=">=">/path/to/MyTest.php</file>
        </testsuite>
      </testsuites>

``phpVersionOperator`` 属性はオプションで、デフォルトは
``>=`` です。

.. _appendixes.configuration.groups:

グループ
####

``<groups>`` 要素とその子要素である
``<include>``、
``<exclude>`` および
``<group>`` を使って、
``@group`` アノテーション
(:ref:`appendixes.annotations.group` を参照ください)
でマークされたテストグループから実行する (しない) ものを選びます。

.. code-block:: bash

    <groups>
      <include>
        <group>name</group>
      </include>
      <exclude>
        <group>name</group>
      </exclude>
    </groups>

上の XML 設定ファイルは、 TextUI テストランナーを以下の引数で起動します。

-

  ``--group name``

-

  ``--exclude-group name``

.. _appendixes.configuration.whitelisting-files:

コードカバレッジ対象のファイルのホワイトリスト
#######################

``<filter>`` 要素とその子要素を使って、
コードカバレッジレポートのホワイトリストを設定します。

.. code-block:: bash

    <filter>
      <whitelist processUncoveredFilesFromWhitelist="true">
        <directory suffix=".php">/path/to/files</directory>
        <file>/path/to/file</file>
        <exclude>
          <directory suffix=".php">/path/to/files</directory>
          <file>/path/to/file</file>
        </exclude>
      </whitelist>
    </filter>

.. _appendixes.configuration.logging:

ログ出力
####

``<logging>`` 要素とその子要素である
``<log>`` を使って、
テストの実行結果のログ出力を設定します。

.. code-block:: bash

    <logging>
      <log type="coverage-html" target="/tmp/report" lowUpperBound="35"
           highLowerBound="70"/>
      <log type="coverage-clover" target="/tmp/coverage.xml"/>
      <log type="coverage-php" target="/tmp/coverage.serialized"/>
      <log type="coverage-text" target="php://stdout" showUncoveredFiles="false"/>
      <log type="junit" target="/tmp/logfile.xml"/>
      <log type="testdox-html" target="/tmp/testdox.html"/>
      <log type="testdox-text" target="/tmp/testdox.txt"/>
    </logging>

上の XML 設定ファイルは、 TextUI テストランナーを以下の引数で起動します。

-

  ``--coverage-html /tmp/report``

-

  ``--coverage-clover /tmp/coverage.xml``

-

  ``--coverage-php /tmp/coverage.serialized``

-

  ``--coverage-text``

-

  ``> /tmp/logfile.txt``

-

  ``--log-junit /tmp/logfile.xml``

-

  ``--testdox-html /tmp/testdox.html``

-

  ``--testdox-text /tmp/testdox.txt``

``lowUpperBound``、``highLowerBound``
および ``showUncoveredFiles``
属性には、TextUI テストランナーで対応するオプションがありません。

-

  ``lowUpperBound``: カバー率がこの値に満たないときに、カバー率が "低い" とみなします。

-

  ``highLowerBound``: カバー率がこの値を超えるときに、カバー率が "高い" とみなします。

-

  ``showUncoveredFiles``: ``--coverage-text`` の出力で、カバレッジ情報だけではなくホワイトリストの全ファイル一覧も表示します。

-

  ``showOnlySummary``: Show only the summary in ``--coverage-text`` output.

.. _appendixes.configuration.test-listeners:

テストリスナー
#######

``<listeners>`` 要素とその子要素である
``<listener>`` を使って、
テスト実行時にテストリスナーをアタッチします。

.. code-block:: bash

    <listeners>
      <listener class="MyListener" file="/optional/path/to/MyListener.php">
        <arguments>
          <array>
            <element key="0">
              <string>Sebastian</string>
            </element>
          </array>
          <integer>22</integer>
          <string>April</string>
          <double>19.78</double>
          <null/>
          <object class="stdClass"/>
        </arguments>
      </listener>
    </listeners>

上の XML 設定は、
``$listener`` オブジェクト (以下を参照ください)
をテストの実行時にアタッチします。

.. code-block:: bash

    $listener = new MyListener(
        ['Sebastian'],
        22,
        'April',
        19.78,
        null,
        new stdClass
    );

.. _appendixes.configuration.php-ini-constants-variables:

PHP INI 項目や定数、グローバル変数の設定
########################

``<php>`` 要素とその子要素を使って、
PHP の設定や定数、グローバル変数を設定します。また、
``include_path`` の先頭にパスを追加することもできます。

.. code-block:: bash

    <php>
      <includePath>.</includePath>
      <ini name="foo" value="bar"/>
      <const name="foo" value="bar"/>
      <var name="foo" value="bar"/>
      <env name="foo" value="bar"/>
      <post name="foo" value="bar"/>
      <get name="foo" value="bar"/>
      <cookie name="foo" value="bar"/>
      <server name="foo" value="bar"/>
      <files name="foo" value="bar"/>
      <request name="foo" value="bar"/>
    </php>

上の XML 設定は、次の PHP コードに対応します。

.. code-block:: bash

    ini_set('foo', 'bar');
    define('foo', 'bar');
    $GLOBALS['foo'] = 'bar';
    $_ENV['foo'] = 'bar';
    $_POST['foo'] = 'bar';
    $_GET['foo'] = 'bar';
    $_COOKIE['foo'] = 'bar';
    $_SERVER['foo'] = 'bar';
    $_FILES['foo'] = 'bar';
    $_REQUEST['foo'] = 'bar';


