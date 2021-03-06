<a name="decl"></a>
## Декларация блоков и элементов

JS-реализация блока или элемента описывает поведение определенных частей веб-интерфейса.
В конкретных интерфейсах каждый блок или элемент может быть представлен несколькими экземплярами. 
Экземпляр реализует функциональность своего класса и имеет собственное, независимое состояние.

В терминах парадигмы **объектно-ориентированного программирования**:

 * блок и элемент — классы;
 * экземпляры блока и элемента — экземпляры классов.

В соответствии с ООП, вся функциональность блока и элемента реализуется модульно в методах класса.

Методы блока и элемента подразделяются на:

 * методы экземпляра;
 * статические методы (методы класса).

Код блока и элемента в `i-bem.js` принято называть **декларацией**, чтобы подчеркнуть принятый
в БЭМ декларативный стиль программирования. 

Поведение блока программируется в декларативном стиле в виде утверждений: `набор условий` — `реакция блока`.

<a name="decl-syntax"></a>
### Синтаксис декларации

#### Блоки и элементы с DOM-представлением

##### Объявление нового блока без родителя

Чтобы задекларировать новый JS-блок **с DOM-представлением** (привязанный к HTML-элементу), нужно воспользоваться методом `declBlock` [ym][]-модуля `i-bem-dom`.

Метод `declBlock` принимает аргументы:

1. Имя `{String}` или класс `{Function}` блока.
2. Методы экземпляра — `{Object}`.
3. Статические методы (методы класса) — `{Object}`.

Объявленные методы будут применяться во всех экземплярах блока независимо от их состояний (модификаторов).

**Пример:** Декларация методов для блока `button`.

```js
modules.define('button', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.declBlock(this.name,
    {
        /* методы экземпляра */
    },
    {
        /* статические методы */
    })
);

});
```

Поле контекста `ym` `this.name`, передаваемое первым аргументом методу `bemDom.declBlock`, содержит ссылку на имя блока, указанное первым аргументом `modules.define`.

##### Объявление нового элемента без родителя

Чтобы задекларировать новый JS-элемент **с DOM-представлением** (привязанный к HTML-элементу), нужно воспользоваться методом `declElem` [ym][]-модуля `i-bem-dom`.

Метод `declElem` принимает аргументы:

1. Имя `{String}` или класс `{Function}` блока.
2. Имя `{String}` или класс `{Function}` элемента.
3. Методы экземпляра — `{Object}`.
3. Статические методы (методы класса) — `{Object}`.

Объявленные методы будут применяться во всех экземплярах элемента независимо от их состояний (модификаторов).

**Пример:** Декларация методов для элемента `menu__item`.

```js
modules.define('menu__item', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.declElem('menu', 'item',
    {
        /* методы экземпляра */
    },
    {
        /* статические методы */
    })
);

});
```

<a name="bem-decl"></a>
#### Блоки и элементы без DOM-представления

Для декларации блоков и элементов без DOM-представления служат аналогичные методы `declBlock` и `declElem` [ym][]-модуля `i-bem`.

Методы принимают те же параметры, что и метод `declBlock` и `declElem` модуля `i-bem-dom`:

```js
modules.define('my-block', ['i-bem'], function(provide, bem) {

provide(bem.declBlock(this.name,
    {
        /* методы экземпляра */
    },
    {
        /* статические методы */
    })
);

});
```


***

**NB** Оформлять инфраструктурный код в виде блока без DOM-представления удобно, если в нем планируется использовать API БЭМ-блоков (состояния, выражаемые модификаторами, БЭМ-события и
т. п.). Если использовать БЭМ-предметную область не планируется,
инфраструктурный код можно оформлять в виде [ym][]-модуля.

**Пример:**

```js
modules.define('router', function(provide) {

provide({
    route : function() { /* ... */ }
});

});
```

***

<a name="inher"></a>
### Наследование

Одна и та же функциональность может быть востребована в нескольких блоках или элементах проекта.
Например, разные блоки могут обращаться за данными к бэкенду, используя AJAX, или совершать однотипные операции с DOM-деревом и т.д.
Чтобы избежать ненужных повторов в коде, общую функциональность можно инкапсулировать в виде модулей, а затем добавлять к блокам.

Наследование позволяет повторно использовать функциональность блока, расширяя её новой логикой.
В `i-bem.js` доступно несколько механизмов наследования. Выбор конкретного механизма зависит от специфики создаваемого блока.

<a name="inher-simple"></a>
#### Простое наследование

В случае простого наследования создаваемый блок объявляется как наследник существующего. Для этого нужно:

1. Указать базовый блок в зависимостях модульной системы.
2. Передать ссылку на базовый блок во втором параметре декларации.

Например, блок `b-block` наследуется от блока `a-block`:

```js
modules.define('a-block', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.declBlock(this.name, {}));

});

modules.define('b-block', ['i-bem-dom', 'a-block'], function(provide, bemDom, ABlock) {

provide(bemDom.declBlock(this.name, ABlock, {}));

});
```

Аналогично для элемента:

```js
modules.define('block__a-elem', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.declElem('block', 'a-elem', {}));

});

modules.define('block__b-elem', ['i-bem-dom', 'block__a-elem'], function(provide, bemDom, BlockAElem) {

provide(bemDom.declElem('block', 'b-elem', BlockAElem, {}));

});
```

Такой механизм позволяет использовать методы базового блока или элемента внутри производного.
Для вызова одноименных методов базового блока служит [вспомогательный метод](i-bem-js-context.ru.md#spec-fields) `this.__base`.

***

**NB** В `i-bem` можно создавать цепочки наследования – блок наследуется от другого, который, в свою очередь, наследуется от третьего и т.д.

***

<a name="inher-over"></a>
#### Доопределение блока или элемента

Чтобы добавить и/или изменить поведение уже существующего блока или элемента, можно **доопределить** базовый класс на *уровне переопределения* проекта.

Для этого в проекте создается декларация блока или элемента с тем же именем, что и у базового. В результате блоку будет доступна вся функциональностью базового.
Реализация одноименных методов и модификаторов, при этом, будет взята из новой декларации.

```js
modules.define('a-block', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.declBlock(this.name, {})); // Объявляем базовый блок

});

modules.define('a-block', ['i-bem-dom'], function(provide, bemDom, ABlock) {

provide(bemDom.declBlock(ABlock, {})); // Доопределяем базовый блок

});
```

Аналогично для элементов с использованием метода `declElem`.

Такая схема наследования часто используется при работе с библиотечными блоками.

<a name="inher-over-modifier"></a>
##### Добавление модификатора

В соответствии с БЭМ-методологией состояния блока или элемента должны описываться [модификаторами](i-bem-js-states.ru.md#modifiers).
Поэтому чтобы расширить функциональность блока или элемента часто нужно реализовать поддержку новых модификаторов.

Для декларации модификатора используется метод `declMod` у класса блока или элемента. В обоих случаях метод принимает аргументы:

1. Хеш с ключами `modName` и `modVal`. Значением для `modName` – имя модификатора, `{String}`. Значением `modVal`, `{String|Boolean|Array}` — значение модификатора.
2. Хеш методов, которые будут доступны для блока с соответствующим модификатором. При наличии одноименных методов и модификаторов, будет использована их реализация из хеша.

```js
modules.define('a-block', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.decl(this.name, {})); // Объявляем базовый блок

});

modules.define('a-block', function(provide, ABlock) {

provide(ABlock.declMod({ modName : 'm1', modVal : 'v1' }, {})); // Доопределяем базовый блок с модификтором _m1_v1

});
```

Аналогично для элементов.

***

**NB** [Cтатические методы][context] блока и элемента будут доступны всем их экземплярам *вне зависимости от значений модификаторов*.
Модификаторы — это свойства экземпляра, а статические методы принадлежат классу и не учитывают состояния модификаторов.

***

<a name="inher-mixins"></a>
#### Миксины

В `i-bem.js` для добавления востребованной функциональности к блокам или элементам могут использоваться специальные сущности – **миксины**.
Главная особенность миксинов состоит в том, что они не участвуют в цепочке наследования. Это позволяет примешивать реализованную в них функциональность
к другим блокам или элементам без риска нарушить их [связи с родительскими классами (`this.__base`)](i-bem-js-context.ru.md#spec-fields).

<a name="inher-mixins-mixindecl"></a>
##### Декларация блока-микса

Для декларации миксина используется метод `declMixin` модуля `i-bem-dom` (или `i-bem`).

Метод принимает параметры:

1. Методы экземпляра — `{Object}`.
2. Статические методы (методы класса) — `{Object}`.

```js
modules.define('my-mixin', ['i-bem-dom'], function(provide, bemDom) {

provide(bemDom.declMixin(
    { /* методы экземпляра */ },
    { /* статические методы */ }
));

});
```

***

**NB** Миксины нельзя инстанцировать и использовать как самостоятельный блок.

***

<a name="inher-mixins-declwithmixin"></a>
##### Примешивание миксина

Чтобы примешать к блоку или элементу один или несколько миксинов, необходимо:

1. Указать необходимые миксины в зависимостях модульной системы.
2. Передать ссылку миксин (или миксины) во втором параметре декларации блока или элемента.

```js
modules.define('my-block', ['i-bem-dom', 'my-mixin'], function(provide, bemDom, myMixin) {

provide(bemDom.declBlock(this.name, myMixin,
    { /* методы экземпляра */ },
    { /* статические методы */ }
));

});
```

В случае нескольких миксинов:

```js
modules.define('my-block', ['i-bem-dom', 'a-mixin', 'b-mixin'], function(provide, bemDom, aMixin, bMixin) {

provide(bemDom.declBlock(this.name, [aMixin, bMixin], {}));

});
```

Аналогично для элемента с использованием метода `declElem`.

***

**NB** В случае если нужно задекларировать наследование и миксины одновременно, во второй параметр следует передавать массив.
Родительский класс обязательно должен быть первым элементом этого массива.

```js
modules.define('b-block', ['i-bem-dom', 'a-block', 'a-mixin'], function(provide, bemDom, ABlock, aMixin) {

provide(bemDom.declBlock(this.name, [ABlock, aMixin], {}));

});
```

***

<a name="trigger-decl"></a>
#### Декларация триггеров

[Триггеры][states], выполняемые при установке модификаторов, описываются в декларации блока или элемента. Для этого в хеше методов экземпляра зарезервированы свойства:

* `beforeSetMod` — триггеры, вызываемые до установки **модификаторов**;
* `onSetMod` — триггеры, вызываемые после установки **модификаторов**;

```js
modules.define('my-block', function(provide, bemDom) {

provide(bemDom.declBlock(this.name,
    {
        /* методы экземпляра */
        beforeSetMod: { /* триггеры до установки модификаторов */},
        onSetMod: { /* триггеры после установки модификаторов */ },
    },
    {
        /* статические методы */
    }
));

});
```

Значение свойств `beforeSetMod` и `onSetMod` — хеш, связывающий изменения модификаторов с триггерами. Триггер получает аргументами:

* `modName` – имя модификатора;
* `modVal` – выставляемое значение модификатора;
* `prevModVal` – предыдущее значение модификатора. Для `beforeSetMod` это текущее значение модификатора, которое будет заменено на `modVal`, если триггер не вернет `false`.

```js
bemDom.declBlock(this.name, {
    onSetMod: {
        'mod1': function(modName, modVal, prevModVal) { /* ... */ }, // установка mod1 в любое значение
        'mod2': {
            'val1': function(modName, modVal, prevModVal) { /* ... */ }, // триггер на установку mod2 в значение val1
            'val2': function(modName, modVal, prevModVal) { /* ... */ }, // триггер на установку mod2 в значение val2
            '': function(modName, modVal, prevModVal) { /* ... */ } // триггер на удаление модификатора mod2
        },
        'mod3': {
            'true': function(modName, modVal, prevModVal) { /* ... */ }, // триггер на установку простого модификатора mod3
            '': function(modName, modVal, prevModVal) { /* ... */ } // триггер на удаление простого модификатора mod3
        },
        'mod4': {
            '!val1' : function() { /* ... */ } // декларация для изменения mod4 в любое значение, кроме val1
            '~val2' : function() { /* ... */ } // декларация для изменения значения mod4 из val2 в любое другое значение
        },
        '*': function(modName, modVal, prevModVal) { /* ... */ } // триггер на установку любого модификатора в любое значение
    }
})
```

Для триггера на установку любого модификатора блока в любое значение
существует сокращенная форма записи:

```js
beforeSetMod: function(modName, modVal, prevModVal) { /* ... */ }
onSetMod: function(modName, modVal, prevModVal) { /* ... */ }
```

Аналогично для элементов с использованием метода `declElem`.

***

**NB** Если модификаторы были заданы в HTML-элементе блока или элемента до момента его инициализации,
триггеры на установку данных модификаторов **не выполняются**.
Экземпляр в этом случае получает начальное состояние, а не меняет его.

***


[ym]: https://github.com/ymaps/modules

[i-bem]: https://ru.bem.info/libs/bem-core/current/desktop/i-bem/jsdoc/

[i-bem-dom]: https://ru.bem.info/libs/bem-core/current/desktop/i-bem-dom/jsdoc/

[decl]: ./i-bem-js-decl.ru.md

[context]: ./i-bem-js-context.ru.md

[states]: ./i-bem-js-states.ru.md
