# overflow: scroll のときのios対応

慣性スクロールが微妙になるとき。

```css
    -webkit-overflow-scrolling: touch;

    & > * {
      -webkit-transform: translateZ(0);
    }

```

translateZはgpuを利用して配下の要素を描画するように。
