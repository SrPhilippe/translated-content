---
title: DataView.prototype.buffer
slug: Web/JavaScript/Reference/Global_Objects/DataView/buffer
tags:
  - DataView
  - JavaScript
  - Property
  - Prototype
  - TypedArrays
translation_of: Web/JavaScript/Reference/Global_Objects/DataView/buffer
---
{{JSRef}}

**`buffer`** アクセサープロパティは、この `DataView` の構築時に参照された {{jsxref("ArrayBuffer")}} または {{jsxref("SharedArrayBuffer")}} を表します。

{{EmbedInteractiveExample("pages/js/dataview-buffer.html")}}

## 解説

`buffer` プロパティは、設定アクセサー関数が `undefined` である、すなわち読み取りのみができるアクセサープロパティです。この値は `DataView` が構築されたときに確立され、変更することができません。

## 例

### buffer プロパティの使用

```js
var buffer = new ArrayBuffer(8);
var dataview = new DataView(buffer);
dataview.buffer; // ArrayBuffer { byteLength: 8 }
```

## 仕様書

| 仕様書                                                                                                                   |
| ------------------------------------------------------------------------------------------------------------------------ |
| {{SpecName('ESDraft', '#sec-get-dataview.prototype.buffer', 'DataView.prototype.buffer')}} |

## ブラウザーの互換性

{{Compat("javascript.builtins.DataView.buffer")}}

## 関連情報

- {{jsxref("DataView")}}
- {{jsxref("ArrayBuffer")}}
- {{jsxref("SharedArrayBuffer")}}