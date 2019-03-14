# [firebase hosting](https://firebase.google.com/docs/hosting/quickstart?hl=ja)

## 作っているelm appをhostingする

### firebaseの初期設定

```
$ firebase init
# 途中 hostingを選び
# 公開用directoryをデフォルトのpublicにして
# SPAなので全てのrequestをindex.htmlに飛ぶように設定（これはあとで変えるかもしれない）
```

何もない状態でも `/public/index.html` を生成してくれる。
ありがたい。

これをhostingするところから始める。

### おもむろにdeploy

```
$ firebase deploy --only hosting
```

これでinitで作られたindex.htmlが

https://watarilog.firebaseapp.com/

ここにhostingされた :tada:

#### memo

localでserveする方法もあるみたい（未確認）

| コマンド | 説明 |
| --- | --- |
| firebase deploy | プロジェクト ディレクトリにデプロイできるすべてのリソースのリリースを作成します |
| firebase deploy --only hosting:target-name | 指定した Hosting ターゲットのリソースのみのリリースを作成します |
| firebase serve | Firebase プロジェクトをローカルでサービス提供します |
| firebase serve --only hosting:target-name | 指定した Hosting ターゲットのリソースのみをローカルでサービス提供します |

### basic authもどきをする

（と思ったけど、自分が作ったelm appが `/app` で動くように想定されて作られていないので、ちょっと辛そう）

hostingだけの機能で実現するのは無理そうなのでfunctionsを導入。

```
$ firebase init functions
# tsを使うようにする
```

#### firebase functionsの設定

これでいけるかな。
全体にbasicauthをかけて、
app以下であれば `/app` にredirect。

```
import * as functions from "firebase-functions";
import * as express from "express";
import * as basicAuth from "basic-auth-connect";

const app = express();

app.use(basicAuth("wataridori", "inc"));

app.get("/app", (_req, res) => {
  res.redirect("/app");
});

exports.app = functions.https.onRequest(app);
```
