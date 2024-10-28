
# Flask Interview Questions and Technical Tasks

## Теоретические вопросы по Flask

1. **Что такое Flask и чем он отличается от других фреймворков, таких как Django?**
   - **Ответ**: Flask — это микро-фреймворк для Python, предназначенный для создания веб-приложений. Он легковесный и модульный, предоставляя разработчику больше гибкости. В отличие от Django, который является "батарейками в комплекте" фреймворком, Flask не имеет встроенных инструментов, таких как ORM или аутентификация, но предоставляет возможность добавлять их по мере необходимости, обеспечивая более гибкий и минималистичный подход.

2. **Как работает маршрутизация (routing) в Flask?**
   - **Ответ**: В Flask маршрутизация определяется через декоратор `@app.route()` или метод `add_url_rule()`. Эти методы связывают URL-адрес с определенной функцией представления. Когда приходит запрос на определенный URL, Flask сопоставляет его с маршрутом и вызывает соответствующую функцию, которая возвращает ответ.

3. **Какие бывают HTTP методы и как они реализуются в Flask?**
   - **Ответ**: Основные HTTP методы включают GET, POST, PUT, DELETE и PATCH. В Flask они указываются как параметры декоратора `@app.route()`. Например, `@app.route('/example', methods=['GET', 'POST'])` определяет, что функция может обрабатывать запросы GET и POST.

4. **Как осуществляется обработка форм и запросов в Flask?**
   - **Ответ**: В Flask данные форм доступны через объект `request`, который импортируется из модуля `flask`. Например, `request.form` содержит данные из формы, отправленной через POST-запрос. Также можно использовать `request.args` для получения данных из строки запроса (GET-параметров).

5. **Как управлять сессиями в Flask?**
   - **Ответ**: Flask поддерживает управление сессиями с помощью модуля `flask.session`, который хранит данные о пользователе между запросами. Сессии по умолчанию подписываются с использованием `SECRET_KEY` для обеспечения безопасности и хранятся на стороне клиента в виде защищенных cookie.

6. **Что такое Blueprint в Flask и для чего он используется?**
   - **Ответ**: Blueprint — это способ организации кода приложения в более структурированную и модульную форму. Blueprints позволяют разделить приложение на различные модули или компоненты, каждый из которых может иметь свои маршруты, статические файлы и шаблоны. Это особенно полезно для крупных приложений.

7. **Как в Flask организована работа со статическими файлами?**
   - **Ответ**: Статические файлы, такие как CSS, JavaScript и изображения, размещаются в папке `static`. Flask предоставляет URL `/static/filename` для доступа к этим файлам. Например, файл `static/style.css` будет доступен по адресу `/static/style.css`.

8. **Как использовать Jinja2 шаблоны и что такое контекст в Flask?**
   - **Ответ**: Jinja2 — это шаблонный движок, встроенный в Flask, который позволяет вставлять Python-код в HTML и обрабатывать его. Контекст — это данные, которые передаются в шаблон из представления с помощью функции `render_template()`. Например, `render_template('index.html', user=user)` передает переменную `user` в шаблон `index.html`.

9. **Как Flask поддерживает работу с базой данных?**
   - **Ответ**: Flask не предоставляет встроенного ORM, но часто используется с SQLAlchemy, который можно интегрировать в проект через Flask-SQLAlchemy. Flask-SQLAlchemy предоставляет методы для взаимодействия с базой данных и работы с моделями. Также можно использовать `Flask-Migrate` для управления миграциями базы данных.

10. **Какие средства для тестирования приложений предлагает Flask?**
    - **Ответ**: Flask предоставляет тестовый клиент через `app.test_client()`, который позволяет отправлять запросы к приложению и проверять ответы, не поднимая сервер. Кроме того, Flask поддерживает тестирование с помощью `pytest` и `unittest`.

---

## Технические задачи по Flask

### Легкие задачи

1. **Задача:** Создать простое Flask-приложение с маршрутом `/hello`, который возвращает строку "Hello, World!".
   - **Решение**:
     ```python
     from flask import Flask

     app = Flask(__name__)

     @app.route('/hello')
     def hello():
         return "Hello, World!"

     if __name__ == '__main__':
         app.run()
     ```

2. **Задача:** Добавить маршрут `/greet/<name>`, который принимает имя в URL и возвращает сообщение "Hello, <name>!".
   - **Решение**:
     ```python
     @app.route('/greet/<name>')
     def greet(name):
         return f"Hello, {name}!"
     ```

---

### Средние задачи

1. **Задача:** Реализовать форму для отправки имени пользователя с помощью POST-запроса на маршрут `/submit`. Форма должна отправлять имя пользователя, и приложение должно отображать приветственное сообщение с этим именем на странице `/welcome`.
   - **Решение**:
     ```python
     from flask import Flask, request, render_template_string

     app = Flask(__name__)

     @app.route('/submit', methods=['GET', 'POST'])
     def submit():
         if request.method == 'POST':
             name = request.form['name']
             return render_template_string("<h1>Welcome, {{ name }}!</h1>", name=name)
         return render_template_string('''
             <form method="post">
                 <input type="text" name="name" placeholder="Enter your name">
                 <input type="submit">
             </form>
         ''')

     if __name__ == '__main__':
         app.run()
     ```

2. **Задача:** Создать API с маршрутом `/json` для обработки GET-запроса, который возвращает JSON-ответ с данными о пользователе (например, имя и возраст).
   - **Решение**:
     ```python
     from flask import Flask, jsonify

     app = Flask(__name__)

     @app.route('/json')
     def get_json():
         return jsonify({"name": "John", "age": 30})

     if __name__ == '__main__':
         app.run()
     ```

---

### Сложные задачи

1. **Задача:** Создать CRUD-приложение для управления списком задач (tasks) с использованием Flask и SQLite. Реализовать функции добавления, удаления, редактирования и просмотра задач.
   - **Решение**:
     - Создать базу данных с таблицей для хранения задач.
     - Настроить маршруты для CRUD-операций.
     - Реализовать шаблоны для отображения задач и форм.

     ```python
     from flask import Flask, request, render_template, redirect, url_for
     from flask_sqlalchemy import SQLAlchemy

     app = Flask(__name__)
     app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///tasks.db'
     db = SQLAlchemy(app)

     class Task(db.Model):
         id = db.Column(db.Integer, primary_key=True)
         description = db.Column(db.String(200), nullable=False)

     db.create_all()

     @app.route('/')
     def index():
         tasks = Task.query.all()
         return render_template('index.html', tasks=tasks)

     @app.route('/add', methods=['POST'])
     def add_task():
         new_task = Task(description=request.form['description'])
         db.session.add(new_task)
         db.session.commit()
         return redirect(url_for('index'))

     @app.route('/delete/<int:id>')
     def delete_task(id):
         task = Task.query.get(id)
         db.session.delete(task)
         db.session.commit()
         return redirect(url_for('index'))

     if __name__ == '__main__':
         app.run()
     ```

2. **Задача:** Создать API с использованием Flask и SQLAlchemy для управления списком пользователей. Реализовать эндпоинты для создания, удаления, редактирования и получения информации о пользователях. Реализовать также авторизацию с использованием токенов JWT для обеспечения доступа к маршрутам.
   - **Решение**:
     - Настроить Flask и SQLAlchemy для работы с базой данных пользователей.
     - Создать эндпоинты для CRUD-операций.
     - Использовать библиотеку `flask-jwt-extended` для реализации JWT-авторизации.

     ```python
     from flask import Flask, jsonify, request
     from flask_sqlalchemy import SQLAlchemy
     from flask_jwt_extended import JWTManager, create_access_token, jwt_required

     app = Flask(__name__)
     app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///users.db'
     app.config['JWT_SECRET_KEY'] = 'super-secret'
     db = SQLAlchemy(app)
     jwt = JWTManager(app)

     class User(db.Model):
         id = db.Column(db.Integer, primary_key=True)
         username = db.Column(db.String(80), unique=True, nullable=False)
         password = db.Column(db.String(120), nullable=False)

     db.create_all()

     @app.route('/login', methods=['POST'])
     def login():
         username = request.json.get('username')
         password = request.json.get('password')
         user = User.query.filter_by(username=username, password=password).first()
         if user:
             access_token = create_access_token(identity=username)
             return jsonify(access_token=access_token)
         return jsonify({"msg": "Bad credentials"}), 401

     @app.route('/user', methods=['POST'])
     @jwt_required()
     def create_user():
         username = request.json['username']
         password = request.json['password']
         user = User(username=username, password=password)
         db.session.add(user)
         db.session.commit()
         return jsonify({"msg": "User created successfully"}), 201

     if __name__ == '__main__':
         app.run()
     ```

---

## Дополнительные задачи по популярным алгоритмам

### 1. Алгоритмы сортировки

1. **Задача:** Реализуйте сортировку выбором (Selection Sort) для списка чисел.
   - **Решение**:
     ```python
     def selection_sort(arr):
         for i in range(len(arr)):
             min_index = i
             for j in range(i + 1, len(arr)):
                 if arr[j] < arr[min_index]:
                     min_index = j
             arr[i], arr[min_index] = arr[min_index], arr[i]
         return arr
     ```

2. **Задача:** Напишите функцию для сортировки массива слиянием (Merge Sort).
   - **Решение**:
     ```python
     def merge_sort(arr):
         if len(arr) > 1:
             mid = len(arr) // 2
             left = arr[:mid]
             right = arr[mid:]

             merge_sort(left)
             merge_sort(right)

             i = j = k = 0
             while i < len(left) and j < len(right):
                 if left[i] < right[j]:
                     arr[k] = left[i]
                     i += 1
                 else:
                     arr[k] = right[j]
                     j += 1
                 k += 1

             while i < len(left):
                 arr[k] = left[i]
                 i += 1
                 k += 1

             while j < len(right):
                 arr[k] = right[j]
                 j += 1
                 k += 1
         return arr
     ```

3. **Задача:** Реализуйте алгоритм быстрой сортировки (Quick Sort).
   - **Решение**:
     ```python
     def quick_sort(arr):
         if len(arr) <= 1:
             return arr
         pivot = arr[len(arr) // 2]
         left = [x for x in arr if x < pivot]
         middle = [x for x in arr if x == pivot]
         right = [x for x in arr if x > pivot]
         return quick_sort(left) + middle + quick_sort(right)
     ```

### 2. Алгоритмы поиска

1. **Задача:** Реализуйте двоичный поиск (Binary Search) для отсортированного списка.
   - **Решение**:
     ```python
     def binary_search(arr, target):
         left, right = 0, len(arr) - 1
         while left <= right:
             mid = (left + right) // 2
             if arr[mid] == target:
                 return mid
             elif arr[mid] < target:
                 left = mid + 1
             else:
                 right = mid - 1
         return -1
     ```

2. **Задача:** Найдите первое и последнее вхождение заданного элемента в отсортированном списке.
   - **Решение**:
     ```python
     def find_first_and_last(arr, target):
         def find_index(arr, target, find_first):
             left, right, result = 0, len(arr) - 1, -1
             while left <= right:
                 mid = (left + right) // 2
                 if arr[mid] == target:
                     result = mid
                     if find_first:
                         right = mid - 1
                     else:
                         left = mid + 1
                 elif arr[mid] < target:
                     left = mid + 1
                 else:
                     right = mid - 1
             return result
         return find_index(arr, target, True), find_index(arr, target, False)
     ```

3. **Задача:** Реализуйте поиск минимального и максимального элементов в массиве.
   - **Решение**:
     ```python
     def find_min_max(arr):
         if not arr:
             return None, None
         min_val, max_val = arr[0], arr[0]
         for num in arr:
             if num < min_val:
                 min_val = num
             elif num > max_val:
                 max_val = num
         return min_val, max_val
     ```

### 3. Алгоритмы работы с данными

1. **Задача:** Удалите дубликаты из списка и сохраните порядок элементов.
   - **Решение**:
     ```python
     def remove_duplicates(arr):
         seen = set()
         return [x for x in arr if not (x in seen or seen.add(x))]
     ```

2. **Задача:** Найдите два элемента в списке, сумма которых равна заданному числу.
   - **Решение**:
     ```python
     def two_sum(arr, target):
         nums_map = {}
         for i, num in enumerate(arr):
             complement = target - num
             if complement in nums_map:
                 return [nums_map[complement], i]
             nums_map[num] = i
         return []
     ```

3. **Задача:** Разверните слова в строке, сохраняя исходный порядок слов.
   - **Решение**:
     ```python
     def reverse_words(sentence):
         return ' '.join(word[::-1] for word in sentence.split())
     ```

---
