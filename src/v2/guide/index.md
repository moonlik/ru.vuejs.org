---
title: Введение
type: guide
order: 2
---

## Что такое Vue.js?

Vue (произносится /vjuː/, похоже на **view**) — это **прогрессивный фреймворк** для создания пользовательских интерфейсов. В отличии от других, монолитных фреймворков, Vue изначально создан пригодным для постепенного внедрения. Ядро фреймворка сфокусированно исключительно на задачах уровня представления (view), что позволяет упростить интеграцию с другими библиотеками и существующими проектами. С другой стороны, Vue также полностью подходит для создания сложных Одностраничных Приложений (SSA, Single-Page Applications) при использовании совместно с [современными инструментами](single-file-components.html) и [поддерживающими библиотеками](https://github.com/vuejs/awesome-vue#libraries--plugins).

Если вы — опытный frontend-разработчик и хотите узнать, чем Vue отличается от остальных библиотек/фреймворков, обратите внимание на [Сравнение с Другими Фреймворками](comparison.html).

## Начало Работы

<p class="tip">В этом официальном руководстве мы предполагаем, что вы уже знакомы с HTML, CSS и JavaScript на базовом уровне. Если вы совсем новичок во фронтенд-разработке, начинать сразу с изучения фреймворка может быть не лучшей идеей - возвращайтесь ухватив основы! Предшествующий опыт с другими фреймворками может помочь, но не является обязательным.</p>

Проще всего попробовать Vue.js используя [пример Hello World на JSFiddle](https://jsfiddle.net/chrisvfritz/50wL7mdz/). Просто откройте его в другой вкладке, и пробуйте на практике примеры, которые мы рассмотрим ниже. А можно и просто создать `.html`-файл и подключить Vue :

``` html
<script src="https://unpkg.com/vue/dist/vue.js"></script>
```

Документация по [Установке](installation.html) описывает больше вариантов установки Vue. Обратите внимание, что мы **не рекомендуем** новичкам начинать с `vue-cli`, особенно в случае отсутствия опыта с инструментами сборки Node.js.

## Декларативный Рендеринг

В ядре Vue.js находится система, которая позволяет декларативно отображать данные в DOM, используя простые шаблоны:

``` html
<div id="app">
  {{ message }}
</div>
```
``` js
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app" class="demo">
  {{ message }}
</div>
<script>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

Вот мы и создали наше первое Vue-приложение! Выглядит довольно похоже на простой рендеринг шаблона, но Vue выполнил немало работы "под капотом". Данные и DOM теперь связаны, и всё **реактивно**. Откуда мы это знаем? Просто откройте консоль JavaScript в своём браузере, и задайте `app.message` другое значение. Вы увидите соответствующее изменение в отображении примера.

В дополнение к интерполяции текста, мы можем также связывать аттрибуты элементов:

``` html
<div id="app-2">
  <span v-bind:title="message">
    Подержи курсор надо мной пару секунд чтобы увидеть динамически связанное значение title!
  </span>
</div>
```
``` js
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'Вы загрузили эту страницу ' + new Date()
  }
})
```
{% raw %}
<div id="app-2" class="demo">
  <span v-bind:title="message">
    Подержи курсор надо мной пару секунд чтобы увидеть динамически связанное значение title!
  </span>
</div>
<script>
var app2 = new Vue({
  el: '#app-2',
  data: {
    message: 'You loaded this page on ' + new Date()
  }
})
</script>
{% endraw %}

Здесь мы встречаемся с чем-то новым. Аттрибут `v-bind`, который вы видите, называется **директивой**. Директивы имеют префикс `v-`, чтобы указать на их особую природу, и как вы уже могли догадаться, они применяют к отображаемому DOM особое реактивное поведение, создаваемое Vue. В данном примере мы указываем "сохраняй значение title этого элемента актуальным в соответствии со свойством `message` инстанса Vue".

Если вы снова откроете консоль JavaScript и введёте `app2.message = 'какое-то новое сообщение'`, вы опять-таки увидите, что связанный код HTML — в данном случае, аттрибут `title` — обновился.

## Условия и Циклы

Управлять наличием / отсутствием элемента тоже довольно просто:

``` html
<div id="app-3">
  <p v-if="seen">Сейчас меня видно</p>
</div>
```

``` js
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
```

{% raw %}
<div id="app-3" class="demo">
  <span v-if="seen">Сейчас меня видно</span>
</div>
<script>
var app3 = new Vue({
  el: '#app-3',
  data: {
    seen: true
  }
})
</script>
{% endraw %}

Попробуйте введсти в консоли `app3.seen = false`. Сообщение должно пропасть.

Этот пример демонстрирует возможность связывать данные не только с текстом и аттрибутами, но также и со **структурой** DOM. Более того, Vue также имеет мощную систему эффектов анимации переходов, которая автоматически применяет [transition-эффекты](transitions.html), когда элементы добавляются/обновляются/удаляются.

Есть несколько других директив, каждая из которых имеет свой особый функционал. Например, директива `v-for` может быть использована для отображения списков, используя данные из массива:

``` html
<div id="app-4">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
```
``` js
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Выучить JavaScript' },
      { text: 'Выучить Vue' },
      { text: 'Создать что-нибудь крутое!' }
    ]
  }
})
```
{% raw %}
<div id="app-4" class="demo">
  <ol>
    <li v-for="todo in todos">
      {{ todo.text }}
    </li>
  </ol>
</div>
<script>
var app4 = new Vue({
  el: '#app-4',
  data: {
    todos: [
      { text: 'Выучить JavaScript' },
      { text: 'Выучить Vue' },
      { text: 'Создать что-нибудь крутое!' }
    ]
  }
})
</script>
{% endraw %}

В консоли, введите `app4.todos.push({ text: 'Новый элемент' })`. Вы увидите, что к списку добавился новый элемент.

## Обработка пользовательского ввода

Чтобы пользователи могли взаимодействовать с вашим приложением, можно использовать директиву `v-on` для указания слушателей событий, которые вызовут методы-обработчики:

``` html
<div id="app-5">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Обратить порядок букв в сообщении</button>
</div>
```
``` js
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
```
{% raw %}
<div id="app-5" class="demo">
  <p>{{ message }}</p>
  <button v-on:click="reverseMessage">Обратить порядок букв в сообщении</button>
</div>
<script>
var app5 = new Vue({
  el: '#app-5',
  data: {
    message: 'Hello Vue.js!'
  },
  methods: {
    reverseMessage: function () {
      this.message = this.message.split('').reverse().join('')
    }
  }
})
</script>
{% endraw %}

Обратите внимание, что в методе мы просто обновялем состояние нашего приложения, не трогая DOM — всё взаимодействие с DOM производится Vue, а код, который вы пишите занимается только логикой приложения.

Vue также содержит директиву `v-model`, позволяющая легко связывать элементы форм и состояние приложения:

``` html
<div id="app-6">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
```
``` js
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
```
{% raw %}
<div id="app-6" class="demo">
  <p>{{ message }}</p>
  <input v-model="message">
</div>
<script>
var app6 = new Vue({
  el: '#app-6',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
{% endraw %}

## Структурирование приложения с помощью Компонентов

Другой важной концепцией Vue являются компоненты, поскольку эта абстракция позволяет создавать большие приложения, используя маленькие компоненты: самостоятельные и зачастую пригодные к повторному использованию. Если подумать, почти любой интерфейс может быть представлен как дерево компонентов:

![Дерево Компонентов](/images/components.png)

Во Vue, компонент — это по сути инстанс Vue с предустановленными опциями. Создание нового компонента во Vue происходит просто:

``` js
// Определяем новый компонент под названием todo-item
Vue.component('todo-item', {
  template: '<li>Это todo</li>'
})
```

Теперь его можно использовать в шаблоне другого компонента:

``` html
<ul>
  <!-- Создаём экземпляр компонента todo-item, созданного выше -->
  <todo-item></todo-item>
</ul>
```

Пока что у нас получилось так, что во всех todo будет содержаться один и тот же текст — это не очень-то интересно. Хотелось бы иметь возможность передать данные от родителя в дочерние компоненты. Давайте изменим определение компонента, чтобы он мог принимать [входной параметр](components.html#Props):

``` js
Vue.component('todo-item', {
  // Компонент todo-item теперь принимает
  // "prop", то есть пользовательский параметр.
  // Этот параметр называется todo.
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
```

Now we can pass the todo into each repeated component using `v-bind`:

``` html
<div id="app-7">
  <ol>
    <!-- Теперь мы можем передать каждому todo описание задачи, -->
    <!-- то есть отображать данные динамически                  -->
    <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
  </ol>
</div>
```
``` js
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})

var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { text: 'Овощи' },
      { text: 'Сыр' },
      { text: 'Что там ещё люди едят?' }
    ]
  }
})
```
{% raw %}
<div id="app-7" class="demo">
  <ol>
    <todo-item v-for="item in groceryList" v-bind:todo="item"></todo-item>
  </ol>
</div>
<script>
Vue.component('todo-item', {
  props: ['todo'],
  template: '<li>{{ todo.text }}</li>'
})
var app7 = new Vue({
  el: '#app-7',
  data: {
    groceryList: [
      { text: 'Овощи' },
      { text: 'Сыр' },
      { text: 'Что там ещё люди едят?' }
    ]
  }
})
</script>
{% endraw %}

Конечно, этот пример слегка надуман, но посмотрите — нам удалось разделить наше приложение на два меньших объекта, и дочерний оказался в разумной мере отвязан от родительского с помощью интерфейса входящих параметров. Теперь мы можем и далее улучшать наш компонент `<todo-item>`, усложняя шаблон и логику и не влияя на родительское приложение.

В крупных приложениях разделение на компоненты является обязательным условием для сохранения управляемости разработки. Разговор о компонентах ещё далеко не закончен — он будет продолжен [далее в этом руководстве](components.html), но уже сейчас можно взглянуть на (вымышленный) пример того, как мог бы выглядеть шаблон приложения, использующего компоненты:

``` html
<div id="app">
  <app-nav></app-nav>
  <app-view>
    <app-sidebar></app-sidebar>
    <app-content></app-content>
  </app-view>
</div>
```

### Отношение к Пользовательским Элементам

Вы могли заметить, что компоненты Vue довольно похожи на **Пользовательские Элементы**, являющиеся частью [Спецификации Web-Компонентов](http://www.w3.org/wiki/WebComponents/). Дело в том, что синтаксис компонентов Vue намеренно следует этой спецификации. Например, компоненты Vue реализуют [API Слотов](https://github.com/w3c/webcomponents/blob/gh-pages/proposals/Slots-Proposal.md) и специальный аттрибут `is`. Вместе с тем, есть и несколько ключевых различий:

1. Спецификация Web-Компонентов всё ещё находится в статусе черновика, и не реализована ни в одном из браузеров. Компоненты Vue, напротив, не требуют никаких полифиллов и устойчиво работают во всех поддерживаемых браузерах (IE9 и выше). При необходимости компоненты Vue могут быть обёрнуты в нативные пользовательские элементы.

2. Компоненты Vue предоставляют важные возможности, недоступные в простых пользовательких элементах, из которых наиболее значимы кросс-компонентная передача данных, коммуникация с использованием пользовательских событий и интеграция с инструментами создания сборок.

## Готовы к большему?

Мы всего лишь кратко представили самые основные возможности ядра Vue.js — остаток этого руководства посвящён значительно более детальному рассмотрению этих и других возможностей, так что удостоверьтесь, что прочитали его целиком!
