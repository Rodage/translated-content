---
title: Document.createTouch()
slug: Web/API/Document/createTouch
tags:
  - API
  - Deprecated
  - HTML DOM
  - Method
  - Mobile
  - Reference
  - createTouch
  - touch
translation_of: Web/API/Document/createTouch
---
{{APIRef("DOM")}}{{Deprecated_Header}}

> **Note:** **注:** {{Gecko("25.0")}} 以前では、このメソッドは {{DOMxRef("DocumentTouch")}} ミックスインで定義されていました。

**`Document.createTouch()`** メソッドは、新しい {{DOMxRef("Touch")}} オブジェクトを生成して返します。

## 構文

```
var touch = DocumentTouch.createTouch(view, target, identifier, pageX, pageY,
                                      screenX, screenY);
```

### 引数

> **Note:** **注:** すべての引数が省略可能です。

- `view`
  - : タッチが発生した {{DOMxRef("window")}} です。
- `target`
  - : タッチの {{DOMxRef("EventTarget")}} です。
- `identifier`
  - : {{DOMxRef("Touch.identifier")}} の値です。
- `pageX`
  - : {{DOMxRef("Touch.pageX")}} の値です。
- `pageY`
  - : {{DOMxRef("Touch.pageY")}} の値です。
- `screenX`
  - : {{DOMxRef("Touch.screenX")}} の値です。
- `screenY`
  - : {{DOMxRef("Touch.screenY")}} の値です。

> **Note:** **注:** このメソッドの以前のバージョンでは、以下の追加の引数を含んでいましたが、これらの引数は下記の標準のいずれにも含まれていません。従って、これらの引数は非推奨であり、使用されないと考えてください。

- `clientX`
  - : {{DOMxRef("Touch.clientX")}} の値です。
- `clientY`
  - : {{DOMxRef("Touch.clientY")}} の値です。
- `radiusX`
  - : {{DOMxRef("Touch.radiusX")}} の値です。
- `radiusY`
  - : {{DOMxRef("Touch.radiusY")}} の値です。
- `rotationAngle`
  - : {{DOMxRef("Touch.rotationAngle")}} の値です。
- `force`
  - : {{DOMxRef("Touch.force")}} の値です。

### 返値

- `touch`
  - : 入力引数で記述されたように構成された {{DOMxRef("Touch")}} オブジェクトです。

## 例

この例は {{DOMxRef("Document.createTouch()")}} メソッドを使用して {{DOMxRef("Touch")}} オブジェクトを生成する様子を示しています。

以下のコードスニペットでは、2 つの {{DOMxRef("Touch")}} オブジェクトが `target` 要素に生成されます。

```js
var target = document.getElementById("target");

var touch1 = Document.createTouch(window, target, 1, 15, 20, 35, 40);
var touch2 = Document.createTouch(window, target, 2, 25, 30, 45, 50);
```

## 仕様書

| 仕様書                                                                                                                                                                                                                                                       | 状態                             | 備考     |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------- | -------- |
| {{SpecName("Touch Events", "#widl-Document-createTouch-Touch-WindowProxy-view-EventTarget-target-long-identifier-long-pageX-long-pageY-long-screenX-long-screenY", "Document.createTouch()")}} | {{Spec2("Touch Events")}} | 初回定義 |

## ブラウザーの互換性

{{Compat("api.Document.createTouch")}}

## 関連情報

- [タッチイベント](/ja/docs/Web/API/Touch_events)
- {{DOMxRef("TouchList")}}
- {{DOMxRef("Touch")}}
- {{DOMxRef("Document.createTouchList()")}}