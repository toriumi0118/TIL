# pattern matching

```elm
  case current of
    JourneyPageModel { journey, globeClicked } ->
      toJourneyPageModel journey globeClicked
      
    _ ->
    ...
```

こういうパターンマッチングできるの知らんかった。

# bug??

inputのカーソル位置が入力ごとに末尾に移動してしまう。という問題があり、それを解消するために `virtual-dom@1.0.2` を入れた。
すると `attribute "" ""` って書いていたところで無限ループ。

エラー内容は `Element要素に "" っていうattribute(key)が入ってきているが不正です` みたいなやつ。
`property "" ""` にしたら直った。

直接の原因としては　`virtual-dom` でvalueの比較しているところが `value => typeof value === 'undefined'` に変わっていたから。
