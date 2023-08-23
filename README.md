# web-serial-gui
## 概要
- 2023/08/23
- [katonobu/cna-react-admin](https://github.com/katonobu/cna-react-admin)からの派生
- react-adminはNext.jsのApp-routerのディレクトリ構成だがroutingは独自っぽそう。
- App-routerのディレクトリ構成、画面構築の枠組みで作り直した版。
- CreateNextAppにMUIのDashboard-exampleを被せたものを起点としている。
- App-routerでは[id]によるdynamic-routingを使うと、サーバー必須になるっぽい。
  - 現状Vercelでdeployしている。
- ライブラリ整備に注力するため、[katonobu/web-serial-gui](https://github.com/katonobu/web-serial-gui)のexampleに移行するかも。

## 最初
1.  [MUI examples/material-next-app-router-ts](https://github.com/mui/material-ui/tree/master/examples/material-next-app-router-ts)以下をコピー。
1. `npm install`,`npm run dev`で動作確認。
1. [dashboard template](https://github.com/mui/material-ui/tree/master/docs/data/material/getting-started/templates/dashboard) の.tsファイルを`src/layouts/Home`にコピー
1. `src/layouts/Home/Home.tsx` の2行目以降を削除し、`src/layouts/Home/Dashboard.tsx`の中身を張り付け
1. `package.json`に`"recharts": "latest",`を追加。

## App-router対応
- `/app` 下に`_components`ディレクトリを掘って、ここにlayout,pageの中身を持ってくる
```
+- src/
   +- app/
   |  +- _components/
   |  +- fonts/
   |  +- favicon.ico
   |  +- layout.tsx:サイドメニュー、ヘッダ、フッタ。Dashboardのメインコンテンツ以外はここに実装
   |  +- page.tsx:port一覧の実装
   |  +- [id]/
   |  |  +- page.tsx:こいつがサーバーレンダリングされる★
   |
   +- features/web-serial
   |  +- SerialPortIcon.tsx:アイコン
   |  +- webSerialDataProvider.ts:カスタムフック
   |  +- webSerialWorkerAdapter.ts:Non-reactのworkerカスタムフック/worker I/F
   |  +- worker/
   |  |  +- webSerialWorker.ts:workerメイン
   |  |  +- workerHandler.ts:ドライバとmainスレッドのI/F
   |  |  +- webSerialPorts.ts:navigator.serialドライバ
   |  |  +- vendors.ts:vid/ベンダー名対応テーブル
   |
   +- components/Theme/ThemeRegistry:特にいじっていない
   |  +- EmotionCache.tsx
   |  +- theme.ts
   |  +- ThemeRegistry.tsx
```

## dynamic routeing対応
- dynamic routingとssgは相性悪そう。
- うまく動いていない。
- vercelだとサーバー側も動いてくれるので、vercelへのデプロイで動作確認できる。

## 課題
- portlist
  - useSerialPorts()だと、portの増減時にしか更新されない。
  - いずれかのポート状態が変化した、を通知するか、ページ更新時にfetchするか。
    - ページ更新時にfetchするのが筋がよさそう。
- dataview
  - 受信時、totalLinesはちゃんと反応している。
  - DELETE disable時、アイコンが薄くならない。
  - データ取得/表示はfetchしないとできない。
    - 1行毎に更新イベントが飛んできてるっぽい
      - 最初の更新から100ms後等、遅延fetchさせたほうがいいか?
  - 「dataviewページ表示中にケーブル抜かれたらportlistに遷移」が未実装
  - 送信フォーム未実装
  - 受信データのスクロールは実装し始めると奥が深そう
- menulist
  - 文字がリンク色になってるのがいけてない。cssの当て方がわからん。
- menuバー
  - タイトル文字列が固定なのがいけてない。
    - urlと、port情報から、idStrとvenderName出したい
  - 右側のベル+バッジは活用したい。
    - ユーザー通知情報としては下記が候補
      - 有効ポートの増減
      - ポートオープン失敗

## 作業優先順位
1. React-Queryの導入
   - portlist表示時の情報取得
     - dataviewでポート状態変えてlistに戻ったら、ちゃんと反映してること。
   - 受信データ取得、表示(Simple版)
1. タイトル文字列URLから取ってくる(済)
1. dataviewページ表示中にケーブル抜かれたらportlistに遷移
1. 送信フォーム(Simple版)
1. スクロール
1. ユーザー通知


# Material UI - Next.js App Router example in TypeScript

## How to use

Download the example [or clone the repo](https://github.com/mui/material-ui):

<!-- #default-branch-switch -->

```sh
curl https://codeload.github.com/mui/material-ui/tar.gz/master | tar -xz --strip=2  material-ui-master/examples/material-next-app-router-ts
cd material-next-app-router-ts
```

Install it and run:

```sh
npm install
npm run dev
```

## The idea behind the example

The project uses [Next.js](https://github.com/vercel/next.js), which is a framework for server-rendered React apps.
It includes `@mui/material` and its peer dependencies, including [Emotion](https://emotion.sh/docs/introduction), the default style engine in Material UI v5. If you prefer, you can [use styled-components instead](https://mui.com/material-ui/guides/interoperability/#styled-components).

## What's next?

<!-- #default-branch-switch -->

You now have a working example project.
You can head back to the documentation, continuing browsing it from the [templates](https://mui.com/material-ui/getting-started/templates/) section.
