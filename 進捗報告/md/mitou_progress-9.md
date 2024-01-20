2023年度未踏 芝山PJ開発進捗報告(9月)
===

# Pythonにトランスパイル可能な静的型付け<br>プログラミング言語の開発
## 芝山駿介

---

# 自己紹介

### 芝山駿介

早稲田大学先進理工学部物理学科4年
大学では場の量子論、量子情報などを<br>勉強しています

![w:300 bg right](icon.png)

---
# 背景

Pythonは人口に膾炙しているが、とても奇妙な言語

![](weird_python.png)

---

## なぜこのような挙動になるか？

A. `__eq__`を実装していないから

下のようにする必要があった

```python
class C:
    x: int
    def __init__(self, x):
        self.x = x
    def __eq__(self, other):
        return self.x == other.x
```

---

でも、`__eq__`を実装していないならエラーにしてほしい
(願わくば、実行前に)
## というか、Pythonに静的型システムが欲しい

---
## 理想

![h:500](erg_eq.png)

---

でも、ただ新しい言語を作るだけではダメ
Pythonのコード資産がそのまま使えるような言語が欲しい

それと、Python代替を目指すならエコシステムも揃っていて欲しい(パッケージマネージャ, フォーマッタ, Language Server, etc.)
→ Pythonの公式エコシステムはあんまり出来も良くないので、後発の強みを活かして改善されているとなお良い
また、せっかく静的検査をやるのだからネイティブコードも出力したい

...と、いうことを実現する言語 __Erg__ を作っています

---
# これまでの進捗(6~8月)

---
## Language Server

未踏期間前から基本的な機能は実装済み
- [x] Completion
- [x] Diagnostics
- [x] Hover
- [x] Go to definition
- [x] Find references
- [x] Renaming
- [x] Inlay hint
- [x] Semantic tokens
- [x] Code actions
- [x] Code lens

未踏期間中の主な進展はLanguage Serverの並列化

---
## パッケージマネージャ

実用的なアプリケーションを作るにはパッケージマネージャが不可欠
パッケージマネージャはErg自身を用いて実装した

現在使用可能なコマンド:
* publish: 作成したパッケージを検証し、GitHub上で管理されるレジストリに登録
* build: コードをコンパイルして成果物をbuildディレクトリに置く
* clean: buildディレクトリをclean-upする
* help: ヘルプを表示する
* init: パッケージを初期化する
* run: アプリケーションパッケージを実行する
* install: (シェルスクリプトを使った擬似)実行可能ファイルを作って`$ERG_PATH/bin`に置く
* metadata: パッケージのメタデータを表示

---
## ネイティブコードバックエンド

ネイティブコードバックエンドは、Rustコードをターゲットとする方式で実装

$$
Ergスクリプト \xrightarrow{Ergコンパイラ} Rustコード \xrightarrow{Rustコンパイラ} バイナリ
$$

ネイティブコードバックエンドは、構成的にはErg to RustトランスパイラとRustコンパイラを呼び出す部分からなる

---
### トランスパイラ

トランスパイラ(Galと命名)の方は、現在

* 関数呼び出し(print, assert)
* 変数・関数・メソッド定義
* コントロールフロー(if, for, while, match)
* 基本的な二項演算
* クラス定義
* import(Ergモジュールのみ)

を変換可能

---
### コンパイル(トランスパイル)実行例

![w:750](gal.png)

---

Rustに変換するので当然ではあるが、fibonacci関数での簡易ベンチマークではPythonよりも10倍以上高速に実行された

![w:500](fib_bench.png)

これからはErgとの互換性を高めるためにバインディングライブラリやPython APIを模倣するRustライブラリを作っていく

---
## 言語機能

主要なものとしては
* スライスの追加
* ユーザー定義再帰型の追加
* パターンマッチの機能強化

---
## コンパイラ

* 型システムのバグ修正
* コード生成器のバグ修正

---

# 9月の進捗

---
## Language Server

* Workspace diagnosticsを実装
プロジェクトを開くと自動的にパッケージ全体の検査をやってくれる機能
実用的にはかなり欲しかった機能なので、実装できて良かった

---

* Workspace symbolを実装
プロジェクト内でセマンティックにシンボルを検索できる機能

![w:700](/Users/shiba/Documents/GitHub/lsp_book_leanpub/images/lsp_workspace_symbol.png)

---

* Call hierarchy機能を実装
関数の呼び出し階層を表示できる機能

![call hierarchy](/Users/shiba/Documents/GitHub/lsp_book_leanpub/images/lsp_incoming_calls.png)

* Folding range機能を実装
importリストを折りたためるようになった

---

* Language Serverのテスト基盤を開発・公開
サーバーと通信を行うダミークライアントを実装、これを用いてテストも実装
コンパイラ本体と分離できたので別リポジトリで公開

<a href="https://github.com/erg-lang/molc"><img src="https://github-link-card.s3.ap-northeast-1.amazonaws.com/erg-lang/molc.png" width="460px"></a>

<!--
```cardlink
url: https://github.com/erg-lang/molc
title: "GitHub - erg-lang/molc: A mock language client for testing language servers"
description: "A mock language client for testing language servers - GitHub - erg-lang/molc: A mock language client for testing language servers"
host: github.com
favicon: https://github.githubassets.com/favicons/favicon.svg
image: https://opengraph.githubassets.com/50abc03681a70fb9a60c27653e5ab307c194db138bf7416f567acc76d29c3091/erg-lang/molc
```
-->

---

また未踏とは関係ありませんが、LSPに関する知見を本にまとめました
近日公開予定([Zenn](https://zenn.dev/mtshiba/books/language_server_protocol)/[Leanpub](https://leanpub.com/lsp-book-jp/overview))

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">春から書いていたLSPの本がようやくまとまりました <a href="https://t.co/mYJdGoxBj0">pic.twitter.com/mYJdGoxBj0</a></p>&mdash; Shiba (@s_sbym) <a href="https://twitter.com/s_sbym/status/1697219132541022712?ref_src=twsrc%5Etfw">August 31, 2023</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<!--
![link](https://twitter.com/s_sbym/status/1697219132541022712?s=20)
-->

---
## フォーマッタ

* スタイルガイドの策定
* フォーマッタ本体の実装(実装中)
インデントの処理がかなり面倒で、完成まではまだしばらくかかりそう

---
## パッケージマネージャ

残念ながら目立った進展なし
言い訳: パッケージマネージャの実装を進めようとするたびコンパイラとLanguage Serverのバグ及び機能不足が立ちはだかるため
ひと段落した感じはあるので、10月は実装を進められそう？

---
## 言語機能

* パターンマッチ機能の改善
ネストしたパターンに対応

![h:550 bg right](nested_pattern.png)

* デコレータ(Pythonと同名の言語機能)の実装

* 内包表記(言語機能)の実装

---
## コンパイラ

* CIの改善
CIはPython 3.11だけで行っていたが、3.7~3.11まで全てサポート
(これのために3.7特有のバグとかを必死に直した)

![h:550 bg right](ci.png)

* その他、バグ修正

---

## 問題

9月に予定した作業が(特に新規実装部分で)あまり進められなかった
Language Serverは概ね完成したので、これからは新規実装を進められそう

---
## 今後の予定

* パッケージマネージャでは、依存関係解決器を実装し、ライブラリの依存関係を管理できるようにする
	* その他、公開に向けて必要な機能を実装する
	* 8合目会議までに一通り動かせるようにしたい

* ネイティブコードバックエンドでは、より多くのコードを変換できるよう機能強化を進めるほか、バインディング機構の実装を行う
	* パッケージマネージャをバイナリにコンパイルするのが目標 

* フォーマッタの実装を進める

* コンパイラ本体では、パッケージマネージャの実装で必要になった機能やバグ修正等を行う

---

# 進捗チャート

灰色が完了、青が進行中(2023/9/30現在)

![](progress.png)
