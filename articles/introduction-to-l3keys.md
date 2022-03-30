---
title: "l3keys の紹介"
emoji: "🦔"
type: "tech"
topics: ["latex", "tex", "expl3"]
published: true
---

これは、[TeX ＆ LaTeX Advent Calendar 2021](https://adventar.org/calendars/6724) の 18 日目の記事です。昨日は mattskala さんの[「MakeでLaTeX言語のビルドしましょう」](https://ansuz.sooke.bc.ca/entry/373)でした。明日は hid_alma1026 さんの[「Speedata Publisherのマークアップ」](https://zenn.dev/hidaruma/articles/2b2722abf123a5)です。

l3keys とは、expl3 のモジュールで、keyval リストのインタフェースを実現します。keyval リストとは、カンマ区切りで `<キー>=<値>` が連なるリストのことで、graphicx パッケージのオプション指定がそれです。

```tex
\includegraphics[width=2cm,height=3cm]{sample.pdf}
```

同じ目的のパッケージとして、他に keyval や xkeyval がありますが、l3keys はキーの定義も keyval リストなため、それと比べて大変使いやすいものになっています。

この記事では、2021-06-18 リリースの expl3 を基に解説します。公式のリファレンスは [`interface3.pdf`](https://ftp.kddilabs.jp/CTAN/macros/latex/contrib/l3kernel/interface3.pdf) で、この記事の情報も全てこれによるものです。

## 前提知識

この記事では、expl3 についての説明は行いません。解説としては、以下の記事が大変分かりやすいです。

- [TeX 言語者のための expl3 入門](https://blog.wtsnjp.com/2018/04/28/expl3-for-tex-users/)
- [講演『expl3 入門』の補足](https://blog.wtsnjp.com/2017/10/29/intro-expl3/)
- [expl3 の標準データ構造 (1) seq](https://blog.wtsnjp.com/2018/08/10/expl3-data-structure1/)
- [expl3 の標準データ構造 (2) prop, clist](https://blog.wtsnjp.com/2018/08/26/expl3-data-structure2/)
- [LaTeX3: Programming in LaTeX with Ease](https://www.alanshawn.com/latex3-tutorial/)

## 基本的な使い方

以下、特に説明がない限り、`\ExplSyntaxOn` `\ExplSyntaxOff` が適切に行われた状態であると仮定します。

l3keys では、`\keys_define:nn` 命令を使って、モジュールごとにキーを定義します。例えば、

```tex
\keys_define:nn { example }{
  name  .code:n     = \hoge{ #1 },
  plice .int_set:N  = \l_tmpa_int
}
```

のようにすると、`example` モジュールに対して、

- キーが指定された時に `\hoge { #1 }` が実行される（`#1` はキーに指定された値が入る）`name` キー
- 指定された値が `\l_tmpa_int` に代入される、`plice` キー

の 2 つのキーが定義されます。

キーを使用するには、`\keys_set:nn` 命令を使います。

```tex
\keys_set:nn { example }{
  name  = tokyo,  % => \hoge{ tokyo } が実行される
  plice = 1000    % => \l_tmpa_int に 1000 が代入される
}
```

実際にパッケージの機能として提供する場合は、xparse パッケージなどを用いて、

```tex
\NewDocumentCommand{ \my }{ m }{
  \keys_set:nn { example }{ #1 }
}
```

のように使う事が出来ます。

## キーの定義

前述の例で見たように、`<モジュール>` に対して、

```tex
\keys_define:nn { <モジュール> }{
  <キー> <プロパティ> = <値>,
  ...
}
```

の形で定義します。キーは必ずプロパティを持ち、プロパティは `<値>` を引数として取ります
（例外あり）。キー名に使える文字の制限はありません。なお、キーは記述された順序で定義されるため、重複・矛盾する定義は後のものに上書きされます。

プロパティには、主なものとして、

1. 変数に値を代入するプロパティ（`.tl_set:N` など）
2. 関数を定義するプロパティ（`.cs_set:Np` など）
3. 選択キーを定義するプロパティ（`.choice:` など）

があります。

### 1. 変数に値を代入するプロパティ

```tex
<キー> <プロパティ> = <変数>
```

`<キー>` に指定された値を `<変数>` に代入します。`<プロパティ>` は基本的に通常の変数代入の命令と同じ名前です。変数が定義されていない場合、グローバルに定義されます。

```tex
\keys_define:nn { example }{
  bool  .bool_set:N   = \l_tmpa_bool,
  clist .clist_set:N  = \l_tmpa_clist,
  dim   .dim_set:N    = \l_tmpa_dim,
  fp    .fp_set:N     = \l_tmpa_fp,
  int   .int_set:N    = \l_tmpa_int,
  muskip .muskip_set:N = \l_tmpa_muskip,
  prop  .prop_put:N   = \l_tmpa_prop,
  skip  .skip_set:N   = \l_tmpa_skip,
  tl    .tl_set:N     = \l_tmpa_tl
}
```

### 2. 関数を定義するプロパティ

```tex
<キー> <プロパティ> = <関数>
```

`<キー>` に指定された値（コード）が `<関数>` の定義になります。`<関数>` が定義されていない場合、グローバルに定義されます。下の例の場合、`\__tmpa_fn:n` が `こんにちは、#1さん` として定義されます（関数などの定義中で用いる場合は、`#` を必要なだけ重ねる必要があります）。

```tex
\keys_define:nn { example }{
  key .cs_set:Np = \__tempa_fn:n #1
}
\keys_set:nn { example }{
  key = こんにちは、#1さん
}
\__tmpa_fn:n { 山田 } % => こんにちは、山田さん
```

また、`.cs_set:cp` を使う場合は、

```tex
\keys_define:nn { example }{
  key .cs_set:cp = { __tempa_fn:n } #1
}
```

のようにします。

### 3. 選択キーを定義するプロパティ

選択キーとは、キーに指定する値が選択肢として事前に用意されたキーのことです。例えば、jlreq クラスの `paper` キーは、`a4` や `b4` などの選択肢が事前に用意されており、用意されていないものを指定するとエラーが出ます。

```tex
\documentclass[paper=b4]{jlreq}
```

```tex
\documentclass[paper=x3]{jlreq} % => ! Class jlreq Error: The paper x3 is unknown.
```

選択キーを定義する方法は 3 つあります。`.choice:` 又は `.choices:nn` は 1 つだけキーを指定するタイプで、`.multichoices:nn` は複数のキーを指定するタイプです。

#### `.choice:` プロパティ

```tex
<キー> .choice:,
<キー> / <サブキー> <プロパティ> = <値>
```

`.choice:` プロパティは `<キー>` を選択キーとして定義し、`<値>` を取りません。選択肢となるキーは、サブキーによって作られます。`<サブキー>` は `<キー>/<サブキー>` とすることで定義出来ます。

下の例では、`key` が選択キーとして、`a` `b` `c` が `key` のサブキーとして定義され、`a` `b` `c` を `key` の選択肢として指定できます。

```tex
\keys_define:nn { example }{
  key .choice:,
  key / a .code:n = キー「a」が選択されました,
  key / b .code:n = キー「b」が選択されました,
  key / c .code:n = キー「c」が選択されました
}
\keys_set:nn { example }{
  key = b % => キー「b」が選択されました
}
```

#### `.choices:nn` プロパティ

機能としては、`.choice:` と同じです。

```tex
<キー> .choices:nn = { <キーの選択肢> }{ <コード> }
```

インデックス[^index]が `\l_keys_choice_int` に、キー名が `\l_keys_choice_tl` に代入され、`<コード>` で使用できます。

下の例では、`1番目の「a」が選択されました。`と出力されます。

```tex
\keys_define:nn { example }{
  key .choices:nn = { a, b, c }{
    \int_use:N\l_keys_choice_int 番目の
    「\tl_use:N\l_keys_choice_tl」が選択されました。
  }
}
\keys_set:nn { example }{
  key = a % => 1番目の「a」が選択されました。
}
```

#### `.multichoices:nn` プロパティ

`.choice:` や `.choices:nn` では 1 つのキーしか選べませんが、`.multichoices:nn`では複数のキーを選べます。書式は、`.choices:nn` と全く同じで、

```tex
<キー> .multichoices:nn = { <キーの選択肢> }{ <コード> }
```

です。`<コード>` はキーごとに実行されます。インデックス[^index]やキー名も同様に使用できます。

以下の例では、`1番目の「a」が選択されました。3番目の「c」が選択されました。`と出力されます。

```tex
\keys_define:nn { example }{
  key .multichoices:nn = { a, b, c }{
    \int_use:N\l_keys_choice_int 番目の
    「\tl_use:N\l_keys_choice_tl」が選択されました。
  }
}
\keys_set:nn { example }{
  key = { a, c }
  % 又は
  % key = a,
  % key = c
}
```

## その他のプロパティ

この記事で扱わなかったプロパティのうち、便利なものを簡単に紹介します。他にも多くの機能があるので、[`interface3.pdf`](https://ftp.kddilabs.jp/CTAN/macros/latex/contrib/l3kernel/interface3.pdf) をぜひ見てください。

| プロパティ | 指定する値 | 説明 |
| :--- | :--- | :--- |
| `.default:n` | デフォルト値 | デフォルト値を設定する |
| `.value_forhidden:n` | 真偽値 | `<値>` を隠し、キーだけで指定できるようにする |
| `.value_required:n` | 真偽値 | `<値>` が指定されていない時に、エラーが出るようにする |
| `.code:n` | コード | キーが指定された時にコードを実行する。指定した値は `#1` に入れられる |

[^index]: インデックスは 1 から始まります。