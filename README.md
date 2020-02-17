# typescript/react/CSS-Modules with sass

SASS modules (CSS-modules with sass)を typescript + reactの環境で使用する。

***

<img width="563" alt="ScreenShot 2020-02-17 19 24 17" src="https://user-images.githubusercontent.com/1215350/74645361-1c139100-51bb-11ea-9558-ffbc07ae5c60.png">

***

## つかいかた

```unix
$ yarn
$ yarn run dev
```

## CSS関係のライブラリ

- postcss-loader
- css-loader
- sass-loader
- style-loader
- autoprefixer

## ポイント解説

### 1. webpack.config の CSS部分

```javascript

module.exports = {
  module: {
    rules: [
      { test: /\.scss$/, use: [
        { loader: "style-loader" },// JSで<html /><head />内にCSSを書き込む
        {
          loader: "css-loader",// CSSをパース
          options: {
            sourceMap: true,
            modules: {
              localIdentName: "[name]-[local]-[hash:base64:5]"// Reactコンポーネントにくっつける ClassNamesの名前を [sassファイル名]-[sass内のclass名]-[5桁のランダムハッシュ値] に:
            }
          }
        },
        {
          loader: "postcss-loader",//プレフィクスを自動でつけるためのみに使用
          options: {
            sourceMap: true,
            plugins: [ require("autoprefixer")() ]
          }
        },
        {
          loader: "sass-loader",// sass読み込み
          options: {
            sourceMap: true
          }
        }
      ]}
    ]
  }
};

```
<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/webpack.config.js>

### 2. CSS設定

#### sass変数の例
sass変数を書く、以後sass側でimportして使用する。
<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/src/styles/colors.scss>

#### グローバルなスタイル

<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/src/styles/app.scss>   
アプリ全体に適用したい style を `app` の下に書く。ここに `<hr>,<a>,<h3>` などのセレクターを上書きする指定を書いてしまうとグローバル汚染になるので注意する。

<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/src/index.tsx#L8>  
エントリーポイントのtsxにsassを読み込む
※ SASSに関しては型の問題から開放されたいので、require を使う。ちょっとキモいけど
 `<div className={ styles.app }>` みたいに class属性を設定
<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/src/index.tsx#L44>

#### コンポーネント単位のスタイル

<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/src/components/question/index.scss>
個別のコンポーネント用のスタイル。class名はこのスコープのみで有効になり、グローバルに影響を与えることがないため、BEMなどのCSSフレームワークを導入する必要はない。後で変えるのも楽。実行時のパフォーマンスを上げたかったら、あまりネストしないようにかくとよい。

<https://github.com/mi77jp/boilerplate-ts-react-sassmodules/blob/master/src/components/question/index.tsx>

sassの読み込みと使用方法はエントリーポイントと同様。ここでは、複数の `Classname` をReactで取り回すためによく使われる `classnames` ライブラリーを使っている。
