---
title: "LaTeX のスタイルファイルを自作する"
emoji: "📋"
type: "tech"
topics: ["latex", "tex"]
published: true
---

:::message
間違いなどがあったら教えてください。
筆者の環境は TeXLive 2021 です。
:::

スタイルファイルとは、LaTeX のマクロパッケージことで、
ドキュメントクラス（`.cls`）やパッケージ（`.sty`）もその一種です。
スタイルファイルを使うことで、同じコマンドを複数の文書で使い回せるようになります。

# 書き方

jsclasses などは、DOCSTRIP というプログラムを使い、

1. コードに説明が付いた`.dtx`ファイルを作成
2. `.dtx`ファイルをコンパイルして、説明付き PDF と`.sty`ファイルを出力

のようにして作成されています。
しかし、このやり方は複雑なので、`.sty`ファイルを直に書く方法を紹介します。

スタイルファイルの中身はプリアンブルと同じように書くことができますが、
`\makeatletter`や`\makeatother`を書く必要はありません。

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

# TeX Live に認識させる

スタイルファイルを作っただけでは、そのディレクトリ以外で使うことが出来ません。
そこで、TeX Live に認識させます。

TeX Live でユーザーのスタイルファイルを入れるディレクトリは`$TEXMFLOCAL/tex/latex/`、
すなわち`texlive/texmf-local/tex/latex/`です
^[スタイルファイルを入れることのできるディレクトリは他にもあります。詳細は TeX Live ガイドを参照してください。]。
手順は以下のようになります。

1. 前述のディレクトリにファイルを保存もしくはシンボリックリンクを作成
2. コマンドラインにて`mktexlsr`を実行（管理者権限が必要）

# パッケージ・クラスファイル向けのコマンド

- `\NeedsTeXFormat{LaTeX2e}`^[2021/04/14 誤植の指摘を受け修正しました。]\
  必要な LaTeX の形態として `LaTeX2e` を指定します。
  `LaTeX2e` の代わりに `pLaTeX2e` などを指定することもできます。
- `\ProvidesPackage{<パッケージ名>}[<日付>]`\
  パッケージの名前（呼び出し名）を定義します。`<日付>` より LaTeX のバージョンが古い場合に
  警告を出すことができます。`<日付>` は、`2020/12/28` のように指定します。
- `\endinput`\
  この命令よりも後の記述を無視します。

# 参考資料

- [［改訂第 7 版］LaTeX2e 美文書作成入門（Amazon）](https://www.amazon.co.jp/%E6%94%B9%E8%A8%82%E7%AC%AC7%E7%89%88-LaTeX2%CE%B5%E7%BE%8E%E6%96%87%E6%9B%B8%E4%BD%9C%E6%88%90%E5%85%A5%E9%96%80-%E5%A5%A5%E6%9D%91-%E6%99%B4%E5%BD%A6/dp/4774187054)
- [独習 LaTeX2e（Amazon）](https://www.amazon.co.jp/%E7%8B%AC%E7%BF%92-LaTeX2%CE%B5-%E5%90%89%E6%B0%B8-%E5%BE%B9%E7%BE%8E/dp/4798115363)
- [TeX Live ガイド（GitHub）](https://github.com/wtsnjp/texlive-ja)
- [LaTeX2e unofficial manual](https://latexref.xyz/_005cendinput.html)
