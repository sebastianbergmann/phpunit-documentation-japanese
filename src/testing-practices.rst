

.. _testing-practices:

=======
テストの進め方
=======

    *Erich Gamma*:

    You can always write more tests. However, you will quickly find that
    only a fraction of the tests you can imagine are actually useful. What
    you want is to write tests that fail even though you think they should
    work, or tests that succeed even though you think they should fail.
    Another way to think of it is in cost/benefit terms. You want to write
    tests that will pay you back with information.

    テストはいくらでも書くことができる。でも、じきにわかるだろうが、
    きみが考えているテストの中で本当に有用なものはごくわずかだ。
    本当に書かなきゃいけないのは、
    これは動くだろうと考えているにもかかわらず失敗するテスト。それから、
    これは失敗するだろうと考えているにもかかわらず実際は成功するテストだ。
    あるいはコストと利益の観点から考えてみてもいいだろう。
    きみに何らかの情報を返してくれるテストを書かないとね。

.. _testing-practices.during-development:

開発中のテスト
#######

開発中のソフトウェアの内部構造を変更し、
わかりやすく変更が簡単なものにする必要が出てきたときのことを考えましょう。
それによってソフトウェアの外部的な振る舞いが変わってしまってはいけません。
この、いわゆる
`リファクタリング <http://martinfowler.com/bliki/DefinitionOfRefactoring.html>`_
`(日本語) <http://capsctrl.que.jp/kdmsnr/wiki/bliki/?DefinitionOfRefactoring>`_
を安全に行うにあたり、テストスイートが非常に重要となります。
もしテストスイートがなければ、リファクタリングによってシステムを壊してしまっても
あなたはそれに気づかないでしょう。

以下の条件が、あなたのプロジェクトのコードや設計を改善するための助けとなるでしょう。
また、ユニットテストを使用することで、リファクタリングによって振る舞いが変化していないこと・
エラーが発生していないことが確認できます。

#.

   すべてのユニットテストが正常に動作すること。

#.

   コードが設計指針を満たしていること。

#.

   コードに冗長性がないこと。

#.

   コードには最小限のクラスおよびメソッドのみが含まれていること。

システムに新しい機能を追加する際には、まず最初にテストを書きます。
そのテストがきちんと実行できるようになった時点で、開発は終了です。
この手法については、次の章で詳しく説明します。

.. _testing-practices.during-debugging:

デバッグ中のテスト
#########

不具合の報告を受けたら、すぐにでもそれを修正したいと思われることでしょう。
しかし、あせって修正しようとしても、経験上なかなかうまくいきません。
不具合を修正したつもりが新たな不具合を引き起こしていたなんてこともありがちですね。

はやる気持ちを抑えて、以下のようにしてみましょう。

#.

   不具合を再現できることを確認します。

#.

   不具合が発生する最小限のコードを見つけます。例えば、
   もしおかしな数値が出力されるのなら、
   その数値を計算しているオブジェクトが何なのかを探します。

#.

   その不具合のせいで今は失敗する (そして、不具合が修正されたら成功する)
   テストを書きます。

#.

   不具合を修正します。

不具合が再現する最小限のコードを見つける過程で、
不具合の原因がわかるかもしれません。テストを書くことによって、
不具合を真の意味で修正できる可能性が高まるでしょう。なぜなら、
テストを書くことで、将来同じ間違いをする可能性を減らせるからです。
これまでに書いたすべてのテストが、
不注意によって別の問題を発生させる可能性を減らすために役立っているのです。

    *Benjamin Smedberg*:

    Unit testing offers many advantages:

    -

      Testing gives code authors and reviewers confidence that patches produce the correct results.

    -

      Authoring testcases is a good impetus for developers to discover edge cases.

    -

      Testing provides a good way to catch regressions quickly, and to make sure that no regression will be repeated twice.

    -

      Unit tests provide working examples for how to use an API and can significantly aid documentation efforts.

    Overall, integrated unit testing makes the cost and risk of any
    individual change smaller. It will allow the project to make \[...]
    major architectural improvements \[...] quickly and confidently.

    ユニットテストには、こんなに多くの利点がある。

    -

      実際にコードを書いた人とコードをレビューする人が意識を共有できるようになり、パッチの精度があがる。

    -

      テストケースを書くことは、いろいろな想定外の事態に備えるための原動力となる。

    -

      テストにより、リグレッションを早期に発見できるようになる。また、同じリグレッションを二度と起こさないようになる。

    -

      ユニットテストそのものが、その API の使用法についてのよいサンプルとなる。ドキュメント作成の際に助けとなるだろう。

    まとめよう。ユニットテストをうまく組み込めば、
    プログラムを変更する際の手間やリスクをより減らすことになるのだ。
    プロジェクトが【中略】のアーキテクチャに関する大きな改修【中略】を素早く、自信を持って行うことを可能にするだろう。


