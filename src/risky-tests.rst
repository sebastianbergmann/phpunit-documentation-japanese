

.. _risky-tests:

=========
リスクを伴うテスト
=========

PHPUnit は、テストを実行する際に、以下のような追加のチェックをすることができます。

.. _risky-tests.useless-tests:

無意味なテスト
#######

PHPUnit はデフォルトで、何も確かめていないテストを検出します。
このチェックを無効にするには、コマンドラインオプション
``--dont-report-useless-tests``
を使うか、あるいは PHPUnit の XML 設定ファイルで
``beStrictAboutTestsThatDoNotTestAnything="false"``
を設定します。

何もアサーションを実行していないテストは、このチェックを有効にしておくと、
危険であるとマークされます。モックオブジェクトでの例外や、
@expectedException などのアノテーションは、アサーションとみなします。

.. _risky-tests.unintentionally-covered-code:

意図せぬうちにカバーされているコード
##################

PHPUnit は、意図せずカバーされているコードを検出することができます。
このチェックを有効にするには、コマンドラインオプション
``--strict-coverage``
を使うか、あるいは PHPUnit の XML 設定ファイルで
``beStrictAboutCoversAnnotation="true"``
を設定します。

@covers アノテーションつきのテストが、
@covers や @uses に記されていないコードを実行している場合に、
このチェックを有効にしておくと、危険であるとマークされます。

.. _risky-tests.output-during-test-execution:

テストの実行時の出力
##########

PHPUnit は、テストの最中の出力を検出することができます。
このチェックを有効にするには、コマンドラインオプション
``--disallow-test-output``
を使うか、あるいは PHPUnit の XML 設定ファイルで
``beStrictAboutOutputDuringTests="true"``
を設定します。

テストコードあるいは被テストコードの中で、print
などで何かを出力している場合に、
このチェックを有効にしておくと、危険であるとマークされます。

.. _risky-tests.test-execution-timeout:

テストの実行時のタイムアウト
##############

``PHP_Invoker`` パッケージがインストールされており、
かつ ``pcntl`` 拡張モジュールが利用可能な場合は、
テストの実行時間に制限を設けることができます。
この時間制限を有効にするには、コマンドラインオプション
``--enforce-time-limit``
を使うか、あるいは PHPUnit の XML 設定ファイルで
``beStrictAboutTestSize="true"``
を設定します。

``@large`` とマークされたテストは、
実行時間が 60 秒を超えたら失敗します。
このタイムアウト時間は、XML 設定ファイルの
``timeoutForLargeTests`` 属性で変更できます。

``@medium`` とマークされたテストは、
実行時間が 10 秒を超えたら失敗します。
このタイムアウト時間は、XML 設定ファイルの
``timeoutForMediumTests`` 属性で変更できます。

``@medium`` とも ``@large`` ともマークされていないテストは、
``@small`` とマークされたものとみなします。
このテストは、実行時間が 1 秒を超えたら失敗します。
このタイムアウト時間は、XML 設定ファイルの
``timeoutForSmallTests`` 属性で変更できます。

.. _risky-tests.global-state-manipulation:

グローバルな状態の変更
###########

PHPUnit は、グローバルな状態を変更するテストを厳格にチェックすることができます。
このチェックを有効にするには、コマンドラインで ``--strict-global-state``
オプションを指定するか、PHPUnit の XML 設定ファイルで
``beStrictAboutChangesToGlobalState="true"``
を指定します。


