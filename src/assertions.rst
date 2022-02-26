

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
さらに ``assertTrue()`` のような形でグローバルなラッパー関数として使うことさえできてしまいます。

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ArrayHasKeyTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertArrayHasKey('foo', ['bar' => 'baz']);
        }
    }

.. parsed-literal::

    $ phpunit ArrayHasKeyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ClassHasAttributeTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertClassHasAttribute('foo', stdClass::class);
        }
    }

.. parsed-literal::

    $ phpunit ClassHasAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ClassHasAttributeTest::testFailure
    Failed asserting that class "stdClass" has attribute "foo".

    /home/sb/ClassHasAttributeTest.php:6

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ClassHasStaticAttributeTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertClassHasStaticAttribute('foo', stdClass::class);
        }
    }

.. parsed-literal::

    $ phpunit ClassHasStaticAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertContains(mixed $needle, iterable $haystack[, string $message = ''])``

``$needle`` が ``$haystack`` の要素でない場合にエラー ``$message`` を報告します。

``assertNotContains()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertContains() の使用法
    :name: appendixes.assertions.assertContains.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ContainsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertContains(4, [1, 2, 3]);
        }
    }

.. parsed-literal::

    $ phpunit ContainsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ContainsTest::testFailure
    Failed asserting that an array contains 4.

    /home/sb/ContainsTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringContainsString()
#############################

``assertStringContainsString(string $needle, string $haystack[, string $message = ''])``

``$needle`` が ``$haystack`` の部分文字列でない場合にエラー ``$message`` を報告します。

``assertStringNotContainsString()`` はこのアサーションの逆で、同じ引数を取ります。

.. code-block:: php
    :caption: assertStringContainsString() の使用法
    :name: appendixes.assertions.assertStringContainsString.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringContainsStringTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringContainsString('foo', 'bar');
        }
    }

.. parsed-literal::

    $ phpunit StringContainsStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 37 ms, Memory: 6.00 MB

    There was 1 failure:

    1) StringContainsStringTest::testFailure
    Failed asserting that 'bar' contains "foo".

    /home/sb/StringContainsStringTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertStringContainsStringIgnoringCase()
########################################

``assertStringContainsStringIgnoringCase(string $needle, string $haystack[, string $message = ''])``

``$needle`` が ``$haystack`` の部分文字列でない場合にエラー ``$message`` を報告します。

テストにおいて、大文字小文字の違いは無視されます。

``assertStringNotContainsStringIgnoringCase()`` はこのアサーションの逆で、同じ引数を取ります。

.. code-block:: php
    :caption: assertStringContainsStringIgnoringCase() の使用法
    :name: appendixes.assertions.assertStringContainsStringIgnoringCase.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringContainsStringIgnoringCaseTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringContainsStringIgnoringCase('foo', 'bar');
        }
    }

.. parsed-literal::

    $ phpunit StringContainsStringIgnoringCaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 40 ms, Memory: 6.00 MB

    There was 1 failure:

    1) StringContainsStringTest::testFailure
    Failed asserting that 'bar' contains "foo".

    /home/sb/StringContainsStringIgnoringCaseTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertContainsOnly:

assertContainsOnly()
####################

``assertContainsOnly(string $type, iterable $haystack[, boolean $isNativeType = null, string $message = ''])``

``$haystack`` の中身の型が ``$type`` だけではない場合にエラー ``$message`` を報告します。

``$isNativeType`` はフラグで、``$type`` がネイティブな PHP の型であるかどうかを表します。

``assertNotContainsOnly()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertContainsOnly() の使用法
    :name: appendixes.assertions.assertContainsOnly.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ContainsOnlyTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertContainsOnly('string', ['1', '2', 3]);
        }
    }

.. parsed-literal::

    $ phpunit ContainsOnlyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ContainsOnlyInstancesOfTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertContainsOnlyInstancesOf(
                Foo::class,
                [new Foo, new Bar, new Foo]
            );
        }
    }

.. parsed-literal::

    $ phpunit ContainsOnlyInstancesOfTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class CountTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertCount(0, ['foo']);
        }
    }

.. parsed-literal::

    $ phpunit CountTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertDirectoryDoesNotExist()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertDirectoryExists() の使用法
    :name: appendixes.assertions.assertDirectoryExists.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DirectoryExistsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertDirectoryExists('/path/to/directory');
        }
    }

.. parsed-literal::

    $ phpunit DirectoryExistsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertDirectoryIsNotReadable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertDirectoryIsReadable() の使用法
    :name: appendixes.assertions.assertDirectoryIsReadable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DirectoryIsReadableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertDirectoryIsReadable('/path/to/directory');
        }
    }

.. parsed-literal::

    $ phpunit DirectoryIsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertDirectoryIsNotWritable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertDirectoryIsWritable() の使用法
    :name: appendixes.assertions.assertDirectoryIsWritable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class DirectoryIsWritableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertDirectoryIsWritable('/path/to/directory');
        }
    }

.. parsed-literal::

    $ phpunit DirectoryIsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertEmpty() の使用法
    :name: appendixes.assertions.assertEmpty.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EmptyTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertEmpty(['foo']);
        }
    }

.. parsed-literal::

    $ phpunit EmptyTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) EmptyTest::testFailure
    Failed asserting that an array is empty.

    /home/sb/EmptyTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertEquals:

assertEquals()
##############

``assertEquals(mixed $expected, mixed $actual[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` が等しくない場合にエラー ``$message`` を報告します。

``assertNotEquals()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertEquals(1, 0);
        }

        public function testFailure2(): void
        {
            $this->assertEquals('bar', 'baz');
        }

        public function testFailure3(): void
        {
            $this->assertEquals("foo\nbar\nbaz\n", "foo\nbah\nbaz\n");
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertEquals(DOMDocument $expected, DOMDocument $actual[, string $message = ''])``

2 つの DOMDocument オブジェクト ``$expected`` と ``$actual`` で表される XML ドキュメントが (コメントを除去して正規化した状態で) 等しくない場合にエラー ``$message`` を報告します。

.. code-block:: php
    :caption: DOMDocument オブジェクトでの assertEquals() の使用法
    :name: appendixes.assertions.assertEquals.example3

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $expected = new DOMDocument;
            $expected->loadXML('<foo><bar/></foo>');

            $actual = new DOMDocument;
            $actual->loadXML('<bar><foo/></bar>');

            $this->assertEquals($expected, $actual);
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
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

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertEquals(['a', 'b', 'c'], ['a', 'c', 'd']);
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

assertEqualsCanonicalizing()
############################

``assertEqualsCanonicalizing(mixed $expected, mixed $actual[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` が等しくない場合にエラー ``$message`` を報告します。

``$expected`` と ``$actual`` の内容は，比較される前に正規化されます。例えば，2つの変数 ``$expected`` と ``$actual`` が配列である場合には，これらの配列は比較される前にソートされます．また， ``$expected`` と ``$actual`` がオブジェクトである場合，各オブジェクトは，すべての private, protected, public 属性を含む配列に変換されます．

``assertNotEqualsCanonicalizing()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: Usage of assertEqualsCanonicalizing()
    :name: appendixes.assertions.assertEqualsCanonicalizing.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsCanonicalizingTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEqualsCanonicalizing([3, 2, 1], [2, 3, 0, 1]);
        }
    }

.. parsed-literal::

    $ phpunit EqualsCanonicalizingTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 42 ms, Memory: 6.00 MB

    There was 1 failure:

    1) EqualsCanonicalizingTest::testFailure
    Failed asserting that two arrays are equal.
    --- Expected
    +++ Actual
    @@ @@
     Array (
    -    0 => 1
    -    1 => 2
    -    2 => 3
    +    0 => 0
    +    1 => 1
    +    2 => 2
    +    3 => 3
     )

    /home/sb/EqualsCanonicalizingTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEqualsIgnoringCase()
##########################

``assertEqualsIgnoringCase(mixed $expected, mixed $actual[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` が等しくない場合にエラー ``$message`` を報告します。

``$expected`` と ``$actual`` の比較において、大文字小文字の違いは無視されます。

``assertNotEqualsIgnoringCase()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: Usage of assertEqualsIgnoringCase()
    :name: appendixes.assertions.assertEqualsIgnoringCase.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsIgnoringCaseTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEqualsIgnoringCase('foo', 'BAR');
        }
    }

.. parsed-literal::

    $ phpunit EqualsIgnoringCaseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 51 ms, Memory: 6.00 MB

    There was 1 failure:

    1) EqualsIgnoringCaseTest::testFailure
    Failed asserting that two strings are equal.
    --- Expected
    +++ Actual
    @@ @@
    -'foo'
    +'BAR'

    /home/sb/EqualsIgnoringCaseTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertEqualsWithDelta()
#######################

``assertEqualsWithDelta(mixed $expected, mixed $actual, float $delta[, string $message = ''])``

2 つの変数 ``$expected`` と ``$actual`` の差の絶対値が ``$delta`` よりも大きい場合にエラー ``$message`` を報告します。

なぜ ``$delta`` が必要なのかについては、 "`What Every Computer Scientist Should Know About Floating-Point Arithmetic <http://docs.oracle.com/cd/E19957-01/806-3568/ncg_goldberg.html>`_"
を参照してください。

``assertNotEqualsWithDelta()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: Usage of assertEqualsWithDelta()
    :name: appendixes.assertions.assertEqualsWithDelta.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class EqualsWithDeltaTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertEqualsWithDelta(1.0, 1.5, 0.1);
        }
    }

.. parsed-literal::

    $ phpunit EqualsWithDeltaTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 41 ms, Memory: 6.00 MB

    There was 1 failure:

    1) EqualsWithDeltaTest::testFailure
    Failed asserting that 1.5 matches expected 1.0.

    /home/sb/EqualsWithDeltaTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertObjectEquals:

assertObjectEquals()
####################

``assertObjectEquals(object $expected, object $actual, string $method = 'equals', string $message = ''])``

``$actual->$method($expected)`` の結果から、 ``$actual`` と ``$expected`` が等しくない場合にエラー ``$message`` を報告します。

``assertEquals()`` (およびこのアサーションの逆である ``assertNotEquals()``) を、自身の比較方法をカスタマイズする custom comparator を用意していないオブジェクトに使うのは推奨されません。手間はかかりますが、アサートを行いたい全てのオブジェクトにそれぞれ custom comparator を実装するのがベストです。

custom comparator の最も一般的なユースケースは、Value Objects です。これらのオブジェクトは通常、2つの値オブジェクト同士を比較するための ``equals(self $other): bool`` メソッド(または似たような名前のメソッド)を持っています。 ``assertObjectEquals()`` はこのようなユースケースにおいて、オブジェクトの比較を簡単に行えるようにします。

.. code-block:: php
    :caption: Usage of assertObjectEquals()
    :name: appendixes.assertions.assertObjectEquals.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SomethingThatUsesEmailTest extends TestCase
    {
        public function testSomething(): void
        {
            $a = new Email('user@example.org');
            $b = new Email('user@example.org');
            $c = new Email('user@example.com');

            // This passes
            $this->assertObjectEquals($a, $b);

            // This fails
            $this->assertObjectEquals($a, $c);
        }
    }

.. code-block:: php
    :caption: Email value object with equals() method
    :name: appendixes.assertions.Email.example

    <?php declare(strict_types=1);
    final class Email
    {
        private string $email;

        public function __construct(string $email)
        {
            $this->ensureIsValidEmail($email);

            $this->email = $email;
        }

        public function asString(): string
        {
            return $this->email;
        }

        public function equals(self $other): bool
        {
            return $this->asString() === $other->asString();
        }

        private function ensureIsValidEmail(string $email): void
        {
            // ...
        }
    }

.. parsed-literal::

    $ phpunit EqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F                                                                   1 / 1 (100%)

    Time: 00:00.017, Memory: 4.00 MB

    There was 1 failure:

    1) SomethingThatUsesEmailTest::testSomething
    Failed asserting that two objects are equal.
    The objects are not equal according to Email::equals().

    /home/sb/SomethingThatUsesEmailTest.php:16

    FAILURES!
    Tests: 1, Assertions: 2, Failures: 1.

注意点:

* ``$method`` という名称のメソッドが、 ``$actual`` のオブジェクトに定義されている必要があります。
* その定義されたメソッドは、引数をひとつだけ取るよう定義されている必要があります。
* 引数や返り値は、型を宣言されている必要があります。
* ``$expected`` オブジェクトはその宣言された型に一致する必要があります。
* そのメソッドは、返り値の型として ``bool`` が宣言されている必要があります。

上記の前提が満たされていない場合、 ``$actual->$method($expected)`` は ``false`` を返し、 アサーションは失敗します。

.. _appendixes.assertions.assertFalse:

assertFalse()
#############

``assertFalse(bool $condition[, string $message = ''])``

``$condition`` が ``true`` の場合にエラー ``$message`` を報告します。

``assertNotFalse()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFalse() の使用法
    :name: appendixes.assertions.assertFalse.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FalseTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFalse(true);
        }
    }

.. parsed-literal::

    $ phpunit FalseTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileEqualsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileEquals('/home/sb/expected', '/home/sb/actual');
        }
    }

.. parsed-literal::

    $ phpunit FileEqualsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertFileDoesNotExist()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileExists() の使用法
    :name: appendixes.assertions.assertFileExists.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileExistsTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileExists('/path/to/file');
        }
    }

.. parsed-literal::

    $ phpunit FileExistsTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertFileIsNotReadable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileIsReadable() の使用法
    :name: appendixes.assertions.assertFileIsReadable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileIsReadableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileIsReadable('/path/to/file');
        }
    }

.. parsed-literal::

    $ phpunit FileIsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertFileIsNotWritable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertFileIsWritable() の使用法
    :name: appendixes.assertions.assertFileIsWritable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class FileIsWritableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertFileIsWritable('/path/to/file');
        }
    }

.. parsed-literal::

    $ phpunit FileIsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertGreaterThan() の使用法
    :name: appendixes.assertions.assertGreaterThan.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class GreaterThanTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertGreaterThan(2, 1);
        }
    }

.. parsed-literal::

    $ phpunit GreaterThanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertGreaterThanOrEqual() の使用法
    :name: appendixes.assertions.assertGreaterThanOrEqual.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class GreatThanOrEqualTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertGreaterThanOrEqual(2, 1);
        }
    }

.. parsed-literal::

    $ phpunit GreaterThanOrEqualTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class InfiniteTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertInfinite(1);
        }
    }

.. parsed-literal::

    $ phpunit InfiniteTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertInstanceOf() の使用法
    :name: appendixes.assertions.assertInstanceOf.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class InstanceOfTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertInstanceOf(RuntimeException::class, new Exception);
        }
    }

.. parsed-literal::

    $ phpunit InstanceOfTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) InstanceOfTest::testFailure
    Failed asserting that Exception Object (...) is an instance of class "RuntimeException".

    /home/sb/InstanceOfTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsArray()
###############

``assertIsArray($actual[, $message = ''])``

``$actual`` の型が ``array`` でない場合にエラー ``$message`` を報告します。

``assertIsNotArray()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsArray() の使用法
    :name: appendixes.assertions.assertIsArray.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ArrayTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsArray(null);
        }
    }

.. code-block:: bash

    $ phpunit ArrayTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ArrayTest::testFailure
    Failed asserting that null is of type "array".

    /home/sb/ArrayTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsBool()
##############

``assertIsBool($actual[, $message = ''])``

``$actual`` の型が ``bool`` でない場合にエラー ``$message`` を報告します。

``assertIsNotBool()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsBool() の使用法
    :name: appendixes.assertions.assertIsBool.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class BoolTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertIsBool(null);
        }
    }

.. code-block:: bash

    $ phpunit BoolTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) BoolTest::testFailure
    Failed asserting that null is of type "bool".

    /home/sb/BoolTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsCallable()
##################

``assertIsCallable($actual[, $message = ''])``

``$actual`` の型が ``callable`` でない場合にエラー ``$message`` を報告します。

``assertIsNotCallable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsCallable() の使用法
    :name: appendixes.assertions.assertIsCallable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class CallableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsCallable(null);
        }
    }

.. code-block:: bash

    $ phpunit CallableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) CallableTest::testFailure
    Failed asserting that null is of type "callable".

    /home/sb/CallableTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsFloat()
###############

``assertIsFloat($actual[, $message = ''])``

``$actual`` の型が ``float`` でない場合にエラー ``$message`` を報告します。

``assertIsNotFloat()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsFloat() の使用法
    :name: appendixes.assertions.assertIsFloat.example

    <?php
    use PHPUnit\Framework\TestCase;

    class FloatTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsFloat(null);
        }
    }

.. code-block:: bash

    $ phpunit FloatTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) FloatTest::testFailure
    Failed asserting that null is of type "float".

    /home/sb/FloatTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsInt()
#############

``assertIsInt($actual[, $message = ''])``

``$actual`` の型が ``int`` でない場合にエラー ``$message`` を報告します。

``assertIsNotInt()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsInt() の使用法
    :name: appendixes.assertions.assertIsInt.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IntTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsInt(null);
        }
    }

.. code-block:: bash

    $ phpunit IntTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) IntTest::testFailure
    Failed asserting that null is of type "int".

    /home/sb/IntTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsIterable()
##################

``assertIsIterable($actual[, $message = ''])``

``$actual`` の型が ``iterable`` でない場合にエラー ``$message`` を報告します。

``assertIsNotIterable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsIterable() の使用法
    :name: appendixes.assertions.assertIsIterable.example

    <?php
    use PHPUnit\Framework\TestCase;

    class IterableTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsIterable(null);
        }
    }

.. code-block:: bash

    $ phpunit IterableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) IterableTest::testFailure
    Failed asserting that null is of type "iterable".

    /home/sb/IterableTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsNumeric()
#################

``assertIsNumeric($actual[, $message = ''])``

``$actual`` の型が ``numeric`` でない場合にエラー ``$message`` を報告します。

``assertIsNotNumeric()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsNumeric() の使用法
    :name: appendixes.assertions.assertIsNumeric.example

    <?php
    use PHPUnit\Framework\TestCase;

    class NumericTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsNumeric(null);
        }
    }

.. code-block:: bash

    $ phpunit NumericTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) NumericTest::testFailure
    Failed asserting that null is of type "numeric".

    /home/sb/NumericTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsObject()
################

``assertIsObject($actual[, $message = ''])``

``$actual`` の型が ``object`` でない場合にエラー ``$message`` を報告します。

``assertIsNotObject()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsObject() の使用法
    :name: appendixes.assertions.assertIsObject.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ObjectTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsObject(null);
        }
    }

.. code-block:: bash

    $ phpunit ObjectTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ObjectTest::testFailure
    Failed asserting that null is of type "object".

    /home/sb/ObjectTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsResource()
##################

``assertIsResource($actual[, $message = ''])``

``$actual`` の型が ``resource`` でない場合にエラー ``$message`` を報告します。

``assertIsNotResource()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsResource() の使用法
    :name: appendixes.assertions.assertIsResource.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ResourceTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsResource(null);
        }
    }

.. code-block:: bash

    $ phpunit ResourceTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ResourceTest::testFailure
    Failed asserting that null is of type "resource".

    /home/sb/ResourceTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsScalar()
################

``assertIsScalar($actual[, $message = ''])``

``$actual`` の型が ``scalar`` でない場合にエラー ``$message`` を報告します。

``assertIsNotScalar()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsScalar() の使用法
    :name: appendixes.assertions.assertIsScalar.example

    <?php
    use PHPUnit\Framework\TestCase;

    class ScalarTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsScalar(null);
        }
    }

.. code-block:: bash

    $ phpunit ScalarTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) ScalarTest::testFailure
    Failed asserting that null is of type "scalar".

    /home/sb/ScalarTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

assertIsString()
################

``assertIsString($actual[, $message = ''])``

``$actual`` の型が ``string`` でない場合にエラー ``$message`` を報告します。

``assertIsNotString()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsString() の使用法
    :name: appendixes.assertions.assertIsString.example

    <?php
    use PHPUnit\Framework\TestCase;

    class StringTest extends TestCase
    {
        public function testFailure()
        {
            $this->assertIsString(null);
        }
    }

.. code-block:: bash

    $ phpunit StringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 5.00Mb

    There was 1 failure:

    1) StringTest::testFailure
    Failed asserting that null is of type "string".

    /home/sb/StringTest.php:8

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertIsReadable:

assertIsReadable()
##################

``assertIsReadable(string $filename[, string $message = ''])``

``$filename`` で指定したファイルあるいはディレクトリが読み込み可能でない場合にエラー ``$message`` を報告します。

``assertIsNotReadable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsReadable() の使用法
    :name: appendixes.assertions.assertIsReadable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class IsReadableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertIsReadable('/path/to/unreadable');
        }
    }

.. parsed-literal::

    $ phpunit IsReadableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

``assertIsNotWritable()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertIsWritable() の使用法
    :name: appendixes.assertions.assertIsWritable.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class IsWritableTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertIsWritable('/path/to/unwritable');
        }
    }

.. parsed-literal::

    $ phpunit IsWritableTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class JsonFileEqualsJsonFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertJsonFileEqualsJsonFile(
              'path/to/fixture/file', 'path/to/actual/file');
        }
    }

.. parsed-literal::

    $ phpunit JsonFileEqualsJsonFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class JsonStringEqualsJsonFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertJsonStringEqualsJsonFile(
                'path/to/fixture/file', json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. parsed-literal::

    $ phpunit JsonStringEqualsJsonFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class JsonStringEqualsJsonStringTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertJsonStringEqualsJsonString(
                json_encode(['Mascot' => 'Tux']),
                json_encode(['Mascot' => 'ux'])
            );
        }
    }

.. parsed-literal::

    $ phpunit JsonStringEqualsJsonStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertLessThan() の使用法
    :name: appendixes.assertions.assertLessThan.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class LessThanTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertLessThan(1, 2);
        }
    }

.. parsed-literal::

    $ phpunit LessThanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertLessThanOrEqual() の使用法
    :name: appendixes.assertions.assertLessThanOrEqual.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class LessThanOrEqualTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertLessThanOrEqual(1, 2);
        }
    }

.. parsed-literal::

    $ phpunit LessThanOrEqualTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class NanTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertNan(1);
        }
    }

.. parsed-literal::

    $ phpunit NanTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class NullTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertNull('foo');
        }
    }

.. parsed-literal::

    $ phpunit NotNullTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class ObjectHasAttributeTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertObjectHasAttribute('foo', new stdClass);
        }
    }

.. parsed-literal::

    $ phpunit ObjectHasAttributeTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

    F

    Time: 0 seconds, Memory: 4.75Mb

    There was 1 failure:

    1) ObjectHasAttributeTest::testFailure
    Failed asserting that object of class "stdClass" has attribute "foo".

    /home/sb/ObjectHasAttributeTest.php:6

    FAILURES!
    Tests: 1, Assertions: 1, Failures: 1.

.. _appendixes.assertions.assertMatchesRegularExpression:

assertMatchesRegularExpression()
################################

``assertMatchesRegularExpression(string $pattern, string $string[, string $message = ''])``

``$string`` が正規表現 ``$pattern`` にマッチしない場合にエラー ``$message`` を報告します。

``assertDoesNotMatchRegularExpression()`` はこのアサーションの逆で、同じ引数をとります。

.. code-block:: php
    :caption: assertMatchesRegularExpression() の使用法
    :name: appendixes.assertions.assertMatchesRegularExpression.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class RegExpTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertMatchesRegularExpression('/foo/', 'bar');
        }
    }

.. parsed-literal::

    $ phpunit RegExpTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringMatchesFormatTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringMatchesFormat('%i', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringMatchesFormatTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringMatchesFormatFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringMatchesFormatFile('/path/to/expected.txt', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringMatchesFormatFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

.. code-block:: php
    :caption: assertSame() の使用法
    :name: appendixes.assertions.assertSame.example

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SameTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertSame('2204', 2204);
        }
    }

.. parsed-literal::

    $ phpunit SameTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class SameTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertSame(new stdClass, new stdClass);
        }
    }

.. parsed-literal::

    $ phpunit SameTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringEndsWithTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringEndsWith('suffix', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringEndsWithTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringEqualsFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringEqualsFile('/home/sb/expected', 'actual');
        }
    }

.. parsed-literal::

    $ phpunit StringEqualsFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class StringStartsWithTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertStringStartsWith('prefix', 'foo');
        }
    }

.. parsed-literal::

    $ phpunit StringStartsWithTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class BiscuitTest extends TestCase
    {
        public function testEquals(): void
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

:numref:`appendixes.assertions.assertThat.tables.constraints` に、
使用できる ``PHPUnit\Framework\Constraint`` クラスをまとめます。

.. rst-class:: table
.. list-table:: 制約
    :name: appendixes.assertions.assertThat.tables.constraints
    :header-rows: 1

    * - 制約
      - 意味
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
    * - ``PHPUnit\Framework\Constraint\LogicalOr greaterThanOrEqual(mixed $value)``
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
    * - ``PHPUnit\Framework\Constraint\LogicalOr lessThanOrEqual(mixed $value)``
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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class TrueTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertTrue(false);
        }
    }

.. parsed-literal::

    $ phpunit TrueTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class XmlFileEqualsXmlFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertXmlFileEqualsXmlFile(
              '/home/sb/expected.xml', '/home/sb/actual.xml');
        }
    }

.. parsed-literal::

    $ phpunit XmlFileEqualsXmlFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class XmlStringEqualsXmlFileTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertXmlStringEqualsXmlFile(
              '/home/sb/expected.xml', '<foo><baz/></foo>');
        }
    }

.. parsed-literal::

    $ phpunit XmlStringEqualsXmlFileTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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

    <?php declare(strict_types=1);
    use PHPUnit\Framework\TestCase;

    final class XmlStringEqualsXmlStringTest extends TestCase
    {
        public function testFailure(): void
        {
            $this->assertXmlStringEqualsXmlString(
              '<foo><bar/></foo>', '<foo><baz/></foo>');
        }
    }

.. parsed-literal::

    $ phpunit XmlStringEqualsXmlStringTest
    PHPUnit |version|.0 by Sebastian Bergmann and contributors.

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


