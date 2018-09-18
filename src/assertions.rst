

.. _appendixes.assertions:

============
アサーション
============

この節では、利用可能なアサーションメソッドの一覧を示します。

.. _appendixes.assertions.static-vs-non-static-usage-of-assertion-methods:

アサーションメソッドはstaticで使うべきか、それとも非staticで使うべきか
######################################################################

PHPUnit のアサーションの実装は、 ``PHPUnit\Framework\Assert``
およびそれを継承した ``PHPUnit\Framework\TestCase`` にあります。

アサーションメソッドは static 宣言されていて、あらゆるコンテキストから
``PHPUnit\Framework\Assert::assertTrue()`` などのように使えます。
また、 ``PHPUnit\Framework\TestCase`` を継承したクラスの中では
``$this->assertTrue()`` や ``self::assertTrue()`` などとしても使えます。

さらに、PHPUnit に含まれるファイル :file:`src/Framework/Assert/Functions.php`
を (手動で) インクルードしてしまえば、グローバルなラッパー関数 ``assertTrue()``
などを使うことさえできてしまいます。これは、 ``PHPUnit\Framework\TestCase``
を継承したクラスを含めてあらゆるコンテキストで使えます。

PHPUnit を使い始めたばかりの開発者の多くは、たとえばアサーションを実行するときに
``$this->assertTrue()`` と ``self::assertTrue()``
のどちらを使うのが「正しい方法」なのだろうかと思うことでしょう。
端的に言って、どちらがよいなどということはありません。同様に、どちらが悪いというものでもありません。
どちらを使うかは、個人的な好みの問題です。

どちらかといえば ``$this->assertTrue()``
を使うほうが自然に感じるという人が多いようです。テストメソッドは、テストオブジェクト上で実行されるからです。
アサーションメソッドが static 宣言されていることによって、
テストオブジェクトのスコープを超えた再利用が可能になります。
また、グローバル関数のラッパーを使えば、
``($this->assertTrue()`` や ``self::assertTrue()`` ではなく
``assertTrue()`` と書くことによって) タイプ量を抑えることができます。

.. _appendixes.assertions.assertArrayHasKey:

assertArrayHasKey()
###################

``assertArrayHasKey(mixed $key, array $array[, string $message = ''])``

``$array`` にキー ``$key`` が存在しない場合にエラー ``$message`` を報告します。

``assertArrayNotHasKey()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertArrayHasKey() の使用法
    :name: appendixes.assertions.assertArrayHasKey.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayHasKeyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }

.. code-block:: bash

    $ phpunit ArrayHasKeyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayHasKeyTest::testFailure
    Failed asserting that an array has the key 'foo'.

    /home/sb/ArrayHasKeyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasAttribute:

assertClassHasAttribute()
#########################

``assertClassHasAttribute(string $attributeName, string $className[, string $message = ''])``

``$className::attributeName`` が存在しない場合にエラー ``$message`` を報告します。

``assertClassNotHasAttribute()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertClassHasAttribute() の使用法
    :name: appendixes.assertions.assertClassHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }

.. code-block:: bash

    $ phpunit ClassHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertArraySubset:

assertArraySubset()
###################

``assertArraySubset(array $subset, array $array[, bool $strict = false, string $message = ''])``

``$array`` が ``$subset`` を含まない場合にエラー ``$message`` を報告します。

``$strict`` フラグを使うと、配列内のオブジェクトの比較にその識別子を利用します。

.. code-block:: php
    :caption: Usage of assertArraySubset()
    :name: appendixes.assertions.assertArraySubset.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArraySubsetTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertArraySubset(['config' => ['key-a', 'key-b']], ['config' => ['key-a']]);
        }
    }

.. code-block:: bash

    $ phpunit ArraySubsetTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) Epilog\EpilogTest::testNoFollowOption
    Failed asserting that an array has the subset Array &0 (
        'config' => Array &1 (
            0 => 'key-a'
            1 => 'key-b'
        )
    ).

    /home/sb/ArraySubsetTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertClassHasStaticAttribute:

assertClassHasStaticAttribute()
###############################

``assertClassHasStaticAttribute(string $attributeName, string $className[, string $message = ''])``

``$className::attributeName`` が存在しない場合にエラー ``$message`` を報告します。

``assertClassNotHasStaticAttribute()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertClassHasStaticAttribute() の使用法
    :name: appendixes.assertions.assertClassHasStaticAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }

.. code-block:: bash

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasStaticAttributeTest::testFailure
    Failed asserting that class "stdClass" has static attribute "foo".

    /home/sb/ClassHasStaticAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContains:

assertContains()
################

``assertContains(mixed $needle, Iterator|array $haystack[, string $message = ''])``

``$needle`` が ``$haystack`` の要素でない場合にエラー ``$message`` を報告します。

``assertNotContains()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeContains()`` と ``assertAttributeNotContains()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を haystack として使用することができます。

.. code-block:: php
    :caption: assertContains() の使用法
    :name: appendixes.assertions.assertContains.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertContains(string $needle, string $haystack[, string $message = '', boolean $ignoreCase = false])``

``$needle`` が ``$haystack`` の部分文字列でない場合にエラー ``$message`` を報告します。

``$ignoreCase`` が ``true`` の場合、テストで大文字小文字を区別しなくなります。

.. code-block:: php
    :caption: assertContains() の使用法
    :name: appendixes.assertions.assertContains.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('baz', 'foobar');
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'foobar' contains "baz".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. code-block:: php
    :caption: assertContains() で $ignoreCase を使う方法
    :name: appendixes.assertions.assertContains.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContains('foo', 'FooBar');
        }

        public function testOK()
        {
            $this->assertContains('foo', 'FooBar', '', true);
        }
    }

.. code-block:: bash

    $ phpunit ContainsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F.

    Time: 0 seconds, Memory: 2.75Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that 'FooBar' contains "foo".

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, Iterator|array $haystack[, boolean $isNativeType = NULL, string $message = ''])``

``$haystack`` の中身の型が ``$type`` だけではない場合にエラー ``$message`` を報告します。

``$isNativeType`` はフラグで、``$type`` がネイティブな PHP の型であるかどうかを表します。

``assertNotContainsOnly()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeContainsOnly()`` と ``assertAttributeNotContainsOnly()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を haystack として使用することができます。

.. code-block:: php
    :caption: assertContainsOnly() の使用法
    :name: appendixes.assertions.assertContainsOnly.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }

.. code-block:: bash

    $ phpunit ContainsOnlyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyTest::testFailure
    Failed asserting that Array (
        0 => '1'
        1 => '2'
        2 => 3
    ) contains only values of type "string".

    /home/sb/ContainsOnlyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnlyInstancesOf:

assertContainsOnlyInstancesOf()
###############################

``assertContainsOnlyInstancesOf(string $classname, Traversable|array $haystack[, string $message = ''])``

``$haystack`` が ``$classname`` クラスの唯一のインスタンスを含まない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertContainsOnlyInstancesOf() の使用法
    :name: appendixes.assertions.assertContainsOnlyInstancesOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }

.. code-block:: bash

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsOnlyInstancesOfTest::testFailure
    Failed asserting that Array ([0]=> Bar Object(...)) is an instance of class "Foo".

    /home/sb/ContainsOnlyInstancesOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertCount:

assertCount()
#############

``assertCount($expectedCount, $haystack[, string $message = ''])``

``$haystack`` の要素数が ``$expectedCount`` でない場合にエラー ``$message`` を報告します。

``assertNotCount()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertCount() の使用法
    :name: appendixes.assertions.assertCount.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CountTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertCount(0, ['foo']);
        }
    }

.. code-block:: bash

    $ phpunit CountTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) CountTest::testFailure
    Failed asserting that actual size 1 matches expected size 0.

    /home/sb/CountTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryExists:

assertDirectoryExists()
#######################

``assertDirectoryExists(string $directory[, string $message = ''])``

``$directory`` で指定したディレクトリが存在しない場合にエラー ``$message`` を報告します。

``assertDirectoryNotExists()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertDirectoryExists() の使用法
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryExistsTest::testFailure
    Failed asserting that directory "/path/to/directory" exists.

    /home/sb/DirectoryExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsReadable:

assertDirectoryIsReadable()
###########################

``assertDirectoryIsReadable(string $directory[, string $message = ''])``

``$directory`` で指定したディレクトリが読み込み可能でない場合にエラー ``$message`` を報告します。

``assertDirectoryNotIsReadable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertDirectoryIsReadable() の使用法
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsReadableTest::testFailure
    Failed asserting that "/path/to/directory" is readable.

    /home/sb/DirectoryIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertDirectoryIsWritable:

assertDirectoryIsWritable()
###########################

``assertDirectoryIsWritable(string $directory[, string $message = ''])``

``$directory`` で指定したディレクトリが書き込み可能でない場合にエラー ``$message`` を報告します。

``assertDirectoryNotIsWritable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertDirectoryIsWritable() の使用法
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }

.. code-block:: bash

    $ phpunit DirectoryIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) DirectoryIsWritableTest::testFailure
    Failed asserting that "/path/to/directory" is writable.

    /home/sb/DirectoryIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEmpty:

assertEmpty()
#############

``assertEmpty(mixed $actual[, string $message = ''])``

``$actual`` が空でない場合にエラー ``$message`` を報告します。

``assertNotEmpty()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeEmpty()`` および ``assertAttributeNotEmpty()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性に対して使えます。

.. code-block:: php
    :caption: assertEmpty() の使用法
    :name: appendixes.assertions.assertEmpty.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EmptyTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEmpty(['foo']);
        }
    }

.. code-block:: bash

    $ phpunit EmptyTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEqualXMLStructure:

assertEqualXMLStructure()
#########################

``assertEqualXMLStructure(DOMElement $expectedElement, DOMElement $actualElement[, boolean $checkAttributes = false, string $message = ''])``

``$actualElement`` の DOMElement の XML 構造が ``$expectedElement`` の DOMElement の XML 構造と等しくない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertEqualXMLStructure() の使用法
    :name: appendixes.assertions.assertEqualXMLStructure.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualXMLStructureTest extends TestCase
    {
        public function testFailureWithDifferentNodeNames()
        {
            $expected = new DOMElement('foo');
            $actual = new DOMElement('bar');

            $this->assertEqualXMLStructure($expected, $actual);
        }

        public function testFailureWithDifferentNodeAttributes()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo bar="true" />');

            $actual = new DOMDocument;
            $actual->loadXML('<foo/>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild, true
            );
        }

        public function testFailureWithDifferentChildrenCount()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><bar/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }

        public function testFailureWithDifferentChildren()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/><bar/><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<foo><baz/><baz/><baz/></foo>');

            $this->assertEqualXMLStructure(
              $expected->firstChild, $actual->firstChild
            );
        }
    }

.. code-block:: bash

    $ phpunit EqualXMLStructureTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFFF

    Time: 0 seconds, Memory: 5.75Mb

    There were 4 failures:

    1) EqualXMLStructureTest::testFailureWithDifferentNodeNames
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'bar'

    /home/sb/EqualXMLStructureTest.php:9

    2) EqualXMLStructureTest::testFailureWithDifferentNodeAttributes
    Number of attributes on node "foo" does not match
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualXMLStructureTest.php:22

    3) EqualXMLStructureTest::testFailureWithDifferentChildrenCount
    Number of child nodes of "foo" differs
    Failed asserting that 1 matches expected 3.

    /home/sb/EqualXMLStructureTest.php:35

    4) EqualXMLStructureTest::testFailureWithDifferentChildren
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualXMLStructureTest.php:48

    FAILURES!
    Tests: 4, Assertions: 8, Failures: 4.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` が等しくない場合にエラー ``$message`` を報告します。

``assertNotEquals()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeEquals()`` と ``assertAttributeNotEquals()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を実際の値として使用することができます。

.. code-block:: php
    :caption: assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2()
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3()
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    FFF

    Time: 0 seconds, Memory: 5.25Mb

    There were 3 failures:

    1) EqualsTest::testFailure
    Failed asserting that 0 matches expected 1.

    /home/sb/EqualsTest.php:6

    2) EqualsTest::testFailure2
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'bar'
    +'baz'

    /home/sb/EqualsTest.php:11

    3) EqualsTest::testFailure3
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
     'foo
    -bar
    +bah
     baz
     '

    /home/sb/EqualsTest.php:16

    FAILURES!
    Tests: 3, Assertions: 3, Failures: 3.

引数 ``$expected`` と ``$actual`` の型により特化した比較については、以下を参照ください。

``assertEquals(float $expected, float $actual[, string $message = '', float $delta = 0])``

2 つの float 値 ``$expected`` と ``$actual`` の絶対差が ``$delta`` より大きい場合にエラー ``$message`` を報告します。2 つの float 値 ``$expected`` と ``$actual`` の絶対差が ``$delta`` *以下* である場合はアサーションに成功します。

なぜ ``$delta`` が必要となるのかについては "`What Every Computer Scientist Should Know About Floating-Point Arithmetic <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_" を参照ください。

.. code-block:: php
    :caption: float 値での assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testSuccess()
        {
            $this->assertEquals(1.0, 1.1, '', 0.1);
        }

        public function testFailure()
        {
            $this->assertEquals(1.0, 1.1);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    .F

    Time: 0 seconds, Memory: 5.75Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that 1.1 matches expected 1.0.

    /home/sb/EqualsTest.php:11

    FAILURES!
    Tests: 2, Assertions: 2, Failures: 1.

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

2 つの DOMDocument オブジェクト ``$expected`` と ``$actual`` で表される XML ドキュメントが (コメントを除去して正規化した状態で) 等しくない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: DOMDocument オブジェクトでの assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example3

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
    -<foo>
    -  <bar/>
    -</foo>
    +<bar>
    +  <foo/>
    +</bar>

    /home/sb/EqualsTest.php:12

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(object $expected, object $actual[, string $message = ''])``

2 つのオブジェクト ``$expected`` と ``$actual`` が同じ属性値を持たない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: オブジェクトでの assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example4

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $expected = new stdClass;
            $expected->foo = 'foo';
            $expected->bar = 'bar';

            $actual = new stdClass;
            $actual->foo = 'bar';
            $actual->baz = 'bar';

            $this->assertEquals($expected, $actual);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
    -    'foo' => 'foo'
    -    'bar' => 'bar'
    +    'foo' => 'bar'
    +    'baz' => 'bar'
     )

    /home/sb/EqualsTest.php:14

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertEquals(array $expected, array $actual[, string $message = ''])``

2 つの配列 ``$expected`` と ``$actual`` が等しくない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: 配列での assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example5

    <?php
    use PHPUnit\Framework\TestCase;

    class EqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit EqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) EqualsTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
         0 => 'a'
    -    1 => 'b'
    -    2 => 'c'
    +    1 => 'c'
    +    2 => 'd'
     )

    /home/sb/EqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

``$condition`` が ``true`` の場合にエラー ``$message`` を報告します。

``assertNotFalse()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFalse() の使用法
    :name: appendixes.assertions.assertFalse.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FalseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFalse(true);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FalseTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FalseTest::testFailure
    Failed asserting that true is false.

    /home/sb/FalseTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileEquals:

assertFileEquals()
##################

``assertFileEquals(string $expected, string $actual[, string $message = ''])``

``$expected`` で指定したファイルと ``$actual`` で指定したファイルの内容が異なる場合にエラー ``$message`` を報告します。

``assertFileNotEquals()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileEquals() の使用法
    :name: appendixes.assertions.assertFileEquals.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileEqualsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileEqualsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) FileEqualsTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    +'actual
     '

    /home/sb/FileEqualsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertFileExists:

assertFileExists()
##################

``assertFileExists(string $filename[, string $message = ''])``

ファイル ``$filename`` が存在しない場合にエラー ``$message`` を報告します。

``assertFileNotExists()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileExists() の使用法
    :name: appendixes.assertions.assertFileExists.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileExistsTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileExists('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileExistsTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileExistsTest::testFailure
    Failed asserting that file "/path/to/file" exists.

    /home/sb/FileExistsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsReadable:

assertFileIsReadable()
######################

``assertFileIsReadable(string $filename[, string $message = ''])``

``$filename`` で指定したファイルが読み込み可能でない場合、あるいはファイルでない場合にエラー ``$message`` を報告します。

``assertFileNotIsReadable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileIsReadable() の使用法
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileIsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsReadableTest::testFailure
    Failed asserting that "/path/to/file" is readable.

    /home/sb/FileIsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertFileIsWritable:

assertFileIsWritable()
######################

``assertFileIsWritable(string $filename[, string $message = ''])``

``$filename`` で指定したファイルが書き込み可能でない場合、あるいはファイルでない場合にエラー ``$message`` を報告します。

``assertFileNotIsWritable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileIsWritable() の使用法
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FileIsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit FileIsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) FileIsWritableTest::testFailure
    Failed asserting that "/path/to/file" is writable.

    /home/sb/FileIsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThan:

assertGreaterThan()
###################

``assertGreaterThan(mixed $expected, mixed $actual[, string $message = ''])``

``$actual`` の値が ``$expected`` の値より大きくない場合にエラー ``$message`` を報告します。

``assertAttributeGreaterThan()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を実際の値として使用することができます。

.. code-block:: php
    :caption: assertGreaterThan() の使用法
    :name: appendixes.assertions.assertGreaterThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreaterThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThan(2, 1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit GreaterThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) GreaterThanTest::testFailure
    Failed asserting that 1 is greater than 2.

    /home/sb/GreaterThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertGreaterThanOrEqual:

assertGreaterThanOrEqual()
##########################

``assertGreaterThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

``$actual`` の値が ``$expected`` の値以上でない場合にエラー ``$message`` を報告します。

``assertAttributeGreaterThanOrEqual()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を実際の値として使用することができます。

.. code-block:: php
    :caption: assertGreaterThanOrEqual() の使用法
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit GreaterThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) GreatThanOrEqualTest::testFailure
    Failed asserting that 1 is equal to 2 or is greater than 2.

    /home/sb/GreaterThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertInfinite:

assertInfinite()
################

``assertInfinite(mixed $variable[, string $message = ''])``

``$variable`` が ``INF`` でない場合にエラー ``$message`` を報告します。

``assertFinite()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertInfinite() の使用法
    :name: appendixes.assertions.assertInfinite.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InfiniteTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInfinite(1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InfiniteTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InfiniteTest::testFailure
    Failed asserting that 1 is infinite.

    /home/sb/InfiniteTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInstanceOf:

assertInstanceOf()
##################

``assertInstanceOf($expected, $actual[, $message = ''])``

``$actual`` が ``$expected`` のインスタンスでない場合にエラー ``$message`` を報告します。

``assertNotInstanceOf()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeInstanceOf()`` および ``assertAttributeNotInstanceOf()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性に対して使えます。

.. code-block:: php
    :caption: assertInstanceOf() の使用法
    :name: appendixes.assertions.assertInstanceOf.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InstanceOfTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InstanceOfTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertInternalType:

assertInternalType()
####################

``assertInternalType($expected, $actual[, $message = ''])``

``$actual`` の型が ``$expected`` でない場合にエラー ``$message`` を報告します。

``assertNotInternalType()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeInternalType()`` および ``assertAttributeNotInternalType()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性に対して使えます。

.. code-block:: php
    :caption: assertInternalType() の使用法
    :name: appendixes.assertions.assertInternalType.example

    <?php
    use PHPUnit\Framework\TestCase;

    class InternalTypeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertInternalType('string', 42);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit InternalTypeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InternalTypeTest::testFailure
    Failed asserting that 42 is of type "string".

    /home/sb/InternalTypeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

``$filename`` で指定したファイルあるいはディレクトリが読み込み可能でない場合にエラー ``$message`` を報告します。

``assertNotIsReadable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsReadable() の使用法
    :name: appendixes.assertions.assertIsReadable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsReadableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit IsReadableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsReadableTest::testFailure
    Failed asserting that "/path/to/unreadable" is readable.

    /home/sb/IsReadableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsWritable:

assertIsWritable()
##################

``assertIsWritable(string $filename[, string $message = ''])``

``$filename`` で指定したファイルあるいはディレクトリが書き込み可能でない場合にエラー ``$message`` を報告します。

``assertNotIsWritable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsWritable() の使用法
    :name: appendixes.assertions.assertIsWritable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IsWritableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit IsWritableTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) IsWritableTest::testFailure
    Failed asserting that "/path/to/unwritable" is writable.

    /home/sb/IsWritableTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertJsonFileEqualsJsonFile:

assertJsonFileEqualsJsonFile()
##############################

``assertJsonFileEqualsJsonFile(mixed $expectedFile, mixed $actualFile[, string $message = ''])``

``$actualFile`` の値が
``$expectedFile`` の値にマッチしない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertJsonFileEqualsJsonFile() の使用法
    :name: appendixes.assertions.assertJsonFileEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonFileEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"Tux"}' matches JSON string "["Mascott", "Tux", "OS", "Linux"]".

    /home/sb/JsonFileEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonFile:

assertJsonStringEqualsJsonFile()
################################

``assertJsonStringEqualsJsonFile(mixed $expectedFile, mixed $actualJson[, string $message = ''])``

``$actualJson`` の値が
``$expectedFile`` の値にマッチしない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertJsonStringEqualsJsonFile() の使用法
    :name: appendixes.assertions.assertJsonStringEqualsJsonFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonFile::testFailure
    Failed asserting that '{"Mascot":"ux"}' matches JSON string "{"Mascott":"Tux"}".

    /home/sb/JsonStringEqualsJsonFileTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertJsonStringEqualsJsonString:

assertJsonStringEqualsJsonString()
##################################

``assertJsonStringEqualsJsonString(mixed $expectedJson, mixed $actualJson[, string $message = ''])``

``$actualJson`` の値が
``$expectedJson`` の値にマッチしない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertJsonStringEqualsJsonString() の使用法
    :name: appendixes.assertions.assertJsonStringEqualsJsonString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }
    ?>

.. code-block:: bash

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) JsonStringEqualsJsonStringTest::testFailure
    Failed asserting that two objects are equal.
    --- Expected
    +++ Actual
    @@ @@
     stdClass Object (
     -    'Mascot' => 'Tux'
     +    'Mascot' => 'ux'
    )

    /home/sb/JsonStringEqualsJsonStringTest.php:5

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertLessThan:

assertLessThan()
################

``assertLessThan(mixed $expected, mixed $actual[, string $message = ''])``

``$actual`` の値が ``$expected`` の値より小さくない場合にエラー ``$message`` を報告します。

``assertAttributeLessThan()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を実際の値として使用することができます。

.. code-block:: php
    :caption: assertLessThan() の使用法
    :name: appendixes.assertions.assertLessThan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThan(1, 2);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LessThanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) LessThanTest::testFailure
    Failed asserting that 2 is less than 1.

    /home/sb/LessThanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertLessThanOrEqual:

assertLessThanOrEqual()
#######################

``assertLessThanOrEqual(mixed $expected, mixed $actual[, string $message = ''])``

``$actual`` の値が ``$expected`` の値以下でない場合にエラー ``$message`` を報告します。

``assertAttributeLessThanOrEqual()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を実際の値として使用することができます。

.. code-block:: php
    :caption: assertLessThanOrEqual() の使用法
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php
    use PHPUnit\Framework\TestCase;

    class LessThanOrEqualTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit LessThanOrEqualTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) LessThanOrEqualTest::testFailure
    Failed asserting that 2 is equal to 1 or is less than 1.

    /home/sb/LessThanOrEqualTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertNan:

assertNan()
###########

``assertNan(mixed $variable[, string $message = ''])``

``$variable`` が ``NAN`` でない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertNan() の使用法
    :name: appendixes.assertions.assertNan.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NanTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNan(1);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit NanTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NanTest::testFailure
    Failed asserting that 1 is nan.

    /home/sb/NanTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertNull:

assertNull()
############

``assertNull(mixed $variable[, string $message = ''])``

``$variable`` が ``NULL`` でないときにエラー ``$message`` を報告します。

``assertNotNull()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertNull() の使用法
    :name: appendixes.assertions.assertNull.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NullTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertNull('foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit NotNullTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NullTest::testFailure
    Failed asserting that 'foo' is null.

    /home/sb/NotNullTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectHasAttribute:

assertObjectHasAttribute()
##########################

``assertObjectHasAttribute(string $attributeName, object $object[, string $message = ''])``

``$object->attributeName`` が存在しない場合にエラー ``$message`` を報告します。

``assertObjectNotHasAttribute()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertObjectHasAttribute() の使用法
    :name: appendixes.assertions.assertObjectHasAttribute.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit ObjectHasAttributeTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertRegExp:

assertRegExp()
##############

``assertRegExp(string $pattern, string $string[, string $message = ''])``

``$string`` が正規表現 ``$pattern`` にマッチしない場合にエラー ``$message`` を報告します。

``assertNotRegExp()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertRegExp() の使用法
    :name: appendixes.assertions.assertRegExp.example

    <?php
    use PHPUnit\Framework\TestCase;

    class RegExpTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertRegExp('/foo/', 'bar');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit RegExpTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) RegExpTest::testFailure
    Failed asserting that 'bar' matches PCRE pattern "/foo/".

    /home/sb/RegExpTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringMatchesFormat:

assertStringMatchesFormat()
###########################

``assertStringMatchesFormat(string $format, string $string[, string $message = ''])``

``$string`` が書式文字列 ``$format`` にマッチしない場合にエラー ``$message`` を報告します。

``assertStringNotMatchesFormat()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertStringMatchesFormat() の使用法
    :name: appendixes.assertions.assertStringMatchesFormat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringMatchesFormatTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+$/s".

    /home/sb/StringMatchesFormatTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

書式文字列には次のプレースホルダを含めることができます。

-

  ``%e``: ディレクトリ区切り文字、たとえば Linux なら ``/`` を表します。

-

  ``%s``: 一文字以上の何か (文字あるいは空白)、ただし改行文字は含みません。

-

  ``%S``: ゼロ文字以上の何か (文字あるいは空白)、ただし改行文字は含みません。

-

  ``%a``: 一文字以上の何か (文字あるいは空白)、改行文字も含みます。

-

  ``%A``: ゼロ文字以上の何か (文字あるいは空白)、改行文字も含みます。

-

  ``%w``: ゼロ文字以上の空白。

-

  ``%i``: 符号付き整数値。例: ``+3142``, ``-3142``

-

  ``%d``: 符号なし整数値。例: ``123.66``

-

  ``%x``: 一文字以上の十六進文字 (``0-9``, ``a-f``, ``A-F``)。

-

  ``%f``: 浮動小数点数値。例: ``3.142``, ``-3.142``, ``3.142E-10``, ``3.142e+10``

-

  ``%c``: 任意の一文字。

-

  ``%%``: パーセント文字 ``%`` そのもの。

.. _appendixes.assertions.assertStringMatchesFormatFile:

assertStringMatchesFormatFile()
###############################

``assertStringMatchesFormatFile(string $formatFile, string $string[, string $message = ''])``

``$string`` が ``$formatFile`` の内容にマッチしない場合にエラー ``$message`` を報告します。

``assertStringNotMatchesFormatFile()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertStringMatchesFormatFile() の使用法
    :name: appendixes.assertions.assertStringMatchesFormatFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringMatchesFormatFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringMatchesFormatFileTest::testFailure
    Failed asserting that 'foo' matches PCRE pattern "/^[+-]?\d+
    $/s".

    /home/sb/StringMatchesFormatFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertSame:

assertSame()
############

``assertSame(mixed $expected, mixed $actual[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` が同じ型・同じ値でない場合にエラー ``$message`` を報告します。

``assertNotSame()`` はこのアサーションの逆で、同じ引数をとります。

``assertAttributeSame()`` と ``assertAttributeNotSame()`` は便利なラッパーで、クラスやオブジェクトの ``public``、``protected``、``private`` 属性を実際の値として使用することができます。

.. code-block:: php
    :caption: assertSame() の使用法
    :name: appendixes.assertions.assertSame.example

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame('2204', 2204);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that 2204 is identical to '2204'.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

``assertSame(object $expected, object $actual[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` が同じオブジェクトを参照していない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: オブジェクトでの assertSame() の使用法
    :name: appendixes.assertions.assertSame.example2

    <?php
    use PHPUnit\Framework\TestCase;

    class SameTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit SameTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) SameTest::testFailure
    Failed asserting that two variables reference the same object.

    /home/sb/SameTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEndsWith:

assertStringEndsWith()
######################

``assertStringEndsWith(string $suffix, string $string[, string $message = ''])``

``$string`` が ``$suffix`` で終わっていない場合にエラー ``$message`` を報告します。

``assertStringEndsNotWith()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertStringEndsWith() の使用法
    :name: appendixes.assertions.assertStringEndsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEndsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringEndsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 1 second, Memory: 5.00Mb

    There was 1 failure:

    1) StringEndsWithTest::testFailure
    Failed asserting that 'foo' ends with "suffix".

    /home/sb/StringEndsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertStringEqualsFile:

assertStringEqualsFile()
########################

``assertStringEqualsFile(string $expectedFile, string $actualString[, string $message = ''])``

``$expectedFile`` で指定したファイルの内容に ``$actualString`` が含まれない場合にエラー ``$message`` を報告します。

``assertStringNotEqualsFile()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertStringEqualsFile() の使用法
    :name: appendixes.assertions.assertStringEqualsFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringEqualsFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringEqualsFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) StringEqualsFileTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'expected
    -'
    +'actual'

    /home/sb/StringEqualsFileTest.php:6

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertStringStartsWith:

assertStringStartsWith()
########################

``assertStringStartsWith(string $prefix, string $string[, string $message = ''])``

``$string`` が ``$prefix`` で始まっていない場合にエラー ``$message`` を報告します。

``assertStringStartsNotWith()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertStringStartsWith() の使用法
    :name: appendixes.assertions.assertStringStartsWith.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringStartsWithTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit StringStartsWithTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringStartsWithTest::testFailure
    Failed asserting that 'foo' starts with "prefix".

    /home/sb/StringStartsWithTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertThat:

assertThat()
############

もっと複雑なアサーションを行う場合には、
``PHPUnit\Framework\Constraint`` クラスを使用します。
これらは、``assertThat()`` メソッドを使用して評価されます。
:numref:`appendixes.assertions.assertThat.example` は、
``logicalNot()`` と ``equalTo()``
を用いて ``assertNotEquals()``
と同じアサーションを行う方法を示すものです。

``assertThat(mixed $value, PHPUnit\Framework\Constraint $constraint[, $message = ''])``

``$value`` が ``$constraint`` にマッチしない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: assertThat() の使用法
    :name: appendixes.assertions.assertThat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class BiscuitTest extends TestCase
    {
        public function testEquals()
        {
            $theBiscuit = new Biscuit('Ginger');
            $myBiscuit  = new Biscuit('Ginger');

            $this->assertThat(
              $theBiscuit,
              $this->logicalNot(
                $this->equalTo($myBiscuit)
              )
            );
        }
    }
    ?>

:numref:`appendixes.assertions.assertThat.tables.constraints` に、
使用できる ``PHPUnit\Framework\Constraint`` クラスをまとめます。

.. rst-class:: table
.. list-table:: 制約
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - 制約
      - 意味
    * - ``PHPUnit\Framework\Constraint\Attribute attribute(PHPUnit\Framework\Constraint $constraint, $attributeName)``
      - 別の制約を、クラスあるいはオブジェクトの属性として適用する制約。
    * - ``PHPUnit\Framework\Constraint\IsAnything anything()``
      - あらゆる入力値を受け入れる制約。
    * - ``PHPUnit\Framework\Constraint\ArrayHasKey arrayHasKey(mixed $key)``
      - 配列が指定したキーを保持していることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\TraversableContains contains(mixed $value)``
      - ``Iterator`` インターフェイスを実装している ``array`` やオブジェクトが、指定した値を保持していることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnly(string $type)``
      - 評価対象の ``array``、あるいは ``Iterator`` インターフェイスを実装したオブジェクトが、指定した型の唯一の値を含むことを保証する制約。
    * - ``PHPUnit\Framework\Constraint\TraversableContainsOnly containsOnlyInstancesOf(string $classname)``
      - 評価対象の ``array``、あるいは ``Iterator`` インターフェイスを実装したオブジェクトが、指定したクラスの唯一のインスタンスを含むことを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsEqual equalTo($value, $delta = 0, $maxDepth = 10)``
      - ある値が別の値と等しいかどうかを調べる制約。
    * - ``PHPUnit\Framework\Constraint\Attribute attributeEqualTo($attributeName, $value, $delta = 0, $maxDepth = 10)``
      - ある値がクラスあるいはオブジェクトの属性と等しいかどうかを調べる制約。
    * - ``PHPUnit\Framework\Constraint\DirectoryExists directoryExists()``
      - 指定した名前のディレクトリが存在するかどうかを調べる制約。
    * - ``PHPUnit\Framework\Constraint\FileExists fileExists()``
      - 指定した名前のファイルが存在するかどうかを調べる制約。
    * - ``PHPUnit\Framework\Constraint\IsReadable isReadable()``
      - 指定した名前のファイルが読み込み可能かどうかを調べる制約。
    * - ``PHPUnit\Framework\Constraint\IsWritable isWritable()``
      - 指定した名前のファイルが書き込み可能かどうかを調べる制約。
    * - ``PHPUnit\Framework\Constraint\GreaterThan greaterThan(mixed $value)``
      - 評価される値が、指定した値より大きいことを保証する制約。
    * - ``PHPUnit\Framework\Constraint\Or greaterThanOrEqual(mixed $value)``
      - 評価される値が、指定した値以上であることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\ClassHasAttribute classHasAttribute(string $attributeName)``
      - 評価されるクラスに、指定した属性があることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\ClassHasStaticAttribute classHasStaticAttribute(string $attributeName)``
      - 評価されるクラスに、指定した static 属性があることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\ObjectHasAttribute objectHasAttribute(string $attributeName)``
      - 評価されるオブジェクトが、指定した属性を保持していることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsIdentical identicalTo(mixed $value)``
      - ある値が別の値と同一であることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsFalse isFalse()``
      - 評価される値が ``false`` であることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsInstanceOf isInstanceOf(string $className)``
      - 評価されるオブジェクトが、指定したクラスのインスタンスであることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsNull isNull()``
      - 評価される値が ``NULL`` であることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsTrue isTrue()``
      - 評価される値が ``true`` であることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\IsType isType(string $type)``
      - 評価される値が、指定した型であることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\LessThan lessThan(mixed $value)``
      - 評価される値が、指定した値より小さいことを保証する制約。
    * - ``PHPUnit\Framework\Constraint\Or lessThanOrEqual(mixed $value)``
      - 評価される値が、指定した値以下であることを保証する制約。
    * - ``logicalAnd()``
      - 論理積 (AND)。
    * - ``logicalNot(PHPUnit\Framework\Constraint $constraint)``
      - 論理否定 (NOT)。
    * - ``logicalOr()``
      - 論理和 (OR)。
    * - ``logicalXor()``
      - 排他的論理和 (XOR)。
    * - ``PHPUnit\Framework\Constraint\PCREMatch matchesRegularExpression(string $pattern)``
      - 評価される文字列が、正規表現にマッチすることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\StringContains stringContains(string $string, bool $case)``
      - 評価される文字列が、指定した文字列を含むことを保証する制約。
    * - ``PHPUnit\Framework\Constraint\StringEndsWith stringEndsWith(string $suffix)``
      - 評価される文字列が、指定したサフィックスで終わることを保証する制約。
    * - ``PHPUnit\Framework\Constraint\StringStartsWith stringStartsWith(string $prefix)``
      - 評価される文字列が、指定したプレフィックスで始まることを保証する制約。

.. _appendixes.assertions.assertTrue:

assertTrue()
############

``assertTrue(bool $condition[, string $message = ''])``

``$condition`` が ``false`` の場合にエラー ``$message`` を報告します。

``assertNotTrue()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertTrue() の使用法
    :name: appendixes.assertions.assertTrue.example

    <?php
    use PHPUnit\Framework\TestCase;

    class TrueTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertTrue(false);
        }
    }
    ?>

.. code-block:: bash

    $ phpunit TrueTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) TrueTest::testFailure
    Failed asserting that false is true.

    /home/sb/TrueTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertXmlFileEqualsXmlFile:

assertXmlFileEqualsXmlFile()
############################

``assertXmlFileEqualsXmlFile(string $expectedFile, string $actualFile[, string $message = ''])``

``$actualFile`` の XML ドキュメントが ``$expectedFile`` の XML ドキュメントと異なる場合にエラー ``$message`` を報告します。

``assertXmlFileNotEqualsXmlFile()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertXmlFileEqualsXmlFile() の使用法
    :name: appendixes.assertions.assertXmlFileEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlFileEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlFileEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 3, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlFile:

assertXmlStringEqualsXmlFile()
##############################

``assertXmlStringEqualsXmlFile(string $expectedFile, string $actualXml[, string $message = ''])``

``$actualXml`` の XML ドキュメントが ``$expectedFile`` の XML ドキュメントと異なる場合にエラー ``$message`` を報告します。

``assertXmlStringNotEqualsXmlFile()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertXmlStringEqualsXmlFile() の使用法
    :name: appendixes.assertions.assertXmlStringEqualsXmlFile.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.25Mb

    There was 1 failure:

    1) XmlStringEqualsXmlFileTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlFileTest.php:7

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

.. _appendixes.assertions.assertXmlStringEqualsXmlString:

assertXmlStringEqualsXmlString()
################################

``assertXmlStringEqualsXmlString(string $expectedXml, string $actualXml[, string $message = ''])``

``$actualXml`` の XML ドキュメントが ``$expectedXml`` の XML ドキュメントと異なる場合にエラー ``$message`` を報告します。

``assertXmlStringNotEqualsXmlString()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertXmlStringEqualsXmlString() の使用法
    :name: appendixes.assertions.assertXmlStringEqualsXmlString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }
    ?>

.. code-block:: bash

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit 7.0.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) XmlStringEqualsXmlStringTest::testFailure
    Failed asserting that two DOM documents are equal.
    --- Expected
    +++ Actual
    @@ @@
     <?xml version="1.0"?>
     <foo>
    -  <bar/>
    +  <baz/>
     </foo>

    /home/sb/XmlStringEqualsXmlStringTest.php:7

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.


