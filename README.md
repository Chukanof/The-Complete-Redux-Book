# Перевод книги [The Complete Redux Book](https://leanpub.com/redux-book) от Ilya Gelman и Boris Dinkevich

## Часть 1. Введение в Redux

### Глава 1. Основные понятия Flux и Redux

Пенициллин, рентген и кардиостимулятор являются известными примерами [непреднамеренных открытий](http://www.businessinsider.com/these-10-inventions-were-made-by-mistake-2010-11?op=1&IR=T). Redux, так же, не должен был стать библиотекой, но оказался отличной реализацией Flux. В мае 2015 года, один из его авторов, [Dan Abramov](https://survivejs.com/blog/redux-interview/), выступал на конференции ReactEurope с докладом о "hot reloading and time travel" (горячей перезагрузке и путешествии во времени). Он признает, что в тот момент понятия не имел, как осуществить time travel. С некоторой помощью [Andrew Clark](https://twitter.com/acdlite) и вдохновленный некоторыми изящными идеями из языка [Elm](http://elm-lang.org/), Dan, в конце концов, придумал очень приятную архитектуру. Когда люди начали понимать это, он принял решение распространять это как библиотеку.

Менее чем за пол года эта маленькая (всего лишь 2KB) библиотека стала основной для React разработчиков, в связи с её крохотным размером, легко читаемым кодом и очень простыми, но аккуратными идеями, с которыми было намного легче справиться, чем с конкурирующей реализаций Flux. Фактически, Redux не является Flux библиотекой, хотя она развилась от идей архитектуры Flux в Facebook. Официальное определение [Redux](https://redux.js.org/) - "_Предсказуемый контейнер состояния для JavaScript приложений_". Это простая идея, которая означает, что вы сохраняете все состояние своего приложения в одном месте и можете узнать какое состояние находится у приложения в данный момент времени.

#### Что такое Flux?

Перед погружением в Redux? мы должны должны ознакомиться с его базой и предшественником - _Flux архитектурой_. "Flux" - это общая архитеутура или паттерн, а не конкретная реализация. Эти идеи были впервые представлены публично Bill Fisher и Jing Chen на конференции Facebook F8 в апреле 2014 года. Flux описывали как переопределение предыдущих идей MVC (Mode-View-Model) и MVVM (Model-View-View-Model) паттернов двусторонней привязки данных, применяемых в других фреймворках. Он предлогал новый паттерн потока событий на frontend стороне - _однонаправленный поток данных._

События во Flux регулируются по одному за раз в круговом потоке с рядом участников: dispatcher, stores, и actions. _Action_ - это структура, описывающая любые изменения в системе: клик мышью, события таймера, Ajax запрос и другие. Action'ы передаются в _dispatcher_ - единственное место в системе, где кто-угодно может отправить action на обработку. Состояние приложения затем сохраняется в _stores_, которые содержат части состояния приложения, и реагируют на команды из dispatcher
 
Это простейший Flux поток:
1. Stores подписываются на подмножество действий.
2. Action отправляется в dispatcher.
3. Dispatcher уведомляет подписанные stores о action.
4. Stores обновляют свое состояние, основываясь на action.
5. Отображаемый контент обновляется в соответствии с новым состоянием в stores.
6. Можно обрабатывать следующий action.

![Flux поток](./source/img/flux-flow.png)

Этот поток обеспечивает легкое понимание того, как actions протекают в системе, что будет причиной изменения состояния и как оно изменится.

Рассмотрим пример приложения на jQuery или AngularJS. Клик по кнопке может привести к вызову нескольких обратных вызовов (callbacks), каждый из которых обрабатывает раличные части системы, что может, в свою очередь, вызвыать обновление в других местах. В этом случае разработчику практически невозможно узнать, как одно событие может изменить состояние приложения и в каком порядке произойдут эти изменения.

Во Flux событие click генерирует один action, который будет изменять store, а затем и отображение. Любые действия, созданные store или другими компонентами во время этого процесса, будут поставлены в очередь и выполняться только после выполнения первого действия и обновления отображения.

Разработчики Facebook изначально не выкладывали в общий доступ их реализацию Flux, а скорее, выложили только её части, например dispatcher. Это привело к тому, что сообщество создало множество реализаций с открытым исходным кодом, некоторые из которых существенно отличаются друг от друга, а некоторые лишь слегка меняют исходные шаблоны. Например, некоторые имеют множество dispatchers, другие вводят зависимости между sores

>У Дмитрия Воронянского есть хорошее сравнение различных реализаций Flux на [GitHub](https://github.com/voronianski/flux-comparison).

#### Redux и Flux

Хотя и Redux происходит от концепций Flux, между этими двумя архитектурами существует несколько различий. В отличии от Flux, Redux имееет только один store, который не содержит в себе никакой логики. Actions отправляются непосредственно в store и обрабатываются им же, что убирает необходимость в отдельном dispatcher. Store, в свою очередь, передает actions в функции, которые управляют изменениям состояния. Эти функции называются _reducers_ - новый тип участника потока, добавленный в Redux.

![Reducer пришел на смену Dispatcher](./source/img/dispatcher-reducer.png)