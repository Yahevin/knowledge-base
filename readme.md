# Table of contents
1. [Scope](#Scope)
    * [Hoisting](#Hoisting)
    * [Execution Context](#execution-context)
    * [Lexical Environment](#lexical-environment)
    * [Variable Environment](#variable-environment)
    * [Scope Chain](#scope-chain)
    * [Closure](#Closure)
    * [This](#This)
1. [Promise](#Promise)
    * [Promise.then()](#Promise.then())
    * [Promise.all()](#Promise.all())
1. [Async/Await](#Async/Await)
1. [Event loop](#event-loop)
    * [Microtasks](#Microtasks)
    * [Macrotasks](#Macrotasks)
    * [Browser`s render queue](#browser’s-render-queue)
        * [window.requestAnimationFrame()](#window.requestAnimationFrame())
    * [Blocking tasks](#blocking-tasks)
    * [Execution context](#execution-context)
1. [Browser events](#browser-events)
    * [Bubbling](#bubbling)
    * [stopPropagation](#stopPropagation)
    * [Capturing](#Capturing)
1. [React hooks](#react-hooks)
    * [useState](#useState)
    * [useEffect](#useEffect)
    * [useContext](#useContext)
    * [useReducer](#useReducer)
    * [useMemo](#useMemo)
        * [React.memo](#React.memo)
    * [useCallback](#useCallback)
    * [useRef](#useRef)
        * [Callback-Ref](#Callback-Ref)
    * [useLayoutEffect](#useLayoutEffect)
1. [React Spring](#react-spring)
    * [useSpring](##useSpring)
    * [useSprings](##useSprings)
    * [useTrail](##useTrail)
    * [useTransition](##useTransition)
    * [useChain](##useChain) 
    * [Height:auto](##Height:auto)
    * [Interpolate](##Interpolate)
1. [React Redux with TS](#React-Redux-with-TS)
    * [Connect](##Connect)
    * [Redux hooks](##Redux-hooks)
        * [useSelector](###useSelector)
        * [useDispatch](###useDispatch)
    * [File System](##File-system)
    * [Code example](##Code-example)
1. [Database](#Database)
    * [MySQL](#MySQL)
    
    
    
    
# Scope

## Hoisting
«поднятие переменной» - при создании переменной в JavaScript, она инициализируются со значением undefined. 
Интерпретатор JavaScript присваивает переменой значение undefined по умолчанию, во время так называемой фазы «Создания».

## Execution Context
При передаче управления исполняемому коду осуществляется вход в контекст исполнения. 
Исполняемый код — это любой код, который мы выполняем в данный момент времени, это может быть и глобальный код, и код какой- либо функции.
Контекст исполнения - это абстракция, с помощью которой типизируют и разграничивают код. 
Код делится на глобальный(любые подключённые скрипты, инлайновые скрипты) и код функций(код вложенных функций не относится к контексту родительских функций).

При входе в контекст исполнения интерпритатор сканирует контекст. Все объявления переменных 
и декларации функций поднимаются к началу контекста. Переменные создаются равными undefined, а функции полностью готовыми к использованию.


## Lexical Environment
Лексическая окружение состоит из записи среды и, возможно, нулевой ссылки на внешнюю Лексическую среду.
Это связь сущности с контекстами её породившими.

Содержит в себе:
* все объявления переменных контекста
* все декларации функций
* все аргументы исполняемой функции
* ссылка на ЛО порождающего контекста

## Variable Environment
Это представление локальной памяти лексического окружения.
В записи окружения лексическое окружение хранит переменные, а также другую информацию, такую ​​как печально известная this.
Переменные окружения отображают локальную область действия данной среды - хранит те переменные, которые определены в данном блоке рабочего кода {}.

## Scope Chain
Цепь областей видимости также является свойством контекста исполнения как и this. 
Она представляет собой список объектов лексических окружений текущего контекста и всех порождающих контекстов. 
Именно в этой цепи происходит поиск переменных при разрешении имён идентификаторов.
```
SC = [LO, LO1, LO2,..., LOglobal];
```
В момент создания функции ей присваивается внутреннее свойство SCOPE.
В SCOPE записывается иерархическая цепь объектов лексических окружений вышестоящих(порождающих) контекстов. 
Это свойство остаётся неизменным до тех пор пока функция не уничтожена сборщиком мусора и являертся свойством самой функции, а не её контекста.

При вызове функции инициализируется и наполняется её контекст исполнения. 
Контексту проставляется ScopeChain = LO(самой функции) + SCOPE(иерархическая цепь LO пораждающих контекстов).

Разрешение имён идентификаторов — последовательный опрос объектов LO в цепи ScopeChain слева направо. 

## Closure 
Замыкание это по сути результат поиска в ScopeChain всех переменных, идентификаторы которых присутствуют в функции.
```js
function init() {
    const name = "myName"; 
    return function displayName() { 
        console.log(name);   
    }  
}
const sayMyName = init(); // function displayName() { ...
sayMyName() // myName
init()() // myName
```
displayName имеет доступ к локальной переменной функции init, даже после ее выполнение.
Переменная name находится в SCOPE ф-ии displayName, как ЛО порождающего контекста, 
который хранится по принципу - пока на объект существует хоть одна ссылка, он не удаляется из памяти

## This 
при выключенном strict mode в this находится глобальный объект(в браузере он проксирован на верхний уровень в объект window), 
при 'use strict' this равен undefined.

this связывает функцию с контекстом исполнения, и зависит от синтаксиса вызова

ReferenceType возвращается для всех объявлений переменных, деклараций функции и обращения к свойству(именно этот случай нас интересует с точки зрения понимания this).
Если слева от скобок активации функции находится ReferenceType, то в this функции проставляется base этого ReferenceType. 
Если слева от скобок любой другой тип, то в this проставляется глобальный объект.
У ReferenceType есть встроенный метод GetValue, который возвращает истинный тип получаемого объекта. 
GetValue срабатывает в зоне выражения: присваивание(=), операторы || или иные логические операторы, тернарный оператор, 
инициализатор массива, перечисление через запятую.

```js
const x = 0;
const obj = {
  x: 10,
  foo: function() {
    return this.x;
  }
}

obj.foo(); //вернёт 10
(obj.foo = obj.foo)(); 
// с обоих сторон от оператора присваивания срабатывает GetValue, 
// поэтому результатом будет тип Function, а не ReferenceType, 
// следовательно вернёт 0 из глобального объекта
(obj.foo || obj.foo)(); //вернёт 0 по тем же причинам
[obj.foo][0](); //вернёт 0 по тем же причинам
```

```js
const x = 0;
function foo() {
  function bar(){
   return this.x;
 }
return bar();
}

const obj = {x:10};
obj.test = foo;
obj.test();//вернёт undefined
```
Это связано с тем, что вызов bar() эквивалентен вызову LE_foo.bar, а объект лексического окружения проставляет undefined в качестве this.

    
# Promise

Promise – это специальный объект, который содержит своё состояние.
Вначале pending («ожидание»), затем – одно из: fulfilled («выполнено успешно») 
или rejected («выполнено с ошибкой»).

Promise не блокирует работу движка браузера (интерфейс страницы попрежнему интерактивен),
дожидаясь завершения работы асинхронной функции. Но результат выполнения промиса можно получить в коде
в любой момент времени, переведя его в состояние fulfilled или rejected.
Это происходит при вызове одного из двух методов: 
- resolve(response) - возвращает переданное ему как аргумент значение
- reject(error) - работает аналогично throw 

```js
const promise = new Promise(function(resolve, reject) {
    // вызов асинхронной функции

    // resolve(результат) при успешном выполнении
    // reject(ошибка) при ошибке
});
```

## Promise.then()

Промис имеет методы, вызываемые после разрешения промиса (“Thenable” object),
которые в свою очередь также возвращают промис. 
Таким образом их можно объединять в цепочки

```js
const test = new Promise((resolve,reject) =>{
    // reject('test reject');
    // or
    // resolve('test resolve');
}).then((value)=>{
    // не будет вызван, если промис будет разрешен с ошибкой
    console.log('promise was resolved',value);
    return value + 'first then';
}).catch((error)=>{
    // будет вызван, если промис будет разрешен успешно
    console.log('promise was rejected',error);
    return error + 'cached'
}).finally(()=>{
    // не изменяет возвращаемое промисом значение
    // и не имеет доступа к результату работы цепи промисов
    console.log('called in any case, just do something');
}).then((value)=>{
    console.log('called after all and in any case');
    return value + 'second then'
});
```

Пример использования - цепочка асинхронных запросов, 
в которой каждая последующая функция использует результат выполнения предыдущей.
Такой подход позволит избежать "callback hell":

```js
const getUserAvatar = getUserByName('name').then(function (user) {
    // Функция getUserAccountById возвращает promise,
    // результат которого попадет в следующий then
    return getUserAccountById(user.id);
  })
  .then(function (userAccount) {
    // Я знаю все о пользователе!
    return userAccount.avatar;
  });
```

## Promise.all()

Выполнение промисов можно запустить параллельно, а затем, 
когда завершится выполнение самой длительной из них, 
обработать полученные результаты всех функций:
```js
const async1 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 1000, "one"); 
}); 
const async2 = new Promise((resolve, reject) => { 
  setTimeout(resolve, 2000, "two"); 
});

Promise.all([async1, async2]).then((values)=>{
    console.log(values);
})

// ['one','two']
```
**[⬆ back to top](#table-of-contents)**

# Async/Await

- **async** указанный перед функцией - гарантирует, что эта функция вернет промис.
- **await** заставит интерпретатор JavaScript ждать до тех пор,
пока промис справа от await не выполнится. Работает только в теле **async** функции

```js
async function f() {

    const promise = new Promise((resolve, reject) => {
        setTimeout(() => resolve("готово!"), 1000)
    });

    const result = await promise; // будет ждать, пока промис не выполнится (*)

    alert(result); // "готово!"
}
```

Аснхронная функция должна вернуть промис:
```js
const promise = function(){
    return new Promise(function(resolve, reject) {});
};

async function f() {
    const result = await promise();

    alert(result); // "готово!"
}
```
**[⬆ back to top](#table-of-contents)**

# Event loop

Задачи поступают на выполнение – движок выполняет их – затем ожидает новые задачи.
Может так случиться, что задача поступает, когда движок занят чем-то другим, тогда она ставится в очередь.

- Heap  — объекты собраны в кучу, которая есть ни что иное, 
как название для наименее структурированной части памяти.
- Call Stack — репрезентация единственного потока выполнения JavaScript-кода. 
Вызовы функций помещаются в стек.
- Browser or Web API’s — встроены в браузер и способны предоставлять данные из браузера 
и окружающей компьютерной среды и давать возможность выполнять с ними полезные и сложные вещи.
Они не являются частью языка JavaScript, но они построены на его основе и предоставляют вам супер силы, 
которые можно использовать в JavaScript коде. 

```js
function main() {
    console.log('A');

    setTimeout(function exec() {
      console.log('B')
    }, 0);
    
    Promise.resolve()
      .then(() => alert('C'));

    console.log('D');
}

// A
// D
// C
// B
```

Как устроен "event loop":
1. Может так случиться, что задача поступает, когда движок занят чем-то другим, тогда она ставится в очередь.
Очередь, которую формируют такие задачи, называют "macrotask queue" (очередь макрозадач). Из этой очереди
задачи поступают по одной на выполнение в "call stack"(стек вызовов).
1. Функция main() находится на дне стека - она будет считаться выполненной после выполнения вложенных функций.
Так и формируется стек вызовов: поверх вызываемой функции помещается вложенная, 
поверх нее, функция вложенная во вторую и т.д. Выполнение начинается с верхней - наиболее глубоко вложенной.
1. setTimeout обращается к браузерном API и передает ему коллбек и задержку. 
Таймер выполняется вне "Main Thread"(основного потока). 
По истечении времени задержки таймер помещает коллбек в "callback queue"(очередь обратных вызовов).
1. После выполнения последней макрозадачи, call stack очищается. 
1. Тогда "event loop" обращается к "microtask queue"(очереди микрозадач). Пока очередь микрозадач не пуста, 
"event loop" будет перемещать микрозадачи в "call stack", 
начиная с самой старой(первой попавшей в "microtasks queue") и выполнять их.
1. После выполнения всех микрозадач в очереди, "event loop" обращается к "render queue". 
Задачи от "render queue" оптимизируются браузером и, если он посчитает, что в этом цикле не нужно ничего перерисовывать, 
то "event loop" просто пойдет дальше. 
1. После этого "event loop" обращается к "callback queue". Выполнение задач из callback queue также начинается с самой первой из них.
Задачи из "callback queue"  перемещаются в "call stack", только если тот пуст. 
У различных макрозадач выше приоритет на выполнение, поэтому "event loop" может переключиться на макрозадачи
до того, как выполнит все задачи из "callback queue'. Круг замкнулся.

## Macrotasks
Макротасками - задачами, называются функции, попадающие в "call stack" из heap, 
то есть вызываемые из кода, а также "browse events"(браузерные события). Их великое множество:
- клики мышкой;
- скроллинг;
- ввод с клавиатуры;
- загрузка скриптов;
- CSS анимации;
- и тд.

Поэтому так важно не блокировать "event loop", иначе вызовы эвентов, 
отвечающих за интерактивность страницы могут зависнуть в ожидании, когда "call stack" будет очищен.

## Microtasks
Асинхронные задачи требуют правильного управления. 
Для этого стандарт предусматривает внутреннюю очередь PromiseJobs,
более известную как "microtask queue"(очередь микрозадач).

- Очередь определяется как первым-пришёл-первым-ушёл (FIFO): задачи, попавшие в очередь первыми, выполняются тоже первыми.
- Выполнение задачи происходит только в том случае,
 если ничего больше не запущено - то есть "call stack" пуст

Или, проще говоря, когда промис выполнен, его обработчики .then/catch/finally попадают в очередь. 
Они пока не выполняются. Движок JavaScript берёт задачу из очереди и выполняет её, 
когда он освободится от выполнения текущего кода.

Сразу после каждой макрозадачи движок исполняет все задачи из очереди микрозадач перед тем,
как выполнить следующую макрозадачу или отобразить изменения на странице, или сделать что-то ещё.
Все микрозадачи завершаются до обработки каких-либо событий или рендеринга,
или перехода к другой макрозадаче.

```js
setTimeout(() => alert("timeout"));

Promise.resolve()
  .then(() => alert("promise"));

alert("code");
```

1. code появляется первым, т.к. это обычный синхронный вызов.
1. promise появляется вторым, потому что .then проходит через очередь микрозадач и выполняется после текущего синхронного кода.
1. timeout появляется последним, потому что это макрозадача.


```js
function foo() {
  Promise.resolve().then(foo);
}

foo();
```
Вызов foo помещает в call stack выражения из microtasks queue, а они выполняются все, пока не закончатся. 
А это значит, что пока Event Loop не закончит их, перейти к следующей задаче он не сможет. 

## Browser`s render queue
Браузер старается обновлять содержимое страницы с частотой обновления дисплей - 60Гц.
Процесс перерисовки страницы каждые ~16.667ms называется "render cycle"(цикл рендера).

Браузер планирует новое событие перерисовки каждый цикл рендеринга в 
"browser’s the render queue"(очереди рендеринга браузера).

Если функции внутри стека вызовов JavaScript не завершают свое выполнение в этом цикле рендеринга, 
тогда не остается достаточно времени для выполнения событий перерисовки.

### window.requestAnimationFrame()

Это метод, который указывает браузеру на то, что вы хотите произвести анимацию, 
и просит его запланировать перерисовку на следующем кадре анимации. 
В качестве параметра метод получает функцию, которая будет вызвана перед перерисовкой.

```js
let start = null;
const element = document.getElementById('SomeElementYouWantToAnimate');

function step(timestamp) {
  if (!start) start = timestamp;
  const progress = timestamp - start;
  element.style.transform = 'translateX(' + Math.min(progress / 10, 200) + 'px)';
  if (progress < 2000) {
    window.requestAnimationFrame(step);
  }
}

window.requestAnimationFrame(step);
```

## Blocking tasks

Рендеринг (отрисовка страницы) никогда не происходит во время выполнения задачи движком. 
Не имеет значения, сколь долго выполняется задача. Изменения в DOM отрисовываются только после того,
как задача выполнена.

Если задача выполняется очень долго, то браузер не может выполнять другие задачи,
обрабатывать пользовательские события, поэтому спустя некоторое время браузер предлагает «убить» долго выполняющуюся задачу.
Такое возможно, когда в скрипте много сложных вычислений или ошибка, ведущая к бесконечному циклу.

Блокирующую задачу можно "разбить на части", после завершения выполнения первой части, 
выполнение следующей откладывается. Например посредством вызова в качестве коллбек функции setTimeout.
Таким образом вызов следующей части попадает в очередь задач, откуда вернется в стэк выполнения,
когда тот очистится от задач, накопившихся за время выполнения первой части.

## Execution context
Как только скрипт попадает в интерпретатор, формируются глобальный контекст и глобальная область видимости, 
в которой держится Variable Object, или VO — объект переменных.

Когда заполняется "call stack", создается глобальный контекст выполнения, 
который является глобальной средой, в которой выполняется наш код JavaScript.
Также для каждой функции, имеющей вложенные объявления переменных и функций, 
создается локальный контекст выполнения

Контекст выполнения - это абстрактная концепция окружения, где код анализируется и выполняется. 
Всякий раз, когда в JavaScript выполняется код, это происходит внутри контекста выполнения.

setTimeout выводит контекст исполнения функции из синхронного потока, помещая его в event loop. 
То же самое происходит и с регистрацией событий. Мы можем подписаться на событие при помощи функции addEventListener. 
Передавая функцию обратного вызова — callback, добавляем её в список функций, 
которые должны быть вызваны при срабатывании этого события.

**[⬆ back to top](#table-of-contents)**

# Browser events

Событие – это сигнал от браузера о том, что что-то произошло. 
Все DOM-узлы подают такие сигналы (хотя события бывают и не только в DOM).
Вот список самых часто используемых DOM-событий, пока просто для ознакомления:

События мыши:
* click – происходит, когда кликнули на элемент левой кнопкой мыши (на устройствах с сенсорными экранами оно происходит при касании).
* contextmenu – происходит, когда кликнули на элемент правой кнопкой мыши.
* mouseover / mouseout – когда мышь наводится на / покидает элемент.
* mousedown / mouseup – когда нажали / отжали кнопку мыши на элементе.
* mousemove – при движении мыши.

События на элементах управления:
* submit – пользователь отправил форму <form>.
* focus – пользователь фокусируется на элементе, например нажимает на <input>.

Клавиатурные события:
* keydown и keyup – когда пользователь нажимает / отпускает клавишу.

События документа:
* DOMContentLoaded – когда HTML загружен и обработан, DOM документа полностью построен и доступен.

CSS events:
* transitionend – когда CSS-анимация завершена.

Событию можно назначить обработчик, то есть функцию, которая сработает, как только событие произошло.
Именно благодаря обработчикам JavaScript-код может реагировать на действия пользователя.

```js
const elem = document.getElementById('btn');

elem.addEventListener( "click" , () => alert('hello world'));
```

## Bubbling
Всплытие эвента. При наступлении события обработчики сначала срабатывают на самом вложенном элементе, 
затем на его родителе, затем выше и так далее, вверх по цепочке вложенности.

```html
<form onclick="alert('form')">FORM
  <div onclick="alert('div')">DIV
    <p onclick="alert('p')">P</p>
  </div>
</form>
```

При клике по абзацу, будут вызваны все 3 обработчки в порядке: **p**, **div**, **form**.

Самый глубокий элемент, который вызывает событие, называется «целевым» 
или «исходным» элементом и доступен как **event.target**. 
Элемент, на котором сработал обработчик доступен через **this**, **event.currentTarget**.

```js
elem.addEventListener( "click" , function(event){
    event.currentTarget.classList.add('reached');
    this.classList.add('another-reached');
});

// this === event.currentTarget === elem
```

## stopPropagation
Но любой промежуточный обработчик может решить, что событие полностью обработано, и остановить всплытие.
Для остановки всплытия нужно вызвать метод event.stopPropagation().

```html
<body onclick="alert('сюда обработка не дойдёт')">
  <button onclick="event.stopPropagation()">Кликни меня</button>
</body>
```

## Capturing
В современном стандарте, кроме «всплытия» событий, предусмотрено ещё и «погружение».
Оно гораздо менее востребовано, но иногда, очень редко, знание о нём может быть полезным.
Строго говоря, стандарт выделяет целых три стадии прохода события:

1. Событие сначала идёт сверху вниз. Эта стадия называется «стадия перехвата» (capturing stage).
1. Событие достигло целевого элемента. Это – «стадия цели» (target stage).
1. После этого событие начинает всплывать. Это – «стадия всплытия» (bubbling stage).

Чтобы поймать событие на стадии перехвата, нужно использовать третий аргумент addEventListener:
*   Если аргумент true, то событие будет перехвачено по дороге вниз.
*   Если аргумент false, то событие будет поймано при всплытии.

[Демо](https://yahevin.github.io/react_project_foundation/dist/events)

**[⬆ back to top](#table-of-contents)**

# React hooks

Хуки — нововведение в React 16.8, 
которое позволяет использовать состояние 
и другие возможности React с функциональными компонентами.
Хуки — это функции, с помощью которых вы можете «подцепиться» к состоянию 
и методам жизненного цикла React из функциональных компонентов.

## useState

useState принимает один аргумент - начальное значение состояния, 
и создает переменную - ссылку на текущее состояние компонента и его сеттер.
Таким образом обновление состояние через сеттер вызывает рендер метод компонента,
а прямое присваивание нового значения - нет.

```js
function ExampleWithManyStates() {
  // Объявляем несколько переменных состояния!
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('банан');
  const [todos, setTodos] = useState([{ text: 'Изучить хуки' }]);
  // ...
}
```

Если новое состояние вычисляется с использованием предыдущего состояния, 
в setState можно передать функцию. Функция получит предыдущее значение и вернёт обновлённое значение.

```typescript jsx
function Counter({initialCount}) {
  const [count, setCount] = useState(initialCount);
  return (
    <button onClick={() => setCount(prevCount => prevCount + 1)}>+</button>
  );
}
```

Чтобы повторно не создавать начальное состояние (которое по факту игнорируется), мы можем передать функцию в useState.
React вызовет эту функцию один раз во время первого рендера. 

```typescript jsx
function Table(props) {
  // createRows() будет вызван только один раз
  const [rows, setRows] = useState(() => createRows(props.count));
  // ...
}
```

Если состояние хука обновляется тем же значением, что и текущее состояние, 
React досрочно выйдет из хука без повторного рендера дочерних элементов и запуска эффектов.

## useEffect

useEffect выполняет ту же роль, что и componentDidMount, componentDidUpdate и componentWillUnmount 
в React-классах, объединив их в единый API. 

Хук эффекта даёт возможность выполнять побочные эффекты в функциональном компоненте.
Побочными эффектами в React-компонентах могут быть: 
загрузка данных, оформление подписки и изменение DOM вручную.
То есть те действия, которые не должны приводить к обновлению состояния.
useEffect будет выполняться после каждого рендера и обновления.

```js
function Example() {
  const [count, setCount] = useState(0);

  // обновляет заголовок каждый раз, как изменилось состояние счетчика
  useEffect(() => {
    document.title = `Вы нажали ${count} раз`;
  });
}
```

В некоторых случаях сброс или выполнение эффекта при каждом рендере может вызвать проблему с производительностью. 
В классовых компонентах это можно решить используя дополнительное сравнение 
prevProps или prevState внутри componentDidUpdate.

useEffect принимает второй необязательный аргумент - значение состояния, при изменения которого будет отрабатывать эффект.
React пропустит вызов эффекта, если эти значения остались без изменений между последующими рендерами

```js
function Example() {
  const [count, setCount] = useState(0);
  const [color, setColor] = useState('#fff');

  useEffect(() => {
    document.title = `Вы нажали ${count} раз`;
  }, [count]) // Перезапускать эффект только если count поменялся
}
```


Хук эффекта также можно использовать более одного раза. 
Это даёт возможность разделять разную несвязанную между собой логику между разными эффектами.
```js
function exampleWithSubscribe() {
  useEffect(() => {
    document.title = `Вы нажали ${count} раз`;
  });
  useEffect(() => {
    document.addEventListener('click', handler);
        
    return () => {
      document.removeEventListener('click', handler);
    };
  });
}
```

Каждый эффект может возвратить функцию, которая сбросит его перед тем, как компонент размонтируется. 
Это даёт возможность объединить вместе логику оформления и отмены подписки.
```js
function FriendStatus(props) {
  useEffect(() => {
    ChatAPI.subscribeToFriendStatus(props.friend.id);

    return () => {
      ChatAPI.unsubscribeFromFriendStatus(props.friend.id);
    };
  });
}
```

Сброс эффекта необходим по той причине, 
что при обновлении пропсов произойдет повторная подписка с новыми данными.
Отсутствие сброса также может привести к утечке памяти 
или вообще к вылету приложения при размонтировании.

После рендера с новыми пропсами, перед повторным вызовом эффекта, 
произойдет сброс предыдущего эффекта с предыдущими пропсами, 
что избавляет от необходимости использовать componentDidUpdate(prevProps).

```js
// Монтируем с пропсами { friend: { id: 100 } } 
ChatAPI.subscribeToFriendStatus(100);     // Выполняем первый эффект

// Обновляем с пропсами { friend: { id: 200 } }
ChatAPI.unsubscribeFromFriendStatus(100); // Сбрасываем предыдущий эффект
ChatAPI.subscribeToFriendStatus(200);     // Выполняем следующий эффект
```

## useContext

Контекст предоставляет способ делиться такими данными между компонентами 
без необходимости явно передавать пропсы через каждый уровень дерева.


```typescript jsx
const ThemeContext = React.createContext('#fff');

// Контекст позволяет передавать значение глубоко
// в дерево компонентов без явной передачи пропсов
// на каждом уровне. Создадим контекст для текущей
// UI-темы (со значением "#fff" по умолчанию).
function App() {
  return (
    <ThemeContext.Provider value={'#000'}>
      <Toolbar />
    </ThemeContext.Provider>
  );
}

// Компонент, который находится в середине,
// больше не должен явно передавать тему вниз.
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

// Определяем contextType, чтобы получить значение контекста.
// React найдёт (выше по дереву) ближайший Provider-компонент,
// предоставляющий этот контекст, и использует его значение.
// В этом примере значение UI-темы будет "#000".
function ThemedButton() {
  const theme = useContext(ThemeContext);
  return (
    <button style={{ background: theme }}>
      Я стилизован темой из контекста!
    </button>
  );
}
```

## useReducer

Хук useReducer - альтернатива useState и обычно предпочтительнее последнего, 
когда у вас сложная логика состояния, которая включает в себя несколько значений, 
или когда следующее состояние зависит от предыдущего. 
useReducer также позволяет оптимизировать производительность компонентов, 
которые запускают глубокие обновления, поскольку вы можете передавать dispatch вместо колбэков.

```js
const [state, dispatch] = useReducer(reducer, initialArg, init);
```

useReducer принимает:
-   функцию редюсер вида (state, action) => newState
-   начальное значение состояния
-   необязательный аргумент, функция устанавливающая начальное состояние 
равным результату вызова init(someArg).

useReducer возвращает:
-   переменную состояния
-   метод dispatch, 
который вызывает редюсер и передает в него вторым аргументом объект действия {type, payload} по аналогии с Redux.

```typescript jsx
function init(initialCount) {
  return {count: initialCount};
}

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return {count: state.count + 1};
    case 'decrement':
      return {count: state.count - 1};
    case 'reset':
      return init(action.payload);
    default:
      throw new Error();
  }
}

function Counter({initialCount}) {
  const [state, dispatch] = useReducer(reducer, initialCount, init);
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'reset', payload: initialCount})}>
        Reset
      </button>
      <button onClick={() => dispatch({type: 'decrement'})}>-</button>
      <button onClick={() => dispatch({type: 'increment'})}>+</button>
    </>
  );
}
```

## useMemo

```js
const memoizedValue = useMemo(() => computeExpensiveValue(a, b), [a, b]);
```

useMemo будет повторно вычислять мемоизированное значение только тогда, 
когда значение какой-либо из зависимостей изменилось. 
Эта оптимизация помогает избежать дорогостоящих вычислений при каждом рендере.


React может решить «забыть» некоторые ранее мемоизированные значения 
и пересчитать их при следующем рендере, например, 
чтобы освободить память для компонентов вне области видимости экрана. 

useMemo позволяет пропускать затратные повторные рендеры дочерних компонентов.

```typescript jsx
function ColorPicker() {
  // Не нарушает неглубокую проверку на равенство свойств компонента Child,
  // система реагирует лишь на реальное изменение цвета.

  const [color, setColor] = useState('pink');
  const MemoizedChild = useMemo(()=> <Child style={color} />,[color]);
  
  return ( <MemoizedChild/> );
}
```

Однако обновление состояния родительского компанента, приводит к ререндеру дочернего компонента, 
даже при условии, что тот использует только неизменившееся мемоизированное свойство.

```typescript jsx
function ColorPicker(props) {
  const [size,setSize] = useState('49.5');
  const [color, setColor] = useState('pink');
  const memoizedProp = useMemo(()=>(color),[color]);
  
  //Вызов любого из setState, либо обновление пропсов приведет к вызову метода render
  return ( <Child style={color} /> );
}
```

[Демо](https://yahevin.github.io/react_project_foundation/dist/memo)

### React.memo                 
Хук useMemo это альтернатива методу: React.memo - HOC (компонент высшего порядка), 
который оборачивает классовый, либо функциональный компоненты.

```typescript jsx
const Memoized = React.memo(() => <div>Hello world</div>)
```

По умолчанию он поверхностно сравнивает вложенные объекты в объекте props. 
Чтобы контролировать сравнение, следует передать функцию сравнения в качестве второго аргумента.
```typescript jsx
const MemoizedName = React.memo(
  (props) => <div>{props.name}</div>
,
  // return true if passing nextProps to render would return the same result as passing prevProps to render, otherwise return false
  (prevProps, nextProps) => prevProps.name === nextProps.name
)
```

## useCallback

Принимает встроенный колбэк и массив зависимостей. 
Хук useCallback вернёт мемоизированную версию колбэка, который изменяется только, 
если изменяются значения одной из зависимостей. 
Это полезно при передаче колбэков оптимизированным дочерним компонентам, 
которые полагаются на равенство ссылок для предотвращения ненужных рендеров (например, shouldComponentUpdate).

useCallback(fn, deps) — это эквивалент useMemo(() => fn, deps)

Функция, объявленная внутри компонента, изменяется в каждой операции рендеринга.
Она будет создаваться заново, даже если используемые параметры остаются неизменными.
В таком случае эффекты, будут срабатывать при каждом обновлении, 
даже будучи привязанными к этой функции.

```js
function SearchResults(props) {
    
  const getFetchUrl = () => {
    return 'https://test/search?query=' + props.query;
  }; 
  // функция обновляется при каждом ререндере компонента 
 
  useEffect(() => {
    const url = getFetchUrl();
    // ... Загрузим данные и что-то с ними сделаем 1...

  }, [getFetchUrl]); // С зависимостями эффекта всё в порядке.


  useEffect(() => {
    const url = getFetchUrl();
    // ... Загрузим данные и что-то с ними сделаем 2...

  }, [getFetchUrl]); // С зависимостями эффекта всё в порядке
}
```

```js
function SearchResults(props) {

  // Если зависимости не меняются, сущность сохраняется  
  const getFetchUrl = useCallback(() => {
    return 'https://test/search?query=' + props.query;
  }, [props.query]) // С зависимостями коллбэка всё в порядке.

  // ...
}
```

## useRef

```js
const refContainer = useRef(initialValue);
```

useRef возвращает изменяемый ref-объект, свойство .current которого инициализируется переданным аргументом (initialValue). 
Возвращённый объект будет сохраняться в течение всего времени жизни компонента.

useRef используется в случаях, когда необходимо императивно изменить дочерний элемент, обойдя обычный поток данных. 
Подлежащий изменениям дочерний элемент может быть как React-компонентом, так и DOM-элементом.

```typescript jsx
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` указывает на смонтированный элемент `input`
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Установить фокус на поле ввода</button>
    </>
  );
}
``` 
Хук useRef пришел на замену CreateRef.
```js
const refContainer = React.CreateRef();
```
Рвзница между этими методами заключается в том, что CreateRef() возвращает новый реф при каждом рендере, 
а то хук useRef даст один и тот же объект с рефом при каждом рендере. 
Возвращаемый объект сохраняется на протяжении всего жизненного цикла компонента.
Это и отличает useRef() от простого создания объекта {current: ...}, 
так что хук useRef ещё применим для сохранения любого мутируемого значения. 
useRef возвращает мутабельный объект, свойству .current которого присваивается 
значение аргумента initialValue.Мутирование свойства .current не вызывает повторный рендер. 

```typescript jsx
function Test() {
   const [renderIndex, setRenderIndex] = React.useState(1);
   const refFromUseRef = React.useRef();
   const refFromCreateRef = createRef();

   if (!refFromUseRef.current) {
      // сработает 1 раз
      refFromUseRef.current = renderIndex
   }

   if (!refFromCreateRef.current) {
      // будет обновляться при каждом рендере
      refFromCreateRef.current = renderIndex
   }

   return (
      <>
         <p>value:{refFromUseRef.current}</p>
         <p>value:{refFromCreateRef.current}</p>

         <button onClick={() => setRenderIndex(prev => prev + 1)}>
            Cause re-render
         </button>
      </>
   )
}
```

[Демо](https://yahevin.github.io/react_project_foundation/dist/ref)

### Callback-Ref
Вместо того, чтобы передавать атрибут ref созданный с помощью createRef(), можно передать функцию. 
Данная функция получит экземпляр React-компонента или HTML DOM-элемент в качестве аргумента, 
которые потом могут быть сохранены или доступны в любом другом месте.

```typescript jsx
function MeasureExample() {
  const [height, setHeight] = useState(0);

  const measuredRef = (node) => {
    // из-за обновления функции коллбэк вызывается дважды
    // и первый вызов с node === null, ref еще не создан
    if (node !== null) {
      setHeight(node.offsetHeight);
    }
  };

  return (
    <>
      <h1 ref={measuredRef}>Привет, мир</h1>
      <h2>Заголовок выше имеет высоту {Math.round(height)} пикселей</h2>
    </>
  );
}
```

При желании вы можете извлечь эту логику в хук для повторного использования:
```typescript jsx
function MeasureExample() {
  const [rect, ref] = useClientRect();
  return (
    <>
      <h1 ref={ref}>Привет, мир</h1>
      {rect !== null &&
      <h2>Заголовок выше имеет высоту {Math.round(rect.height)} пикселей</h2>
      }
    </>
  );
}

function useClientRect() {
  const [rect, setRect] = useState(null);
  const ref = (node) => {
    if (node !== null) {
      setRect(node.offsetHeight);
    }
  };
  return [rect, ref];
}
```

[Демо](https://yahevin.github.io/react_project_foundation/dist/callback_ref)

## useLayoutEffect

useEffect работает синхронно и выполняется после обновления контента на странице:
-   запускается функция рендер
-   страница обновлена
-   потом запускается useEffect

useLayoutEffect работает асинхронно, после рендера, но до обновления страницы:
-    запускается функция рендер
-    потом запускается useLayoutEffect, и React ожидает конца выполнения эффекта
-    страница обновлена

useLayoutEffect пможет быть использован, если обновление представляет собой двухэтапный (или многоэтапный) процесс 
- например, он сначала отображается в частично готовом состоянии, 
а затем сразу же повторно отображается в своем конечном состоянии.

useLayoutEffect позволяет «сгруппировать» пару обновлений перед перерисовкой экрана, 
таким образом компонент не будет мерцать - визуально будет заметно только одно обновление.

```typescript jsx
const BlinkyRender = () => {
  const [value, setValue] = useState(0);

  useLayoutEffect(() => {
    if (value === 0) {
      setValue(100000);
    }
  }, [value]);

  return (
    <div onClick={() => setValue(0)}>
      value: {value}
    </div>
  );
};
```

[Демо](https://yahevin.github.io/react_project_foundation/dist/layout_effect)

**[⬆ back to top](#table-of-contents)**

# React spring
[Библиотека](https://www.react-spring.io/) для создания анимаций, имитирующих физику пружины.

Поведение задается через render-props api либо хуки.
Как и везде в этом пособии, мы будем топить за хуки, делая фолл-бэк к "устаревшим" технологиям.

## useSpring
```typescript jsx
import {useSpring, animated} from 'react-spring';

function fade({toggle}) {
    const props = useSpring({
        opacity: toggle ? 1 : 0,
        config: { mass: 5, tension: 350, friction: 40 } 
    });
    
    return (
        <animated.div style={props}>
            hello world
        </animated.div>
    )
}
```

Существует сокращенная запись "to", однако все еще нужен "from", 
либо значение в "to" должно меняться в зависимости от состояния.
```typescript jsx
// This ...
const props = useSpring({opacity: 1, color: 'red'});
// is a shortcut for this ...
const props = useSpring({to: {opacity: 1, color: 'red'}};
```

Хук возвращает функцию сеттер.
```typescript jsx
function App() {
  const [props, set, stop] = useSpring(() => ({opacity: 1}));

  return (
     <>
        <animated.div style={props}
            onMouseOver={()=>{set({opacity: 0})}}
            onMouseLeave={()=>{set({opacity: 1})}}
        >
          Try to catch
        </animated.div>

        <button onClick={stop}>Stop animation</button>
     </>
  )
}

set(prev => !prev)
// не сработает, потому что prev - всегда будет возращать в данном случае 1, т.к. это указано в initial
```


Необязательно использовать <animated.tag> 
```typescript jsx
const Wrap = styled.div`
    overflow: hidden;
    background: #000;
    border-radius: 10px;   
`;
const Animated = animated(Wrap);

function animation() {
    const props = useSpring({opacity: toggle ? 1 : 0});
    
    return (
        <Animated style={props}>
            hello world
        </Animated>
    )
}
```

## useSprings
Создает множество анимированных объектов с уникальными конфигурациями.
Хук задается аналогично useSpring, отличие - в аргументы передается количество объектов.  

```typescript jsx
const springs = useSprings(number, items.map((item, index)) => ({ opacity: item.opacity }));

const [springs, set, stop] = useSprings(number, index => ({opacity: 1}));
```

```typescript jsx
const pages: string[] = [...];

function Viewpager() {
  const height = 80;
  
  // this is equal  
  const [props, set] = useSprings(pages.length, i => ({ tr: i * height, from: {tr: 0} }));
  // to this  
  const [props, set] = useSprings(pages.length, i => ({ tr: 0 }));
  set(i => {
    return {tr: i * height};
  });
  //


  return (
    <div>
      {props.map((item, i) => (
        <animated.div 
          key={i} 
          style={{ 
            position: 'absolute',
            height: height + 'px', 
            transform: item.tr.interpolate(x => `translate3d(0,${x}px,0)`), 
            backgroundImage: `url(${pages[i]})` 
          }} 
        />
      ))}
    </div> 
  ) 
}

```

## useTrail
Создает множество анимированных объектов с одинаковой конфигурацией. 
Каждая последующая анимация следует за предыдущей.

Хук задается аналогично useSprings.

```typescript jsx
const trail = useTrail(data.length, {opacity: 1, from: {opacity: 0}});

return (
    <>
      { trail.map((props,index) => (
        <animated.div style={props}>{data[index]}</animated.div>
      ))}
    </>
)
```

## useTransition
Позволяет анимировать монтирование, размонтирование элементов.
В отличие от useSpring, скрытые элементы полностью удаляются из DOM.

useTransition(item, key, config)                                
item - массив выводимых элементов, либо любое другое значение - 
в таком случае transitions.map просто вернет вложенный компонент, 
а переданное значение item можно будет использовать для управления отображаемым контентом.

```typescript jsx
function Toggle({toggle}) {
    const transitions = useTransition(toggle, null, {
        from: { position: 'absolute', opacity: 0 },
        enter: { opacity: 1 },
        leave: { opacity: 0 },
    });

    return (
        <>
            transitions.map(({ item, key, props }) => 
                item
                  ? <animated.div style={props}>😄</animated.div>
                  : <animated.div style={props}>🤪</animated.div>
                )
        </>
    )
}
```

Свойство trail конфигурации хука определяет задержку в ms анимирования каждого следующего элемента списка.
```typescript jsx
const data = [{name:'first',css:'green'}, ...]; //items array;

function TransitionWithTrail({isOpen}) {
    const transitions = useTransition(isOpen ? data : [], item => item.name, {
        unique: true,
        trail: 400 / data.length,
        from: { opacity: 0, transform: 'scale(0)' },
        enter: { opacity: 1, transform: 'scale(1)' },
        leave: { opacity: 0, transform: 'scale(0)' }
    });

    return (
            <>
                {transitions.map(({ item, key, props }) => (
                    <animated.div key={key} style={{ ...props, background: item.css }} />
                ))}
            </>
    )
}
```

useTransition можно использовать для анимирования переключения страниц роутера.
```typescript jsx
function Content() {
    const location = useLocation();

    const transitions = useTransition(location, location => location.pathname, {
        from: { opacity: 0, transform: `translate3d(100%,0,0)`},
        enter: { opacity: 1, transform: 'translate3d(0%,0,0)' },
        leave: { opacity: 0,transform: `translate3d(-50%,0,0)`, position: 'absolute'},
    });

    return (
        <main>
            {transitions.map(({item,props,key})=>(
                <animated.div key={key} style={props}>
                    <Switch location={item}>
                        <Route path={'/first'}>
                            <First />
                        </Route>
                        <Route path={'/second'}>
                            <Second/>
                        </Route>
                        <Route path={'/third'}>
                            <Third/>
                        </Route>
                    </Switch>
                </animated.div>
            ))}
        </main>
    )
}
```

## useChain
Устанавливает порядок выполнения ранее определенных обработчиков анимации, 
при котором одна анимация запускается последовательно за другой. 
Для определения порядка следует указать рефы анимируемых объектов.
Порядок можно изменить в последующих проходах рендеринга.

```typescript jsx
// Build a spring and catch its ref
const springRef = useRef()
const props = useSpring({...values, ref: springRef})
// Build a transition and catch its ref
const transitionRef = useRef()
const transitions = useTransition({...values, ref: transitionRef})
// First run the spring, when it concludes run the transition

// The spring will start right away: 0.0 * 1000ms = 0ms
// The transition will start after: 0.5 * 1000ms (the timeFrame default) = 500ms
useChain([springRef, transitionRef], [0, 0.5] /*1000*/)
// Use the animated props like always
return (
  <animated.div style={props}>
    {transitions.map(({item, key, props}) => (
      <animated.div key={key} style={props} />
    ))}
  </animated.div>
)
```

## Height:auto
Хуки не умеют в height: "auto", придется использовать <Spring>.
```typescript jsx
import { Spring, animated } from 'react-spring/renderprops';

function Toggle({isOpen}) {
    return (
        <Spring
            from={{ height: isOpen ? 0 : 'auto' }}
            to={{ height: isOpen ? 'auto' : 0 }}>
            {style => (
                <animated.div style={style}>
                    Some content
                </animated.div>
            )}
        </Spring>
    )
}
```

## Interpolate
Позволяет использовать интреполируемое значение(число), либо целую строку css кода(её можно, например, дополнить).

```typescript jsx
const Translate = () => {
  const spring = useSpring({
    from: {
      X: 0,
    },
    to: {
      X: 120,
    },
  });

  return (
    <animated.div
      style={{
        transform: spring.X.interpolate(value => `translate(${value}px)`),
      }}
    >
      Translated
    </animated.div>
  );
};
```

**[⬆ back to top](#table-of-contents)**

# React Redux with TS
чтобы сделать хранилище доступным по всему дереву компонентов,
следует обернуть все приложение в компонент \<Provider>, передав тому объект [хранилища](###store/index):
```js
import {Provider} from "react-redux";
import store from "@/store";

ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root');
)
```

## Connect
Функция connect () подключает компонент React к хранилищу Redux. 
Он предоставляет свой связанный компонент с частями необходимых ему данных из store и функциями, 
которые он может использовать для отправки действий в store.

```typescript
function connect(mapStateToProps?, mapDispatchToProps?, mergeProps?, options?);
```
MapStateToProps и mapDispatchToProps работают с state и dispatch хранилища Redux.

```typescript jsx
function App(props) {
  return (
    <h1 onClick={props.onClick}>{props.todoList}</h1>
  )
}

function mapStateToProps(state) {
  return state.todoList;
}
function mapDispatchToProps(dispatch) {
  return {
    onClick: event => dispatch({type: 'CLICK', payload: event.target}),
}

export default connect(mapStateToProps, mapDispatchToProps)(App);
```

## Redux hooks
Хуки облегчают написание кода - они освобождают от необходимости использования connect().

### useSelector
Селектор будет вызываться со всем состоянием хранилища Redux в качестве единственного аргумента. 
Селектор будет запускаться всякий раз, когда функциональный компонент выполняет рендеринг. 
useSelector () также будет подписываться на хранилище Redux и обновляться при каждом вызове dispatch().

```typescript
const page = useSelector((store:TStore) => store.pageReducer.page);
```
[TStore](###store/reducer) - тип глобального хранилища. 
Его использование позволяет ide давать подсказки о содержимом стора.
В этом примере store формируется из стейтов нескольких редукторов.

### useDispatch
Возвращает функцию обновления глобального хранилища. 

```typescript jsx
function StartPage() {
    const dispatch = useDispatch();

    const startHandler = useCallback(() => {
        dispatch({
             type: 'SET_PAGE',
             payload: page,
        });
    }, []);

    return (
        <button callback={startHandler}>
            Start
        </button>
    )
}

export default StartPage;
```
Для типизации передаваемых в редуктор объектов действия(action) можно создать автоматически заполняемый тип.
Пример реализации: [action](###store/page/action), [reducer](###store/page/reducer).

Тип PageActionTypes позволяет типизировать объект действия - 
таким образом тип action.payload будет определен для каждого соответсвующего action.type.
Тип action.type типизирован как строковый литерал - 
а заначит на каждый case в конструкции switch будет соответсвующий тип экшена.

## File system
constants                                       
&emsp;&emsp;\\__ [PAGES.ts](###constants/PAGES)                                 
store    
&emsp;&emsp;\\__ [actions.ts](###store/actions)                                                                                          
&emsp;&emsp;\\__ [index.ts](###store/index)                                                                           
&emsp;&emsp;\\__ [reducer.ts](###store/reducer)                                                                         
&emsp;&emsp;\\__ page          
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;\\__ [action.ts](###store/page/action)          
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;\\__ [IPageState.ts](###store/page/IPageState)              
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;\\__ [reducer.ts](###store/page/reducer)         
&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;&emsp;\\__ [state.ts](###store/page/state)               

## Code example

###store/actions
[Файловая система](##File-system)
```typescript
export const SET_PAGE = 'SET_PAGE' as 'SET_PAGE';
```

###store/index
[Файловая система](##File-system):
- [reducer](###store/reducer)

```typescript
import {createStore} from 'redux';
import {reducer} from "@/store/reducer";

const store = createStore(reducer);

export default store;
```


###store/reducer
[Файловая система](##File-system):
- [pageReducer](###store/page/reducer)

```typescript
import pageReducer from "@/store/page/reducer";
...

const reducers = {
    pageReducer,
    ...
};

type InferResultType<T> = T extends ((...args:any) => infer U) ? U : never;
type InferReducerType<T> = {[K in keyof T]: InferResultType<T[K]>};

type TStore = InferReducerType<typeof reducers>
const reducer = combineReducers(reducers);


export {reducer, TStore};
```


###constants/PAGES
```typescript
enum PAGES {
    START = '/',
    AUTH = '/auth',
    MAIN = '/main'
}

export default PAGES;
```


###store/page/state
[Файловая система](##File-system):
- [PAGES](###constants/PAGES)
- [IPageState](###store/page/IPageState)

```typescript
import PAGES from "@/constants/Pages";
import IPageState from "@/store/page/IPageState";

const pageState: IPageState = {
    page: PAGES.START
};

export default pageState;
```


###store/page/action
[Файловая система](##File-system):
- [SET_PAGE](###store/actions)
- [PAGES](###constants/PAGES)

```typescript
import {SET_PAGE} from "@/store/actions";
import PAGES from "@/constants/Pages";

const PageAction = {
    set: function(page: PAGES) {
        return {
            type: SET_PAGE,
            payload: page,
        }
    },
};

type InferValueTypes<T> = T extends {[key: string]: infer U} ? U : never;
type PageActionTypes = ReturnType<InferValueTypes<typeof PageAction>>;

export {PageAction, PageActionTypes};
```

###store/page/IPageState
[Файловая система](##File-system):
- [PAGES](###constants/PAGES)

```typescript
import PAGES from "@/constants/Pages";

interface IPageState {
    page: PAGES
}

export default  IPageState;
```


### store/page/reducer
[Файловая система](##File-system):
- [pageState](###store/page/state)
- [PageActionTypes](###store/page/actions)
- [IPageState](###store/page/IPageState)

```typescript
import {SET_PAGE} from "@/store/actions";
import pageState from "@/store/page/state";
import {PageActionTypes} from "@/store/page/action";
import IPageState from "@/store/page/IPageState";


function pageReducer(state = pageState, action: PageActionTypes) : IPageState {
    switch (action.type) {
        case SET_PAGE: {
            return {
                ...state,
                page: action.payload
            }
        }
        default: {
            return state;
        }
    }
};

export default pageReducer;
```

# Database

## MySQL

Обновление пароля рута:
```sql
ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
-- Or 
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';
```

Cписок бд:
```sql
SHOW DATABASES;
```
Создание бд:
```sql
CREATE DATABASE db_name;
```

Переключение бд:
```sql
USE db_name;
```

Создание юзера: 
```sql
CREATE USER 'username'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'password';
-- Or 
CREATE USER 'username'@'localhost' IDENTIFIED WITH mysql_native_password BY 'password';

GRANT ALL PRIVILEGES ON  db_name.* TO 'username'@'localhost';
```

В случае "Your password does not satisfy the current policy requirements":
```sql
SHOW GLOBAL VARIABLES LIKE 'validate_password%';

SET GLOBAL validate_password_special_char_count = 0;
SET GLOBAL validate_password.length = 0;
SET GLOBAL validate_password.mixed_case_count = 0;
SET GLOBAL validate_password.number_count = 0;
SET GLOBAL validate_password.policy = LOW;
```

Import dump:
```sql
mysql -u username -p db_name < file.sql;
```




