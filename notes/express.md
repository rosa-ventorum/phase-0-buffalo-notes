Сервер - программа, способная принимать и обрабатывать запросы от других программ

Клиент - программа, которая отправляет запросы серверу и получает от него ответы

Протокол - набор правил, по которому общается клиент и сервер

Сервер получает request и отправляет response

HTTP - HyperText Transfer Protocol - протокол передачи HTML или бирарного кода

HTTP 1.1 - текстовый протокол

HTTP/2 и HTTP/3 - бинарные протоколы (пока на начальной стадии)

TCP/IP - сетевой прокол, в него входит HTTP. На этом протоколе построено все взаимодействие пользователей в IP-сетях

Для HTTP/HTTPS - порты 8080 или 80 и 443

Для FTP - порты 20/21

SMTP - почтовый - 25

SSH - для безопасного соединения с удаленной машиной - 22
---

HTTP-ресурс - любой ресурс, доступный в WWW

URI - унифицированный идентификатор ресурса

URL - унифицированный определитель местонахождения ресурса

URN - унифицированное имя ресурса

## HTTP-запрос

Он состоит из:

    1) стартовой строки (метода запроса - GET или POST)

    2) пути - "/some/path/"

    3) версия протокола - "HTTP/1.1"

Метод - это желаемое действие над ресурсом

GET - получить данные (read)

POST - отправить данные (create)

PUT - изменить данные (update)

DELETE - удалить данные (delete)

### HTTP - заголовки

    - мета-информация о запросе или ответе

В заголовке нужно указывать, что мы формируем ответ в виде json или html

    - Content-Type: application/json

    - Content-Type: text/html

### Тело сообщения
Может быть пустым, либо содержать произвольные данные. Всегда отделяется пустой строкой

Важно не путать что мы делаем, если мы получаем то используем GET, если добавляем - POST

Когда мы вводм в адресную строку некий путь мы формируем GET-request на сервер, если все ок то мы как respoce получаем http файл

### HTTP - ответ

Версия протокола - HTTP/1.1 200 OK

Версия протокола - код ответа - текст кода ответа

Коды бывают разные
    - 100 - информация (например, смена протокола с HTTP на WebSocket)
    - 200 - успешное подключение, запрос выполнен (например, 200 - ок, обезличенный, а 201 - object created (сообщает что что-то было успешно создано))
    - 300 - redirect (переход на другую страницу, или 304 - мы получили инфу из кэша если body не отличается от запроса серверу)
    - 400 - ошибка со стороны клиента (400 - противоположно 200, тоже обезличен, 403 - доступ к данным запрещен, 404 - запрошенные данные не найдены)
    - 500 - ошибка со стороны сервера

text/plain - простой текст

text/html - HTML-документ

application/json - данные в JSON-формате

application/xml - XML-документ

multipart/form-data - бинарные данные (картинки, 3D-модели и тп)

## БАЗА

Сервер это программа, которая постоянно крутится и ждет responce

Клиент отправляет request и отправляет его на сервер, например Request /users GET

После этого он проходится по роутеру, который сравнивает что пришло с клиента и обращается к модели, т.е. БД, а потом формирует ответ и отправляет его на клиент

У нас есть
    - контроллер - сервер
    - браузер - view
    - модель - база данных

Это все MVC - model view controller

<<<<<<<<<<<=======================================================>>>>>>>>>>>

<<<<<<<<<<<=======================================================>>>>>>>>>>>

<<<<<<<<<<<=======================================================>>>>>>>>>>>

# EXPRESS ИНИЦИАЛИЗАЦИЯ ПРОЕКТА!

// инициализация сервера
- `npm i express morgan`
- `npm i -D nodemon` - вотчер, который автоматом перезагружает сервер при изменениях

// какая-то штука чтобы все работало
- `npm i @babel/core @babel/preset-env @babel/preset-react @babel/register`
- `npm i react react-dom`

npm run dev

"dev": "nodemon src/app.js --ext js,jsx"

создаем папку src

Там файл `app.js`
``` javascript
const express = require('express'); // подключаем экспресс - это класс
const app = express(); // это инстанс
const morgan = require("morgan") // третья миддлварина, всегда будет в скелете
const { User } = require db blabla

app.use(express.urlencoded({ extended: true }));
app.use(express.json()); // преобразует json в объект
app.use(morgan("dev")) // логирование

const PORT = 3000;

app.listen(PORT, () => {
  console.log(`Сервер запущен: http://localhost:${PORT}`)
});
```

Чтобы сервер начал обрабатывать респонсы нужно написать эндпоинты

Чтобы отрисовать главную страницу нужно написать GET-запрос

``` javascript
app.get("/", (req, res) => {
  res.status(202).send('<h2>Hello world</h2>')
})
// внутри передается путь и коллбек, т.е. путь это что мы вводим в адресную строку
// А коллбек это то что мы отправим на клиент
```

- `res.status(202)` - не завершает респонс
- `res.sendStatus(400)` - завершает респонс и отправляет статус

``` javascript
app.get("/users", async (req, res) => {
  try {
    const users = await User.findAll();
    res.json(users);
  } catch (error) {
    res.sendStatus(400)
  }
})
```

`thunderClient` - утилита которая позволяет прокидывать реквесты на сервер

и смотреть какой пришел респонс и с каким кодом

``` javascript
app.post("/users", async (req, res) => {
  try {
    const user = await User.create({})
    res.end() // просто завершает респонс метода, потому что в любом запросе должно быть завершение
  }
})
```

чтобы получить body нужно 2 промежуточных обработчика - миддлварки

чтобы достать параметры из query (?= после адресной строки)

- `const queryParams = req.query;` - для гет-запроса
- `const queryParams = req.body;` - для пост-запроса

users/one?id=2&clean=1

## ROUTER

создаем папку routers

В ней создаем файлы
- `index-router.js` - в нем будут храниться роуты с главной страницы или одинокие лендинги с главной страницы
- `user-router.js` - в нем будут храниться роуты где путь начинается с /user/...

В `user-router.js`

``` javascript
const userRouter = require('express').Router();

userRouter.get или post, в общем он заменяет нам app

module.exports = userRouter;
```

В `app.js`

``` javascript
const indexRouter = require('./routers/index-router'); // объявляем роуты
const userRouter = require('./routers/user-router')

app.use('/', indexRouter);
app.use('/users', userRouter); // делаем "редирект" на файл с роутами
```
//? Блокируется ли выполнение кода при синхронном чтении файла ???

# REACT SSR

SSR - server side rendering

клиент просто получает статичную верстку

Взрослый реакт выполняется на стороне клиента

REACT SSR - библиотека для отрисовки пользовательских интерфейсов

От отличается от вью тем, что он чуть более свободный, не дает готовых решений от фреймворка

JSX - расширение компонентов в реакте, в нем можно писать html код полноценный с подсветкой

и вставкой переменных, условными переменными и циклами

BABEL - инструмент который позволяет перекомпилировать JS код в некоторый другой код

Babel.JS – это транспайлер, переписывающий код на ES6 (ES-2015) в код на предыдущем стандарте ES5

# ИНИЦИАЛИЗАЦИЯ

- `npm i @babel/core @babel/preset-env @babel/preset-react @babel/register`
- `npm i react react-dom`

Во-первых нужно сделать конфиг в корне `.babelrc`, в нем пишем
``` javascript
{
  "presets": ["@babel/preset-env", "@babel/preset-react"]
}
```

Далее в `app.js` пишем `require("@babel/register")`

Все заработает, просто без сохранения в константу

Далее делаем вьюшки, для этого нужен файл jsx и особый эндпоинт для рендера

Создаем папку views

В ней создаем папку components и pages

Компонент - кнопка, форма, может быть использован где угодно

Страница - каркас с индивидуальными особенностями (страница с черным бг, с белым бг и тд)

А то, что наполняет страницу - компонент

Есть story - это страница, она сожержит кучу feature внутри себя

feature может состоять из кучи компонентов

Далее можно создать файл в папке pages, называем ее например Home.jsx

Называем в паскаль кейсе потому что это по сути (исторически) класс

В этот файл импортируем react
``` javascript
const React = require("react")
const Home = require('./views/pages/Home')
const ReactDOMServer = require('react-dom/server')

module.exports = function Home() {
  const name = "buffaloes"
  return (
    А тут уже html код
    <div> Привет, { name }! </div>
  )
}
```

Скобки нужны для того, чтобы игнорировать перенос строки

Чтобы отправить эту страницу в app.js пишем

`const React = require("react")`

внутри `app.get`
``` javascript
const element = React.createElement(Home, {}); // передаем функцию которая находится в папке JSX
// Пропс - это свойства этого объекта (Home)

const html = ReactDOMServer.renderToStaticMarkup();

res.write("<!DOCTYPE html>")
res.write(html)
res.end();
```
Чтобы вынести эту функцию

в src делаем папку `lib`

там делаем файл `renderTemplate.js`

Забираем импорты react и react-dom

``` javascript
const React = require("react")
const ReactDOMServer = require('react-dom/server')

module.exports = function renderTemplate(component, props, res) {
  const element = React.createElement(component, props);
  const html = ReactDOMServer.renderToStaticMarkup();
  res.write("<!DOCTYPE html>")
  res.write(html)
  res.end();
}
```

А в `app.get` передаем функицю и импортируем ее

`renderTemplate(Home, {}, res)`

---
# ПРОПСЫ

На главной можно добавить форму с инпутами

``` javascript
app.post('/api/v1/posts', async (req, res) => {
  const {title, body} = req.body;

  await Post.create({title, body})

  res.sendStatus(201)
})
```

в файле `home.jsx`

``` javascript
<form method="POST" action="/api/v1/posts">
  <input placeholder='title' name='title'>
  <input placeholder='text' name='body'>
  <button type='submit'>ADD</button>
</form>
```

Можем прокинуть новую страницу

в файле `List.jsx`

``` javascript
const React = require("react");
const Button = require("../components/Button")

module.exports = function Button(props) {
  return (
    <form method="POST" action="/api/v1/posts">
      <input placeholder='title' name='title'>
      <input placeholder='text' name='body'>
      <Button type="submit" text={'ad' + 'd'}/> // это компонент реакта Button
    </form>
  )
}
```

в папке components в файле `Button.jsx`

``` javascript
const React = require("react");

module.exports = function Button({type, text, color}) { // тут наши пропсы
  return (
    <Layout test="123">
      <button style={{ background: color, padding: "10px", border: "none" }} type={type}>{text}</button>
      // <>123</> - это "пустой див", jsx-фрагмент, в него можно оборачивать
      // несколько тэгов подряд без обертки
    </Layout>
  )
}
```

Если в атрибут в jsx передаем просто border то в компоненте на borderRadius: border ? "8px" : "0px"

Чтобы сделать обертку можно сделать компонент Layout.jsx прямо в папке views

У пропса в `Layout` есть зарезервированное свойство `children`, чтобы это свойство изменить

Нужно layoutom обернуть что угодно (любой компонент)

``` javascript
<Layout test="123">
  <div>123</div>
</Layout>
```

Из пропса можно достать то что внутри него

HOC - high ordered component

Чтобы сделать цикл по пропсу-массиву

``` javascript
{ posts.map(el => (
  <div>
    {el.title}
    {el.body}
  </div>
)) }
```

# СТАТИКИ, СТИЛИ

в `app.js`

-`app.use(express.static(path.join(process.cwd(), "public")))` - вместо dirname

public/css/style.css

В реакте нет классов, есть className="card"

А в layout в линке href пишем css/styles.css

<div className="card"></div>


# ДЕНЬ 3 - MIDDLEWARE DOTENV

Чтобы закрыть доступ к окружению, паролям и портам, энкриптят и декриптят пароли

Нужно использовать process.env

`npm i dotenv` - библиотека, которая позволяет использовать защищенность

в app.js
require('dotenv').config()

В корне создаем .env
Там прописываем важную инфу

```
PORT=3001
```

Можно посмотреть что находится в process.env через консоль лог

Это все большой объект с полями, из которого можно декомпозировать или обратиться по ключу

Чтобы прокинуть объект настройки базы данных

В базах данных есть строка идентификации

`DATABASE=[dialect]://[user]:[password]@[host]:[port]/[dbname]`

Эта строка есть в документации постгреса - на каждую СУБД есть авторизационные строки

`DATABASE=postgres://admin:123@localhost:5432/dbname`

```
{
    "development": {
        "use_env_variable": "DATABASE"
    }
}
```
Чтобы это работало нужно еще импортировать `require('dotenv').config()` в файле `.sequelizerc`

Это все делается до миграций

# BOOTSTRAP

Пишем лейаут он будет повторяться из проекта в проект

Создаем jsxы

Заходим на bootstrap 5, в components есть куча разных приколов

Подключем БТ через CDN via jsDelivr (4 строчки)

Скрипты подключаем перед закрытием тэга body в layout

# Параметризированный get запрос

``` javascript
router.get('/posts/:id', (req, res) => {
    console.log(req.params) // {id: '123'}
    res.end();
})
```

Параметризированные эндпоинты должны всегда находиться в нижней части файла (в конце)

## Промежуточные обработчики

До срабатывания ендпоинтов на каждый запрос он запускает обработчики

Создание своего обработчика

/src/middlewares/isAdmin.js
``` javascript
module.exports = function isAdmin(req, res, next) {
    const admin = true; // это будет браться из сессии
    if (admin) {
        next();
    } else {
        res.send("Вы не админ!")
    }
}
```

в app.js
`app.use(isAdmin)`
`const isAdmin = require('./middlewares/isAdmin')`

это подключение срабатывает на каждый запрос

Еще можно подключить миддлварку isAdmin в ендпоинт после пути

`router.get("/dev", isAdmin, async (req, res) => ...)`

app.use((req, res, next) => {
    console.log("К нам на сайт зашли!")
    next();
})