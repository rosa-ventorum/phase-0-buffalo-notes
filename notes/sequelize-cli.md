## ИНИЦИАЛИЗАЦИЯ ПРОЕКТА И БАЗЫ ДАННЫХ

1. `npm init -y`
2. `npx create-gitignore node`
3. `npm init @eslint/confing` // далее идет настройка линта, все по классике

4. `npm i pg pg-hstore sequelize`
5. `npm i -D sequelize-cli`

6. в корне проекта создаем файл `.sequelizerc`, который инициализирует структуру БД внутри проекта, в который копируем следующие строчки:

    ```
    const path = require('path');
    
    module.exports = {
        'config': path.resolve('db', 'config', 'database.js'),
        'models-path': path.resolve('db', 'models'),
        'seeders-path': path.resolve('db', 'seeders'),
        'migrations-path': path.resolve('db', 'migrations')
    };
    ```

7. `npx sequelize-cli init` // ицициализируем структуру папок БД на основе `.sequelizerc`, после этого в корне создастся папка `bd`, в которой мы и будет страдать.

8. npm i dotenv // подключаем библиотеку для хранения секретных данных: паролей, портов  и.т.д.
9. в корне проекта создаем файл: ***.env*** с содержимым вида:
    ```
    DB_USERNAME=postgres
    DB_PASSWORD=11111
    DB_HOST=localhost
    DB_NAME=db_name
    DB_PORT=3000
    ```
10. добавдяем .env  в файл .gitignor
11. создаем в коре проекта файл ***env.example*** с содержимым:
    ```
    DB_USERNAME=
    DB_PASSWORD=
    DB_HOST=
    DB_NAME=
    DB_PORT=
    ```
12. подключаем .env в файлах 1) .sequelizerc 2) главном index.js файле 3) db/config/database.j
    первой строкой:
    ```
    require('dotenv').config();

    ```
13. в файле `config/database.json` описываем в объекте `development` нашу базу данных и информацию о подключении

если .env используем:
```
"development": {
    "username": process.env.DB_USERNAME,
    "password": process.env.DB_PASSWORD,
    "database": process.env.DB_NAME,
    "host": process.env.DB_HOST,
    "dialect": "postgres"
  }
```
```Вариант без .env
{
  "development": {
    "username": "admin",
    "password": "123",
    "database": "dbName",
    "host": "127.0.0.1",
    "port": "5432",
    "dialect": "postgres"
  }
}
```
14. в `sqlShell` создаем базу данных через `CREATE DATABASE dbName OWNER admin;`
   Или если у admin есть права на создание БД пишем

- `npx sequelize-cli db:create`

---

## СОЗДАЕМ МОДЕЛИ МИГРАЦИЙ (1 модель = 1 таблица = 1 команда)

1. `npx sequelize-cli model:generate --name Groupname в единственном числе с большой буквы --attributes column1:string,column2:integer`

- // npx sequelize-cli model:generate --name Product --attributes product_name:string
- // npx sequelize-cli model:generate --name User --attributes user_name:string,user_fav_products:integer

после этого создается файл миграций в `migrations`, там можно подправить поля (`allowNull: false`, `unique: true` и тд)
также создается модель по названию в папке `models`, там описывается класс созданной таблицы

учитываем, что сначала создаются все таблички у которых нет зависимых полей (типа `group_id`)

2. в файле миграций чтобы создать референс на другую таблицу, добавляем в поле `somefield_id` следующий код

```
references: {
  model: {
    tableName: "Groups",
  },
  key: "id",
},
```

3. после того как все поправили можно запустить миграции

- `npx sequelize db:migrate`

и посмотреть что получилось в бобре

---

## СОЗДАЕМ СВЯЗИ aka АССОЦИАЦИИ

Чтобы модели знали кто с кем дружит (в секвалайзе), нужно в моделях прописать их ассоциации, потому что постгресу без разницы, он просто выставляет связи, а sequelize есть разница

Для того, чтобы связать две модели, мы в методе **associate** прописываем 

- `this.hasMany(target: models.Student, { foreignKey: 'group_id' })`

где `target` это целевая модель, типа кого мы хотим принять во владение многими

В свою очередь для модели студента

- `this.belongsTo(models.Group, { foreignKey: 'group_id' })`

В этом случае в группе может быть много студентов, а студент может быть только в одной группе. Эта связь называется один ко многим

**_!!! Тот, кто принимает в свой ключ - тот и `belongsTo`, а тот кто отдает ключ - тот `hasMany`._**

Также можно декоструктуризировать `models` на `{ User }`

И в `hasMany` использовать как таргет только `User` вместо `models.User`


## Варианты ассоциаций

1. **Если 1:1**, то главная таблица ` hasOne`, вторая таблица `belongsTo` . Обязательно в миграции `Profile` написать `unique` для `user_id`.
   1.1 Для `User` будет `this.hasOne(Profile, {foreignKey: "user_id"})` .
   1.2 Для `Profile` будет `this.belongsTo(User, {foreignKey: "user_id"})`
2. **Если 1:M**, то главная таблица `hasMany`, вторая таблица `belongsTo`.
   2.1 Для `Library` будет `this.hasMany(Book, {foreignKey: "library_id"})` .
   2.2 Для `Book` будет `this.belongsTo(Library, {foreignKey: "library_id"})`
3. **Если M:M**, то в связующей таблице ничего не пишем.
   3.1 В модели `Student` будет `belongsToMany(Group, {foreignKey: "student_id", through: "student_groups"})` .
   3.2 В модели `Group` будет `belongsToMany(Student, {foreignKey: "group_id", through: "student_groups"})`
---

## СОЗДАЕМ СИДЕРЫ

1. выполняем команды на создание файлов-сидеров

- `npx sequelize-cli seed:generate --name seedProducts`

- `npx sequelize-cli seed:generate --name seedUsers`

2. правим все сидеры, раскомментируем код который находится внутри, заполняем значениями

объект должен выглядеть так:

```
{
  product_name: "Bananas",
  createdAt: new Date(),
  updatedAt: new Date(),
},
```

Нюанс: поля `createdAt` и `updatedAt` должны быть обязательно (как я потом попробовал, может и не обязательно, все и без них работает)

также раскомментируем функцию `down` для отката, там пишем название таблицы чтобы сиды накатывались на чистую

Удаляем лишние пустые объекты в сидере и в балкДелите

3. Сидим!

- `npx sequelize db:seed:all`

Команда чтобы отменить все миграции и сиды, в общем чтобы дропнуть все таблицы и их значения

- `npx sequelize db:migrate:undo:all`

Команда чтобы выполнить только конкретный сидер, допустим, в случае ошибки в одном из сидеров

- `npx sequelize db:seed --seed 20240112071944-seedStudents.js`

Команда-трансформер_уа-а-а-а-а-а

- `npx sequelize db:migrate:undo:all && npx sequelize db:migrate && npx sequelize db:seed:all`

---

## ПИШЕМ ЗАПРОСЫ!!!

Чтобы написать запрос можно выйти в основной файл в директории проекта и там:

1. получаем доступ к нашим моделям через `const { Student } = require('./db/models')`

указываем путь до моделей относительно этого файла и деконструируем нужные модели

2. пишем функцию, которая будет описывать что мы хотим получить. Например:

```
async function findAllStudents () {
    try {
        const students = await Student.findAll( тут тоже могут быть инструкции where );
        console.log(students)
    } catch (error) {
        console.log(error)
    }
}
```

Методы моделей:

- `Model.findAll();` - выдает массив всех результатов, вместе с метаданными.

Для того чтобы отсечь все лишнее, после того как получили этот массив на нем применяем `map(el => el.get({ plain: true }))` и возвращаем полученное.

В общем `el.get({ plain: true })` отсекает лишнее, выдает только исходные данные

- `Model.findOne({ where: { id: someInput, age: someInput2 }})`

Выдает один результат по двум совпадениям

Чтобы сделать `join` нужно в аргумент передать объект с опцией `include { model: Group }`

Эту опцию нужно писать на том же уровне что и `where`

Приклеивает целую ячейку из группы

Их можно чейнить, если прокидывать массив в значение `include`, например:

`include [{ model: Group }, { model: Room }]`

Если мы хотим выкусить из группы определенные атрибуты, то внутри `include` можем писать `attributes: ["name", "id"]`

Эти атрибуты мы можем писать и на верхнем уровне, но обязаны оставить `group_id`

Если не оставим то связям будет не из чего формироваться, получим ошибку

Например, этот запрос джойнит две таблицы по id, показывает заказы, которые сделал некий user_name и выдает только нужные поля (user_name, product_name и order_time):

```
const allOrders = await Order.findAll({
  include: [
    {
      model: User,
      attributes: ["user_name"],
      where: { user_name: ТутПеременнаяИзПараметровФункции },
    },
    { model: Product, attributes: ["product_name"] },
  ],
  attributes: ["order_time"],
});
const allOrdersPlain = allOrders.map((el) => el.get({ plain: true }));
console.log(allOrdersPlain);
```

---

### Прочее...

После всего, как бы не было тяжко:

- `npx sequelize db:drop`

Правила для еслинта чтобы не ругался на лишнее

```
"rules": {
    "strict": 0,
    "quotes": ["error", "double"],
    "no-console": "off",
    "import/no-dynamic-require": "off",
    "global-require": "off"
}
```
