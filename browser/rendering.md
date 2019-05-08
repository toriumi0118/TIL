# レンダリングの仕組み

## 流れ

Loading => Scripting => Rendering => Painting

### Loading

HTML, CSSのダウンロードしてDOMツリーの構築、CSSツリーの構築。

### Scripting

jsの実行

### Rendering

スタイルの計算とレイアウト

### Painting

ラスタライズやレイヤー合成。ここで全体が見える。

## 目安の速度

パフォーマンス計測においては以下の要素ごとに目標値がある。

### Response

ユーザーのクリック、アニメーションの開始など。操作に対して100msで返答しよう。

### Animation

今の端末のほとんどが60fps。
なので1フレームあたり16msくらいで表示できないといけない

### Idle

アイドル状態（何もしていない時）。
jsで何かやるときにはメインスレッドのために余裕を持たせましょう。

### Load

ユーザーがアクションを起こしてから1s以内にDOMの生成をしましょう。

## 便利ツール

- uncss: 使ってないcssを除去する
- Intersection Observer: ターゲット要素が祖先要素/最上位のviewportと交差する変更を非同期的に監視する。
  特定のDOM要素が画面内に入っているのかとか、領域外のコンテンツを領域に近づいたら読み込むとかできるようになる。(IE以外は使えそう）
  refs: https://developer.mozilla.org/ja/docs/Web/API/Intersection_Observer_API
  refs: https://qiita.com/yamanoku/items/027308e23cfc69845d7e

