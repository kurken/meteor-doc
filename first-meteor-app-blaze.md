## Установка Meteor
Запустите следующую команду в своем терминале, чтобы установить последнюю официальную версию Meteor:

` curl https://install.meteor.com/ | sh `

## Создание первого meteor-приложения(шаблонизатор blaze)

В этом уроке мы создадим простое приложение для управления списком дел и совместной работы с другими участниками над этими задачами.
Чтобы создать приложение, откройте терминал и введите команду:

` meteor create simple-todos `

Эта команда создаст новую папку под названием simple-todos со всеми файлами, которые нужны приложению Meteor:

```client/main.js        # a JavaScript entry point loaded on the client
client/main.html      # an HTML file that defines view templates
client/main.css       # a CSS file to define your app's styles
server/main.js        # a JavaScript entry point loaded on the server
test/main.js          # a JavaScript entry point when running tests
package.json          # a control file for installing npm packages
package-lock.json     # describes the npm dependency tree
node_modules/         # packages installed by npm
.meteor/              # internal Meteor files
.gitignore            # a control file for git```

Запустим только что созданное приложение:

```cd simple-todos
meteor```

Откройте свой веб-браузер и перейдите по адресу http://localhost:3000 чтобы увидеть запущенное приложение.

Вы можете немного поиграть с этим приложением по умолчанию, прежде чем мы продолжим. 
Например, попробуйте отредактировать текст в <h1> внутри client / main.html с помощью вашего любимого текстового редактора. 
Когда вы сохраните файл, страница в вашем браузере автоматически обновится с новым содержимым. 
Мы называем этот горячий код толчком.

Теперь, когда у вас есть некоторый опыт редактирования файлов в вашем приложении Meteor, давайте начнем работать над простым приложением todo list. 
Если вы обнаружите ошибку или ошибку в учебнике, пожалуйста, отправьте запрос на удаление на GitHub.

Определение представлений с помощью шаблонов
Чтобы начать работу над нашим приложением todo list, давайте заменим код стартового приложения по умолчанию приведенным ниже кодом. 
А потом мы поговорим о том, что он делает.

Во-первых, давайте удалим тело из нашей точки входа HTML (оставив только тег <head> ):

2.1  Удалите начальный HTML код client/main.html »
<head>
  <title>simple-todos</title>
</head>

Создайте новый каталог с именем imports внутри папки simple-todos. Затем мы создаем несколько новых файлов в директории imports/:

2.2  Добавьте начальный HTML код в imports/ui/body.html »

<body>
    <div class="container">
      <header>
        <h1>Todo List</h1>
      </header>
  
      <ul>
        {{#each tasks}}
          {{> task}}
        {{/each}}
      </ul>
    </div>
  </body>
  
  <template name="task">
    <li>{{text}}</li>
  </template>

2.3  Добавьте стартовый код JS imports/ui/body.js »

import { Template } from 'meteor/templating';
 
import './body.html';
 
Template.body.helpers({
  tasks: [
    { text: 'This is task 1' },
    { text: 'This is task 2' },
    { text: 'This is task 3' },
  ],
});

Внутри нашего интерфейсного файла точки входа JavaScript, client/main.js, мы удалим остальную часть кода и импортируем imports/ui/body.js:

2.4  Импорт стартового JS client/main.js »

import '../imports/ui/body.js';

Подробнее о том, как работает импорт и как структурировать код, вы можете прочитать в статье структура приложения руководства Meteor Guide.

В вашем браузере приложение теперь будет выглядеть примерно так:

	Todo List
		This is task 1
		This is task 2
		This is task 3

А теперь давайте выясним, что делают все эти кусочки кода!

HTML файлы в Meteor определяют шаблоны
Meteor анализирует HTML-файлы и идентифицирует три тега верхнего уровня: < head>, <body> и <template>.
Все, что находится внутри любых тегов < head>, добавляется в раздел head HTML, отправляемого клиенту, а все, 
что находится внутри тегов <body>, добавляется в раздел body, как и в обычном HTML-файле.
Все, что находится внутри тегов <template>, компилируется в шаблоны Meteor, 
которые могут быть включены в HTML с помощью {{> templateName}} или ссылаться на ваш JavaScript с помощью Template.templateName.
Кроме того, в области тела могут быть ссылки в JavaScript с помощью Template.body. 
Думайте об этом как о специальном" Родительском " шаблоне, который может включать в себя другие дочерние шаблоны.

Добавление логики и данных в шаблоны
Весь код в ваших HTML-файлах компилируется с помощью компилятора Meteor Spacebars. 
Spacebars использует операторы, окруженные двойными фигурными скобками, такими как {{#each}} и {{#if}}, чтобы вы могли добавлять логику и данные в свои представления.
Вы можете передавать данные в шаблоны из вашего кода JavaScript, определяя помощников. 
В приведенном выше коде мы определили помощника под названием tasks on Template.body, которое возвращает массив.
Внутри тега body HTML мы можем использовать {{#each tasks}} для итерации по массиву и вставки шаблона задачи для каждого значения. 
Внутри блока #each мы можем отобразить свойство text каждого элемента массива с помощью {{text}}.
На следующем этапе мы увидим, как с помощью помощников можно заставить наши шаблоны отображать динамические данные из коллекции баз данных.

Стилизация с помощью CSS
Чтобы иметь лучший опыт во время выполнения этого урока, мы предлагаем вам скопировать и вставить следующий CSS-код в ваше приложение:

2.5  Добавьте CSS в client/main.css »
...
см. файл main.css в папке с инструкцией


Хранение задач в коллекции
Коллекции-это способ Метеора хранить постоянные данные. 
Особенность коллекций в Meteor заключается в том, что они могут быть доступны как с сервера, так и с клиента, 
что позволяет легко писать логику представления без необходимости писать много серверного кода. 
Они также обновляются автоматически, поэтому компонент представления, поддерживаемый коллекцией, 
автоматически отображает самые последние данные.

Подробнее о коллекциях вы можете прочитать в статье "Коллекции"(http://guide.meteor.com/collections.html) руководства Meteor Guide.

Создать новую коллекцию так же просто, как позвонить в new Mongo.Collection("my-collection")в вашем JavaScript. 
На сервере это создает коллекцию MongoDB, называемую my-collection; на клиенте это создает кэш, подключенный к коллекции сервера. 
Мы узнаем больше о разделении клиент-сервер на шаге 12, но пока мы можем написать наш код с предположением, что вся база данных присутствует на клиенте.

Чтобы создать коллекцию, мы определяем новый imports/api/tasks.js модуль, который создает коллекцию Mongo и экспортирует ее:

3.1  Создание коллекции задач: imports/api/tasks.js »

import { Mongo } from 'meteor/mongo';
export const Tasks = new Mongo.Collection('tasks');

Обратите внимание, что мы помещаем этот файл в новый каталог imports/api. 
Это разумное место для хранения файлов, связанных с API для приложения. 
Мы начнем с того, что поместим здесь "коллекции", а затем добавим" публикации", которые читают из них, и" методы", которые пишут в них. 
Подробнее о том, как структурировать код, вы можете прочитать в статье структура приложения руководства Meteor Guide.

Теперь нам нужно импортировать модуль imports/api/tasks.js в server/main.JS:

3.2  Импортируем коллекцию задач в server/main.js »

import '../imports/api/tasks.js';

Импорт imports/api/tasks.js на сервере создает коллекцию MongoDB и настраивает канал для передачи данных клиенту.

Давайте обновим наш клиентский код JavaScript, чтобы получить наши задачи из коллекции, а не из статического массива:

3.3  Загрузим задачи из коллекции "Задачи": imports/ui/body.js »

import { Template } from 'meteor/templating';
import { Tasks } from '../api/tasks.js';
import './body.html';
Template.body.helpers({
   tasks() {
       return Tasks.find({});
     },
});

Когда вы внесете эти изменения в код, вы заметите, что задачи, которые раньше были в списке задач, исчезли. 
Это потому, что наша база данных в настоящее время пуста — нам нужно вставить некоторые задачи!
Вставка задач из консоли базы данных на стороне сервера
Элементы внутри коллекций называются документами. 
Давайте воспользуемся консолью базы данных сервера, чтобы вставить некоторые документы в нашу коллекцию. 
Пока ваше приложение работает, на новой вкладке терминала перейдите в каталог приложений и введите:

meteor mongo

Это открывает консоль в локальной базе данных разработки вашего приложения. В командной строке введите:

db.tasks.insert({ text: "Hello world!", createdAt: new Date() });

В вашем веб-браузере вы увидите, что пользовательский интерфейс вашего приложения немедленно обновляется, чтобы показать новую задачу. 
Вы можете видеть, что нам не нужно было писать никакого кода, чтобы подключить серверную базу данных к нашему интерфейсному коду-это просто произошло автоматически.

Вставьте еще несколько задач из консоли базы данных с другим текстом. 
На следующем шаге мы увидим, как добавить функциональность в пользовательский интерфейс нашего приложения, чтобы мы могли добавлять задачи без использования консоли базы данных.

Добавление задач с помощью формы
На этом шаге мы добавим поле ввода для пользователей, чтобы добавить задачи в список.

Во-первых, давайте добавим форму в наш HTML-код:

4.1  Добавьте форму для новых задач:: imports/ui/body.html »

    <div class="container">
      <header>
        <h1>Todo List</h1>
 
        <form class="new-task">
            <input type="text" name="text" placeholder="Type to add new tasks" />
          </form>
 
      </header>
  
 
 
      <ul>
        {{#each tasks}}
          {{> task}}

Вот код JavaScript, который нам нужно добавить, чтобы прослушать событие submit в форме:

4.2  Добавить обработчик событий для отправки формы: imports/ui/body.js »

    tasks() {
        return Tasks.find({});
      },
});
 
Template.body.events({
    'submit .new-task'(event) {
      // Запрет отправки формы браузера по умолчанию
      event.preventDefault();
  
      // Получение значения из элемента формы
      const target = event.target;
      const text = target.text.value;
  
      // Вставка задачи в коллекцию
      Tasks.insert({
        text,
        createdAt: new Date(), // current time
      });
  
      // Очистка формы
      target.text.value = '';
    },
  });


Теперь ваше приложение имеет новое поле ввода. Чтобы добавить задачу, просто введите ее в поле ввода и нажмите enter. 
Если вы откроете новое окно браузера и снова откроете приложение, то увидите, что список автоматически синхронизируется между всеми клиентами.

Прикрепление событий к шаблонам
Прослушиватели событий добавляются в шаблоны почти так же, как и помощники: вызовом Template.templateName.events(...) со словарем. 
Ключи описывают событие для прослушивания, а значения-это обработчики событий, которые вызываются при возникновении события.

В нашем случае выше мы прослушиваем событие submit для любого элемента, который соответствует CSS селектору .new-task. 
Когда это событие инициируется пользователем, нажавшим enter внутри поля ввода, вызывается наша функция обработчика событий.

Обработчик событий получает аргумент под названием event, содержащий некоторую информацию о событии, которое было инициировано. 
В данном случае event.target - это наш элемент формы,и мы можем получить значение вашего ввода с помощью event.target.text.value. 
Вы можете просмотреть все остальные свойства объекта события, добавив console.log(event) и инспектируя объекты в консоли браузера.

Наконец, в последней строке обработчика событий мы очищаем входные данные, чтобы подготовиться к другой новой задаче.

Вставка в коллекцию
Внутри обработчика событий мы добавляем задачу в коллекцию tasks, вызывая Tasks.insert(). 
Мы можем присвоить объекту задачи любые свойства, например время создания, поскольку нам никогда не нужно определять схему для коллекции.

Возможность вставлять что-либо в базу данных от клиента не очень безопасна, но пока все в порядке. 
На шаге 10 мы узнаем, как сделать наше приложение безопасным и ограничить способ вставки данных в базу данных.

Сортировка наших задач
В настоящее время наш код отображает все новые задачи в нижней части списка. 
Это не очень хорошо для списка задач, потому что мы хотим сначала увидеть самые новые задачи.

Мы можем решить эту проблему, сортируя результаты с помощью поля createdAt, которое автоматически добавляется нашим новым кодом. 
Просто добавим опцию сортировки для поиска вызовов в задачи помощника:
-------------------
4.3  Показать новые задачи в верхней части экрана: imports/ui/body.js »
 
Template.body.helpers({
    tasks() {
        // Показать новые задачи в верхней части экрана
        return Tasks.find({}, { sort: { createdAt: -1 } });
      },
});
 
На следующем шаге мы добавим некоторые очень важные функции списка дел: проверка и удаление задач.

Изменение и удаление задач
До сих пор мы взаимодействовали только с коллекцией, вставляя документы. Теперь мы узнаем, как обновить и удалить их.

Давайте поработаем над нашим шаблоном задачи-начнем с перемещения его в свой собственный файл, с некоторыми новыми функциями, флажком и кнопкой удаления:

5.1  Добавим кнопок в компонент задачи: imports/ui/task.html »

<template name="task">
    <li class="{{#if checked}}checked{{/if}}">
      <button class="delete">&times;</button>
  
      <input type="checkbox" checked="{{checked}}" class="toggle-checked" />
  
      <span class="text">{{text}}</span>
    </li>
  </template>

5.2 Удалите предыдущий шаблон задачи

Поскольку мы добавили сюда новый шаблон задачи, нам нужно удалить старый шаблон задачи, который был у нас раньше. 
Откройте файл imports/ui/body.html - файл и удалите весь <template name= "task">...< / template> раздел с конца.

Теперь мы добавили элементы пользовательского интерфейса, но они еще ничего не делают! Мы должны добавить некоторые обработчики событий:

5.3  Добавление обработчиков событий для кнопок "Задач": imports/ui/task.js »

import { Template } from 'meteor/templating';
import { Tasks } from '../api/tasks.js';
import './task.html';
Template.task.events({
  'click .toggle-checked'() {
    // Set the checked property to the opposite of its current value
    Tasks.update(this._id, {
      $set: { checked: ! this.checked },
    });
  },
  'click .delete'() {
    Tasks.remove(this._id);
  },
});

Шаблон тела использует шаблон задачи, поэтому нам также нужно импортировать его:

5.4  Импорт компонента задачи из тела: imports/ui/body.js »
 
import { Tasks } from '../api/tasks.js';
import './task.js';
import './body.html';
Template.body.helpers({

Получение данных в обработчиках событий
Внутри обработчиков событий это относится к отдельному объекту задачи. 
В коллекции каждый вставленный документ имеет уникальное поле _id, которое можно использовать для ссылки на этот конкретный документ. 
Мы можем получить _id текущей задачи с помощью this._id. 
Как только у нас есть _id, мы можем использовать update и remove для изменения соответствующей задачи.

Обновление
Функция обновления коллекции принимает два аргумента.
Первый-это селектор, определяющий подмножество коллекции, а второй-параметр обновления, указывающий, что должно быть сделано с соответствующими объектами.

В этом случае селектор - это просто _id соответствующей задачи.
Параметр update использует $set для переключения поля checked, которое будет представлять, была ли задача выполнена.

Удаление
Функция remove принимает один аргумент-селектор, который определяет, какой элемент следует удалить из коллекции.

Использование свойств объекта или помощников для добавления/удаления классов
If you try checking off some tasks after adding all of the above code, you will see that checked off tasks have a line through them. 
Если вы проверите некоторые задачи после добавления всего вышеприведенного кода, 
Вы увидите, что проверенные задачи перечеркнуты. Это включено следующим фрагментом кода:

<li class="{{#if checked}}checked{{/if}}">

С помощью этого кода, если свойство checked задачи имеет значение true, то проверяемый класс добавляется в наш элемент списка. 
Используя этот класс, мы можем заставить проверенные задачи выглядеть по-другому в нашем CSS.

Storing temporary UI state in a Reactive Dictionary
In this step, we'll add a client-side data filtering feature to our app, so that users can check a box to only see incomplete tasks. We're going to learn how to use a ReactiveDict to store temporary reactive state on the client. A ReactiveDict is like a normal JS object with keys and values, but with built-in reactivity.

First, we need to add a checkbox to our HTML:

7.1  Add hide-completed checkbox to HTMLimports/ui/body.html »
      <header>
        <h1>Todo List</h1>
 
    <label class="hide-completed">
        <input type="checkbox" />
        Hide Completed Tasks
      </label>
 
        <form class="new-task">
            <input type="text" name="text" placeholder="Type to add new tasks" />
          </form>
...some lines skipped...
      </header>
  
 
      <ul>
        {{#each tasks}}
          {{> task}}
Now we need to add the reactive-dict package:

meteor add reactive-dict
Then we need to set up a new ReactiveDict and attach it to the body template instance (as this is where we'll store the checkbox's state) when it is first created:

7.3  Add state dictionary to the bodyimports/ui/body.js »
import { Template } from 'meteor/templating';
import { ReactiveDict } from 'meteor/reactive-dict';
 
import { Tasks } from '../api/tasks.js';
 
import './task.js';
import './body.html';
 
Template.body.onCreated(function bodyOnCreated() {
    this.state = new ReactiveDict();
  });
 
Template.body.helpers({
    tasks() {
        // Show newest tasks at the top
Then, we need an event handler to update the ReactiveDict variable when the checkbox is checked or unchecked. An event handler takes two arguments, the second of which is the same template instance which was this in the onCreated callback:

7.4  Add event handler for checkboximports/ui/body.js »
      // Clear form
      target.text.value = '';
    },
    'change .hide-completed input'(event, instance) {
        instance.state.set('hideCompleted', event.target.checked);
      },
  });
Now, we need to update Template.body.helpers. The code below has a new if block to filter the tasks if the checkbox is checked:

7.5  Add helpers to body templateimports/ui/body.js »
 
Template.body.helpers({
    tasks() {
        const instance = Template.instance();
        if (instance.state.get('hideCompleted')) {
          // If hide completed is checked, filter tasks
          return Tasks.find({ checked: { $ne: true } }, { sort: { createdAt: -1 } });
        }
        // Otherwise, return all of the tasks
        return Tasks.find({}, { sort: { createdAt: -1 } });
      },
});
Now if you check the box, the task list will only show tasks that haven't been completed.

ReactiveDicts are reactive data stores for the client
Until now, we have stored all of our state in collections, and the view updated automatically when we modified the data inside these collections. This is because Mongo.Collection is recognized by Meteor as a reactive data source, meaning Meteor knows when the data inside has changed. ReactiveDict is the same way, but is not synced with the server like collections are. This makes a ReactiveDict a convenient place to store temporary UI state like the checkbox above. Just like with collections, we don't have to write any extra code for the template to update when the ReactiveDict variable changes — just calling instance.state.get(...) inside the helper is enough.

You can read more about patterns for writing components in the Blaze article of the Meteor Guide.

One more feature: Showing a count of incomplete tasks
Now that we have written a query that filters out completed tasks, we can use the same query to display a count of the tasks that haven't been checked off. To do this we need to add a helper and change one line of the HTML.

7.6  Add incompleteCount helper to bodyimports/ui/body.js »
        }
        // Otherwise, return all of the tasks
        return Tasks.find({}, { sort: { createdAt: -1 } });
    },
    incompleteCount() {
        return Tasks.find({ checked: { $ne: true } }).count();
      },
});
 
7.7  Display incompleteCountimports/ui/body.html »
<body>
    <div class="container">
      <header>
      <h1>Todo List ({{incompleteCount}})</h1>
 
      <label class="hide-completed">
      <input type="checkbox" />
          Hide Completed Tasks
      </label>
 
      <form class="new-task">
        <input type="text" name="text" placeholder="Type to add new tasks" />
      </form>
 
      </header>
  
      <ul>
        {{#each tasks}}
          {{> task}}
