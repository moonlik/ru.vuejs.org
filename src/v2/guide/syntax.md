---
title: Синтаксис Шаблонов
type: guide
order: 4
---

Vue.js использует основанный на HTML синтаксис, позволяющий декларативно связывать отображаемый DOM и данные инстанса Vue, лежащие в его основе. Все шаблоны Vue.js являются валидным HTML-кодом, проходящим парсинг следующими спецификации парсерами и браузерами.

Внутри, Vue компилирует шаблоны в функции рендеринга Virtual DOM. В сочетании с системой реактивности, Vue способен интеллектуально определить минимально необходимый для ререндеринга набор компонентов и применить минимальное количество манипуляций с DOM при изменении состояния приложения.

Если вы знакомы с концепцией Virtual DOM и предпочитаете использовать ничем не ограниченную мощь JavaScript, вы можете также [писать render-функции напрямую](render-function.html) вместо шаблонов, опционально задействуя JSX.

## Интерполяции

### Текст

Наиболее простой формой связывания данных является текстовая интерполяция с использованием синтаксиса "Mustache" (двойные фигурные скобки):

``` html
<span>Сообщение: {{ msg }}</span>
```

Выражение в фигурных скобках будет заменено значением свойства `msg` соответствующего объекта данных. Кроме того, оно будет обновлено при любом изменении этого свойства.

Возможно также выполнение однократного интерполирования, которое не обновится при изменении данных — используйте для этой цели [v-once директиву](../api/#v-once), но учтите что это повлияет сразу на все связанные переменные в рамках данного элемента:

``` html
<span v-once>Это никогда не изменится: {{ msg }}</span>
```

### Сырой HTML

Значение выражения, обрамлённого двойными фигурными скобками, интепретируется как простой текст, а не как HTML. Если вы хотите вывести HTML, вам понадобится директива `v-html`:

``` html
<div v-html="rawHtml"></div>
```

Содержимое вставляется как обыкновенный HTML — связывание данных игнорируется. Обратите внимание, что вы не можете использовать `v-html` для вложения шаблонов друг в друга, так как Vue не является движком шаблонов, основывающимся на строках. Напротив, основным предпочтительным элементом являются компоненты, что позволяет достичь целей композиции и повторного использования элементов UI.

<p class="tip">Динамический рендеринг проивзольного HTML-кода на вашем сайте может быть крайне опасен, так как он может легко привести к [XSS атакам](https://en.wikipedia.org/wiki/Cross-site_scripting). Используйте интерполяцию HTML только на доверенном, и **никогда** — на пользовательском контенте.</p>

### Аттрибуты

Синтаксис двойных фигурных скобок не работает с HTML-аттрибутами, вместо него используется [директива v-bind](../api/#v-bind):

``` html
<div v-bind:id="dynamicId"></div>
```

С булевыми аттрибутами тоже работает - в случае ложного значения аттрибут будет удалён полностью:

``` html
<button v-bind:disabled="someDynamicCondition">Кнопка</button>
```

### Использование Выражений JavaScript

До сих пор мы связывались со свойствами в шаблонах только просто по их ключам. Но Vue.js в действительности поддерживает всю мощь выражений JavaScript при связывании данных:

``` html
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```

Эти выражения будут вычислены как код на JavaScript в области видимости данных соответствующего инстанса Vue. Единственным ограничением является то, что допускается только **одно единственное выражение**, так что код ниже **НЕ** сработает:

``` html
<!-- это не вычисляемое выражение, а определение переменно: -->
{{ var a = 1 }}

<!-- операторы управления потоком выполнения тоже не сработают, используйте условный оператор -->
{{ if (ok) { return message } }}
```

<p class="tip">Выражения в шаблонах выполняются в "режиме песочницы" и имеют доступ только к белому списку глобальных объектов, таких как `Math` и `Date`. Не пытайтесь получить доступ к пользовательским глобальным объектам внутри используемых в шаблонах выражений.</p>

### Фильтры

Vue.js позволяет определять фильтры, которые используются для распространённых задач форматирования текста. Фильтры добавляются в конце **выражения текстовой интерполяции** и обозначаются вертикальной чертой:

``` html
{{ message | capitalize }}
```

<p class="tip">Во Vue 2.x фильтры могут быть использованы только в выражениях в двойных фигурных скобках. для достижения аналогичного эффекта при связывании в директивах, используйте [Вычисляемые Свойства](computed.html).</p>

Функция-фильтр всегда получает первым аргументом значение вычисленного выражения.

``` js
new Vue({
  // ...
  filters: {
    capitalize: function (value) {
      if (!value) return ''
      value = value.toString()
      return value.charAt(0).toUpperCase() + value.slice(1)
    }
  }
})
```

Фильтры можно объединять в цепочки:

``` html
{{ message | filterA | filterB }}
```

Фильтры являются JavaScript-функциями, и потому могут принимать параметры:

``` html
{{ message | filterA('arg1', arg2) }}
```

В данном случае строка `'arg1'` будет переданна в фильтр на позиции второго аргумента, а значение выражения `arg2` — как третий аргумент.

## Директивы

Директивы — это специальные аттрибуты с префиксом `v-`. В качестве значения этих аттрибутов ожидается **одиночное выражение JavaScript** (за исключением `v-for`, которая будет рассмотрена дальше). Работа директивы состоит в реактивном применении к DOM некоторых побочных эффектов при изменении значения наблюдаемого выражения. Давайте вспомним пример, который мы видели во введении:

``` html
<p v-if="seen">Сейчас меня видно</p>
```

В данном случае директива `v-if` удалит/вставит элемент `<p>` в зависимости от истинности значения выражения `seen`.

### Аргументы

Некоторые директивы могут принимать "агрумент", указываемый после двоеточия после названия директивы. Например, директива `v-bind`используется для реактивного обновления аттрибутов HTML:

``` html
<a v-bind:href="url"></a>
```

В данном случае `href` является аргументом, говорящим директиве `v-bind` связать аттрибут `href` элемента со значением выражения `url`.

В качестве другого примера можно привести директиву `v-on`, которая слушает события DOM:

``` html
<a v-on:click="doSomething">
```

В данном случае аргументом является название типа событий. На обработке событий мы также подробнее остановимся позднее.

### Модификаторы

Модификаторы — это особые постфиксы, добавляемые после точки, которые указывают, что директива должна быть связана каким-то определённым образом. Например, модификатор `.prevent` говорит директиве `v-on` вызвать `event.preventDefault()` при обработке произошедшего события:

``` html
<form v-on:submit.prevent="onSubmit"></form>
```

Мы увидим больше примеров применения модификаторов позднее, когда будем более подробно разбирать `v-on` и `v-model`.

## Сокращения

The `v-` prefix serves as a visual cue for identifying Vue-specific attributes in your templates. This is useful when you are using Vue.js to apply dynamic behavior to some existing markup, but can feel verbose for some frequently used directives. At the same time, the need for the `v-` prefix becomes less important when you are building an [SPA](https://en.wikipedia.org/wiki/Single-page_application) where Vue.js manages every template. Therefore, Vue.js provides special shorthands for two of the most often used directives, `v-bind` and `v-on`:

Префикс `v-` служит как визуальное напоминание для обнаружения Vue-специфичных аттрибутов в ваших шаблонах. Это может быть полезно, когда  Vue.js используется для добавления динамического поведения уже существующей размётке, но для часто используемых директив может показаться слишком многословным. В то же время, необходимость в `v-` становится куда менее важной при создании [SPA](https://en.wikipedia.org/wiki/Single-page_application), где Vue.js контроллирует все шаблоны. Поэтому, Vue.js предоставляет специальные сокращения для двух наиболее часто используемых директив, `v-bind` и `v-on`:

### Сокращение `v-bind`

``` html
<!-- полный синтаксис -->
<a v-bind:href="url"></a>

<!-- сокращение -->
<a :href="url"></a>
```


### Сокращение `v-on`

``` html
<!-- полный синтаксис -->
<a v-on:click="doSomething"></a>

<!-- сокращение -->
<a @click="doSomething"></a>
```

Они могут выглядеть несколько отлично от обыкновенного HTML-кода, но символы `:` и `@` являются валидными для названий аттрибутов, и все поддерживаемые Vue.js браузеры могут их корректно распарсить. В дополнение, в результирующей размётке они не появятся. Сокращённый синтаксис является полностью необязательным к использованию, но скорее всего вам он понравится, когда вы узнаете больше о его использовании далее.