# overflow: scroll のときのios対応

慣性スクロールが微妙になるとき。

```css
    -webkit-overflow-scrolling: touch;

    & > * {
      -webkit-transform: translateZ(0);
    }

```

translateZはgpuを利用して配下の要素を描画するように。

## 問題点

`background-color: rbga(0,0,0,0.5);` で指定している部分がtransform中にちょっと濃くなる。（たまに）
なぜかはわからん。。。
gpuアクセラレーターと食い合わせが悪いのか、なんか移動中の演算に間違いでもあるのか。。。
