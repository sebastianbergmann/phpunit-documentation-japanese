

.. _risky-tests:

==================
リスクを伴うテスト
==================

PHPUnit は、テストを実行する際に、以下のような追加のチェックをすることができます。

.. _risky-tests.useless-tests:

無意味なテスト
##############

PHPUnit はデフォルトで、何も確かめていないテストを検出します。
このチェックを無効にするには、
:ref:`コマンドライン <textui.clioptions>` オプション
``--dont-report-useless-tests`` を使うか、あるいは PHPUnit の
:ref:`設定ファイル <appendixes.configuration>` で
``beStrictAboutTestsThatDoNotTestAnything="false"`` を設定します。

何もアサーションを実行していないテストは、このチェックを有効にしておくと、
危険であるとマークされます。モックオブジェクトの Expectation はアサーションとみなします。

.. _risky-tests.unintentionally-covered-code:

意図せぬうちにカバーされているコード
####################################

PHPUnit は、意図せずカバーされているコードを検出することができます。
このチェックを有効にするには、
:ref:`コマンドライン <textui.clioptions>` オプション
``--strict-coverage`` を使うか、あるいは PHPUnit の
:ref:`設定ファイル <appendixes.configuration>` で
``beStrictAboutCoversAnnotation="true"``
を設定します。

:ref:`@covers <appendixes.annotations.covers>` アノテーションつきのテストが、
:ref:`@covers <appendixes.annotations.covers>` や :ref:`@uses <appendixes.annotations.uses>` に記されていないコードを実行している場合に、
このチェックを有効にしておくと、危険であるとマークされます。

その上、 PHPUnit の
:ref:`設定ファイル <appendixes.configuration>` で ``forceCoversAnnotation="true"`` を設定しておくと、
:ref:`@covers <appendixes.annotations.covers>` アノテーションが設定されていないテストは危険であるとマークされます。

.. _risky-tests.output-during-test-execution:

テストの実行時の出力
####################

PHPUnit は、テストの最中の出力を検出することができます。
このチェックを有効にするには、
:ref:`コマンドライン <textui.clioptions>` オプション ``--disallow-test-output``
を使うか、あるいは PHPUnit の
:ref:`設定ファイル <appendixes.configuration>` で ``beStrictAboutOutputDuringTests="true"`` を設定します。

テストコードあるいは被テストコードの中で、print
などで何かを出力している場合に、
このチェックを有効にしておくと、危険であるとマークされます。

.. _risky-tests.test-execution-timeout:

テストの実行時のタイムアウト
############################

``PHP_Invoker`` パッケージがインストールされており、
かつ ``pcntl`` 拡張モジュールが利用可能な場合は、
テストの実行時間に制限を設けることができます。 この時間制限を有効にするには、
:ref:`コマンドライン <textui.clioptions>` オプション
``--enforce-time-limit`` を使うか、あるいは PHPUnit の
:ref:`設定ファイル <appendixes.configuration>` で
``beStrictAboutTestSize="true"`` を設定します。

``@large`` とマークされたテストは、
実行時間が 60 秒を超えたら失敗します。
このタイムアウト時間は、
:ref:`設定ファイル <appendixes.configuration>` の ``timeoutForLargeTests`` 属性で変更できます。

``@medium`` とマークされたテストは、
実行時間が 10 秒を超えたら失敗します。
このタイムアウト時間は、
:ref:`設定ファイル <appendixes.configuration>` の ``timeoutForMediumTests`` 属性で変更できます。

``@medium`` とも ``@large`` ともマークされていないテストは、
``@small`` とマークされたものとみなします。
このテストは、実行時間が 1 秒を超えたら失敗します。
このタイムアウト時間は、
:ref:`設定ファイル <appendixes.configuration>` の ``timeoutForSmallTests`` 属性で変更できます。

.. _risky-tests.global-state-manipulation:

グローバルな状態の変更
######################

PHPUnit は、グローバルな状態を変更するテストを厳格にチェックすることができます。
このチェックを有効にするには、
:ref:`コマンドライン <textui.clioptions>` で ``--strict-global-state`` オプションを指定するか、PHPUnit の
:ref:`設定ファイル <appendixes.configuration>` で ``beStrictAboutChangesToGlobalState="true"`` を指定します。
