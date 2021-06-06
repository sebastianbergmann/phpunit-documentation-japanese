

.. _installation:

======================
PHPUnit のインストール
======================

.. _installation.requirements:

要件
####

PHPUnit |version| は PHP 7.3 以降のバージョンで動作しますが、最新版の PHP
を使うことを強く推奨します。

PHPUnit を使うには、拡張モジュール `dom <http://php.net/manual/ja/dom.setup.php>`_、`json <http://php.net/manual/ja/json.installation.php>`_、
が必要です。これらは通常、デフォルトで有効になっています。

PHPUnit また、拡張モジュール
`pcre <http://php.net/manual/ja/pcre.installation.php>`_、
`reflection <http://php.net/manual/ja/reflection.installation.php>`_、
そして `spl <http://php.net/manual/ja/spl.installation.php>`_
も必要です。これらは標準の拡張モジュールとしてデフォルトで有効になっており、
PHP のビルドシステムやソースファイルに手を加えない限り、
無効にすることはできません。

コードカバレッジをサポートするには `Xdebug <http://xdebug.org/>`_
2.7.0 以降と `tokenizer <http://php.net/manual/ja/tokenizer.installation.php>`_
拡張モジュールが必要です。
XML 形式で情報を出力するには、`xmlwriter <http://php.net/manual/ja/xmlwriter.installation.php>`_
拡張モジュールも必要です。

.. _installation.phar:

PHP Archive (PHAR)
##################

PHPUnit を入手する一番簡単な方法は、`PHP Archive (PHAR) <http://php.net/phar>`_ をダウンロードすることです。
必要な依存コンポーネントがすべて (オプションのコンポーネントの一部も含めて)
ひとつのファイルにまとめられています。

PHP Archives (PHAR) を利用するには、
`phar <http://php.net/manual/en/phar.installation.php>`_
拡張モジュールが必要です。

PHAR の ``--self-update`` 機能を使うには、
`openssl <http://php.net/manual/en/openssl.installation.php>`_
拡張モジュールが必要です。

`Suhosin <http://suhosin.org/>`_ 拡張モジュールが有効になっている場合は、
``php.ini`` で PHAR の実行を許可する必要があります。

.. parsed-literal::

    suhosin.executor.include.whitelist = phar

PHPUnit の PHAR は、ダウンロードするだけですぐに使えます。

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

PHAR ファイルに実行可能属性をつけておくのが一般的です。

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ chmod +x phpunit-|version|.phar
    $ ./phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.verification:

PHPUnit の PHAR リリースの検証
==============================

PHPUnit プロジェクトが配布する公式リリースにはすべて、
リリースマネージャーによる署名がついています。
検証用の PGP 署名と SHA256 ハッシュは、`phar.phpunit.de <https://phar.phpunit.de/>`_
から取得できます。

リリースの検証をどのように行うのかについて、説明しましょう。まず、
:file:`phpunit.phar` をダウンロードし、さらにその
PGP 署名 :file:`phpunit.phar.asc` もダウンロードします。

.. parsed-literal::

    $ wget https://phar.phpunit.de/phpunit-|version|.phar
    $ wget https://phar.phpunit.de/phpunit-|version|.phar.asc

ダウンロードした PHPUnit の PHP Archive (:file:`phpunit-x.y.phar`)
を、署名 (:file:`phpunit-x.y.phar.asc`) で検証します。

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

リリースマネージャーの公開鍵 (``6372C20A``)
が、ローカルシステム上に存在しないようです。
検証を進めるには、リリースマネージャーの公開鍵を、鍵サーバーから取得する必要があります。
鍵サーバーには、たとえば :file:`pgp.uni-mainz.de` などがあります。
公開鍵サーバーはお互いリンクしあっているので、どの鍵サーバーを使ってもかまいません。

.. parsed-literal::

    $ curl --silent https://sebastian-bergmann.de/gpg.asc | gpg --import
    gpg: key 4AA394086372C20A: 452 signatures not checked due to missing keys
    gpg: /root/.gnupg/trustdb.gpg: trustdb created
    gpg: key 4AA394086372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1
    gpg: no ultimately trusted keys found

これで、"Sebastian
Bergmann <sb@sebastian-bergmann.de>" さんの公開鍵を取得できました。
ただ、この鍵を作ったのが本当に Sebastian Bergmann という人なのかは、確かめようがありません。
ともあれ、もう一度リリースの署名を検証してみましょう。

.. parsed-literal::

    $ gpg phpunit-|version|.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Good signature from "Sebastian Bergmann <sb@sebastian-bergmann.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian@php.net>"
    gpg:                 aka "Sebastian Bergmann <sebastian@thephp.cc>"
    gpg:                 aka "Sebastian Bergmann <sebastian@phpunit.de>"
    gpg:                 aka "Sebastian Bergmann <sebastian.bergmann@thephp.cc>"
    gpg:                 aka "[jpeg image of size 40635]"
    gpg: WARNING: This key is not certified with a trusted signature!
    gpg:          There is no indication that the signature belongs to the owner.
    Primary key fingerprint: D840 6D0D 8294 7747 2937  7831 4AA3 9408 6372 C20A

とりあえず、署名が正しいことはわかりました。
ただ、この署名が信頼できるものであるかどうかは、まだわかりません。
ここで言う「署名が正しい」とは、リリースのファイルが改ざんされていないということです。
しかし、公開鍵暗号方式の性質上、これだけでは不十分です。
``6372C20A`` を作ったのが
Sebastian Bergmann 本人であることを、確かめる必要があります。

公開鍵を作って公開鍵サーバーにアップロードするのは、誰にだってできることです。
当然、悪意のある攻撃者にも可能なことです。
攻撃者は、このニセの鍵を使って署名した、悪意のあるリリースを作ることもできます。
このリリース (そして署名) をダウンロードして検証すると、成功するでしょう。
なぜならその公開鍵は、悪意のある攻撃者が作ったニセの鍵だからです。
こういったことを防ぐために、鍵の作者も検証しなければいけないのです。
公開鍵の作者を検証する方法については、このマニュアルの範囲を超えるので、割愛します。

GPGを使っていちいち署名を検証したり PHPUnit の PHAR を検証したりするのはつまらない作業です。
そこで作られたのが PHIVE で、これは PHAR のインストールや検証状況を管理するためのツールです。
詳しくは `ウェブサイト <https://phar.io/>`_ をご覧ください。

.. _installation.composer:

Composer
########

`Composer <https://getcomposer.org/>`_
を使ってプロジェクトの依存関係を管理するには、
``phpunit/phpunit`` への (開発時の) 依存情報をプロジェクトの
:file:`composer.json` ファイルに追加します。

.. parsed-literal::

    composer require --dev phpunit/phpunit ^\ |version|

.. _installation.global:

グローバルなインストール
##########################

PHPUnitをグローバルに（たとえば ``/usr/bin/phpunit`` や
``/usr/local/bin/phpunit`` などとして）インストールすることはおすすめできません。

PHPUnitはプロジェクト単位でローカルな依存として管理すべきです。

PHPUnitの特定のバージョンのPHARをプロジェクトの ``tools`` ディレクトリに置く（そしてPHIVEで管理する）か、
Composerを使っているならそのプロジェクトで必要とするPHPUnitのバージョンを ``composer.json`` に書きましょう。

Webserver
#########

PHPUnitはテストを実行するためのフレームワークであり、また、コマンドラインツールです。テストのコーディングおよび実行は開発時に行うものであるため、ウェブサーバにPHPUnitをインストールする理由は全くありません。

**ウェブサーバにPHPUnitをインストールすると、そのデプロイは壊れた状態となります。より一般的な話をすると、** ``vendor`` **ディレクトリをウェブサーバ上で公開したときにも同様にそのデプロイは壊れた状態となります。**

PHPUnitをウェブサーバにアップロードすることは良くないことであると認識してください。`警告はしましたからね。 <https://thephp.cc/news/2020/02/phpunit-a-security-risk>`_
