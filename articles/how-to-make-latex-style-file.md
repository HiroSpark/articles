---
title: "LaTeX のスタイルファイルを自作する"
emoji: "📋"
type: "tech"
topics: ["latex", "tex"]
published: true
---

スタイルファイルとは、LaTeX のマクロパッケージのことで、ドキュメントクラス（`.cls`）やパッケージ（`.sty`）もその一種です。スタイルファイルを使うことで、同じコマンドを複数の文書で使い回せるようになります。

:::message
筆者の環境は TeXLive 2020 です。
:::

# 書き方

jsclasses[^jsclasses]などは、Docstrip というプログラムを使って、

[^jsclasses]: `jsarticle`や`jsbook`などの「pLaTeX2e 新ドキュメントクラス」のことです。

1. コードに説明が付いた`.dtx`ファイルを作成
2. `.dtx`ファイルをコンパイルして、説明付き PDF と`.sty`ファイルを出力

のようにして作成されています。しかし、このやり方は複雑なので、`.sty`ファイルを直に書く方法を紹介します。

スタイルファイルの中身はプリアンブルと同じように書くことができますが、`\makeatletter`や`\makeatother`を書く必要はありません。

以上の事を踏まえたスタイルファイルは次のようになります。

```latex:mypkg.sty
% パッケージの読み込み
\usepackage{graphicx}

% jlreqの見出しをカスタマイズ
\NewBlockHeading{section}{1}{%
    font={\normalfont\fontsize{32Q}{32H}\bfseries},
    before_space=0mm,
    after_space=8mm,
    align=left
}
```

このスタイルファイルをパッケージとして呼ぶには、このスタイルファイルを使いたいソースのプリアンブルに、

```latex
\usepackage{mypkg}
```

のように書きます。

# スタイルファイル向けのコマンド

スタイルファイルで使うと便利、もしくは使われてるのを見かけるコマンドです。

- `\NeedsTeXFormat{LaTeX2e}`[^typo]\
  必要な LaTeX の形態として`LaTeX2e`を指定します。`LaTeX2e`の代わりに`pLaTeX2e`などを指定することもできます。
- `\ProvidesPackage{<パッケージ名>}[<日付>]`\
  パッケージの名前を定義します。`<日付>`より LaTeX のバージョンが古い場合に警告を出すことができます。`<日付>`は、`2020/12/28`のように指定します。
- `\ProvidesClass{<クラス名>}[<日付>]`\
  クラスの名前を定義します。他は`\ProvidesPackage`と同じです。
- `\LoadClass[<オプション>]{<クラス名>}`\
  クラスを読み込みます。自分用のクラスを作る時に便利で、これを使うと最初の例は以下のようになります。
  ```latex:mycls.cls
  \LoadClass{jlreq}
  \RequirePackage{graphicx}
  \NewBlockHeading{section}{1}{%
      font={\normalfont\fontsize{32Q}{32H}\bfseries},
      before_space=0mm,
      after_space=8mm,
      align=left
  }
  ```
  ```latex
  \documentclass{mycls}
  ```
- `\endinput`\
  この命令よりも後の記述を無視します。

さらに詳しい内容を知りたい方は、[クラス・パッケージ作者のためのLaTeX 2ε（抄訳）](https://qiita.com/takahashim/items/5fcc68fbc8a3eca42f07)を読んでみてください。

[^typo]: 2021/04/14 誤植の指摘を受け修正しました。

# TeX Liveに認識させる

スタイルファイルを作っただけでは、そのディレクトリ[^folder]以外で使うことが出来ません。そこで、TeX Live に認識させます。

TeX Live でユーザーのスタイルファイルを入れるディレクトリは`$TEXMFLOCAL/tex/latex/`、すなわち`texlive/texmf-local/tex/latex/`です[^directory]。手順は以下のようになります。

1. 前述のディレクトリにファイルを保存もしくはシンボリックリンクを作成
1. コマンドラインにて`mktexlsr`を実行（管理者権限が必要[^permission]）

[^folder]: いわゆるフォルダのことです（厳密には少し違います）。
[^directory]: スタイルファイルを入れることのできるディレクトリは他にもあります。詳細は TeX Live ガイドを参照してください。
[^permission]: Windows なら管理者権限でコマンドプロンプトを起動してから実行してください。UNIX 系なら`sudo`を頭につけて実行してください。

# 参考資料

- LaTeX2e 美文書作成入門［改訂第 7 版］
- 独習 LaTeX2e
- TeX Live ガイド 2020
- [LaTeX2e unofficial manual](https://latexref.xyz/_005cendinput.html)

---

この記事は以下のシリーズの一部です。

https://zenn.dev/hirospark/scraps/549a0863ca408d
