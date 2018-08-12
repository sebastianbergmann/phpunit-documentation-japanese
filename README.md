# 翻訳

（日本語以外の）他言語版のドキュメントは、それぞれ個別のリポジトリで管理されています。

* [英語](https://github.com/sebastianbergmann/phpunit-documentation-english)
* [スペイン語](https://github.com/sebastianbergmann/phpunit-documentation-spanish)
* [フランス語](https://github.com/sebastianbergmann/phpunit-documentation-french)
* [ポルトガル語（ブラジル）](https://github.com/sebastianbergmann/phpunit-documentation-brazilian-portuguese)
* [日本語 ^^;](https://github.com/sebastianbergmann/phpunit-documentation-japanese)
* [簡体字中文](https://github.com/sebastianbergmann/phpunit-documentation-chinese)
* [ロシア語](https://github.com/sebastianbergmann/phpunit-documentation-russian)

## 新しい言語への翻訳

これら以外の言語への翻訳をしたい場合は、英語版ドキュメントのリポジトリにissueを立てて、
何語に翻訳したいのかを知らせてください。新しいリポジトリを用意するので、そこで翻訳を進めていきましょう。

既に英語版をフォークして翻訳を進めているのであれば、用意した公式リポジトリにそれをインポート
するといいでしょう。

# ドキュメントをビルドするには

## 必要なもの

- Python
- [Sphinx](http://www.sphinx-doc.org/)
- [Read the Docs Sphinx Theme](https://github.com/rtfd/sphinx_rtd_theme)

## HTMLのビルド

HTML形式のドキュメント作るには次のコマンドを実行します。

    make html

# 出力先

HTMLファイルは `build/html` ディレクトリに出力されます。
