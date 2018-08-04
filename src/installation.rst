

.. _installation:

======================
PHPUnit のインストール
======================

.. _installation.requirements:

要件
####

PHPUnit |version| は PHP 7.1 以降のバージョンで動作しますが、最新版の PHP
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
2.5.0 以降と `tokenizer <http://php.net/manual/ja/tokenizer.installation.php>`_
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

.. code-block:: bash

    suhosin.executor.include.whitelist = phar

PHAR をグローバルにインストールするには、次のようにします。

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-|version|.phar
    $  chmod +x phpunit-|version|.phar
    $  sudo mv phpunit-|version|.phar /usr/local/bin/phpunit
    $  phpunit --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

ダウンロードした PHAR ファイルを直接使ってもかまいません。

.. code-block:: bash

    $  wget https://phar.phpunit.de/phpunit-|version|.phar
    $  php phpunit-|version|.phar --version
    PHPUnit x.y.z by Sebastian Bergmann and contributors.

.. _installation.phar.windows:

Windows
=======

PHAR をグローバルにインストールする方法は、
`Composer を Windows に手動でインストールする <https://getcomposer.org/doc/00-intro.md#installation-windows>`_
のと同じ手順です。

#.

   PHP バイナリ用のディレクトリを作ります（例：:file:`C:\\bin`）

#.

   ;C:\bin を、環境変数 ``PATH``
   に追記します
   (`参考資料 <http://stackoverflow.com/questions/6318156/adding-python-path-on-windows-7>`_)。

#.

   `<https://phar.phpunit.de/phpunit-|version|.phar>`_ をダウンロードして、
   :file:`C:\\bin\\phpunit.phar` に保存します。

#.

   コマンドプロンプトを開きます (
   :kbd:`Windows`:kbd:`R`
   » cmd
   » :kbd:`ENTER`)。

#.

   以下のようにして、バッチスクリプト
   (:file:`C:\\bin\\phpunit.cmd`)
   を作ります。

   .. code-block:: bash

       C:\Users\username>  cd C:\bin
       C:\bin>  echo @php "%~dp0phpunit.phar" %* > phpunit.cmd
       C:\bin>  exit

#.

   コマンドプロンプトをもう一枚開き、どこからでも PHPUnit を実行できることを確認します。

   .. code-block:: bash

       C:\Users\username>  phpunit --version
       PHPUnit x.y.z by Sebastian Bergmann and contributors.

Cygwin や MingW32 (TortoiseGit など) のシェル環境で使う場合は、
五番目のステップは飛ばしてもかまいません。単にファイルを
:file:`phpunit`
という名前 (拡張子 :file:`.phar` は不要) で保存して、あとは
chmod 775 phpunit
で実行可能にしておきましょう。

.. _installation.phar.verification:

PHPUnit の PHAR リリースの検証
==============================

PHPUnit プロジェクトが配布する公式リリースにはすべて、
リリースマネージャーによる署名がついています。
検証用の PGP 署名と SHA1 ハッシュは、`phar.phpunit.de <https://phar.phpunit.de/>`_
から取得できます。

リリースの検証をどのように行うのかについて、説明しましょう。まず、
:file:`phpunit.phar` をダウンロードし、さらにその
PGP 署名 :file:`phpunit.phar.asc` もダウンロードします。

.. code-block:: bash

    wget https://phar.phpunit.de/phpunit.phar
    wget https://phar.phpunit.de/phpunit.phar.asc

ダウンロードした PHPUnit の PHP Archive (:file:`phpunit.phar`)
を、署名 (:file:`phpunit.phar.asc`) で検証します。

.. code-block:: bash

    gpg phpunit.phar.asc
    gpg: Signature made Sat 19 Jul 2014 01:28:02 PM CEST using RSA key ID 6372C20A
    gpg: Can't check signature: public key not found

リリースマネージャーの公開鍵 (``6372C20A``)
が、ローカルシステム上に存在しないようです。
検証を進めるには、リリースマネージャーの公開鍵を、鍵サーバーから取得する必要があります。
鍵サーバーには、たとえば :file:`pgp.uni-mainz.de` などがあります。
公開鍵サーバーはお互いリンクしあっているので、どの鍵サーバーを使ってもかまいません。

.. code-block:: bash

    gpg --keyserver pgp.uni-mainz.de --recv-keys 0x4AA394086372C20A
    gpg: requesting key 6372C20A from hkp server pgp.uni-mainz.de
    gpg: key 6372C20A: public key "Sebastian Bergmann <sb@sebastian-bergmann.de>" imported
    gpg: Total number processed: 1
    gpg:               imported: 1  (RSA: 1)

これで、"Sebastian
Bergmann <sb@sebastian-bergmann.de>" さんの公開鍵を取得できました。
ただ、この鍵を作ったのが本当に Sebastian Bergmann という人なのかは、確かめようがありません。
ともあれ、もう一度リリースの署名を検証してみましょう。

.. code-block:: bash

    gpg phpunit.phar.asc
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

PHPUnit のインストールを管理するためのシェルスクリプトを用意しておくのもいいでしょう。
GnuPG の署名を検証してから、テストスイートを実行させるようなものです。たとえば次のようになります。

.. code-block:: bash

    #!/usr/bin/env bash
    clean=1 # Delete phpunit.phar after the tests are complete?
    aftercmd="php phpunit.phar --bootstrap bootstrap.php src/tests"
    gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
    if [ $? -ne 0 ]; then
        echo -e "\033[33mDownloading PGP Public Key...\033[0m"
        gpg --recv-keys D8406D0D82947747293778314AA394086372C20A
        # Sebastian Bergmann <sb@sebastian-bergmann.de>
        gpg --fingerprint D8406D0D82947747293778314AA394086372C20A
        if [ $? -ne 0 ]; then
            echo -e "\033[31mCould not download PGP public key for verification\033[0m"
            exit
        fi
    fi

    if [ "$clean" -eq 1 ]; then
        # Let's clean them up, if they exist
        if [ -f phpunit.phar ]; then
            rm -f phpunit.phar
        fi
        if [ -f phpunit.phar.asc ]; then
            rm -f phpunit.phar.asc
        fi
    fi

    # 最新のリリースとその署名の取得
    if [ ! -f phpunit.phar ]; then
        wget https://phar.phpunit.de/phpunit.phar
    fi
    if [ ! -f phpunit.phar.asc ]; then
        wget https://phar.phpunit.de/phpunit.phar.asc
    fi

    # 実行前の検証
    gpg --verify phpunit.phar.asc phpunit.phar
    if [ $? -eq 0 ]; then
        echo
        echo -e "\033[33mBegin Unit Testing\033[0m"
        # Run the testing suite
        `$after_cmd`
        # Cleanup
        if [ "$clean" -eq 1 ]; then
            echo -e "\033[32mCleaning Up!\033[0m"
            rm -f phpunit.phar
            rm -f phpunit.phar.asc
        fi
    else
        echo
        chmod -x phpunit.phar
        mv phpunit.phar /tmp/bad-phpunit.phar
        mv phpunit.phar.asc /tmp/bad-phpunit.phar.asc
        echo -e "\033[31mSignature did not match! PHPUnit has been moved to /tmp/bad-phpunit.phar\033[0m"
        exit 1
    fi

.. _installation.composer:

Composer
########

`Composer <https://getcomposer.org/>`_
を使ってプロジェクトの依存関係を管理するには、
``phpunit/phpunit`` への (開発時の) 依存情報をプロジェクトの
:file:`composer.json` ファイルに追加します。

.. code-block:: bash

    composer require --dev phpunit/phpunit ^|version|

.. _installation.optional-packages:

オプションのパッケージ
######################

オプションのパッケージとして、これらが使えます。

``PHP_Invoker``

    callable をタイムアウトつきで実行するユーティリティクラス。
    テストのタイムアウトを厳格に指定するために必要なパッケージ。

    このパッケージは、PHPUnit の PHAR 版の中に含まれています。
    Composer でインストールするには、次のコマンドを実行します。

    .. code-block:: bash

        composer require --dev phpunit/php-invoker

``DbUnit``

    DbUnit の PHP/PHPUnit 向けの移植。データベースとのやりとりをテスト可能にする。

    このパッケージは、PHPUnit の PHAR 版の中に含まれていません。
    Composer でインストールするには、次のコマンドを実行します。

    .. code-block:: bash

        composer require --dev phpunit/dbunit


