---
title: "LaTeX のスタイルファイルを自作する"
emoji: "📋"
type: "tech"
topics: ["latex", "tex"]
published: true
---

:::message
間違いなどがあったら教えてください。
筆者の環境はTeXLive 2020です。
:::

# スタイルファイルとは

スタイルファイルとは、LaTeXのマクロパッケージことで、ドキュメントクラス（`.cls`）やパッケージ（`.sty`）もその一種です。スタイルファイルを使うことで、同じコマンドを複数の文書で使い回せるようになります。

# 書き方

jsclasses^[`jsarticle`や`jsbook`などの「LaTeX2e新ドキュメントクラス」のことです。]などは、

1. コードに説明が付いた`.dtx`ファイルを作成
2. `.dtx`ファイルをコンパイルして、説明付きPDFと`.sty`ファイルを出力

のようにして作成されています。しかし、このやり方は複雑なので、`.sty`ファイルを直に書く方法を紹介します。

スタイルファイルの中身はプリアンブルと同じように書くことができますが、`\makeatletter`や`\makeatother`を書く必要はありません。

以上の事を踏まえたスタイルファイルは次のようになります。

```LaTeX
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

```LaTeX
\usepackage{mypkg}
```

のように書きます。

スタイルファイルには以下の命令を記述することもできます（しなくても構いません）。

| 命令 | 説明 |
| :-- | :-- |
| `\NeedsTeXFormat{LaTeX2e}`^[2021/04/14 誤植の指摘を受け修正しました。] | 必要なLaTeXの形態として`LaTeX2e`を指定します。`LaTeX2e`の代わりに`pLaTeX2e`などを指定することもできます。 |
| `\ProvidesPackage{<パッケージ名>}[<日付>]` | パッケージの名前（呼び出し名）を定義します。`<日付>`よりLaTeXのバージョンが古い場合に警告を出すことができます。`<日付>`は、`2020/12/28`のように指定します。 |
| `\endinput` | この命令よりも後の記述を無視します。 |

# TeX Liveに認識させる

スタイルファイルを作っただけでは、そのディレクトリ^[いわゆるフォルダのことです（厳密には少し違います）。]以外で使うことが出来ません。そこで、TeX Liveに認識させます。

TeX Liveでユーザーのスタイルファイルを入れるディレクトリは`$TEXMFLOCAL/tex/latex/`、すなわち`texlive/texmf-local/tex/latex/`です^[スタイルファイルを入れることのできるディレクトリは他にもあります。詳細はTeX Liveガイドを参照してください。]。手順は以下のようになります。

1. 前述のディレクトリにファイルを保存もしくはシンボリックリンクを作成
1. コマンドラインにて`mktexlsr`を実行（管理者権限が必要^[Windowsなら管理者権限でコマンドプロンプトを起動してから実行してください。Unix系なら`sudo`を頭につけて実行してください。]）

# 参考資料

- LaTeX2e美文書作成入門［改訂第7版］
- 独習LaTeX2e
- TeX Liveガイド 2020
- [LaTeX2e unofficial manual](https://latexref.xyz/_005cendinput.html)