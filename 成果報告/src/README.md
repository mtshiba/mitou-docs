# ビルドの仕方

成果報告書は[SATySFi](https://github.com/gfngfn/SATySFi/blob/master/README-ja.md)([2017年度未踏事業](https://www.ipa.go.jp/jinzai/mitou/it/2017/gaiyou_t-4.html)で開発された静的型付け組版システム)で記述されています。

依存関係はsatyrographosを使用して以下のようにインストールしてください。

```sh
opam install satysfi-code-printer
opam install satysfi-enumitem
opam install satysfi-figbox
satyrographos install
```

```sh
satysfi report.saty
satysfi detail.saty
```
