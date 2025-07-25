---
title: "LuaLaTeXで自由にフォントを設定する"
emoji: "👍"
type: "tech"
topics: ["lualatex", "latex", "tex"]
published: true
---

:::message
筆者の環境は TeX Live 2020 です。
:::

# TeX、LaTeX、LuaLaTeX?

LuaTeX は比較的新しい TeX のエンジン（組版をするソフトウェア）で、
今まで主流だった物と異なり、フォントを簡単に設定することが出来ます。

この記事はある程度のLaTeXやLuaLaTeXに関する知識があることを前提に解説しています。
興味を持った方は「美文書作成入門」や「独習 LaTeX2e」といった書籍を購入すると良いと思われます。

# LuaLaTeX のフォントを指定する方法

フォントを設定するための事前知識となる、LaTeX のフォント指定についてです。
LaTeX は、エンコーディング、ファミリー（書体の分類）、シリーズ（太さ）、シェイプ（形）、サイズ
の 5 属性でフォントを管理しています。
ここでは、フォントの見かけに関係のあるファミリー、シリーズ、シェイプの属性変更コマンドを扱います。

以下のコマンドはいずれも組み合わせて使用することが出来ます
（LaTeX のバージョンによっては意図した結果にならない場合もあります）。

クラスファイルなどで定義された基準書体に戻すには、 `\normalfont` や
`\textnormal{}` を使用してください。

## ファミリー（書体の分類）

| コマンド                 | ファミリー       |
| ------------------------ | ---------------- |
| `\rmfamily`, `\textrm{}` | ローマン体       |
| `\sffamily`, `\textsf{}` | サンセリフ体     |
| `\ttfamily`, `\texttt{}` | タイプライター体 |
| `\mcfamily`, `\textmc{}` | 明朝体           |
| `\gtfamily`, `\textgt{}` | ゴシック体       |

## シリーズ（太さ）

| コマンド                 | シリーズ |
| ------------------------ | -------- |
| `\mdseries`, `\textmd{}` | 通常     |
| `\bfseries`, `\textbf{}` | 太字     |

## シェイプ（形）

| コマンド                | シェイプ               |
| ----------------------- | ---------------------- |
| `\upshape`, `\textup{}` | 直立体                 |
| `\itshape`, `\textit{}` | イタリック体           |
| `\slshape`, `\textsl{}` | スラント体             |
| `\scshape`, `\textsc{}` | スモール・キャップス体 |

# `luatexja-fontspec`パッケージを使う

`luatexja-fontspec`パッケージは、`fontspec`パッケージを日本語に対応させた物です
（内部で`fontspec`を読み込みます）。
このパッケージを使うことで、システムに登録されているフォントにアクセスできるようになります。

`luatexja-fontspec`パッケージには沢山の機能があり、様々な方法でフォントの設定を行うことができます。
和文と欧文のフォントを区別しているため、それぞれのコマンドに欧文用と和文用があります。

| 欧文用         | 和文用          | 説明                                 |
| -------------- | --------------- | ------------------------------------ |
| `\setmainfont` | `\setmainjfont` | 通常のフォントを設定                 |
| `\setsansfont` | `\setsansjfont` | ローマン体で使うフォントを設定       |
| `\setmonofont` | `\setmonojfont` | タイプライター体で使うフォントを設定 |
| `\fontspec`    | `\jfontspec`    | 一回だけ使うフォントを選択           |
| `\newfontface` | `\newjfontface` | 単一のフォントを選択する命令を定義   |

これらのコマンドは、`\***{ <フォント名> }[ <オプション> ]`
というように使います（`***`はそれぞれのコマンド）。 `<フォント名>`
がファイル名でない場合、LaTeX の方で太字やイタリック体などのフォントを割り振ります。

```latex
\documentclass{jlreq}
\usepackage{luatexja-fontspec}
\setmainjfont{NotoSerifJP}
\newjfontface\myfont{NotoSansJP}
\begin{document}

ABCDEFG あいうえお % 「NotoSerifJP」に
{\myfont かきくけこ} % 「NotoSansJP」に
{\fontspec{TimesNewRoman} XYZ} % 「TimesNewRoman」に

\end{document}
```

`<オプション>`は、keyval 形式（`<key>=<value>`）で設定を行うことができます。

```latex
\setmainjfont{HiraginoSans}[%
  UprightFont = HiraginoSans-W2,
  BoldFont = HiraginoSans-W5,
  Scale = 0.9
]
```

指定できる`<key>`は以下の通りです。

| `<key>`の名前   | 説明                                   |
| --------------- | -------------------------------------- |
| BoldFont        | **BoldFont**の時に使うフォントを指定   |
| ItalicFont      | ItalicFont 　〃                        |
| BoldItalicFont  | BoldItalicFont 　〃                    |
| SlantedFont     | SlantedFont 　〃                       |
| BoldSlantedFont | BoldSlantedFont 　〃                   |
| SmallCapsFont   | SmallCapsFont 　〃                     |
| UprightFont     | UprightFont 　〃                       |
| Scale           | 指定されたフォントサイズからの拡大縮小 |

# 参考資料

この記事で紹介したのは、`luatexja-fontspec`パッケージの一部でしかありません。
さらに知りたい方は、公式ドキュメントを参照してください。

- [luatexja – Typeset Japanese with Lua(La)TeX（CTAN）](https://www.ctan.org/pkg/luatexja)
- [fontspec – Advanced font selection in XeLaTeX and LuaLaTeX（CTAN）](https://www.ctan.org/pkg/fontspec)
- [独習 LaTeX2e（Amazon）](https://www.amazon.co.jp/%E7%8B%AC%E7%BF%92-LaTeX2%CE%B5-%E5%90%89%E6%B0%B8-%E5%BE%B9%E7%BE%8E/dp/4798115363)
