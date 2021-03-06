# uri

Блок предоставляет объект, содержащий набор методов для декодирования строки из формата URI.

## Обзор

### Свойства и методы объекта

| Имя | Возвращаемое значение | Описание |
| --- | --------------------- | -------- |
| <a href="#fields-decodeURI">decodeURI</a>(<code>{String} str</code>) | <code>{String}</code> | Служит для декодирования URI. |
| <a href="#fields-decodeURIComponent">decodeURIComponent</a>(<code>{String} str</code>) | <code>{String}</code> | Служит для декодирования URI компонента. |

### Элементы блока

| Элемент | Способы использования | Описание |
| --------| --------------------- | -------- |
| <a href="#elems-querystring">querystring</a> | <code>JS</code> | Элемент предоставляет объект, содержащий набор методов для работы со строкой запроса формата URI. предназначен для декодирования строки из формата URI. |

#### Свойства и методы объекта

| Элемент | Имя | Возвращаемое значение | Описание |
| ------- | --- | --------------------- | -------- |
| <a href="#elems-querystring">querystring</a> | <a href="#elems-querystring-fields-parse">parse</a>(<code>{String} str</code>) | <code>{Object}</code> | Формирует объект на основании параметров запроса адресной строки. |
| | <a href="#elems-querystring-fields-stringify">stringify</a>(<code>{Object} obj</code>) | <code>{String}</code> | Формирует строку запроса на основании свойств объекта. |

### Публичные технологии блока

Блок реализован в технологиях:

* `vanilla.js`

<a name="fields"></a>
### Свойства и методы объекта

Оба метода являются оберткой над соответствующими стандартными методами JavaScript `decodeURI` и `decodeURIComponent`.

В ходе работы методы проверяют соответствие формата переданной строки UTF-8. При несоответствии генерируется ошибка.

<a name="fields-decodeURI"></a>
#### Метод `decodeURI`

Служит для декодирования URI. Метод идентичен стандартному методу JavaScript [decodeURI](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/decodeURI), но поддерживает кириллическую кодировку `CP-1251`.

Принимаемые аргументы:

* `str` `{String}` – строка с последовательностями экранирования. Обязательный аргумент.

Возвращаемое значение: `{String}`. В случае если последовательности экранирования в строке не найдены метод возвращают ее без изменений.

```js
modules.require('uri', function(uri){
    uri.decodeURI("https://developer.mozilla.org/ru/docs/JavaScript_%D1%88%D0%B5%D0%BB%D0%BB%D1%8B");
    // "https://developer.mozilla.org/ru/docs/JavaScript_шеллы"
})
```


<a name="fields-decodeURIComponent"></a>
#### Метод `decodeURIComponent`

Служит для декодирование компонента URI. Метод идентичен стандартному методу JavaScript [decodeURIComponent](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/decodeURIComponent), но поддерживает кириллическую кодировку `CP-1251`.

Принимаемые аргументы:

* `str` `{String}` – строка с последовательностями экранирования. Обязательный аргумент.

Возвращаемое значение: `{String}`. В случае если последовательности экранирования в строке не найдены метод возвращают ее без изменений.

```js
modules.require('uri', function(uri){
    uri.decodeURIComponent("JavaScript_%D1%88%D0%B5%D0%BB%D0%BB%D1%8B");
    // "JavaScript_шеллы"
})
```
<a name="elems"></a>
### Элементы блока

<a name="elems-querystring"></a>
#### Элемент `querystring`

Элемент предоставляет объект, содержащий набор методов для работы со строкой запроса формата URI.


<a name="elems-name-fields"></a>
### Свойства и методы объекта

<a name="elems-querystring-fields-parse"></a>
#### Метод `parse`

Формирует объект на основании строки параметров в формате URI.

Принимаемые аргументы:

* `str` `{String}` – строка с параметрами в виде пар ключ-значение. Ключ отделяется от значения символом `=`. Пары разделяются символом `&`. В процессе обработки, ключи и значения декодируются из формата URI. Обязательный аргумент.

Возвращаемое значение: `{Object}`. Объект, сформированный на основании параметров адресной строки.

<a name="elems-querystring-fields-stringify"></a>
#### Метод `stringify`

Формирует строку запроса в формате URI на основании объекта.

Принимаемые аргументы:

`obj` `{Object}` – объект, на основании которого формируется строка. Обязательный аргумент.

Возвращаемое значение: `{String}`. Имена свойств в строке отделяются от значений символом `=`, пары разделяются символом `&`.
