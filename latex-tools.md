---
title: "LaTeXの便利な周辺ツール"
emoji: "👍"
type: "tech"
topics: ["latex", "tex"]
published: true
---

# Make

`.tex`ファイルをコンパイルするのに、コマンドを何回も打つのは面倒です。そこで**Make**を使って簡単にPDFにコンパイルできるようにします。Makeを使うには、以下の形で書かれた`Makefile`をMakeを使いたいディレクトリに配置します。

- `<生成したいファイル>`が`<ファイル>`のタイムスタンプ（最終更新時刻）より古い、もしくは存在しない場合に`<処理>`が実行されます
- `<処理>`は必ずインデントする必要があります
- `<処理>`は上の行から行われます
- インデントは`tab`のみしか受け付けません

```make
<生成したいファイル>: <ファイル>
	<処理>
```

そしてコマンドラインで`make`と打ち込んで実行すれば、`Makefile`に書いた処理を実行してくれます。例えば以下のように書くと、`test.pdf`のタイムスタンプが`test.tex`か`test.sty`よりも古い時に、`ptex2pdf -l test.tex`と`open test.pdf`がコマンドラインで実行されます。

```make
test.pdf: test.tex test.sty
	ptex2pdf -l test.tex
	open test.pdf
```

他にもMakeは様々なことができるので調べてみてください。

# Pandoc

**Pandoc**は、ドキュメント変換ツールです。LaTeX, Markdown, HTML, Word... とかなり沢山のファイル形式の変換に対応しています。詳しくは公式のドキュメントなどを読んで欲しいのですが、MarkdownからLaTeXやPDFへの変換が便利なので紹介します。

例えば以下のように書くと、`<元のファイル>`から`<生成するファイル>`が生成されます。変換は生成するファイルのファイル形式に依存します。

```shell
$ pandoc <元のファイル> -o <生成するファイル>
```

例えば、以下のように書くと、`test.md`から`test.tex`が生成されます。

```shell
$ pandoc test.md -o test.tex
```

面白い使い方として、以下のように書くと、`<元のファイル>`から`<生成されるファイル（PDF）>`が作られます。その時のコンパイルが`ltjsarticle`と`lualatex`を使ったものになります。

```shell
$ pandoc <元のファイル> -o <生成するファイル（PDF）> -V documentclass=ltjsarticle --pdf-engine=lualatex
```

# 参考資料

- [LaTeX2e美文書作成入門［改訂第7版］](https://www.amazon.co.jp/%E6%94%B9%E8%A8%82%E7%AC%AC7%E7%89%88-LaTeX2%CE%B5%E7%BE%8E%E6%96%87%E6%9B%B8%E4%BD%9C%E6%88%90%E5%85%A5%E9%96%80-%E5%A5%A5%E6%9D%91-%E6%99%B4%E5%BD%A6/dp/4774187054)
- [Pandoc ユーザーズガイド 日本語版](http://sky-y.github.io/site-pandoc-jp/users-guide/)
- [多様なフォーマットに対応！ドキュメント変換ツールPandocを知ろう](https://qiita.com/sky_y/items/80bcd0f353ef5b8980ee)（Qiita）