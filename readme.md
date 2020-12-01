![pic](http://i.piccy.info/i9/b6d11db08a3cc3fee10d62a051082c5b/1571984134/191246/1333692/HOW_TO_DEPLOY_A_PROJECT.png)

## Table of contents
 
### [Webpack](#Webpack)
  - [Концепт](#Концепт_Webpack)  
  - [Entry](#Entry)  
  - [Output](#Output)  
  - [Loaders](#Loaders)  
  - [Plugins](#Plugins)   
   
  - [Установка](#Установка)  
  - [Структура](#Структура)   
      - [context](#Context)  
      - [mode](#Mode)   

  - [Loaders_Plugins](#Loaders_Plugins)  
    - [Babel](#babel)  
      - [Support_proposal features](#Support_proposal_features)
      - [babel-polyfill](#Babel_polyfill)
      - [CoreJS](#Core_JS)
    - [React](#React)
      - [React CDN](#React_CDN)
      - [Dynamic import](#Dynamic_import)
    - [Hot loader](#HotLoader)
    - [HTML](#HTML)  
    - [CSS](#CSS)  
    - [Mini CSS](#Mini_CSS)  
    - [SCSS](#SCSS)  
    - [Post CSS](#PostCSS)  
    - [Clean](#Clean)  
    - [Image](#IMAGE)   

  - [Bundle analyzer](#Bundle_Analyzer)
  - [Devserver](#DEVSERVER)  
  - [Devtool](#DEVTOOL)
  - [Chunks](#Chunks)
  - [GH Pages](#GH_PAGES)

#### [Config splitting](#Config_splitting)

### [Gitignore](#Gitignore)

### [Browserlist](#Browserslist)

### [Testing](#Testing)

### [Code quality](#Code_quality)
  - [Editorconfig](#Editorconfig)
  - [Prettier](#Prettier)
  - [Eslint](#Eslint)
  - [Lint_staged](#Lint_staged)
  - [Husky](#Husky)

------------

## Webpack   

Webpack - это менеджер модульных зависимостей, *aka* сборщик модулей.
Webpack анализирует ваше дерево зависимостей, создает для них модули и объединяет всю сеть в управляемые выходные файлы.
Он так же позволяет разработчикам использовать CommonJS, AMD или ES6 модули.
Webpack можно научить трансформировать все ваши ресурсы, такие как HTML и CSS, и даже изображения. Он может дать вам больше контроля над количеством HTTP-запросов создаваемых приложением итд. Webpack также позволяет легко использовать npm-пакеты.

#### Настройка   

[Документация webpack](https://webpack.js.org/concepts/)   
[Webpack 4: практические рекомендации по настройке](https://tproger.ru/translations/configure-webpack4/)   
[Guide to Webpack 4 and Module Bundling](https://www.sitepoint.com/beginners-guide-webpack-module-bundling/)   
[Webpack : Getting Started](https://webpack.js.org/guides/getting-started/)  
 
### Концепт_Webpack

 - точка входа [entry](#Entry)
 - точка вывода [output](#Output), 
 - загрузчики [loaders](#Loaders) 
 - плагины [plugins](#Plugins)
 

#### Entry

**Точка входа (entry)**
`Entry` должен содержать как мимум одну точку входа, с которой начинается построение дерева зависимостей.  
Это  `.js` файл в котором производится import других необходимых файлов и пишется основная логика

#### Output 

**Точка вывода (output)**

Это  папка `build/` или `dist/` или `wateveryounameit/` папка, где будет размещен конечный `.js` файл. 
Это ваш окончательный результат, который впоследствии будет загружен в `index.html.` 
 
`Output` это всегда объект, которому необходимо указать папку куда положить файл.

**Обязательные поля** 
- `path` - путь к файлу должен быть абсолютный, так как мы заранее не знаем такого пути, воспользуемся `Node` модулем `path`
который заимпортируем вначале файла. Синтаксис импорта RequireJS так как код будет исполняться в среде `Node`.
- `filename` - имя файла , куда соберется js

Импортируем модуль `path` 

```js
// webpack.config.js
// модуль который будет содержать адрес/путь проекта на жестком диске
const path = require('path');
```

Модуль `path` нужен для работы с путями , это объект у которого есть метод `.resolve()`
В `node` существует такая глобальная переменная  `__dirname` которая вседа будет указывать на текущую директорию.
Вызываем метод `.resolve()` в который передаем путь в проекту и имя папки, метод вернет полный путь


```js
// webpack.config.js
// помещаем в поле exports модуля данные которые будут импортироваться


module.exports = {
  entry: './src/index.js', // точка входа
  output: {
  // в какую ПАПКУ разместить  скомпилированный файл
    path: path.resolve(__dirname, 'имя папки'), 
    filename: 'имя_файла.js' //  в какой ФАЙЛ записать  
  }
}; 
``` 

#### Loaders
  
- Загрузчики обрабатывают определенные типы файлов. 
- По умолчанию `Webpack` может обрабатывать только `.js` и `.json` файлы.
- для определенного типа файла отдельный загрузчик. (`.css` , `.jpg`, `.js`, etc)
- Loader применяется к каждому файлу отдельно

Все загрузчики находятся в поле `module` , это объект у которого есть свойство `rules`
`rules` это массив, где содержатся настройки для `загрузчиков`

Правила для `загрузчиков` это объект который содержит свойства
- `test` - регуляркой матчим файл
- `exclude` - регулярка которая матчит то, что мы хотим исключить из правила, например чтобы не обрабатывались `.js` файлы которые лежат в папке `node_modules`  
- `use`  - строка содержащая названия загрузчика, который будет применяться к заматченному файлу. Если загрузчиков несколько, то указываем как массив строк `['loader1', 'loader2']`. Если мы передаем лоадер как строку, то все настройки этого лоадера применятся по умолчанию. Для кастомизации загрузчика нужно передать его как объект с полями `[{ loader: 'babel-loader', options: { тут настройки } }]`

```js
module.exports = {
  entry: './src/index.js',  
  output: { 
    path: path.resolve(__dirname, 'имя папки'), 
    filename: 'имя_файла.js'  
  },
  // тут лежат загрузчики
  module : {
    // правила для загрузчиков
    rules : [
      { test: /\.js/, exclude: /node_modules/, use: ['babel-loader']}
    ]
  }
}; 
```  
Для добавления нового загрузчика, помещаем в массив `rules` новый объект с аналогичной структурой. 

> В случае обработки фала несколькими загрузчиками важен порядок написания.  
> Загрузчики обрабатываются СПРАВА НАЛЕВО. ['loader-1', 'loader-2', 'loader-3'].   
> Порядок обработки: **`loader-3` -> `loader-2` -> `loader-1`**  



#### Plugins 

- Плагины применяются к результирующему файлу (БАНДЛУ) - `bundle`
- Плагины жду пока отработают загрузчики 
- И только когда готов `bundle.js` применяются к нему
- Плагины - это конструкторы, поэтому необходимо создать экземпляр
- Плагин необходимо импортировать в файл настроек `webpack.config.js`

Все плагины находятся в поле `plugins`, это массив в котором находятся **экземпляры конструкторов**

```javascript 
const path = require('path');

 // импортим  плагин
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      }
    ]
  },
// поле для плагинов
  plugins: [ 
      new HtmlWebpackPlugin()
  ] 
}; 
```

*** 

### Установка 
Установим Webpack

```javascript
// ставим два пакета, сам вебпак и его консольную версию  webpack-cli
npm i --save-dev webpack webpack-cli
```

### Структура 

В структуре проекта создаем папку  SRC, которая будет основной и где будут лежать рабочие файлы проекта такие как **index.js** файл стилей **style.css**, html шаблон **index.html** и т.д. из которых будет компилироваться проект в папку build

Создадим файл **index.js** который является корнем проекта

В корне проекта создаем файл **webpack.config.js**, который является основным для webpack, и управляет пакетами проекта

####  Context

Для того чтобы каждый раз в настойке webpack не писать папку `src` ее можно объявть как контекстную.
Для этого создаем поле `context` где указываем папку 

```js
module.exports = {
  context: path.resolve(__dirname, 'src'), 
  entry: './index.js', // тут уже можно писать просто index.js
  output: {
  // в какую ПАПКУ разместить  скомпилированный файл
    path: path.resolve(__dirname, 'имя папки'), 
    filename: 'имя_файла.js' //  в какой ФАЙЛ записать  
  }
}; 
```

### Mode

В `webpack` существуют несколько вариантов сборки 
- `production` - бандл меньше весит, js и css минимфицирован
- `develop`

Определить какой режим можно либо в `package.json` либо прописать его в поле `mode` в файле настроек

```js
module.exports = {
  mode: 'develop', // определяем режим сборки
  context: path.resolve(__dirname, 'src'), 
  entry: './index.js',  
  output: {   
    path: path.resolve(__dirname, 'dist'), 
    filename: 'bundle.js'  
  }
}; 

```

В основном файле настроек проекта `package.json` напишем **Npm скрипты**

```javascript
// --mode development режим разработки
// --mode production  режим продакшн
"scripts": {
    "start": "webpack --mode development",
    "build": "webpack --mode production" 
}
``` 
  
###  Loaders_Plugins

#### Babel

Ставим транспайлер Babel. Трансформитрует написанный код (ES6-9 + proposal feature)  в код который поддерживается всеми браузерами(ES5)
   
```javascript
// babel-core - основной пакет
// babel-core загрузчик для webpack
// пресет для babel - для обработки самого последнего синтаксиса языка и новых фичей
npm install -D @babel/core @babel/cli @babel/preset-env  
```

Также установим сразу и `babel-loader` для `webpack` и пропишем лоадер для `.js`
в `webpack.config.js`. правила.

```js
npm i -D babel-loader
```


- Для того чтобы заработал babel, нужно добавить его в  файл настроек webpack в **module**
- **module** - это объект, который описывает набор правил для наших загрузчиков

```javascript 
const path = require('path');

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'), 
    filename: 'имя_файла.js'  
  },
  module: {
//массив загрузчиков
  	rules: [ 
	{
		test: /\.js$/, // отбираем только с расширением.js 
        exclude: /node_modules/, //игнор папке node_modules 
        use:  ["babel-loader"], // используй загрузчик babel-loader
        // Вся остальная конфигурация будет описана в  `.babelrc`. 
      }
    ]
  } 
}; 
```


Для конфигурирования `babel` удобно вынести настройки в отдельный файл `.babelrc`. 
`Babel-loader` будет искать файл настроек `.babelrc` поэтому его можно вынести отдельно
  
```js
// .babelrc
{
    "presets": ["@babel/preset-env" ]  
}
``` 

--------

#### Support_proposal_features

В стандартный пресет `babel` не входят фичи из стадии `proposal`, например как
синтаксис полей класса `state = { field: value }`

Чтобы `babel` смог обрабатыватть такой код нужно расширить пресеты

Ставим

```js
npm i -D @babel/plugin-proposal-class-properties
```

И добавляем в настроки  новый плагин
 

```js
// .babelrc
{
    presets: ['@babel/preset-env',],
		plugins: ['@babel/plugin-proposal-class-properties']
}
``` 

--------

#### Babel_polyfill

`Babel polyfill` поддержка новых фичей js в браузерах.

```js
npm i -S @babel/polyfill
```

[read about babel polyfills...](https://babeljs.io/docs/en/babel-polyfill)

После установки, чтобы не загружать всю либу, нужно определить браузеры которые
нужно поддерживать.
Посмотреть список -  `npx browserslist` 
Нужно прописать список браузеров в настроках `babel-loader` в `base` конфиге либо в файле конфигурации `.babelrc` либо создать отдельный файл конфигурации `.browserlistrc`

Babel будет применять поддержку к указанным в `targets` браузерам.

Детально с конфигурацией настройки целевых браузеров
[browserlist github...](https://github.com/browserslist/browserslist)

```js
presets: [['@babel/preset-env', {  
  useBuiltIns: 'usage' // значение 'usage' - подгрузит только те, которые необходимы . 'entry' - загрузит все
}]],
plugins: ['@babel/plugin-proposal-class-properties']
  }
},
```

--------

#### Core_JS

`Core-JS` ядро JS. Наиболее актуальный способ.

[read ...](https://babeljs.io/blog/2019/03/19/7.4.0#core-js-3-7646)

```js
npm i core-js
```

После установки нужно прописать в конфиге `.babelrc`

```js
// .babelrc
"presets": [
		["@babel/preset-env", { 
    "corejs": 3,
		"useBuiltIns": "usage"
	}],
```

--------

#### React
 
Ставим `react` , `react-dom`, `prop-types`.

```js
npm i -S react react-dom prop-types
```

Для того чтобы `Babel` смог обработать jsx код нужно добавить пресет для react.

```js
npm i -D @babel/preset-react
```

Не забываем обновить `Babel загрузчик`. Если будут файлы с расширением `.jsx` то
нужно добавить их в тест регулярки.

```js
// webpack

rules: [
  {
    test: /\.js|\.jsx$/,
    loader: 'babel-loader',
    exclude: /node_modules/, 
  }
];
```
Расширим `babelrc`

```js 
// .babelrc

"presets": [
		["@babel/preset-env", { 
    "corejs": 3,
		"useBuiltIns": "usage"
  }], '@babel/preset-react'
]
```  

--------

#### React_CDN
 
С целью оптимизации бандла можем вынести `react` и `react-dom` в режиме продакшена, включив их минифицированные сборки с помощью CDN.

[ссылки на CDN](https://ru.reactjs.org/docs/cdn-links.html)

В `production` конфиге нужно добавить поле `externals` в которой указать внешние
источники

```js 
// webpack

  externals: {
    react: 'React',
    'react-dom': 'ReactDOM',
  },
});
```

В файле `src/index.html` нужно прилинковать ссылки на CDN

```html
<div id="root"></div>
<% if(process.env.NODE_ENV === 'production') {%>
<script
  crossorigin
  src="https://unpkg.com/react@16/umd/react.production.min.js"
></script>
<script
  crossorigin
  src="https://unpkg.com/react-dom@16/umd/react-dom.production.min.js"
></script>
<%} %>
```

Webpack при сборке проекта прилинкует `react` и `reactDom`.


#### Dynamic_import  

Для поддержки синтаксиса динамического импорта

`React.lazy(()=> import('./component'))` нужно добавить `babel` плагин.

```js
npm i -D @babel/plugin-syntax-dynamic-import @babel/plugin-syntax-dynamic-import-node
```

После этого нужно обновить массив плагинов `babel` в `babelrc` конфиге

```js
// .babelrc
"presets": [
		["@babel/preset-env", { 
    "corejs": 3,
		"useBuiltIns": "usage"
  }], '@babel/preset-react'
],
plugins: [ 
  '@babel/plugin-proposal-class-properties',
  '@babel/plugin-syntax-dynamic-import',
];
```

-------- 

#### HotLoader

[github](https://github.com/gaearon/react-hot-loader)  
[читаем-раз](https://gaearon.github.io/react-hot-loader/getstarted/)  
[читаем-два](https://habr.com/ru/post/433122/)  

Горячая перезагрузка позволяет перерендеривать компонент без перезагрузки
страницы, тем самым избегая потери состояния компоненты.

Ставим пакет как `dependencies`.

```js
npm i -S react-hot-loader
```

**После нужно**

- на уровне Арр импортировать `import {hot} from 'react-hot-loader/root'` ПЕРЕД  импортом React
- при экспорте компоненты обернуть в хок `export default hot(App)`
```jsx
import { hot } from 'react-hot-loader/root';
import React from 'react'; 

const App = () => {
  return (
    <div className="title">
     ...some code
    </div>
  );
};

export default hot(App); 
```

- обновить плагины в конфиге `babelrc`

```js
// .babelrc
"presets": [
		["@babel/preset-env", { 
    "corejs": 3,
		"useBuiltIns": "usage"
  }], '@babel/preset-react'
],
plugins: [ 
  'react-hot-loader/babel'
  '@babel/plugin-proposal-class-properties',
  '@babel/plugin-syntax-dynamic-import',
]; 
```
- создать новый скрипт в package.json с добавлением флага `--hot`

```js
"dev:hot": "npm run dev -- --hot"
```

--------

#### HTML 
 Создадим и добавим файл index.html в рабочую папку SRC.
 Чтобы использовать этот файл в качестве шаблона, нам понадобится html-плагин **HtmlWebpackPlugin** .

О плагине

[Github плагина](https://github.com/jantimon/html-webpack-plugin)
[HABR](https://habr.com/post/350886/)

```javascript
npm install html-webpack-plugin --save-dev
```

- После установки плагина нужно **добавить его в файл настроек webpack** в верхней части 

```javascript 
const path = require('path');
 // подключаем плагин
const HtmlWebpackPlugin = require('html-webpack-plugin');

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      }
    ]
  },
// поле для плагинов
  plugins: [ 
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html', // откуда взять файл для обработки
      filename: 'index.html' // имя файла который нужно будет создать
    })
  ] 
}; 
```
**Теперь наш файл из ./src/index.html стал шаблоном для конечного файла index.html, котрый будет в билде(основной сборке).**


Реакт будет вставлять сгенерированный `DOM` в этот тег, и `webpack` в конец `body` подключит собранный бандл. 

--------


####  СSS
Создаем style.css в нашей папке ./src И сразу сделаем **импорт в src/index.js**

```javascript
import './style.css'
```

**CSS** по умолчанию не поддерживается, поэтому необходимо установить **загрузчик** для него.

```javascript 
// style-loader - берет css и инлайнит его в <head> в теге <style>
// css-loader - забирает заимпортированный в `.js` `.css` файл и обрабатывает его 
// sass-loader - загрузчик для sass файлов
// node-sass - для преобразования sass в css 

npm install --save-dev style-loader css-loader sass-loader node-sass
```
- Нужно **добавить** загрузчики в **файл настроки webpack**

```javascript 
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin'); 

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      {
// проверяем все файлы которые с расширением .css
        test: /\.css$/, 
// игнорируем все  что в node_modules
        exclude: /node_modules/,  
// справа налево указываем какими загрузчиками их обрабатывать 
        use: ["style-loader", "css-loader"]  
      }
    ]
  }, 
  plugins: [ 
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html',  
      filename: 'index.html'  
    })
  ] 
}; 
```

--------

#### Mini_CSS
mini-css-extract получает файл css и компилирует его в папку build

[Github плагина](https://github.com/webpack-contrib/mini-css-extract-plugin)

```javascript
npm install --save-dev mini-css-extract-plugin
```

После установки 
- прописываем его в файл настроки webpack в верхней части где мы подключали предыдущие плагины 
- прописать его в качестве лоадера для CSS
- прописать его в массив плагинов

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');  

// подключили плагин MiniCssExtractPlugin
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      { 
        test: /\.css$/,  
        exclude: /node_modules/, / 
// добавили лоадер MiniCssExtractPlugin.loader
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader"]  
      }
    ]
  },
//
  plugins: [ 
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html',  
      filename: 'index.html'  
    }),
// добавили в плагины
    new MiniCssExtractPlugin({
      filename: 'style.css', // файл в рабочей папке src
    }),
  ] 
}; 
```

--------


#### SCSS 

Создаем style.scss в нашей папке ./src  И сразу сделаем **импорт в src/index.js**

```javascript
import './style.css';
// импорт style.scss
import './style.scss'
```
Установим пакет
```javascript  
// sass-loader - загрузчик для sass файлов
// node-sass - для преобразования sass в css 

npm install --save-dev sass-loader node-sass
```

После установки 

- прописываем его в файл настроки webpack в верхней части где мы подключали предыдущие плагины 
- прописать его в качестве лоадера для CSS
- прописать его в массив плагинов

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin');  

// подключили плагин MiniCssExtractPlugin
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      { 
        test: /\.css$/,  
        exclude: /node_modules/, /   
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader"]  
      },
// ОБЪЯВИЛИ
       { 
        test: /\.scss$/,  
        exclude: /node_modules/, / 
// добавили лоадер , ПОСЛЕ css-loader
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "sass-loader"]  
      }
    ]
  },
//
  plugins: [ 
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './public/index.html',  
      filename: 'index.html'  
    }), 
    new MiniCssExtractPlugin({
      filename: 'style.css',  
    }),
  ] 
}; 
```

--------

#### PostCSS

PostCSS предоставляет разные возможности нам **autoprefixer**, **cssnano/cleancss** (оптизицация размера css файла) и пр.

[Post CSS Wikipedia](https://ru.wikipedia.org/wiki/PostCSS)

[Github плагина](https://github.com/postcss/postcss)

[Каталог PostCSS плагинов](https://www.postcss.parts/)

Установим например  **autoprefixer**

```javascript
// postcss-loader
// autoprefixer
npm install postcss-loader autoprefixer --save-dev 
``` 

После установки   

- создать в корне проекта файл конфигурации **postcss.config.js** , где указать что нам для работы необходим плагин **autoprefixer**

```javascript
// postcss.config.js

module.exports = {
    plugins: [require('autoprefixer')];
}
```

- **прописываем** его в файл конфигурации webpack **в загрузчики css и scss.**
- поскольку post-css может рабоать только с .css файлами, то нужно в массиве загрузчиков **CSS ставить его первым**, а если мы работаем с **SCSS** то указываем его **ПОСЛЕ sass-loader НО перед css-loader**

 
```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin'); 

// плагин выдирает css в отдельный файл
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      { 
        test: /\.css$/,  
        exclude: /node_modules/,   
// добавили лоадер , ПЕРЕД css-loader
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader"]  
      }, 
       { 
        test: /\.scss$/,  
        exclude: /node_modules/, 
// добавили лоадер , ПОСЛЕ cass-loader НО перед css-loader
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader", "sass-loader"], 
      }
    ]
  },
//
  plugins: [ 
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html',  
      filename: 'index.html'  
    }), 
    new MiniCssExtractPlugin({
      filename: 'style.css',  
      chunkFilename: 'chunk.css'
    }),
  ] 
}; 
```

--------

####  Clean
Чтобы перед перегенерацией файлов очистить нашу билд папку ./build используем плагин clean-webpack-plugin

[Github плагина](https://github.com/johnagan/clean-webpack-plugin)

```javascript
npm i clean-webpack-plugin --save-dev
```

После установки 
- добавляем его в файле конфигурации как необходимый 

- добавляем в поле plugins

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin'); 
const MiniCssExtractPlugin = require("mini-css-extract-plugin");

// добавляем как необходимый плагин
const CleanWebpackPlugin = require('clean-webpack-plugin')

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      { 
        test: /\.css$/,  
        exclude: /node_modules/,   
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader"] 

      }, 
       { 
        test: /\.scss$/,  
        exclude: /node_modules/,   
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader", "sass-loader"] 

      }
    ]
  },
//
  plugins: [ 
// добавляем сюда
      new CleanWebpackPlugin('build'),
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html',  
      filename: 'index.html'  
    }), 
    new MiniCssExtractPlugin({
      filename: 'style.css',  
    }),
  ] 
}; 
```

--------

#### IMAGE 
Для загрузки картинок  нужен лоадер **file-loader**

```javascript
npm i -D file-loader
```

После установки 
- импортируем в коневой файл src/index.js

```javascript
//импортируем 
import imageUrl from './имя_папки/имя_файла.png';
```
- добавляем в конфиге webpack в поле modules в массив правил

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin'); 
const MiniCssExtractPlugin = require("mini-css-extract-plugin"); 
const CleanWebpackPlugin = require('clean-webpack-plugin')

module.exports = {
  entry: './src/index.js',  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      { 
        test: /\.css$/,  
        exclude: /node_modules/,   
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader"]  
      }, 
       { 
        test: /\.scss$/,  
        exclude: /node_modules/,  
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader", "sass-loader"]  
      },
// добавляем сюда
      {
// отбираем все .png .svg .jpg .gif
        test: /\.(png|svg|jpg|gif)$/, 
        use: ["file-loader"]
       },
    ]
  },
  plugins: [  
      new CleanWebpackPlugin('build'),
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html',  
      filename: 'index.html'  
    }), 
    new MiniCssExtractPlugin({
      filename: 'style.css',  
    }),
  ] 
}; 
``` 

--------

#### Bundle_Analyzer
 
[инфа и опции](https://github.com/webpack-contrib/webpack-bundle-analyzer#options-for-plugin)

```js
npm i -D webpack-bundle-analyzer
```

После установки обновляем `production` конфиг

```js 
const { BundleAnalyzerPlugin } = require('webpack-bundle-analyzer'); 
 
  plugins: [
    new BundleAnalyzerPlugin({
      analyzerMode: 'static',
    }),
  ], 
```

Запустив режим продакшена, в браузере откроется окно в котором будет отображена
полная информация о составе бандла.

Передав в качестве опции `analyzerMode: 'static'` получим в папку `dist` файл
`report.html` c информацие о бандле.

--------

#### DEVSERVER

Поднимает webserver , для отображения результата разработки 

[Github плагина](https://github.com/webpack/webpack-dev-server)

```javascript
npm install webpack-dev-server --save-dev
```

После установки необходимо в файле-манифесте **package.json** в npm скрипте "start" **добавить значение  "webpack --mode development" и флаг --open**  чтобы открывалось по умолчанию окно браузера

```javascript
"scripts": {
    "start": "webpack-dev-server webpack --mode development --open",
    "build": "webpack --mode production",
}
```

Также нужно **добавить новое поле devServer** для объекта, которое содержит настройки

Про доступные настройки [читаем тут](https://webpack.js.org/configuration/dev-server/)  

```javascript
const path = require('path');
const HtmlWebpackPlugin = require('html-webpack-plugin'); 
const MiniCssExtractPlugin = require("mini-css-extract-plugin"); 
const CleanWebpackPlugin = require('clean-webpack-plugin');

module.exports = {
  entry: ['babel-polyfill','./src/index.js'],  
  output: {
    path: path.resolve(__dirname, 'имя папки'),  
    filename: 'имя_файла.js' 
  },
  module: {
    rules: [
      { 
        test: /\.js$/,  
        exclude: /node_modules/, 
        use:  ["babel-loader"],
      },
      { 
        test: /\.css$/,  
        exclude: /node_modules/,   
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader"]  
      }, 
       { 
        test: /\.scss$/,  
        exclude: /node_modules/,  
        use: ["style-loader", MiniCssExtractPlugin.loader, "css-loader", "postcss-loader", "sass-loader"]  
      }, 
      { 
        test: /\.(png|svg|jpg|gif)$/, 
        use: ["file-loader"]
       },
    ]
  },
  plugins: [  
      new CleanWebpackPlugin('build'),
      new HtmlWebpackPlugin({ 
      hash: true,
      template: './src/index.html',  
      filename: 'index.html'  
    }), 
    new MiniCssExtractPlugin({
      filename: 'style.css',  
    }),
  ],
// создаем новое поле 
  devServer: {
    contentBase: DIST_DIR,
    publicPath: '/',
    hot: true,
    historyApiFallback: true,
    noInfo: false,
    quiet: false,
    stats: 'errors-only',
    clientLogLevel: 'warning',
    compress: true,
    port: 9001, // порт можно указать любой
  },  
};  
```

--------

### DEVTOOL
 
[read ...](https://webpack.js.org/configuration/devtool/#devtool)  

В основном нужны для читаемого js кода который пришел в бандле.
Создает `source-map`. Для режима **dev** и **prod** можно указывать разные опции, оптимыльные значение указаны по ссылке выше.
  
Для этого нужно создать поле `devtool`

```js
module.exports = {
// предыдущий код 
   devServer: {
   // настройки devServer
    clientLogLevel: 'warning',
    compress: true,
    port: 9001,  
  },
// прописываем новое свойство
devtool: 'eval-source-map'
};  
```

--------

#### Chunks
[hackernoon...](https://medium.com/hackernoon/the-100-correct-way-to-split-your-chunks-with-webpack-f8a9df5b7758) 

--------

### GH_PAGES

[tutorial](https://medium.com/the-andela-way/how-to-deploy-your-react-application-to-github-pages-in-less-than-5-minutes-8c5f665a2d2a)

Пакет для автоматического деплоя на `github-pages`. 

--------


#### Config_splitting

Для разных режимов сборки существуют разные настройки для плагинов. Чтобы не городить в `base` кофиге проверки на режим сборки нужно разбить основной конфиг на три части
1. base
2. development
3. production

Чтобы `webpack` динамически мог определять режим разработки, нужно засетить режим в скриптах `package.json`
Вызов `--env.mode modeName` установит в переменную окружения текущий режим .

```javascript
"scripts": {
    "start": "webpack --env.mode development",
    "build": "webpack --env.mode production" 
}
```

Затем мы получим объект окружения `env` аргументом и в зависмости от этого будем применять те или иные настройки

```js
// wepack.config.js


module.exports = (env) => ({
  mode: env.mode, // в поле mode лежит текущий режим 
  context: path.resolve(__dirname, 'src'), 
  entry: './index.js',  
  output: { 
    path: path.resolve(__dirname, 'dist'), 
    filename: 'bundle.js'  
})
```

Создадим в корне проекта папку `webpack` в которой создадим файлы для разных режимов соответственно и положим их в папку `configs`
Для общих настроек `base.js`
Для режима разработки `development.js` 
Для продакшн режима `production.js`
 
В каждом фале нужно создать свой конфиг с необходимыми лоадерами и плагинами.

Затем файлы будут сшиваться с помощью `webpack-merge`.

[github](https://github.com/survivejs/webpack-merge)

```js
npm i webpack-merge
```

В корне папки `webpack` cоздадим файл который будет собирать нужные нам режимы, например `config.js` 

Это функция в которую первым аргументом передадим основной конфиг, а вторым , тот конфиг которые соответсвует режиму разаботки

```js
// config.js

const webpackMerge = require('webpack-merge');

// ссылка на базовый конфиг
const loadBase = require('./configs/base');

// динамически определенный режим, который лежит в папке config, имя должно файла должно соответствовать 
const loadMode = env => require(`./configs/${env.mode}`);

// мерджим и експортирем
module.exports = env => webpackMerge(loadBase, loadMode(env));

```
В `package.json` также нужно указать откуда забирать файл конфига `webpack`

```json
"scripts": {
    "build": "webpack --env.mode production  --config ./webpack/config.js",
    "dev": "webpack-dev-server --open --env.mode development --config ./webpack/config.js", 
  },
```

--------

###  Gitignore

Чтобы нежелательные файлы не попадали в репозиторий и в индекс VSC Git необходимо создать в корне проекта файл `.gitignore`
Git проигнорирует все, что указано в файле

```json
// .gitignore

# production
/build

# Logs
logs
*.log
npm-debug.log*
yarn-debug.log*
yarn-error.log*
 
# dotenv environment variables file
.env
  
# Directory for instrumented libs generated by jscoverage/JSCover
lib-cov
  
# Dependency directories
node_modules/ 
  
# Optional npm cache directory
.npm

# Optional eslint cache
.eslintcache

# Optional REPL history
.node_repl_history

# Output of 'npm pack'
*.tgz

# Yarn Integrity file
.yarn-integrity
 
# misc
.DS_Store
.env.local
.env.development.local
.env.test.local
.env.production.local  
```
  
---------------

#### Browserslist

[github browserlists...](https://github.com/browserslist/browserslist)

Сконфигурировать целевые браузеры можно тремя способами

1. `.babelrc`
2. `package.json`
3. `.browserlistrc`

- Также список поддерживаемых браузеров можно указать в `package.json`

```js
// package.json

"browserslist": [
  'last 2 versions',
    'not dead',
    'not < 2%',
    'not ie 11'
]
```

- Или путем создания конфигруациооного файла `.browserslistrc` где без кавычек и
  с новой строки указываем список поддерживаемых браузеров

```js
// .browserslistrc

[development]
last 1 chrome version
last 1 firefox version
last 1 safari version

[production]
>0.2%
not dead
not op_mini all
```



#### Testing 

**Jest**, **testing-library/jest-dom** , **testing-library/react**

Уставновив jest нужно прописать скрипт в `package.json`

```js
npm i -D jest @testing-library/jest-dom  @testing-library/react
```

```json
 "scripts": {
    "build": "webpack --config webpack.config.prod.js",
    "dev": "webpack-dev-server --open --config webpack.config.dev.js",
    "dev:hot": "npm run dev -- --hot",
    "test": "jest"
  },
```

Конфигурируем `jest`

[конфигурация jest...](https://jestjs.io/docs/ru/configuration)

- в корне создаем `jest.config.js` и `setup-tests.js`

Чтобы в тестовых файлах каждый раз не импортировать например

```js
import '@testing-library/jest-dom';
import '@testing-library/react';
```

импорт этих модулей можно произвести единожды в файле `setup-tests.js` и затем
прописать путь к нему в `jest.congif.js`

```js
const path = require('path');

module.exports = {
  setupFilesAfterEnv: [require.resolve('./setup-tests.js')],
};
```

- babel-jest


Также для работы с тестами поставим

```js
npm i -D babel-jest babel-core@bridge
```

Также для поддержки динамического импорта в `node`

```js
npm i babel-plugin-dynamic-import-node
```

После обновим кофиг `.babelrc` добавив новое поле `env` со значением `test`

```js
"env": {
  "test": {
    "plugins": ["dynamic-import-node"]
  }
}
```

#### Code_quality


**Linters, Prettier, Husky**

Ставим пакеты

```js
npm i -D prettier eslint-config-airbnb eslint-config-prettier eslint-plugin-prettier eslint-plugin-react eslint-plugin-import eslint-plugin-jsx-a11y husky lint-staged babel-eslint
```

Читаемый список

```js
prettier
babel-eslint
eslint-config-airbnb
eslint-config-prettier
eslint-plugin-prettier
eslint-plugin-react
eslint-plugin-import
eslint-plugin-jsx-a11y
husky
lint-staged
```

### Ставим плагины для IDE

- prettier
- eslint
- editorconfig

Husky работает в связке с lint-staged. **До установки в проект, папка с проектом
уже должна отслеживаться git**
 
#### Editorconfig 

**.editorconfig**

[docs editorconfig](https://editorconfig.org/#example-file)

Чтобы все пользовались едиными настройками, касающимися, например, отступов или
символов перевода строки, применяем файл .editorconfig. Он помогает поддерживать
единый набор правил в неоднородных командах.

```js
root = true

[*.md]
trim_trailing_whitespace = false

[*.js]
trim_trailing_whitespace = true

# Переводы строк в стиле Unix с пустой строкой в конце файла
[*]
indent_style = space
indent_size = 2
end_of_line = lf
charset = utf-8
insert_final_newline = true
max_line_length = 80
```

#### Prettier

**.prettierrc**

[docs prettier...](https://prettier.io/docs/en/options.html)

```js
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": false,
  "semi": true,
  "singleQuote": true,
  "trailingComma": "all",
  "bracketSpacing": true,
  "jsxBracketSameLine": false,
  "arrowParens": "avoid",
  "proseWrap": "always"
}
```

#### Eslint 

**.eslintrc**

[docs eslint...](https://eslint.org/docs/user-guide/configuring)  
[Airbnb styleguide for JS](https://github.com/leonidlebedev/javascript-airbnb)  
[Airbnb styleguide for React](https://github.com/leonidlebedev/javascript-airbnb/tree/master/react)  


Нужно заинитить `eslint`

```js
npx eslint --init
```

структура конфига `eslint`

```js
{
   env:{},
   extends: {},
   plugin: {},
   parser: {},
   parserOptions: {},
   rules: {},
}
```

- `env` — позволяет задавать **список сред**, код для которых планируется  проверять. В нашем случае тут имеются свойства es6, browser и node, установленные в true. Параметр **es6** включает возможности ES6 за исключением   модулей (эта возможность автоматически устанавливает, в блоке parserOptions,  параметр ecmaVersion в значение 6). Параметр **browser** подключает глобальные   переменные браузера, такие, как Windows. Параметр **node** добавляет   глобальные переменные среды Node.js и области видимости, например — global. 

[подробно о env...](https://eslint.org/docs/user-guide/configuring#specifying-environments)

- `extends` — представляет собой массив строк с конфигурациями, при этом каждая   дополнительная конфигурация расширяет предыдущую.

- `plugins` — тут представлены правила линтинга, которые мы хотим использовать.
  У нас применяются правила `["react", "import", "prettier", "jsx-a11y"]`

- `parser` — по умолчанию ESLint использует синтаксический анализатор Espree,  но, так как мы работаем с Babel, нам надо пользоваться babel-esLint.

- `parserOptions` — так как мы изменили стандартный синтаксический анализатор на  babel-eslint, нам необходимо задать и свойства в этом блоке. Свойство  **ecmaVersion**, установленное в значение 6, указывает **ESLint** на то, что   проверяться будет ES6-код. Так как код мы пишем в EcmaScript-модулях, свойство  **sourceType** установлено в значение module. И, наконец, так как мы  используем React, что означает применение JSX, то в свойство **ecmaFeatures**   записывается объект с ключом jsx, установленным в true.

- `rules` — эта часть файла позволяет настраивать правила ESLint. Все правила, которые мы расширили или добавили с помощью плагинов, можно менять или  переопределять, и делается это именно в блоке rules.

Примерный конфиг

```js
{
  "extends": [
    "airbnb",
    "prettier",
    "prettier/react"
    "eslint:recommended",
    "plugin:react/recommended",
    "plugin:jsx-a11y/recommended",
    "plugin:prettier/recommended",
    "plugin:import/errors",
  ],
  "plugins": ["react", "import", "prettier", "jsx-a11y"],
  "parser": "babel-eslint",
  "parserOptions": {
    "ecmaVersion": 6,
    "sourceType": "module",
    "ecmaFeatures": {
      "jsx": true
    }
  },
  "env": {
    "es6": true,
    "browser": true,
    "node": true,
    "jest": true
  },
  "rules": {
    "no-console": 1,
    "linebreak-style": ["error", "unix"],
    "react/jsx-filename-extension": [1, { "extensions": [".js", ".jsx"] }],
    "react/destructuring-assignment": [
      2,
      "always",
      { "ignoreClassFields": true }
    ]
  },
  "globals": {
    "window": true,
    "document": true,
    "localStorage": true,
    "FormData": true,
    "FileReader": true,
    "Blob": true,
    "navigator": true
  },
  "settings": {
    "react": {
      "version": "16.10.2"
    }
  }
}
```

Файл `.eslintignore`. Этот файл принимает список путей, представляющий папки, содержимое которых не должно обрабатываться с помощью ESLint.

```js
`/.git` — мне не нужно, чтобы ESLint проверял файлы, относящиеся к Git.
`/.vscode` — в проекте имеется эта папка из-за того, что я использую VS Code.
  Тут редактор хранит конфигурационные сведения, которые можно задавать для
  каждого проекта. Эти данные тоже не должны обрабатываться линтером.
`node-modules` — файлы зависимостей также не нужно проверять линтером.
```

#### Lint_staged

**.lintstagedrc**

[docs lint-staged...](https://github.com/okonet/lint-staged)

Пакет `Lint-staged` позволяет проверять с помощью линтера индексированные файлы, что помогает предотвратить отправку в репозиторий кода с ошибками.

```js
// lintstagedrc

"src/**/*.{json,css}": ["prettier --write", "git add"],
"src/**/*.js": ["prettier --write", "eslint --fix", "git add"]

```

#### Husky 

**.huskyrc**

[docs husky](https://github.com/typicode/husky)

Пакет `Husky` позволяет задействовать хуки `Git`. Это означает, что появляется возможность выполнять некие действия перед выполнением коммита или перед отправкой кода репозиторий.

В примере ниже перед коммитом будет выполнена комманда `"lint-staged"`, которая выполнит команды указанные в ее конфиге `.lintstagedrc`

```js
{
  "hooks": {
    "pre-commit": "lint-staged"
  }
}
```

### Создание задач

В `package.json` нужно дополнить поле `scripts`

- lint (запускает отслеживание)
- lint:fix (запускает исправление некоторых ощибок)
- lint:format (запускает испоавления стиля кода)

```js
// дополнить к существующим скриптам
"scripts": {
  "lint": "eslint src/**/*.js",
  "lint:fix": "eslint src/**/*.js --fix",
  "lint:format": "prettier src/**/*.{js,css,json} --write"
  }
```

### Настройки IDE VSCode

```js
{
  "files.autoSave": "onFocusChange",
  "editor.formatOnSave": true,
  "eslint.autoFixOnSave": true,
  "eslint.alwaysShowStatus": true
}
``` 
[неплохая статья о code quality на хабре ](https://habr.com/ru/company/ruvds/blog/428173/)


![pic](http://i.piccy.info/i9/6e8562bb0b74c130c7f22ca5287744a6/1571940814/24079/1333692/all.jpg)
