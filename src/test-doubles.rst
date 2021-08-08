

.. _test-doubles:

============
テストダブル
============

Gerard Meszaros は、テストダブルの概念を
:ref:`Meszaros2007 <appendixes.bibliography>` でこのように述べています。

    *Gerard Meszaros*:

    Sometimes it is just plain hard to test the system under test (SUT)
    because it depends on other components that cannot be used in the test
    environment. This could be because they aren't available, they will not
    return the results needed for the test or because executing them would
    have undesirable side effects. In other cases, our test strategy requires
    us to have more control or visibility of the internal behavior of the SUT.

    \- テスト対象のシステム (SUT: system under test)
    をテストすることは、時に非常に困難なこととなります。というのも、
    システムが他のコンポーネントに依存しており、
    そのコンポーネントをテスト環境で利用できないことがあるからです。
    そもそも使用不可能であったりテストで必要な結果を返さなかったり、
    あるいは好ましくない副作用があったりといったことです。
    それ以外の場合も、テスト環境の内部的な振る舞いをきちんと制御して
    目に見えるようにしておくことが必要です。

    When we are writing a test in which we cannot (or chose not to) use a real
    depended-on component (DOC), we can replace it with a Test Double. The
    Test Double doesn't have to behave exactly like the real DOC; it merely
    has to provide the same API as the real one so that the SUT thinks it is
    the real one!

    \- 実際に依存するコンポーネント (DOC: depended-on component)
    を使わないテストを書く場合は、それをテストダブルで置き換えることができます。
    テストダブルは、必ずしも実際の DOC
    とまったく同様に動作する必要はありません。
    単に実際のものと同じ API を提供し、
    SUT に「これは本物だ!」と思わせるだけでいいのです。

PHPUnit の ``createStub($type)`` メソッドや ``getMockBuilder($type)`` メソッドを使うと、
指定した元インターフェイス (あるいは元クラス) のテストダブルとして振る舞うオブジェクトを自動的に生成することができます。
このテストダブルオブジェクトは、元のオブジェクトを要するすべての場面で使うことができます。

``createStub($type)`` 、および ``createMock($type)`` メソッドは、指定した型 (インターフェイスやクラス)
のテストダブルオブジェクトをその場で返します。
テストダブルの作成は、デフォルトではベストプラクティスに沿って行われます
(元クラスの ``__construct()`` や ``__clone()``
は実行しません)。また、テストダブルのメソッドに渡された引数はクローンされません。
デフォルトと異なる挙動を求める場合は、
``getMockBuilder($type)`` メソッドを用いてテストダブルの生成処理をカスタマイズする必要があります。

デフォルトでは、元クラスのすべてのメソッドが置き換えられて、
(元のメソッドは呼び出さずに) 単に ``null``
を返すダミー実装になります。たとえば
``will($this->returnValue())`` メソッドを使うと、
ダミー実装がコールされたときに値を返すよう設定することができます。

.. admonition:: 制限：final、private および static メソッド

   ``final``, ``private`` および
   ``static`` メソッドのスタブやモックは作れないことに注意しましょう。
   PHPUnit のテストダブル機能ではこれらを無視し、元のメソッドの振る舞いをそのまま維持します。
   ただし ``static`` メソッドは例外で、これは
   ``\PHPUnit\Framework\MockObject\BadMethodCallException``
   をスローするメソッドに置き換えられます。

.. _test-doubles.stubs:

スタブ
######

実際のオブジェクトを置き換えて、
設定した何らかの値を (オプションで) 返すようなテストダブルのことを
*スタブ* といいます。
*スタブ* を使うと、
「SUT が依存している実際のコンポーネントを置き換え、
SUT の入力を間接的にコントロールできるようにすることができます。
これにより、SUT が他の何者も実行しないことを強制させることができます。」

:numref:`test-doubles.stubs.examples.StubTest.php`
に、スタブメソッドの作成と返り値の設定の方法を示します。まず、
``PHPUnit\Framework\TestCase`` クラスの
``createStub()`` メソッドを用いて
``SomeClass`` オブジェクトのスタブを作成します
(:numref:`test-doubles.stubs.examples.SomeClass.php`)。
次に、PHPUnit が提供する、いわゆる
`Fluent Interface <http://martinfowler.com/bliki/FluentInterface.html>`_
(`流れるようなインターフェイス <http://capsctrl.que.jp/kdmsnr/wiki/bliki/?FluentInterface>`_)
を用いてスタブの振る舞いを指定します。簡単に言うと、
いくつもの一時オブジェクトを作成して、
それらを連結するといった操作は必要ないということです。
そのかわりに、例にあるようにメソッドの呼び出しを連結します。
このほうが、より読みやすく "流れるような" コードとなります。

.. code-block:: php
    :caption: スタブを作りたいクラス
    :name: test-doubles.stubs.examples.SomeClass.php

    <?php declare(strict_types=1);
    class SomeClass
    {
        public function doSomething()
        {
            // なにかをします
        }
    }

.. code-block:: php
    :caption: メソッドに固定値を返させるスタブ
    :name: test-doubles.stubs.examples.StubTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->willReturn('foo');

            // $stub->doSomething() をコールすると
            // 'foo' を返すようになります
            $this->assertSame('foo', $stub->doSomething());
        }
    }

.. admonition:: 制限： "method" という名前のメソッド

   この例がきちんと動作するのは、元のクラスで "method" という名前のメソッドが宣言されていない場合だけです。

   元のクラスで "method" という名前のメソッドが宣言されている場合は、
   ``$stub->expects($this->any())->method('doSomething')->willReturn('foo');`` としなければいけません。

舞台裏では、``createStub()`` メソッドが使われたときに
PHPUnit が自動的に、求める振る舞いを実装した新たな PHP のクラスを生成しています。


なお、 ``createStub()`` は、メソッドの戻り値の型に基づいて自動的に、そして再帰的に戻り値をスタブ化します。以下のような例を考えてみましょう。

.. code-block:: php
    :caption: 返り値について型宣言されたメソッド
    :name: test-doubles.stubs.examples.returnTypeDeclaration.php

    <?php declare(strict_types=1);
    class C
    {
        public function m(): D
        {
            // Do something.
        }
    }

上の例のように、 ``C::m()`` は返り値として ``D`` のオブジェクトを返すことが宣言されています。 ``C`` のテストダブルを作成し、返り値を ``willReturn()`` メソッドなどで設定しなかった場合、 ``C`` のテストダブルで ``m()`` のメソッドを呼び出すと、 PHPUnit は自動的に ``D`` のテストダブルを作成して返します。
同様に ``m`` の型定義がスカラー型であった場合は、 ``0`` ( ``int`` の場合)、  ``0.0`` ( ``float`` の場合)、 ``[]`` ( ``配列`` の場合) が生成され返されます。

:numref:`test-doubles.stubs.examples.StubTest2.php` に例を示します。
これは、モックビルダーの流れるようなインターフェイスを使って、テストダブルの作成方法を設定するものです。
このテストダブルで使っている設定は、``createStub()``
がデフォルトで使用するベストプラクティスと同じです。

.. code-block:: php
    :caption: モックビルダー API を使った、生成されるテストダブルクラスの変更
    :name: test-doubles.stubs.examples.StubTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->getMockBuilder(SomeClass::class)
                         ->disableOriginalConstructor()
                         ->disableOriginalClone()
                         ->disableArgumentCloning()
                         ->disallowMockingUnknownTypes()
                         ->getMock();

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->willReturn('foo');

            // $stub->doSomething() をコールすると
            // 'foo' を返すようになります
            $this->assertSame('foo', $stub->doSomething());
        }
    }

ここまでの例では、
``willReturn($value)`` を使ってシンプルな値を返していました。
この構文は、
``will($this->returnValue($value))`` と同じ意味です。
この長い構文での検証を使うと、より複雑な動きをするスタブも作れます。

時には、メソッドをコールした際の引数のひとつを
(そのまま) スタブメソッドコールの返り値としたいこともあるでしょう。
:numref:`test-doubles.stubs.examples.StubTest3.php` は、
``returnValue()`` のかわりに
``returnArgument()`` を用いてこれを実現する例です。

.. code-block:: php
    :caption: メソッドに引数のひとつを返させるスタブ
    :name: test-doubles.stubs.examples.StubTest3.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnArgumentStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->will($this->returnArgument(0));

            // $stub->doSomething('foo') は 'foo' を返します
            $this->assertSame('foo', $stub->doSomething('foo'));

            // $stub->doSomething('bar') は 'bar' を返します
            $this->assertSame('bar', $stub->doSomething('bar'));
        }
    }

流れるようなインターフェイスをテストするときには、
スタブメソッドがオブジェクト自身への参照を返すようにできると便利です。
:numref:`test-doubles.stubs.examples.StubTest4.php` は、
``returnSelf()`` を使ってこれを実現する例です。

.. code-block:: php
    :caption: スタブオブジェクトへの参照を返すメソッドのスタブ
    :name: test-doubles.stubs.examples.StubTest4.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnSelf(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->will($this->returnSelf());

            // $stub->doSomething() は $stub を返します
            $this->assertSame($stub, $stub->doSomething());
        }
    }

スタブメソッドをコールした結果として、
定義済みの引数リストにあわせて異なる値を返さなければならないこともあるでしょう。
``returnValueMap()`` を使えば、
マップを作って引数と関連付け、それを返り値に対応させることができます。
:numref:`test-doubles.stubs.examples.StubTest5.php` を参照ください。

.. code-block:: php
    :caption: メソッドにマップからの値を返させるスタブ
    :name: test-doubles.stubs.examples.StubTest5.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnValueMapStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // 値を返すための、引数のマップを作製します
            $map = [
                ['a', 'b', 'c', 'd'],
                ['e', 'f', 'g', 'h']
            ];

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->will($this->returnValueMap($map));

            // $stub->doSomething() は、渡した引数に応じて異なる値を返します
            $this->assertSame('d', $stub->doSomething('a', 'b', 'c'));
            $this->assertSame('h', $stub->doSomething('e', 'f', 'g'));
        }
    }

スタブメソッドをコールした結果として固定値
(``returnValue()`` を参照ください) や (不変の) 引数
(``returnArgument()`` を参照ください)
ではなく計算した値を返したい場合は、
``returnCallback()`` を使用します。
これは、スタブメソッドからコールバック関数やメソッドの結果を返させます。
:numref:`test-doubles.stubs.examples.StubTest6.php`
を参照ください。

.. code-block:: php
    :caption: メソッドにコールバックからの値を返させるスタブ
    :name: test-doubles.stubs.examples.StubTest6.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testReturnCallbackStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->will($this->returnCallback('str_rot13'));

            // $stub->doSomething($argument) は str_rot13($argument) を返します
            $this->assertSame('fbzrguvat', $stub->doSomething('something'));
        }
    }

コールバックメソッドを設定するよりももう少しシンプルな方法として、
希望する返り値のリストを指定することもできます。この場合に使うのは
``onConsecutiveCalls()`` メソッドです。
:numref:`test-doubles.stubs.examples.StubTest7.php`
の例を参照ください。

.. code-block:: php
    :caption: メソッドに、リストで指定した値をその順で返させるスタブ
    :name: test-doubles.stubs.examples.StubTest7.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testOnConsecutiveCallsStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->will($this->onConsecutiveCalls(2, 3, 5, 7));

            // $stub->doSomething() は毎回異なる値を返します
            $this->assertSame(2, $stub->doSomething());
            $this->assertSame(3, $stub->doSomething());
            $this->assertSame(5, $stub->doSomething());
        }
    }

値を返すのではなく、スタブメソッドで例外を発生させることもできます。
:numref:`test-doubles.stubs.examples.StubTest8.php`
に、``throwException()`` でこれを行う方法を示します。

.. code-block:: php
    :caption: メソッドに例外をスローさせるスタブ
    :name: test-doubles.stubs.examples.StubTest8.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StubTest extends TestCase
    {
        public function testThrowExceptionStub(): void
        {
            // SomeClass クラスのスタブを作成します
            $stub = $this->createStub(SomeClass::class);

            // スタブの設定を行います
            $stub->method('doSomething')
                 ->will($this->throwException(new Exception));

            // $stub->doSomething() は例外をスローします
            $stub->doSomething();
        }
    }

また、スタブを使用することで、よりよい設計を行うことができるようにもなります。
あちこちで使用されているリソースを単一の窓口 (façade : ファサード)
経由でアクセスするようにすることで、
それをスタブに置き換えられるようになります。例えば、
データベースへのアクセスのコードをそこらじゅうにちりばめるのではなく、
その代わりに ``IDatabase`` インターフェイスを実装した単一の
``Database`` オブジェクトを使用するようにします。すると、
``IDatabase`` を実装したスタブを作成することで、
それをテストに使用できるようになるのです。同時に、
テストを行う際にスタブデータベースを使用するか
本物のデータベースを使用するかを選択できるようになります。
つまり開発時にはローカル環境でテストし、
統合テスト時には実際のデータベースでテストするといったことができるようになるのです。

スタブ化しなければならない機能は、たいてい同一オブジェクト内で密結合しています。
この機能ををひとつの結合したインターフェイスにまとめることで、
システムのそれ以外の部分との結合を緩やかにすることができます。

.. _test-doubles.mock-objects:

モックオブジェクト
##################

実際のオブジェクトを置き換えて、
(メソッドがコールされたことなどの) 期待する内容を検証するテストダブルのことを
*モック* といいます。

*モックオブジェクト* は
"SUT の間接的な出力の内容を検証するために使用する観測地点です。
一般的に、モックオブジェクトにはテスト用スタブの機能も含まれます。
まだテストに失敗していない場合に、間接的な出力の検証用の値を SUT に返す機能です。
したがって、モックオブジェクトとは
テスト用スタブにアサーション機能を足しただけのものとは異なります。
それ以外の用途にも使うことができます" (Gerard Meszaros)。

.. admonition:: 制限：期待値の自動検証

   そのテストのスコープ内で生成されたモックオブジェクトだけが、PHPUnit による自動検証の対象となります。
   たとえば、データプロバイダなどで生成されたモックオブジェクトや
   ``@depends`` アノテーションで注入されたオブジェクトについては、PHPUnit では検証しません。

ひとつ例を示します。ここでは、別のオブジェクトを観察している
あるオブジェクトの特定のメソッド (この例では ``update()``)
が正しくコールされたかどうかを調べるものとします。
:numref:`test-doubles.mock-objects.examples.SUT.php`
は、テスト対象のシステム (SUT) の一部である
``Subject`` クラスと ``Observer`` クラスのコードです。

.. code-block:: php
    :caption: テスト対象のシステム (SUT) の一部である Subject クラスと Observer クラス
    :name: test-doubles.mock-objects.examples.SUT.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    class Subject
    {
        protected $observers = [];
        protected $name;

        public function __construct($name)
        {
            $this->name = $name;
        }

        public function getName()
        {
            return $this->name;
        }

        public function attach(Observer $observer)
        {
            $this->observers[] = $observer;
        }

        public function doSomething()
        {
            // なにかをします
            // ...

            // なにかしたということをオブザーバに通知します
            $this->notify('something');
        }

        public function doSomethingBad()
        {
            foreach ($this->observers as $observer) {
                $observer->reportError(42, 'Something bad happened', $this);
            }
        }

        protected function notify($argument)
        {
            foreach ($this->observers as $observer) {
                $observer->update($argument);
            }
        }

        // その他のメソッド
    }

    class Observer
    {
        public function update($argument)
        {
            // なにかをします
        }

        public function reportError($errorCode, $errorMessage, Subject $subject)
        {
            // なにかをします
        }

        // その他のメソッド
    }

:numref:`test-doubles.mock-objects.examples.SubjectTest.php`
では、モックオブジェクトを作成して
``Subject`` オブジェクトと ``Observer``
オブジェクトの対話をテストする方法を説明します。

まず
``PHPUnit\Framework\TestCase`` クラスの
``createMock()`` メソッドを使用して ``Observer`` のモックオブジェクトを作成します。

あるメソッドがコールされたのかどうか、そしてどんな引数を渡してコールされたのかを検証したいので、
``expects()`` メソッドと ``with()`` メソッドを利用します。
これらを使って、このやりとりがどのように行われるのかを指定します。

.. code-block:: php
    :caption: あるメソッドが、指定した引数で一度だけコールされることを確かめるテスト
    :name: test-doubles.mock-objects.examples.SubjectTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SubjectTest extends TestCase
    {
        public function testObserversAreUpdated(): void
        {
            // Observer クラスのモックを作成します。
            // update() メソッドのみのモックです。
            $observer = $this->createMock(Observer::class);

            // update() メソッドが一度だけコールされ、その際の
            // パラメータは文字列 'something' となる、
            // ということを期待しています。
            $observer->expects($this->once())
                     ->method('update')
                     ->with($this->equalTo('something'));

            // Subject オブジェクトを作成し、Observer オブジェクトの
            // モックをアタッチします。
            $subject = new Subject('My subject');
            $subject->attach($observer);

            // $subject オブジェクトの doSomething() メソッドをコールします。
            // これは、Observer オブジェクトのモックの update() メソッドを、
            // 文字列 'something' を引数としてコールすることを期待されています。
            $subject->doSomething();
        }
    }

``with()`` メソッドには任意の数の引数を渡すことができます。
これは、モック対象のメソッドの引数の数に対応します。
メソッドの引数に対して、単なるマッチだけでなくより高度な制約を指定することもできます。

.. code-block:: php
    :caption: メソッドが引数つきでコールされることを、さまざまな制約の下でテストする例
    :name: test-doubles.mock-objects.examples.SubjectTest2.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SubjectTest extends TestCase
    {
        public function testErrorReported(): void
        {
            // Observer クラスのモックを作成します。
            // reportError() メソッドをモックします。
            $observer = $this->createMock(Observer::class);

            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                           $this->greaterThan(0),
                           $this->stringContains('Something'),
                           $this->anything()
                       );

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // doSomethingBad() メソッドは、
            // reportError() メソッドを通じてオブザーバにエラーを報告しなければなりません。
            $subject->doSomethingBad();
        }
    }

``withConsecutive()`` メソッドには、
テスト対象の呼び出しにあわせて、引数の配列を好きなだけ渡せます。
個々の配列は制約のリストです。
``with()`` と同様に、これがモック対象メソッドのそれぞれの引数に対応します。

.. code-block:: php
    :caption: あるメソッドが、指定した引数つきで 2 回呼び出されることを確かめるテスト
    :name: test-doubles.mock-objects.examples.with-consecutive.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FooTest extends TestCase
    {
        public function testFunctionCalledTwoTimesWithSpecificArguments(): void
        {
            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['set'])
                         ->getMock();

            $mock->expects($this->exactly(2))
                 ->method('set')
                 ->withConsecutive(
                     [$this->equalTo('foo'), $this->greaterThan(0)],
                     [$this->equalTo('bar'), $this->greaterThan(0)]
                 );

            $mock->set('foo', 21);
            $mock->set('bar', 48);
        }
    }

``callback()`` 制約を使えば、より複雑な引数の検証ができます。
この制約は、PHP のコールバックを引数として受け取ります。
このコールバックは、検証したい引数を受け取って、検証を通過した場合に ``true``、
それ以外の場合に ``false`` を返します。

.. code-block:: php
    :caption: より複雑な引数の検証
    :name: test-doubles.mock-objects.examples.SubjectTest3.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SubjectTest extends TestCase
    {
        public function testErrorReported(): void
        {
            // Observer クラスのモックを作成します。
            // reportError() メソッドをモックします。
            $observer = $this->createMock(Observer::class);
            $observer->expects($this->once())
                     ->method('reportError')
                     ->with(
                         $this->greaterThan(0),
                         $this->stringContains('Something'),
                         $this->callback(function($subject)
                         {
                             return is_callable([$subject, 'getName']) &&
                                    $subject->getName() == 'My subject';
                         }
                     ));

            $subject = new Subject('My subject');
            $subject->attach($observer);

            // doSomethingBad() メソッドは、
            // reportError() メソッドを通じてオブザーバにエラーを報告しなければなりません。
            $subject->doSomethingBad();
        }
    }

.. code-block:: php
    :caption: メソッドが一度だけ呼ばれ、同じオブジェクトが渡されたことを確かめるテスト
    :name: test-doubles.mock-objects.examples.clone-object-parameters-usecase.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed(): void
        {
            $expectedObject = new stdClass;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->setMethods(['foo'])
                         ->getMock();

            $mock->expects($this->once())
                 ->method('foo')
                 ->with($this->identicalTo($expectedObject));

            $mock->foo($expectedObject);
        }
    }

.. code-block:: php
    :caption: パラメータのクローンの有効にしたモックオブジェクトの作成
    :name: test-doubles.mock-objects.examples.enable-clone-object-parameters.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FooTest extends TestCase
    {
        public function testIdenticalObjectPassed(): void
        {
            $cloneArguments = true;

            $mock = $this->getMockBuilder(stdClass::class)
                         ->enableArgumentCloning()
                         ->getMock();

            // これでモックがパラメータをクローンするようになり、
            // identicalTo 制約は失敗します
        }
    }

:ref:`appendixes.assertions.assertThat.tables.constraints`
はメソッドの引数に適用できる制約、そして
:numref:`test-doubles.mock-objects.tables.matchers`
は起動回数を指定するために使える matcher です。

.. rst-class:: table
.. list-table:: Matchers
    :name: test-doubles.mock-objects.tables.matchers
    :header-rows: 1

    * - Matcher
      - 意味
    * - ``PHPUnit\Framework\MockObject\Matcher\AnyInvokedCount any()``
      - 評価対象のメソッドがゼロ回以上実行された際にマッチするオブジェクトを返します。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount never()``
      - 評価対象のメソッドが実行されなかった際にマッチするオブジェクトを返します。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtLeastOnce atLeastOnce()``
      - 評価対象のメソッドが最低一回以上実行された際にマッチするオブジェクトを返します。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount once()``
      - 評価対象のメソッドが一度だけ実行された際にマッチするオブジェクトを返します。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedCount exactly(int $count)``
      - 評価対象のメソッドが指定した回数だけ実行された際にマッチするオブジェクトを返します。
    * - ``PHPUnit\Framework\MockObject\Matcher\InvokedAtIndex at(int $index)``
      - 評価対象のメソッドが ``$index`` 回目に実行された際にマッチするオブジェクトを返します。

.. admonition:: Note

   ``at()`` マッチャーのパラメータ ``$index`` は、
   指定したモックオブジェクトでの *すべてのメソッドの実行*
   の、ゼロからはじまるインデックスを参照します。
   このマッチャーを使うときには注意しましょう。テストが実装の詳細とあまりにも密結合になり、
   脆いテストになってしまう可能性があるからです。

最初に説明したとおり、 ``createStub()`` および ``createMock()``
メソッドが用いるデフォルトのテストダブル生成方法がニーズを満たさない場合は、
``getMockBuilder($type)`` メソッドを使えば生成方法をカスタマイズできます。
モックビルダーが提供するメソッドの一覧は、次のとおりです。

-

  ``setMethods(array $methods)`` をモックビルダーオブジェクト上でコールすると、テストダブルで置き換えるメソッドを指定することができます。その他のメソッドの挙動は変更しません。``setMethods(NULL)`` とすると、どのメソッドも置き換えません。

-

  ``setMethodsExcept(array $methods)`` をモックビルダーオブジェクト上でコールすると、テストダブルで置き換えないメソッドを指定することができます。その他のすべてのpubilicメソッドは置き換えられます。このメソッドは ``setMethods()`` の逆の働きをします。


-

  ``setConstructorArgs(array $args)`` をコールしてパラメータの配列を渡すと、それを元クラスのコンストラクタに渡すことができます (デフォルトのダミー実装では、コンストラクタは置き換えません)。

-

  ``setMockClassName($name)`` を使うと、生成されるテストダブルクラスのクラス名を指定することができます。

-

  ``disableOriginalConstructor()`` を使うと、元クラスのコンストラクタを無効にすることができます。

-

  ``disableOriginalClone()`` を使うと、元クラスのクローンコンストラクタを無効にすることができます。

-

  ``disableAutoload()`` を使うと、テストダブルクラスを生成するときに ``__autoload()`` を無効にすることができます。

.. _test-doubles.mocking-traits-and-abstract-classes:

トレイトと抽象クラスのモック
############################

``getMockForTrait()`` メソッドは、指定したトレイトを使ったモックオブジェクトを返します。
そのトレイトのすべての抽象メソッドがモックの対象となります。
これを使えば、トレイトの具象メソッドをテストすることができます。

.. code-block:: php
    :caption: トレイトの具象メソッドのテスト
    :name: test-doubles.mock-objects.examples.TraitClassTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    trait AbstractTrait
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    final class TraitClassTest extends TestCase
    {
        public function testConcreteMethod(): void
        {
            $mock = $this->getMockForTrait(AbstractTrait::class);

            $mock->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($mock->concreteMethod());
        }
    }

``getMockForAbstractClass()`` メソッドは、
抽象クラスのモックオブジェクトを返します。
そのクラスのすべての抽象メソッドがモックの対象となります。
これを使えば、抽象クラスにある具象メソッドをテストすることができます。

.. code-block:: php
    :caption: 抽象クラスの具象メソッドのテスト
    :name: test-doubles.mock-objects.examples.AbstractClassTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    abstract class AbstractClass
    {
        public function concreteMethod()
        {
            return $this->abstractMethod();
        }

        public abstract function abstractMethod();
    }

    final class AbstractClassTest extends TestCase
    {
        public function testConcreteMethod(): void
        {
            $stub = $this->getMockForAbstractClass(AbstractClass::class);

            $stub->expects($this->any())
                 ->method('abstractMethod')
                 ->will($this->returnValue(true));

            $this->assertTrue($stub->concreteMethod());
        }
    }

.. _test-doubles.stubbing-and-mocking-web-services:

ウェブサービスのスタブおよびモック
##################################

ウェブサービスとのやりとりを行うアプリケーションを、
実際にウェブサービスとやりとりすることなくテストしたくなることもあるでしょう。
ウェブサービスのスタブやモックを作成するために ``getMockFromWsdl()``
メソッドが用意されており、これは ``getMock()`` (上を参照ください)
とほぼ同様に使うことができます。唯一の違いは、
``getMockFromWsdl()`` が返すスタブやモックが WSDL
のウェブサービス記述にもとづくものであるのに対して ``getMock()``
が返すスタブやモックが PHP のクラスやインターフェイスにもとづくものであるという点です。

:numref:`test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php`
は、``getMockFromWsdl()`` を使って
:file:`GoogleSearch.wsdl` に記述されたウェブサービスのスタブを作る例です。

.. code-block:: php
    :caption: ウェブサービスのスタブ
    :name: test-doubles.stubbing-and-mocking-web-services.examples.GoogleTest.php

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class GoogleTest extends TestCase
    {
        public function testSearch(): void
        {
            $googleSearch = $this->getMockFromWsdl(
              'GoogleSearch.wsdl', 'GoogleSearch'
            );

            $directoryCategory = new stdClass;
            $directoryCategory->fullViewableName = '';
            $directoryCategory->specialEncoding = '';

            $element = new stdClass;
            $element->summary = '';
            $element->URL = 'https://phpunit.de/';
            $element->snippet = '...';
            $element->title = '<b>PHPUnit</b>';
            $element->cachedSize = '11k';
            $element->relatedInformationPresent = true;
            $element->hostName = 'phpunit.de';
            $element->directoryCategory = $directoryCategory;
            $element->directoryTitle = '';

            $result = new stdClass;
            $result->documentFiltering = false;
            $result->searchComments = '';
            $result->estimatedTotalResultsCount = 3.9000;
            $result->estimateIsExact = false;
            $result->resultElements = [$element];
            $result->searchQuery = 'PHPUnit';
            $result->startIndex = 1;
            $result->endIndex = 1;
            $result->searchTips = '';
            $result->directoryCategories = [];
            $result->searchTime = 0.248822;

            $googleSearch->expects($this->any())
                         ->method('doGoogleSearch')
                         ->will($this->returnValue($result));

            /**
             * $googleSearch->doGoogleSearch() はスタブが用意した結果を返し、
             * ウェブサービスの doGoogleSearch() が呼び出されることはありません
             */
            $this->assertEquals(
              $result,
              $googleSearch->doGoogleSearch(
                '00000000000000000000000000000000',
                'PHPUnit',
                0,
                1,
                false,
                '',
                false,
                '',
                '',
                ''
              )
            );
        }
    }


