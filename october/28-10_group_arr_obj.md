
# Урок: Группировка данных и полезные методы работы с массивами и объектами в Flask

В этом уроке мы создадим простое приложение Flask, которое группирует список продуктов по категориям и отображает их в HTML-шаблоне. Также разберём методы массивов и объектов Python, полезные для динамической работы с данными.

## Код для группировки данных

```python
from flask import Flask, render_template
from itertools import groupby
from operator import itemgetter

app = Flask(__name__)

@app.route('/grouped-products')
def grouped_products():
    products = [
        {'name': 'Apple', 'category': 'Fruits'},
        {'name': 'Orange', 'category': 'Fruits'},
        {'name': 'Tomato', 'category': 'Vegetables'},
        {'name': 'Cucumber', 'category': 'Vegetables'}
    ]
    products.sort(key=itemgetter('category'))
    grouped_products = {k: list(g) for k, g in groupby(products, key=itemgetter('category'))}
    return render_template('grouped_products.html', products=grouped_products)
```

### Объяснение кода

1. **Импорт библиотек**: Мы импортируем `Flask`, `render_template` из `flask`, `groupby` из `itertools` и `itemgetter` из `operator` для группировки и сортировки данных.
2. **Создание списка продуктов**: Продукты представлены в виде словарей с именем (`name`) и категорией (`category`).
3. **Сортировка списка**: Сортируем продукты по ключу `category`.
4. **Группировка продуктов**: Используем `groupby` для создания словаря `grouped_products`, где ключи — это категории, а значения — списки продуктов.

### Итоговая структура данных

После выполнения кода переменная `grouped_products` будет содержать следующую структуру:

```python
grouped_products = {
    'Fruits': [
        {'name': 'Apple', 'category': 'Fruits'},
        {'name': 'Orange', 'category': 'Fruits'}
    ],
    'Vegetables': [
        {'name': 'Tomato', 'category': 'Vegetables'},
        {'name': 'Cucumber', 'category': 'Vegetables'}
    ]
}
```

Эти данные передаются в шаблон `grouped_products.html`.

## Пример шаблона

```html
<!DOCTYPE html>
<html lang="ru">
<head>
    <meta charset="UTF-8">
    <title>Сгруппированные продукты</title>
</head>
<body>
    {% for category, items in products.items() %}
        <h2>{{ category }}</h2>
        <ul>
            {% for item in items %}
                <li>{{ item.name }}</li>
            {% endfor %}
        </ul>
    {% endfor %}
</body>
</html>
```

### Результат в браузере

После запуска приложения Flask и перехода на страницу `/grouped-products`, вы увидите следующий вывод:

**Fruits**
- Apple
- Orange

**Vegetables**
- Tomato
- Cucumber

## Полезные методы массивов (списков) и объектов (словарей) в Python

### Методы для массивов (списков)

- **`append()`**: добавляет элемент в конец списка.
- **`extend()`**: расширяет список, добавляя все элементы из переданного итерируемого объекта.
- **`insert(index, element)`**: вставляет элемент в определённое место.
- **`remove(element)`**: удаляет первый найденный элемент с указанным значением.
- **`pop(index)`**: удаляет и возвращает элемент по индексу.
- **`sort()`**: сортирует список.
- **`reverse()`**: разворачивает список.
- **`count(element)`**: возвращает количество определённых элементов в списке.
- **`index(element)`**: находит индекс первого вхождения элемента.

Эти методы полезны для обработки данных перед отображением в шаблонах Flask.

### Методы для объектов (словарей)

- **`get(key, default)`**: возвращает значение по ключу, если он существует, иначе – значение по умолчанию.
- **`update(dict)`**: обновляет словарь значениями из другого словаря.
- **`pop(key)`**: удаляет ключ и возвращает его значение.
- **`keys()`** и **`values()`**: возвращают списки всех ключей или значений словаря.
- **`items()`**: возвращает пары ключ-значение для итерации.

Эти методы будут полезны для передачи и динамического отображения данных в HTML-шаблонах Flask, особенно при использовании Jinja.

## Заключение

Этот урок показал, как использовать методы для группировки и обработки данных с последующим динамическим отображением в Flask. Методы массивов и объектов позволяют гибко управлять данными и упрощают подготовку данных для фронтенд-части приложения.
