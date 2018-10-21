# 🍚meCSS

meCSS（めしーえすえす）は、さまざまな規模のフロントエンドプロジェクトに活用できるCSS命名規則、および実践的知見の寄せ集めです。

## meCSSの２つのパート

meCSSは大きく２つのパートに分かれます。クラス命名規則やCSS、HTMLの書き方のルールを定めた**meCSS Rule**と、meCSSが目指す「よりよいコード」を実現するための実践的知見を集めた**meCSS Practice**です。

- [meCSS Rule](rule.md)
- [meCSS Practice](practice.md)

## 前提知識：BEM

BEMは、CSS設計にモジュラー性を取り入れることで再利用性や拡張性を実現した優れた手法です。meCSSはBEMを基本的な考え方として取り入れています。とりわけ次の概念はmeCSSの理解には欠かせません。

- **Block - ブロック**  
  それ自身が独立して意味のあるまとまり。 `header`, `menu`, `checkbox`, `input` など

- **Element - エレメント**  
  ブロックの一部分で、それ自身では独立できないまとまり。 `menu item`, `list item`, `checkbox caption`, `header title` など

- **Modifier - モディファイア**  
  ブロックやエレメントの状態や派生パターンを表現するフラグ。 `disabled`, `highlighted`, `checked`, `size big`, `color yellow` など

BEMに関する詳細は[本家BEMウェブサイト](http://getbem.com/)を参照するか、日本語の解説リソースを参照してください。
