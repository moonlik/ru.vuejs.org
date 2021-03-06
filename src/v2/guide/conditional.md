---
title: Условный Рендеринг
type: guide
order: 7
---

## v-if

В строковых шаблонизаторах, например Handlebars, мы бы написали блок с условием таким образом:

``` html
<!-- шаблон Handlebars -->
{{#if ok}}
  <h1>Yes</h1>
{{/if}}
```

Во Vue, мы используем директиву `v-if` для тех же целей:

``` html
<h1 v-if="ok">Да</h1>
```

Также можно добавить блок "else", используя `v-else`:

``` html
<h1 v-if="ok">Да</h1>
<h1 v-else>Нет</h1>
```

### Использование v-if с псевдоэлементом Template

Поскольку `v-if` — это директива, она должна быть указана для одиночного элемента. Но что если мы хотим управлять отображением сразу нескольких элементов? В этом случае мы можем применить `v-if` к элементу `<template>`, служащему невидимой обёрткой. Результаты рендеринга не будут включать элемент `<template>`.

``` html
<template v-if="ok">
  <h1>Заголовок</h1>
  <p>Абзац 1</p>
  <p>Абзац 2</p>
</template>
```

### v-else

Можно использовать директиву `v-else` для указания "блока else" для `v-if`:

``` html
<div v-if="Math.random() > 0.5">
  Сейчас меня видно
</div>
<div v-else>
  А теперь — нет
</div>
```

Элемент с директивой `v-else` должен непосредственно следовать за элементом с директивой `v-if` - в противном случае он не будет опознан.

## v-show

Ещё одной опцией для условного отображения элемента является директива `v-show`. Используется она в целом так же:

``` html
<h1 v-show="ok">Привет!</h1>
```

Разница состоит в том, что элемент с `v-show` будет всегда оставаться в DOM, а изменяться будет лишь свойство `display` в его параметрах CSS.

<p class="tip">Обратите внимание, что `v-show` не поддерживает использование `<template>` и не работает с `v-else`.</p>

## v-if vs. v-show

`v-if` производит "настоящий" условный рендеринг, удостоверяясь что слушатели и дочерние компоненты внутри условного блока должным образом уничтожаются и воссоздаются при переключении.

`v-if` также **ленив**: если условие ложно на момент первоначального рендеринга, он не произведёт никаких действий - условный блок не будет отображён, пока условие впервые не станет истинным.

`v-show`, напротив, куда проще — элемент всегда присутствует в DOM, и только CSS-свойство переключается в зависимости от значения выражения.

Говоря обобщённо, стоимость переключения `v-if` выше, в то время как для `v-show` выше стоимость первичного рендеринга. Так что если переключения предполагаются частыми — то используйте `v-show`, если же редкими или вовсе маловероятными — то `v-if`.
