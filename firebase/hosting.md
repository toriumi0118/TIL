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

