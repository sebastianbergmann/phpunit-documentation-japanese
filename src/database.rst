

.. _database:

==========
データベースのテスト
==========

初級者・中級者向けのユニットテストのサンプルは、
どんな言語を対象としたものであっても、
テストしやすいようなロジックに対してシンプルなテストをしているものばかりです。
データベースを扱う一般的なアプリケーションを考えると、これはまったく現実離れしています。
たとえば WordPress や TYPO3、あるいは Symfony で Doctrine や Propel などを使い始めるとすぐに、
PHPUnit でのテストがやりづらいことを実感するはずです。
データベースとこれらのライブラリが密結合になっているからです。

.. admonition:: Note

   PHP の ``pdo`` 拡張モジュール、そしてデータベースごとの拡張モジュール
   (``pdo_mysql`` など) がインストールされていることを確認しておきましょう。
   これらがインストールされていないと、以下のサンプルは動きません。

きっと日々の業務やプロジェクトでも身に覚えがあることでしょう。
自分の持つ PHPUnit に関する知識を駆使して作業を進めようとしたのに、
こんな問題のせいで行き詰ってしまうことが。

#.

   テストしたいメソッドがかなり大きめの JOIN 操作を実行し、
   データを使って重要な結果を算出している。

#.

   ひとつのビジネスロジックの中で SELECT、INSERT、UPDATE そして DELETE
   を組み合わせて実行している。

#.

   ふたつ以上の (おそらくもっと多い) テーブルから初期データを準備しないと
   そのメソッドのテストができない。

DBUnit 拡張を使うと、テスト用のデータベースのセットアップを単純化でき、
データベース操作後の内容の検証もすることができます。

.. _database.supported-vendors-for-database-testing:

データベースのテストに対応しているベンダー
#####################

DBUnit が現在サポートしているのは、MySQL および
PostgreSQL、Oracle、SQLite です。
`Zend Framework <http://framework.zend.com>`_ や
`Doctrine 2 <http://www.doctrine-project.org>`_
を使うと、IBM DB2 や Microsoft SQL Server のような他のデータベースにもアクセスできます。

.. _database.difficulties-in-database-testing:

データベースのテストの難しさ
##############

ウェブ上にあるユニットテストのサンプルの中にデータベースを扱うものが全く見当たらない理由はなぜか。
それは、データベースを扱うテストは準備するのも保守するのもたいへんだからです。
データベースを使うテストをするには、このようなことに気をつける必要があります。

-

  データベースのスキーマやテーブル

-

  テーブルへの、テストで必要となるレコードの追加

-

  テスト実行後のデータベースの状態の検証

-

  テスト実行ごとのデータベースの後始末

PDO や MySQLi あるいは OCI8 といったデータベース API はどれも使いにくい上に、
こういった処理を自分で書こうとすると長ったらしくなってしまって面倒です。

テストコードはできる限り簡潔に、そして明確に書かねばなりません。その理由は次のとおりです。

-

  製品コードにちょっと手を加えるたびに大量のテストコードを変更する羽目になるのは困る。

-

  数ヵ月後に改めて読み直したときにも
  読みやすく理解しやすいテストコードであってほしい。

さらに知っておく必要があることは、
データベースは基本的に、自分のコードへのグローバルな入力変数であるということです。
テストスイート内にあるふたつのテストを同じデータベースに対して実行すると、
おそらくデータを複数回再利用することになります。あるテストが失敗すると
それ以降のテストの結果にも影響を及ぼしやすく、テストを進めるのが非常に難しくなります。
先ほど箇条書きでまとめた中の「後始末」こそが、この
“データベースがグローバルな入力になる“
問題を解決するために重要です。

DbUnit を使うと、
データベースのテストにおけるこれらの問題をシンプルにする助けになります。

PHPUnit では助けようにもどうにもならないことが、
データベースのテストはデータベースを使わないものに比べてとても遅くなるという事実です。
テストの実行時間がどれくらいになるかはデータベースとのやりとりの量に依存しますが、
各テストで使うデータの量を少なめにしておいて
可能な限りはデータベースを使わないテストで済ませるようにすれば、
巨大なテストスイートであっても 1 分未満で実行させるのは容易です。

`Doctrine 2 プロジェクト <http://www.doctrine-project.org>`_ がよい例です。
このプロジェクトのテストスイートには現時点で約 1000 件のテストが含まれています。
そのほぼ半数がデータベースを扱うテストですが、
標準的なデスクトップコンピューター上の MySQL
を使ってテストスイートを実行しても 15 秒程度でテストが完了します。

.. _database.the-four-stages-of-a-database-test:

データベーステストの四段階
#############

Gerard Meszaros は、著書 xUnit Test Patterns でユニットテストを次の四段階に分類しています。

#.

   フィクスチャのセットアップ (Setup)

#.

   テストしたいシステムの実行 (Exercise)

#.

   結果の検証 (Verify)

#.

   後始末 (Teardown)

    *フィクスチャとは?*

    フィクスチャとは、アプリケーションやデータベースの初期状態のことです。
    テストを実行する前に用意します。

データベースをテストするには、少なくとも setup と teardown
のときにはテーブルに接続してフィクスチャのクリーンアップや書き込みをしなければなりません。
しかし、データベース拡張には、
データベーステストの四段階を次のようなワークフローに振り向ける十分な理由があります。
このフローは、個々のテストに対して実行します。

.. _database.clean-up-database:

1. データベースのクリーンアップ
=================

データベースを扱う最初のテストというのはいつでも存在します。
実際のところ、そのときテーブルにデータが存在するのかどうかはわかりません。
PHPUnit は指定した全テーブルに対して TRUNCATE を実行し、
テーブルの中身を空にします。

.. _database.set-up-fixture:

2. フィクスチャの準備
============

その後、PHPUnit はフィクスチャの各行を順次処理し、対応するテーブルに書き込みます。

.. _database.run-test-verify-outcome-and-teardown:

3–5. テストの実行、結果の検証、そして後始末
========================

データベースをリセットして初期状態を読み込んだら、
実際のテストを PHPUnit が実行します。
テストコードのこの部分は Database Extension の存在を知っている必要はなく、
コードに対してなんでもお好みのテストをすることができます。

テストの中で ``assertDataSetsEqual()``
という特殊なアサーションを使って検証しているかもしれません。
しかし、この機能は完全なオプションです。
この機能は “データベースアサーション“ で説明します。

.. _database.configuration-of-a-phpunit-database-testcase:

PHPUnit のデータベーステストケースの設定
########################

通常、PHPUnit を使うテストケースでは
``PHPUnit\Framework\TestCase``
クラスを継承してこのようにします。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;

    class MyTest extends TestCase
    {
        public function testCalculate()
        {
            $this->assertSame(2, 1 + 1);
        }
    }

テストコードで Database Extension を使う場合は少しだけ複雑になり、
別の抽象テストケースを継承しなければなりません。そして、二つの抽象メソッド
``getConnection()`` と
``getDataSet()`` を実装します。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit\Extensions\Database\DB\IDatabaseConnection
         */
        public function getConnection()
        {
            $pdo = new PDO('sqlite::memory:');
            return $this->createDefaultDBConnection($pdo, ':memory:');
        }

        /**
         * @return PHPUnit\Extensions\Database\DataSet\IDataSet
         */
        public function getDataSet()
        {
            return $this->createFlatXMLDataSet(dirname(__FILE__).'/_files/guestbook-seed.xml');
        }
    }

.. _database.implementing-getconnection:

getConnection() の実装
===================

クリーンアップとフィクスチャの読み込みの機能を動かすには、
PHPUnit Database Extension からデータベース接続にアクセスできなければなりません。
データベース接続の抽象化には PDO ライブラリを使います。
重要なのは、PHPUnit のデータベース拡張を使うためだけに
わざわざアプリケーションを PDO ベースにする必要はないということです。
この接続を使うのは、単にクリーンアップとフィクスチャの準備のためだけです。

先ほどの例では、インメモリの SQLite 接続を作って
``createDefaultDBConnection`` メソッドに渡しました。
このメソッドは PDO のインスタンスをラップしたもので、二番目のパラメータ
(データベース名) に非常にシンプルなデータベース接続の抽象化レイヤーを渡します。このパラメータの型は
``PHPUnit\DbUnit\Database\Connection`` です。

“データベース接続APIの使い方“で、このインターフェイスの API
と、その活用法について説明します。

.. _database.implementing-getdataset:

getDataSet() の実装
================

``getDataSet()`` メソッドで定義するのは、
個々のテストを実行する前のデータベースの初期状態がどうあるべきかということです。
データベースの状態の抽象化は DataSet と DataTable
という概念を使って行い、これらをそれぞれ
``PHPUnit\DbUnit\DataSet\IDataSet`` および
``PHPUnit\DbUnit\DataSet\IDataTable``
というインターフェイスで表します。次の節でこれらの概念を詳しく説明し、
これをデータベースのテストに使うと何がうれしいのかについても示します。

実装するために最低限知っておくべきことは、
``getDataSet()`` メソッドがコールされるのが
``setUp()`` の中で一度だけであり、
ここでフィクスチャのデータセットを取得してデータベースに挿入するということです。
先ほどの例では、ファクトリメソッド
``createFlatXMLDataSet($filename)``
を使って XML 形式のデータセットを表しました。

.. _database.what-about-the-database-schema-ddl:

データベーススキーマ (DDL) とは?
====================

PHPUnit は、テストの実行前にデータベーススキーマ
(すべてのテーブル、トリガー、シーケンス、ビューを含むもの)
ができあがっていることを想定しています。つまり開発者としては、
テストスイートを実行する前にデータベースを正しく準備しておかねばならないということです。

データベースのテストにおけるこの事前条件を満たす方法には、次のようなものがあります。

#.

   インメモリの SQLite ではなく永続化したデータベースを使うのなら、
   最初に一度 phpMyAdmin (MySQL の場合) などのツールでデータベースを用意しておけば、
   あとはテストを実行するたびにそれを再利用できます。

#.

   `Doctrine 2 <http://www.doctrine-project.org>`_ や
   `Propel <http://www.propelorm.org/>`_
   といったライブラリを使っている場合は、その API を使えばテストの実行前に必要なデータベーススキーマを作ることができます。
   `PHPUnit のブートストラップ <textui.html>`_
   機能を使うと、そのコードをテスト実行時に毎回実行させることもできます。

.. _database.tip-use-your-own-abstract-database-testcase:

ヒント: 自前でのデータベーステストケースの抽象化
=========================

先の実装例を見ればすぐにわかるでしょうが、
``getConnection()`` メソッドはきわめて静的なものであり、
さまざまなデータベーステストケースで再利用することができます。
さらに、テストのパフォーマンスを良好に保ちつつデータベースのオーバーヘッドを下げるために、
ちょっとしたリファクタリングを施して汎用的な抽象テストケースを用意しましょう。
このようにしても、テストケースごとに異なるデータフィクスチャを指定することができます。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class MyApp_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // PDO のインスタンス生成は、クリーンアップおよびフィクスチャ読み込みのときに一度だけ
        static private $pdo = null;

        // PHPUnit\DbUnit\Database\Connection のインスタンス生成は、テストごとに一度だけ
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO('sqlite::memory:');
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, ':memory:');
            }

            return $this->conn;
        }
    }

しかし、これはまだデータベースへの接続情報を
PDO 接続の設定にハードコードしてしまっています。
PHPUnit にはさらにすばらしい機能があるので、それを使ってテストケースをより汎用的にしましょう。
`XML 設定ファイル <appendixes.configuration.html#appendixes.configuration.php-ini-constants-variables>`_
を使えば、テストの実行のたびにデータベース接続を設定できます。
まずは “phpunit.xml“ というファイルをアプリケーションの tests/ ディレクトリに作り、
中身をこのようにします。

.. code-block:: bash

    <?xml version="1.0" encoding="UTF-8" ?>
    <phpunit>
        <php>
            <var name="DB_DSN" value="mysql:dbname=myguestbook;host=localhost" />
            <var name="DB_USER" value="user" />
            <var name="DB_PASSWD" value="passwd" />
            <var name="DB_DBNAME" value="myguestbook" />
        </php>
    </phpunit>

テストケースはこのように書き直せます。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    abstract class Generic_Tests_DatabaseTestCase extends TestCase
    {
        use TestCaseTrait;

        // PDO のインスタンス生成は、クリーンアップおよびフィクスチャ読み込みのときに一度だけ
        static private $pdo = null;

        // PHPUnit\DbUnit\Database\Connection のインスタンス生成は、テストごとに一度だけ
        private $conn = null;

        final public function getConnection()
        {
            if ($this->conn === null) {
                if (self::$pdo == null) {
                    self::$pdo = new PDO( $GLOBALS['DB_DSN'], $GLOBALS['DB_USER'], $GLOBALS['DB_PASSWD'] );
                }
                $this->conn = $this->createDefaultDBConnection(self::$pdo, $GLOBALS['DB_DBNAME']);
            }

            return $this->conn;
        }
    }

データベースの設定情報を切り替えてテストスイートを実行するには、
コマンドラインから次のようにします。

.. code-block:: bash

    $ user@desktop> phpunit --configuration developer-a.xml MyTests/
    $ user@desktop> phpunit --configuration developer-b.xml MyTests/

データベースのテストを実行するときにターゲットデータベースを切り替えられるようにしておくことは、
開発機で作業をしている場合などは特に重要です。
複数の開発者が同じデータベース接続を使ってデータベースのテストを実行したりすると、
レースコンディション (競合条件) によるテストの失敗が頻発するでしょう。

.. _database.understanding-datasets-and-datatables:

データセットとデータテーブルについて知る
####################

PHPUnit Database Extension の中心となる概念が
データセットとデータテーブルです。まずはこの考え方を理解することが、
PHPUnit でのデータベースのテストをマスターする近道です。
データセットとデータテーブルは、データベースのテーブルや行、
そしてカラムの抽象化レイヤーです。シンプルな API
によってデータベースの内容をオブジェクト構造に隠蔽できるだけでなく、
データベース以外のソースによる実装もできるようになっています。

この抽象化を使って、データベースの実際の中身と我々が期待する内容を比較します。
期待する内容は XML や YAML そして CSV などのファイルでも表せますし、
PHP の配列として表すこともできます。
DataSet インターフェイスと DataTable インターフェイスのおかげで、
これらの全く異なる概念のソースをリレーショナルデータベースに見立てて
同様に扱えるようになります。

データベースのアサーションをテストの中で行う流れは、
次のようにシンプルな三段階となります。

-

  ひとつあるいは複数のテーブルをデータベース内から指定する
  (実際のデータセット)。

-

  期待するデータセットをお好みのフォーマット (YAML, XML など)
  で用意する。

-

  両者がお互いに等しいことを確認する。

データセットやデータテーブルの
PHPUnit Database Extension における使い道は、
何もアサーションだけだというわけではありません。先ほどの節で見たように、
これらを使ってデータベースの初期状態の内容を記述することもできます。
フィクスチャとなるデータセットを Database TestCase
で定義すると、それをこのように使うことができます。

-

  データセットで指定したテーブルのすべての行を削除する。

-

  データテーブルのすべての行をデータベースに書き込む。

.. _database.available-implementations:

利用できる実装
=======

これら三種類のデータセット/データテーブルが用意されています。

-

  ファイルベースのデータセットやデータテーブル

-

  クエリベースのデータセットやデータテーブル

-

  フィルタ用や合成用のデータセットやデータテーブル

ファイルベースのデータセットやデータテーブルは、
初期状態のフィクスチャを定義したり期待する状態を定義したりするときによく使います。

.. _database.flat-xml-dataset:

フラット XML データセット
---------------

最も一般的なデータセットは、フラット XML と呼ばれるものです。
これは非常にシンプルな xml 形式で、ルートノード
``<dataset>``
の中のタグがデータベースのひとつの行を表します。
テーブルと同じ名前のタグが追加する行を表し、
その属性がカラムを表します。
単純な掲示板アプリケーションの例は、このようになります。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
    </dataset>

見るからに書きやすそうですね。この場合は
``<guestbook>`` がテーブル名で、
2 行が追加されます。そして、四つのカラム “id“、
“content“、“user“ そして
“created“ に、それぞれ対応する値が設定されています。

しかし、この単純性による問題もあります。

たとえば、先ほどの例で空のテーブルはどうやって指定すればいいのかがよくわかりません。
実は、何も属性を指定せずにテーブルと同じ名前のタグを追加すれば、空のテーブルを表すことができます。
空の guestbook テーブルを表すフラット xml ファイルは、このようになります。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook />
    </dataset>

フラット xml データセットでの NULL 値の処理は、あまりおもしろいものではありません。
ほとんどのデータベースでは、NULL 値と空文字列は別のものとして扱います
(例外のひとつは Oracle です) が、これをフラット xml
形式で表すのは困難です。NULL 値を表すには、
行の指定のときに属性を省略します。
この例の掲示板で、匿名の投稿を許可し、そのときには
user カラムに NULL を指定することにしましょう。
guestbook テーブルの状態は、このようになります。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" created="2010-04-26 12:14:20" />
    </dataset>

この例では、二番目のエントリが匿名の投稿を表します。
しかし、これはカラムの認識において深刻な問題につながります。
データセットが等しいことを確認するアサーションでは、各データセットでテーブルの持つカラムを指定しなければなりません。
ある属性がデータテーブルのすべての行で NULL だったなら、
Database Extension はそのカラムがテーブルに存在することをどうやって知るというのでしょう?

フラット XML データセットはここで、重大な前提を使っています。
テーブルの最初の行で定義されている属性が、そのテーブルのカラムを定義しているものと見なすのです。
先ほどの例では、guestbook テーブルのカラムが
“id“ と “content“、“user“ そして
“created“ であると見なすということです。二番目の行には
“user“ が定義されていないので、データベースには
NULL を挿入します。

guestbook の最初のエントリをデータセットから削除すると、guestbook テーブルのカラムは
“id“、“content“ そして
“created“ だけになってしまいます。
“user“ が指定されていないからです。

フラット XML データセットを効率的に使うには、NULL 値がからむ場合は
各テーブルの最初の行には NULL を含まないようにします。
それ以降の行では、属性を省略して NULL を表すことができます。
これはあまりスマートなやり方ではありません。
というのも、データベースのアサーションで行の順番が影響してしまうからです。

一方、テーブルのカラムの一部だけをフラット XML データセットで指定すると、
それ以外のカラムにはデフォルト値が設定されます。
そのため、もし省略したカラムの定義が
“NOT NULL DEFAULT NULL“
などの場合はエラーになります。

結論として言えるのは、フラット XML データセットを使うなら
NULL 値が不要な場合だけにしておいたほうがよい、ということだけです。

フラット XML データセットのインスタンスを
Database TestCase から作るには、
``createFlatXmlDataSet($filename)`` メソッドを使います。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
        }
    }

.. _database.xml-dataset:

XML データセット
----------

もうひとつ別の構造の XML データセットもあります。これは多少冗長な書き方ですが、
フラット XML データセットにおける NULL の問題は発生しません。
ルートノード ``<dataset>`` の配下に指定できるタグは、
``<table>`` や
``<column>``、``<row>``、
``<value>`` そして
``<null />`` です。
先に定義した Guestbook のフラット XML と同様のデータセットは、このようになります。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <table name="guestbook">
            <column>id</column>
            <column>content</column>
            <column>user</column>
            <column>created</column>
            <row>
                <value>1</value>
                <value>Hello buddy!</value>
                <value>joe</value>
                <value>2010-04-24 17:15:23</value>
            </row>
            <row>
                <value>2</value>
                <value>I like it!</value>
                <null />
                <value>2010-04-26 12:14:20</value>
            </row>
        </table>
    </dataset>

``<table>`` には name が必須で、
さらにすべてのカラムの名前を定義しなければなりません。
また、ゼロ個以上の ``<row>``
要素を含めることができます。``<row>``
要素を定義しなければ、そのテーブルが空であることになります。
``<value>`` タグや
``<null />`` タグは、先に指定した
``column>`` 要素の順番で指定しなければなりません。
``<null />`` タグは、
見た目の通り、値が NULL であることを表します。

XML データセットのインスタンスを
Database TestCase から作るには、
``createXmlDataSet($filename)`` メソッドを使います。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createXMLDataSet('myXmlFixture.xml');
        }
    }

.. _database.mysql-xml-dataset:

MySQL XML データセット
----------------

この新しい XML フォーマットは、
`MySQL データベース <https://www.mysql.com>`_ 専用です。
PHPUnit 3.5 以降で対応します。この形式のファイルを生成するには、
`mysqldump <https://dev.mysql.com/doc/refman/5.0/ja/mysqldump.html>`_
を使います。``mysqldump`` では CSV データセットも対応していますが、
それとは違ってこの XML 形式の場合はひとつのファイルに複数のテーブルを含めることができます。
この形式のファイルを作るには、
``mysqldump`` を次のように実行します。

.. code-block:: bash

    $ mysqldump --xml -t -u [username] --password=[password] [database] > /path/to/file.xml

このファイルを Database TestCase で使うには、
``createMySQLXMLDataSet($filename)`` メソッドをコールします。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MyTestCase extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            return $this->createMySQLXMLDataSet('/path/to/file.xml');
        }
    }

.. _database.yaml-dataset:

YAML データセット
-----------

あるいは、YAML データセットを使って、guestbook の例をこのように表すこともできます。

.. code-block:: bash

    guestbook:
      -
        id: 1
        content: "Hello buddy!"
        user: "joe"
        created: 2010-04-24 17:15:23
      -
        id: 2
        content: "I like it!"
        user:
        created: 2010-04-26 12:14:20

これは、シンプルで便利なうえに、さらにフラット XML
データセットが持つ NULL の問題も解決しています。
NULL を YAML で表すには、単にカラム名の後に何も値を指定しなければよいのです。
空文字列を指定する場合は
``column1: ""``
のようにします。

YAML Dataset 用のファクトリーメソッドは今のところ Database TestCase
に存在しないので、手動でインスタンスを生成しなければなりません。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\YamlDataSet;

    class YamlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new YamlDataSet(dirname(__FILE__)."/_files/guestbook.yml");
        }
    }

.. _database.csv-dataset:

CSV データセット
----------

さらにもうひとつのファイルベースのデータセットとして、CSV
ファイルを使ったものもあります。データセット内の各テーブルを、
それぞれ単一の CSV ファイルとして扱います。
guestbook の例では、このようなファイル
guestbook-table.csv を定義します。

.. code-block:: bash

    id,content,user,created
    1,"Hello buddy!","joe","2010-04-24 17:15:23"
    2,"I like it!","nancy","2010-04-26 12:14:20"

この形式は Excel や OpenOffice で編集できるという点で非常に便利ですが、
CSV データセットでは NULL 値を指定することができません。
空のカラムは、データベースのデフォルトに基づいた空の値として扱われます。

CSV データセットを作るには、このようにします。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;
    use PHPUnit\DbUnit\DataSet\CsvDataSet;

    class CsvGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            $dataSet = new CsvDataSet();
            $dataSet->addTable('guestbook', dirname(__FILE__)."/_files/guestbook.csv");
            return $dataSet;
        }
    }

.. _database.array-dataset:

Array データセット
------------

PHPUnit の Database Extension のバージョン 1.3.2 以降では、
配列ベースのデータセットが使えます。
guestbook の例だと、このようになります。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ArrayGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        protected function getDataSet()
        {
            return new MyApp_DbUnit_ArrayDataSet(
                [
                    'guestbook' => [
                        [
                            'id' => 1,
                            'content' => 'Hello buddy!',
                            'user' => 'joe',
                            'created' => '2010-04-24 17:15:23'
                        ],
                        [
                            'id' => 2,
                            'content' => 'I like it!',
                            'user' => null,
                            'created' => '2010-04-26 12:14:20'
                        ],
                    ],
                ]
            );
        }
    }

PHP の DataSet には、これまでのファイルベースのデータセットに比べて明らかな利点があります。

-

  PHP の配列は ``NULL`` 値を扱える。

-

  アサーション用に新たなファイルを用意する必要がなく、
  直接テストケース内で指定できる。

このデータセットでは、フラット XML や CSV そして YAML
データセットと同様に、最初に指定した行のキーがテーブルのカラム名を表します。
つまり、先ほどの例だと “id“、
“content“、“user“ そして
“created“ です。

このデータセットの実装は、シンプルでわかりやすいものです。

.. code-block:: php

    <?php

    use PHPUnit\DbUnit\DataSet\AbstractDataSet;
    use PHPUnit\DbUnit\DataSet\DefaultTableMetaData;
    use PHPUnit\DbUnit\DataSet\DefaultTable;
    use PHPUnit\DbUnit\DataSet\DefaultTableIterator;

    class MyApp_DbUnit_ArrayDataSet extends AbstractDataSet
    {
        /**
         * @var array
         */
        protected $tables = [];

        /**
         * @param array $data
         */
        public function __construct(array $data)
        {
            foreach ($data as $tableName => $rows) {
                $columns = [];
                if (isset($rows[0])) {
                    $columns = array_keys($rows[0]);
                }

                $metaData = new DefaultTableMetaData($tableName, $columns);
                $table = new DefaultTable($metaData);

                foreach ($rows as $row) {
                    $table->addRow($row);
                }
                $this->tables[$tableName] = $table;
            }
        }

        protected function createIterator($reverse = false)
        {
            return new DefaultTableIterator($this->tables, $reverse);
        }

        public function getTable($tableName)
        {
            if (!isset($this->tables[$tableName])) {
                throw new InvalidArgumentException("$tableName is not a table in the current database.");
            }

            return $this->tables[$tableName];
        }
    }

.. _database.query-sql-dataset:

Query (SQL) データセット
------------------

データベースのアサーションでは、ファイルベースのデータセットだけでなく
Query/SQL ベースのデータセットでデータベースの実際の中身を含むものが必要になることもあります。
そんなときに使えるのが Query データセットです。

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook');

単にテーブル名だけを指定してテーブルを追加すると、
次のクエリを実行してデータテーブルを定義したのと同じ意味になります。

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT * FROM guestbook');

ここでテーブルに対して任意のクエリを実行して、
取得する行や列を絞り込んだり
``ORDER BY`` 句を追加したりすることができます。

.. code-block:: php

    <?php
    $ds = new PHPUnit\DbUnit\DataSet\QueryDataSet($this->getConnection());
    $ds->addTable('guestbook', 'SELECT id, content FROM guestbook ORDER BY created DESC');

データベースアサーションの節で、このデータセットを使う方法をより詳しく説明しています。

.. _database.database-db-dataset:

Database (DB) データセット
--------------------

テスト用のデータベース接続にアクセスすると、
自動的にすべてのテーブルとその中身を含むデータセットを生成します。
接続先のデータベースは、接続用のファクトリーメソッドの二番目のパラメータで指定します。

データベース全体の完全なデータセットを作るには
``testGuestbook()`` のようにします。
ホワイトリスト形式で指定したテーブルだけに絞り込むには
``testFilteredGuestbook()`` メソッドのようにします。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class MySqlGuestbookTest extends TestCase
    {
        use TestCaseTrait;

        /**
         * @return PHPUnit\DbUnit\Database\Connection
         */
        public function getConnection()
        {
            $database = 'my_database';
            $user = 'my_user';
            $password = 'my_password';
            $pdo = new PDO('mysql:...', $user, $password);
            return $this->createDefaultDBConnection($pdo, $database);
        }

        public function testGuestbook()
        {
            $dataSet = $this->getConnection()->createDataSet();
            // ...
        }

        public function testFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet($tableNames);
            // ...
        }
    }

.. _database.replacement-dataset:

Replacement データセット
------------------

これまで、フラット XML や CSV のデータセットには NULL
の問題があると説明してきました。
しかし、ちょっとわかりにくい回避策を使えばこれらのデータセットで
NULL を扱うこともできます。

Replacement データセットは既存のデータセットに対するデコレータで、
データセットの任意のカラムの値を別の値で置換することができます。
guestbook の例で NULL 値を扱うには、このようなファイルを作ります。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

そして、フラット XML データセットを Replacement データセットでラップします。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ReplacementTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds = $this->createFlatXmlDataSet('myFlatXmlFixture.xml');
            $rds = new PHPUnit\DbUnit\DataSet\ReplacementDataSet($ds);
            $rds->addFullReplacement('##NULL##', null);
            return $rds;
        }
    }

.. _database.dataset-filter:

データセットフィルタ
----------

巨大なフィクスチャファイルを扱うときには、
データセットフィルタをホワイトリストあるいはブラックリストとして使って
テーブルやカラムを絞り込んだサブデータセットを作ることができます。
これは、DB データセットと組み合わせて
データセットのカラムを絞り込むときに使うと非常に便利です。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class DataSetFilterTest extends TestCase
    {
        use TestCaseTrait;

        public function testIncludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit\DbUnit\DataSet\DataSetFilter($dataSet);
            $filterDataSet->addIncludeTables(['guestbook']);
            $filterDataSet->setIncludeColumnsForTable('guestbook', ['id', 'content']);
            // ..
        }

        public function testExcludeFilteredGuestbook()
        {
            $tableNames = ['guestbook'];
            $dataSet = $this->getConnection()->createDataSet();

            $filterDataSet = new PHPUnit\DbUnit\DataSet\DataSetFilter($dataSet);
            $filterDataSet->addExcludeTables(['foo', 'bar', 'baz']); // only keep the guestbook table!
            $filterDataSet->setExcludeColumnsForTable('guestbook', ['user', 'created']);
            // ..
        }
    }

.. admonition:: Note

    ひとつのテーブルに対してカラムの exclude フィルタと
    include フィルタを同時に使うことはできません。
    さらに、テーブルのホワイトリストとブラックリストはどちらか一方しか指定できません。

.. _database.composite-dataset:

Composite データセット
----------------

Composite データセットは、既存の複数のデータセットをひとつにまとめるときに有用です。
複数のデータセットに同名のテーブルが含まれる場合は、
指定した順で行を連結します。
たとえば、このようなふたつのデータセットがあるものとしましょう。
まずは *fixture1.xml*。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
    </dataset>

そして *fixture2.xml*。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="2" content="I like it!" user="##NULL##" created="2010-04-26 12:14:20" />
    </dataset>

Composite データセットを使えば、両方のフィクスチャファイルをまとめることができます。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class CompositeTest extends TestCase
    {
        use TestCaseTrait;

        public function getDataSet()
        {
            $ds1 = $this->createFlatXmlDataSet('fixture1.xml');
            $ds2 = $this->createFlatXmlDataSet('fixture2.xml');

            $compositeDs = new PHPUnit\DbUnit\DataSet\CompositeDataSet();
            $compositeDs->addDataSet($ds1);
            $compositeDs->addDataSet($ds2);

            return $compositeDs;
        }
    }

.. _database.beware-of-foreign-keys:

外部キーには注意
========

フィクスチャを準備するとき、PHPUnit の Database Extension
はフィクスチャ内で定義された順に行を追加していきます。
データベースのスキーマ定義で外部キーを使っている場合は、
外部キー制約に違反しないような順番でテーブルを指定しなければなりません。

.. _database.implementing-your-own-datasetsdatatables:

自作のデータセットやデータテーブルの実装
====================

データセットやデータテーブルの内部構造を理解するために、
まずはデータセットのインターフェイスから見ていきましょう。
自分でデータセットやデータテーブルを作るつもりのない人は、
読み飛ばしてもかまいません。

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\DataSet;

    interface IDataSet extends IteratorAggregate
    {
        public function getTableNames();
        public function getTableMetaData($tableName);
        public function getTable($tableName);
        public function assertEquals(IDataSet $other);

        public function getReverseIterator();
    }

公開インターフェイスは、データベーステストケースの
``assertDataSetsEqual()``
アサーションで内部的に使われており、これでデータセットの内容を検証します。
IDataSet は ``IteratorAggregate``
インターフェイスから ``getIterator()``
メソッドを継承しており、これを使ってデータセット内の全テーブルの反復処理を行います。
リバースイテレータを使うと、PHPUnit で作ったテーブルのデータの切り詰めを、
テーブルを作ったときと逆の順番で行えます。これで、外部キー制約に違反せずに済むようになります。

テーブルのインスタンスをデータセットに追加するには、
実装によってさまざまな手法があります。たとえば
``YamlDataSet`` や
``XmlDataSet`` そして ``FlatXmlDataSet``
のようなファイルベースのデータセットでは、
データセットの作成時にソースファイルを使って内部的に追加します。

テーブルは、このようなインターフェイスを使って表します。

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\DataSet;

    interface ITable
    {
        public function getTableMetaData();
        public function getRowCount();
        public function getValue($row, $column);
        public function getRow($row);
        public function assertEquals(ITable $other);
    }

``getTableMetaData()`` メソッドは別として、
それ以外のメソッドはまさに文字通りの働きをするものです。
これらのメソッドはすべて、Database Extension のさまざまなアサーションで必須となります。
その詳細は次の章で説明します。
``getTableMetaData()`` メソッドの返す値は、
``PHPUnit\DbUnit\DataSet\ITableMetaData``
インターフェイスを実装したものでなければなりません。
このインターフェイスはテーブルの構造を表し、このような情報を保持します。

-

  テーブル名。

-

  テーブルのカラム名の配列。並び順は、結果セットに登場する順と同じ。

-

  主キーカラムの配列。

このインターフェイスには、ふたつの TableMetaData
のインスタンスがお互いに等しいかを調べるアサーションも定義されています。
これは、データセットの同一性を調べるアサーションで利用するものです。

.. _database.the-connection-api:

データベース接続APIの使い方
######

Connection インターフェイスには、三種類のおもしろいメソッドが用意されています。
このインターフェイスは、データベーステストケースの
``getConnection()`` メソッドが返すものです。

.. code-block:: php

    <?php
    namespace PHPUnit\DbUnit\Database;

    interface Connection
    {
        public function createDataSet(array $tableNames = null);
        public function createQueryTable($resultName, $sql);
        public function getRowCount($tableName, $whereClause = null);

        // ...
    }

#.

   ``createDataSet()`` メソッドは、Database
   (DB) データセットを作ります。これは、データセットの実装の節で説明したものです。

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSet()
           {
               $tableNames = ['guestbook'];
               $dataSet = $this->getConnection()->createDataSet();
           }
       }

#.

   ``createQueryTable()`` メソッドを使うと、
   QuryTable のインスタンスを作れます。引数には、結果の名前と SQL クエリを渡します。
   これは、次の節 (データベースアサーション API)
   で説明する結果やテーブルのアサーションで有用なメソッドです。

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateQueryTable()
           {
               $tableNames = ['guestbook'];
               $queryTable = $this->getConnection()->createQueryTable('guestbook', 'SELECT * FROM guestbook');
           }
       }

#.

   ``getRowCount()`` は、
   テーブル内の行数を手軽に取得するためのメソッドです。
   オプションで、where 句によるフィルタリングもできます。
   これを使えば、シンプルな同一性のアサーションが可能です。

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class ConnectionTest extends TestCase
       {
           use TestCaseTrait;

           public function testGetRowCount()
           {
               $this->assertSame(2, $this->getConnection()->getRowCount('guestbook'));
           }
       }

.. _database.database-assertions-api:

データベースアサーション API
################

テストツール用として、Database Extension ではいくつかのアサーションを提供しています。
これらを使えば、データベースやテーブルの現在の状態
そしてテーブルの行数を検証できます。この節では、
これらの機能の詳細を説明します。

.. _database.asserting-the-row-count-of-a-table:

テーブルの行数のアサーション
==============

テーブルの行数が特定の値であるかどうかを調べられれば便利なことがよくあります。
これは、接続 API を使ってちょっとしたコードを書かなくとも簡単に実現できます。
guestbook に行を追加した後で、初期登録した 2 エントリ以外にもう一行増えて
3 行になっていることを調べるには、このようにします。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $this->assertSame(2, $this->getConnection()->getRowCount('guestbook'), "Pre-Condition");

            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $this->assertSame(3, $this->getConnection()->getRowCount('guestbook'), "Inserting failed");
        }
    }

.. _database.asserting-the-state-of-a-table:

テーブルの状態のアサーション
==============

先ほどのアサーションも有用ですが、本当にチェックしたいのは、
すべての値が正しいカラムにきちんと登録されたかどうかです。
これは、テーブルのアサーションで実現します。

そのために、QueryTable のインスタンスを定義しました。
テーブル名と SQL クエリからその内容を取得し、
それをファイルベースあるいは配列ベースのデータセットと比較します。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class GuestbookTest extends TestCase
    {
        use TestCaseTrait;

        public function testAddEntry()
        {
            $guestbook = new Guestbook();
            $guestbook->addEntry("suzy", "Hello world!");

            $queryTable = $this->getConnection()->createQueryTable(
                'guestbook', 'SELECT * FROM guestbook'
            );
            $expectedTable = $this->createFlatXmlDataSet("expectedBook.xml")
                                  ->getTable("guestbook");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }

さて次に、このアサーションに使うフラット XML ファイル
*expectedBook.xml* を用意しましょう。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" created="2010-04-24 17:15:23" />
        <guestbook id="2" content="I like it!" user="nancy" created="2010-04-26 12:14:20" />
        <guestbook id="3" content="Hello world!" user="suzy" created="2010-05-01 21:47:08" />
    </dataset>

残念ながら、このアサーションが成功するのは、ちょうど
*2010–05–01 21:47:08* に実行したときだけになります。
日付はデータベースのテストでいつも問題になるものなので、それを回避する手段として
“created“ カラムをアサーションで無視させることができます。

調整後のフラット XML ファイル *expectedBook.xml*
はこのようになり、これでアサーションを通過させることができます。

.. code-block:: bash

    <?xml version="1.0" ?>
    <dataset>
        <guestbook id="1" content="Hello buddy!" user="joe" />
        <guestbook id="2" content="I like it!" user="nancy" />
        <guestbook id="3" content="Hello world!" user="suzy" />
    </dataset>

QueryTable の呼び出しも修正しなければなりません。

.. code-block:: php

    <?php
    $queryTable = $this->getConnection()->createQueryTable(
        'guestbook', 'SELECT id, content, user FROM guestbook'
    );

.. _database.asserting-the-result-of-a-query:

クエリの結果のアサーション
=============

複雑なクエリの結果に対するアサーションも、
QueryTable 方式で可能です。単に結果の名前とクエリを指定して、
それをデータセットと比較すればよいのです。

.. code-block:: php

    <?php
    use PHPUnit\Framework\TestCase;
    use PHPUnit\DbUnit\TestCaseTrait;

    class ComplexQueryTest extends TestCase
    {
        use TestCaseTrait;

        public function testComplexQuery()
        {
            $queryTable = $this->getConnection()->createQueryTable(
                'myComplexQuery', 'SELECT complexQuery...'
            );
            $expectedTable = $this->createFlatXmlDataSet("complexQueryAssertion.xml")
                                  ->getTable("myComplexQuery");
            $this->assertTablesEqual($expectedTable, $queryTable);
        }
    }

.. _database.asserting-the-state-of-multiple-tables:

複数のテーブルの状態のアサーション
=================

もちろん、複数のテーブルの状態を一度に確かめたり
クエリデータセットをファイルベースのデータセットと比較したりすることも可能です。
データセットのアサーションには二通りの方法があります。

#.

   接続の Database (DB) データセットを使い、
   それをファイルベースのデータセットと比較する。

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testCreateDataSetAssertion()
           {
               $dataSet = $this->getConnection()->createDataSet(['guestbook']);
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');
               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }

#.

   データセットを自分で作ることもできます。

   .. code-block:: php

       <?php
       use PHPUnit\Framework\TestCase;
       use PHPUnit\DbUnit\TestCaseTrait;
       use PHPUnit\DbUnit\DataSet\QueryDataSet;

       class DataSetAssertionsTest extends TestCase
       {
           use TestCaseTrait;

           public function testManualDataSetAssertion()
           {
               $dataSet = new QueryDataSet();
               $dataSet->addTable('guestbook', 'SELECT id, content, user FROM guestbook'); // additional tables
               $expectedDataSet = $this->createFlatXmlDataSet('guestbook.xml');

               $this->assertDataSetsEqual($expectedDataSet, $dataSet);
           }
       }

.. _database.frequently-asked-questions:

よくある質問
######

.. _database.will-phpunit-re-create-the-database-schema-for-each-test:

PHPUnit は、テストごとにデータベーススキーマを作り直すの?
=================================

いいえ。PHPUnit は、テストスイートの開始時にすべてのデータベースオブジェクトが存在することを前提とします。
データベースやテーブル、シーケンス、トリガー、そしてビューなどは、
テストスイートを実行する前に作っておく必要があります。

`Doctrine 2 <http://www.doctrine-project.org>`_ や
`eZ Components <http://www.ezcomponents.org>`_
の強力なツールを使えば、定義済みのデータ構造からデータベーススキーマを作成できます。
しかし、これらを使うには PHPUnit extension にフックで組み込まねばなりません。
そうしないと、テストスイートを実行する前にデータベースの自動再作成ができなくなります。

各テストの実行後はデータベースをクリアするので、
テストを実行するたびにデータベースを再作成する必要はありません。
事前に作ったデータベースをずっと使いまわすことができます。

.. _database.am-i-required-to-use-pdo-in-my-application-for-the-database-extension-to-work:

PDO を使ったアプリケーションじゃないと Database Extension を使えないの?
================================================

いいえ。PDO が必要なのは、フィクスチャの準備や後始末とアサーションのときだけです。
テスト対象のコード内では、なんでもお好みの方法でデータベースにアクセスできます。

.. _database.what-can-i-do-when-i-get-a-too-much-connections-error:

“Too much Connections“ というエラーが出たらどうすればいい?
=========================================

テストケースの ``getConnection()`` メソッドで作った
PDO インスタンスをキャッシュしていなければ、
データベースを使うテストを実行するたびにデータベースへの接続の数は増加し続けます。
デフォルトの設定では MySQL が受け付ける同時接続は 100 までであり、
他のデータベースにも同様の接続数制限があります。

“自前でのデータベーステストケースの抽象化“
に、このエラーを回避する方法を示しています。
ひとつの PDO インスタンスをキャッシュして、すべてのテストで使いまわす方法です。

.. _database.how-to-handle-null-with-flat-xml-csv-datasets:

フラット XML や CSV のデータセットで NULL を扱う方法は?
====================================

そんな方法はありません。NULL が使いたければ XML あるいは YAML データセットを使わないといけません。


