---
title: "デフォルト キャンセルを選択しているダイアログを表示"
emoji: "📝"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["angular", "material"]
published: true
---

[Angular Material]:https://material.angular.io/
[Angular Material CDK の Accessibility]:https://material.angular.io/cdk/a11y/overview

# 概要

[Angular Material]を使用していて、削除の確認を求めるダイアログを表示しているとき
Enterボタンなど連打してしまっても、うっかり削除しないように、デフォルトでキャンセルボタンにフォーカスを当てたい

# 環境

- @angular/core 13.1.1
- @angular/cdk 13.1.1

# Point

[Angular Material CDK の Accessibility]を使用

# ダイアログ サンプル

- どちらもフォーカスされていない状態
![](https://storage.googleapis.com/zenn-user-upload/1134816903d3-20220124.png)

- はいボタンにフォーカスされている状態(今回は使いません)
![](https://storage.googleapis.com/zenn-user-upload/4c47eea0d157-20220124.png)

- いいえボタンにフォーカスされている状態
![](https://storage.googleapis.com/zenn-user-upload/be698f55b6fa-20220124.png)

ダイアログ表示時に、「いいえ」にフォーカスされていると、Enter連打されても、誤って削除を防げます。


# 準備

- `app.module.ts`にCDKの `A11yModule` を追加


```ts:app.module.ts
import { A11yModule } from '@angular/cdk/a11y';

@NgModule({
  imports: [
    A11yModule,
  ],
})

```

# 1. 変更前ダイアログ

- HTMLだけで実装できるので、ts側のコードは省略します。

```html:dialog.component.html
<h1 mat-dialog-title="">削除します。よろしいですか？</h1>
<mat-dialog-actions fxLayout="row" fxLayoutAlign="space-between">
  <button [mat-dialog-close]="true" color="warn" mat-raised-button="">
    <mat-icon>delete</mat-icon>
    はい
  </button>
  <button [mat-dialog-close]="false" mat-raised-button="">
    <mat-icon>close</mat-icon>
    いいえ
  </button>
</mat-dialog-actions>
```

これだと、ダイアログは、どのボタンにもフォーカスが当てられていない状態で表示されます。

![](https://storage.googleapis.com/zenn-user-upload/1134816903d3-20220124.png)

# 2. 変更後ダイアログ

```diff html:dialog.component.html
 <h1 mat-dialog-title="">削除します。よろしいですか？</h1>
+ <mat-dialog-actions fxLayout="row" fxLayoutAlign="space-between" cdkTrapFocus [cdkTrapFocusAutoCapture]="true">
+  <button [mat-dialog-close]="true" color="warn" mat-raised-button="" cdkFocusRegionStart>
    <mat-icon>delete</mat-icon>
    はい
  </button>
+  <button [mat-dialog-close]="false" mat-raised-button="" cdkFocusRegionEnd cdkFocusInitial>
    <mat-icon>close</mat-icon>
    いいえ
  </button>
 </mat-dialog-actions>
```

- タブキーを押したときに、ダイアログ以外をフォーカスしないようにするために、`cdkTrapFocus` を追加
- はい・いいえ ボタンのふたつだけフォーカスの範囲なので、`cdkFocusRegionStart` と `cdkFocusRegionEnd` を追加
- いいえボタンが、フォーカスの起点なので、いいえボタンに`cdkFocusInitial`を追加
- `cdkFocusInitial`を使用するときは、`[cdkTrapFocusAutoCapture]="true"`になっていないといけないようので、`cdkTrapFocus`と同じタグに`[cdkTrapFocusAutoCapture]="true"`を追加

上記の変更を行うと、ダイアログ表示時、デフォルトでいいえにフォーカスが当たっている状態になりました。

![](https://storage.googleapis.com/zenn-user-upload/be698f55b6fa-20220124.png)


# 参照

https://material.angular.io/cdk/a11y/overview#regions