---
title: "LaTeXの見出しをカスタマイズする"
emoji: "🔧"
type: "tech"
topics: ["latex", "tex"]
published: true
---

見出しのカスタマイズは、自分好みの文書を作るのに必須ともいえることです。この記事では、LaTeX の（別行）見出しのカスタマイズ方法をドキュメントクラスごとに紹介します。

:::message
説明のコードに出てくる`<文字>`を実際に入力する必要はありません。
筆者の環境は TeXLive 2020 です。
:::

<!--前アキと後アキの説明の図-->

# jsclasses の場合

jsclasses とは、`jsbook`・`jsreport`・`jsarticle`の 3 つのドキュメントクラスを指します。

```latex
\renewcommand{<コマンド>}{%
  \@startsection{<コマンド>}{<レベル>}{<インデント>}%
  {<前アキ>}{<後アキ>}{<スタイル>}%
}
```

- `<コマンド>`: コマンド名
- `<レベル>`: 見出しのレベル
- `<インデント>`: 見出しのインデント
- `<前アキ>`: 見出し前のスペースを設定
- `<後アキ>`: 見出し後のスペースを設定
- `<スタイル>`: 見出しについての設定

例えば、下のように定義できます。

```latex
\renewcommand{\section}{%
  \@startsection{section}{1}{0mm}%
  {0mm}{8mm}{\normalfont\fontsize{32Q}{32H}\bfseries}%
}
```

詳細は[LaTeX2e新ドキュメントクラス](https://ctan.math.washington.edu/tex-archive/language/japanese/jsclasses/jsclasses.pdf)を見てください。

# jlreq の場合

バージョン: 2020-02-07

```latex
\NewBlockHeading{<コマンド>}{<レベル>}{%
  <スタイル>
}
```

- `<コマンド>`: 見出しのコマンド名（`\`は除いてください）
- `<レベル>`: 見出しのレベル
- `<スタイル>`: 見出しについての設定（`,`区切りの keyval 形式で指定します）
   - `font=<フォントの設定>`: フォント関連の指定
   - `align=<揃え方>`: `left/center/right`のいずれかを指定
   - `before_space=<寸法>`: 見出し前のスペースを設定
   - `after_space=<寸法>`: 見出し後のスペースを設定

例えば、下のように定義できます。

```latex
\NewBlockHeading{section}{1}{%
  font={\normalfont\fontsize{32Q}{32H}\bfseries},
  before_space=0mm,
  after_space=8mm,
  align=center
}
```

他にも設定できるプロパティが沢山あります。知りたい人は、[jlreqのREADME](https://github.com/abenori/jlreq/blob/master/README-ja.md#見出し)を参照してください。

## 見出しが連続する時のスペースをカスタマイズする

jlreq には、見出しが連続する時に、スペースを調節できるという機能があります。

```latex
\SetBlockHeadingSpaces{%
  {_<コマンド名（1つ目）>{<スペースの指定>},_<コマンド名（2つ目）>{<スペースの指定>}}%
}
```

- 上の書き方をすると、`コマンド名（1つ目）`と`コマンド名（2つ目）`が連続した場合のスペースの取り方を定義できます。この定義はいくつも並べることができます（`,`で区切る必要はありません）。
- `_<コマンド名>{<スペースの指定>}`で、`<コマンド名>`のスペースを`<スペースの指定>`にします。この定義同士は`,`で区切ります。

指定方法は以下のようになります。

- `_<コマンド名>`: 見出しのコマンド名（`\`を除く）の前に`_`を付けます。
- `<スペースの指定>`: スペースの指定を行います。スペースに関するプロパティは、見出しの定義と同じです。ただ、ここの定義は上書きします。そのため、`before_space`しか書かないと、`after_space`の指定がなくなってしまいます。

以上のことを踏まえて、

```latex
\SetBlockHeadingSpaces{%
  {_section{after_space=7mm},_subsection{before_space=0mm,after_space=4mm}}%
  {_section{after_space=7mm},_subsection{after_space=4mm},_subsubsection{after_space=4mm}}%
}
```

のように書くことができます。

# 参考資料

- [pLaTeX2e新ドキュメントクラス](https://ctan.math.washington.edu/tex-archive/language/japanese/jsclasses/jsclasses.pdf)
- [jlreqのGitHubリポジトリの日本語README](https://github.com/abenori/jlreq/blob/master/README-ja.md)
- [jsclasses (TeX Wiki)](https://texwiki.texjp.org/?jsclasses)

---

この記事は以下のシリーズの一部です。

https://zenn.dev/hirospark/scraps/549a0863ca408d
