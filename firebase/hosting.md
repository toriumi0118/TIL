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
| firebase serve | Firebase プロジェクトをローカルでサービス提供します
| firebase serve --only hosting:target-name | 指定した Hosting ターゲットのリソースのみをローカルでサービス提供します|

### なんとかしてbasic authもどきをする

firebase functions経由で
