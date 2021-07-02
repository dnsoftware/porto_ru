# Porto (Архитектурный шаблон)

![](/assets/porto-logo.png)


## Добро пожаловать в Porto

- [Вступление](#Introduction)
- [Приступая к работе](#Getting-Started)
	- [1) Слой Корабля](#Ship-Layer)
	- [2) Слой Контейнеров](#Containers-Layer)
- [Компоненты](#Components)
	- [1) Основные компоненты](#Main-Components)
		- [1.1) Схема взаимодействия компонентов](#Components-Interaction-Diagram)
		- [1.2) Жизненный цикл запроса](#Request-Life-Cycle)
		- [1.3) Определения и принципы основных компонентов](#Components-Details)
			- [Маршруты (Routes)](#Routes)
			- [Контроллеры (Controllers)](#Controllers)
			- [Запросы (Requests)](#Requests)
			- [Действия (Actions)](#Actions)
			- [Задачи (Tasks)](#Tasks)
			- [Модели (Models)](#Models)
			- [Шаблоны (Views)](#Views)
			- [Преобразователи (Transformers)](#Transformers)
			- [Исключения (Exceptions)](#Exceptions)
			- [Поддействия (Sub-Actions)](#Sub-Actions)
	- [2) Дополнительные компоненты](#Optional-Components)
- [Типичная структура контейнера](#Typical-Container-Structure)
- [Атрибуты Качества Porto](#Quality-Attributes)
- [Реализации (Построены на базе Porto)](#Implementations-Projects)
- [Обратная связь и вопросы](#Feedback)










<a id="Introduction"></a>
# Вступление

**Porto** это современный архитектурный шаблон программного обеспечения, состоящий из методических рекомендаций, принципов и шаблонов, которые помогают разработчикам 
организовать свой код максимально удобным и многократно используемым способом.

Porto - отличный вариант для средних и крупных веб-проектов, поскольку со временем они, как правило, становятся более сложными. 

С помощью Porto разработчики могут создавать супер масштабируемые монолитные системы, которые при необходимости можно легко разделить на несколько микросервисов.
При этом обеспечивается возможность повторного использования бизнес-логики (функций приложения) в нескольких проектах.

**Porto** унаследовал концепции из **DDD** *(Domain Driven Design)*, **Modular**, **Micro Kernel**, **MVC** *(Model View Controller)*, **Layered** and **ADR** *(Action Domain Responder)* архитектур. 
<br>
И он придерживается списка удобных принципов дизайна, таких как **SOLID**, **OOP**, **LIFT**, **DRY**, **CoC**, **GRASP**, **Generalization**, **High Cohesion** и **Low Coupling**.

<br>

Это начиналось как экспериментальная архитектура, направленная на решение общих проблем, с которыми сталкиваются веб-разработчики при создании крупных проектов.


*Мы очень ценим отзывы и предложения.*


> "Простота является необходимым условием надежности.” — Эдсгер Дейкстра










<a id="Getting-Started"></a>
# Приступая к работе




<a id="Layers-Overview"></a>
## Обзор слоев

По своей сути Порто состоит из 2-х слоев "папок" `Контейнеры` & `Корабль`. 

Эти слои могут быть созданы в любом месте в любой выбранной структуре.

*(Пример: в Laravel или Rails они могут быть созданы в `app/` директории или в новой `src/` директории в корне.)*










### Visual Overview

![](/assets/porto_visual_diagram.png)


Прежде чем объяснить, где должен размещаться код каждого типа, давайте разберемся в различных уровнях кода, которые у нас будут:


#### Уровни кода

- **Низкоуровневый код**: код фреймворка (реализует основные операции, такие как чтение файлов с диска или взаимодействие с базой данных). Обычно живет в каталоге поставщиков (vendors).
- **Среднеуровневый код**: общий код приложения (реализует функциональность, которая обслуживает код высокого уровня. И он полагается на низкоуровневый код для работы). Должен быть в слое "Корабль".
- **Высокоуровневый код**: код бизнес-логики (инкапсулирует сложную логику и для функционирования полагается на код среднего уровня). Должен быть в слое "Контейнеры".










### Схема Слоев

Слой Контейнеров (грузовые контейнеры) >> опирается >> на слой Корабля (грузовой корабль) >> опирается >> на Фреймворк (море).

![](/assets/porto_layers.png)










### От Монолита к Микро-сервисам

Porto создан, чтобы масштабироваться вместе с вами! В то время как большинство компаний переходят от монолита к микро-сервисам *(и в последнее время без серверов)* по мере их расширения. Porto предлагает гибкость, позволяющую превратить ваш монолитный продукт в микроуслуги (или SOA) в любое время с наименьшими возможными усилиями.

В терминах Porto Монолит это эквивалент одного грузового корабля с контейнерами, в то время как Микро-сервисы это эквивалент нескольких контейнерных кораблей. *(Независимо от их размеров)*

Porto предлагает гибкость, позволяющую начинать с малого, с одной хорошо организованной монолитной службы и расти всякий раз, когда вам это нужно, путем разделения контейнеров на несколько служб по мере роста вашей команды.

Как вы можете себе представить, эксплуатация двух или более судов в море, а не одного, приведет к увеличению затрат на техническое обслуживание (два репозитория, два CI пайплайна,...), 
но также даст вам гибкость, когда каждое судно может работать с разной скоростью и направлением. 
Это технически приводит к тому, что каждая служба масштабируется по-разному в зависимости от ожидаемого трафика.

То, как сервисы взаимодействуют друг с другом, полностью зависит от разработчиков.


<br>









<a id="Ship-Layer"></a>

## 1) Слой Корабля

Слой "Корабль" содержит родительские "Базовые" классы *(классы, расширяемые каждым отдельным компонентом)* и некоторый Служебный Код.

Родительские классы «Базовые классы» слоя «Корабль» предоставляют полный контроль над Компонентами Контейнера (например, добавление функции к классу Базовой Модели делает его доступным в каждой Модели в ваших Контейнерах).

Слой «Корабль» также играет важную роль в отделении кода приложения от кода фреймворка. Это облегчает обновление Фреймворка, не затрагивая код приложения.

В Porto уровень «Корабль» очень тонкий, он НЕ содержит общих многоразовых функций, таких как Аутентификация или Авторизация, 
поскольку все эти функции предоставляются контейнерами, которые можно заменять по мере необходимости. Предоставляя разработчикам большей гибкости.











### Структура Корабля

Уровень корабля содержит следующие типы кода:

- **Код Ядра**: это двигатель корабля, который автоматически регистрирует и автоматически загружает все компоненты вашего Контейнера для загрузки вашего Приложения. Он содержит большую часть магического кода, который обрабатывает все, что не является частью вашей бизнес-логики. И в основном содержит код, который облегчает разработку за счет расширения возможностей платформы.
- **Общий код Контейнеров**
	- **Родительские классы**: базовые классы каждого компонента в вашем контейнере. (Добавление функций в родительские классы делает их доступными в каждом контейнере). Родители созданы, чтобы содержать общий код для ваших Контейнеров.
	- **Универсальные Классы**: многоразовые функции и классы, которые могут использоваться каждым Контейнером. Например, глобальные исключения, миддлвары приложения, файлы глобальной конфигурации и т.д.

Примечание: Все компоненты контейнера ДОЛЖНЫ расширяться или наследоваться от уровня «Корабль» *(в частности, от родительской папки)*.


При отделении **Ядра** в отдельный пакет Корабль-Родители должны расширять Ядро-Родителей (могут быть названы Абстрактными, поскольку большинство из них должны быть Абстрактными Классами). 
Корабль-Родители содержат общую бизнес-логику вашего пользовательского приложения, в то время как Ядро-Родители (Абстрактные) содержат общий код вашей платформы, 
в основном все, что не является бизнес-логикой, должно быть скрыто от разрабатываемого приложения.

<br>









<a id="Containers-Layer"></a>

## 2) Слой Контейнеров

Porto управляет сложностью проблемы, разбивая ее на более мелкие управляемые контейнеры.

Слой Контейнеров - это место, где живет бизнес-логика приложения *(Особенности приложения / функциональность)*. Вы будете проводить 90% своего времени на этом слое, создавая компоненты.

Вот пример того, как создаются Контейнеры:

"В приложении объекты "Задача", "Пользователь" и "Календарь" будут находиться в разных контейнерах, 
каждый из которых имеет свои собственные Маршруты (Routes), Контроллеры (Controllers), Модели (Models), Исключения (Exceptions) и т.д. 
И каждый контейнер отвечает за получение запросов и возврат ответов от любого поддерживаемого пользовательского интерфейса (Web, API..)."

Рекомендуется использовать одну Модель для каждого Контейнера, однако в некоторых случаях вам может потребоваться больше, чем одна Модель, и это совершенно нормально. 
(Даже если у вас есть одна Модель, у вас также могут быть значения (Values) "также известные как Объекты-Значения (Value Objects)" 
 (Values аналогичны моделям, но не представлены в базе данных в их собственных таблицах, а как данные в Моделях), эти объекты создаются автоматически после того как 
 их данные извлекаются из БД, такие как Цена, Местоположение, Время ...)

Просто имейте в виду, что две Модели означают два Репозитория, два Преобразователя (Transformers) и т.д. Если вы не хотите всегда использовать обе модели вместе, разделите их на 2 контейнера.

Примечание: если у вас есть сильные зависимости между двумя контейнерами по своей природе, то размещение их в одной Секции упростит их повторное использование в других проектах.









<a id="Containers-Structure"></a>
### Базовая структура Контейнеров

```
Container 1
	├── Actions
	├── Tasks
	├── Models
	└── UI
	    ├── WEB
	    │   ├── Routes
	    │   ├── Controllers
	    │   └── Views
	    ├── API
	    │   ├── Routes
	    │   ├── Controllers
	    │   └── Transformers
	    └── CLI
	        ├── Routes
	        └── Commands

Container 2
	├── Actions
	├── Tasks
	├── Models
	└── UI
	    ├── WEB
	    │   ├── Routes
	    │   ├── Controllers
	    │   └── Views
	    ├── API
	    │   ├── Routes
	    │   ├── Controllers
	    │   └── Transformers
	    └── CLI
	        ├── Routes
	        └── Commands
```











<a id="Containers-Interactions"></a>
### Взаимодействие Контейнеров и Связи

- Контейнер МОЖЕТ зависеть от одного или многих других контейнеров. *(Желательно в том же разделе.)*
- Контроллер МОЖЕТ вызывать задачи из другого контейнера.
- Модель МОЖЕТ иметь связь с моделью из другого контейнера.
- Возможны также другие формы связи, такие как Прослушивание Событий, запускаемых другими Контейнерами.

*Если вы используете обмен данными между контейнерами на основе событий, вы можете использовать тот же механизм после разделения кодовой базы на несколько служб (сервисов).* 

> Примечание. Если вы не знакомы с разделением кода на модули / домены или по какой-то причине не предпочитаете такой подход. Вы можете создать все свое приложение в одном контейнере. (Не рекомендуется, но абсолютно возможно).











<a id="Containers-Sections"></a>
### Секции Контейнеров

Контейнерные "Секции" - еще один очень важный аспект в архитектуре Porto.

*Думайте о секции как о рядах контейнеров на грузовом корабле. Хорошо организованные контейнеры в рядах, ускоряют погрузку и разгрузку связанных контейнеров для конкретного клиента.*

Чтобы избежать наличия десятков контейнеров в корне вашей папки с контейнерами, вы можете сгруппировать связанные контейнеры в Секции.

Секция - это по сути папка, содержащая связанные контейнеры. Тем не менее преимущества огромны. Думайте о Секциях как о ограниченном контексте, где каждый раздел представляет собой часть вашей системы.

Пример: если вы создаете гоночную игру, такую как Need for Speed, у вас могут быть следующие два раздела: Секция Гонки и Секция Lobby, 
где каждая секция содержит Контейнер Автомобиля и Модель Автомобиля внутри, но с разными свойствами и функциями. 
В этом примере Модель Автомобиля в Секции Гонки может содержать бизнес-логику для ускорения и управления автомобилем, 
в то время как Модель Автомобиля в Секции Lobby содержит бизнес-логику для настройки автомобиля перед гонкой.

Разделы позволяют разделить большую Модель на более мелкие. И они могут предоставить границы для различных Моделей в вашей системе.

Если вы предпочитаете простоту или у вас только одна команда, работающая над проектом, вы можете вообще не иметь Секций (где все контейнеры находятся в папке контейнеров), 
что означает, что ваш проект представляет собой одну Секцию. В этом случае, если проект быстро растет и вы решили, что вам нужно начать использовать Секции, 
вы можете создать новый проект также с одним разделом, это называется Микросервисами. В Микросервисах каждая секция «часть проекта» находится 
в своем собственном проекте (репозитории), и они могут обмениваться данными по сети, как правило, с использованием протокола HTTP. 

<br>










<a id="Components"></a>
# Компоненты

На уровне Контейнера есть набор `Компонентов` "Классы" с предопределенными обязанностями. 

Каждый отдельный фрагмент кода, который вы пишете, должен находиться в Компоненте (функции класса). Porto определяет для вас огромный список этих Компонентов с набором рекомендаций, которым следует следовать при их использовании, чтобы процесс разработки проходил гладко.

Компоненты обеспечивают согласованность и упрощают обслуживание вашего кода, поскольку вы уже знаете, где должен быть найден каждый фрагмент кода.


<a id="Components-Types"></a>
### Типы Компонентов

Каждый Контейнер состоит из нескольких компонентов, в **Porto** Компоненты разделены на два типа:
`Основные Компоненты` и `Дополнительные Компоненты`.











<a id="Main-Components"></a>
## 1) Основные Компоненты

Вы должны использовать эти компоненты, поскольку они необходимы практически для всех типов веб-приложений:

Маршруты (Routes) - Контроллеры (Controllers) - Запросы (Requests) - Действия (Actions) - Задачи (Tasks) - Модели (Models) - Представления (Views) - Преобразователи (Transformers).

> **Шаблоны:** следует использовать в случае, если приложение обслуживает HTML-страницы.
> <br>
> **Преобразователи (Transformers):** следует использовать в случае, если приложение обслуживает данные JSON или XML.











<a id="Components-Interaction-Diagram"></a>
### 1.1) Схема взаимодействия Основных Компонентов

![](/assets/porto_container_interactions.png)











<a id="Request-Life-Cycle"></a>
### 1.2) Жизненный цикл Запроса

*Базовый сценарий вызова API, навигация по основным компонентам:*

1. **Пользователь** вызывает `Endpoint` в файле `Маршрута`.
2. `Endpoint` вызывает `Посредника (Middleware)` для обработки Аутентификации.
3. `Endpoint` вызывает соответствующий `Контроллер` .
4. `Запрос` внедренный в `Контроллер` автоматически применяет валидацию и правила авторизации.
5. `Контроллер` вызывает `Действие (Action)` передает ему данные `Завпроса`.
6. `Действие (Action)` выполняет бизнес-логику, *ИЛИ может вызывать столько Задач (Tasks), сколько необходимо для повторного использования подмножеств бизнес-логики. 
7. `Задачи (Tasks)` выполняют многократно используемые подмножества бизнес-логики (`Задача (Task)` может выполнять единственную часть основного Действия (Action). 
8. `Действие (Action)` подготавливает данные для возврата в Контроллер, *некоторые данные могут быть собраны из Задач (Tasks)*. 
9. `Контроллер` создает ответ используя `Представление (View)` (или `Преобразователь (Transformer)`) и отправляет его назад **Пользователю**











<a id="Components-Details"></a>
### 1.3) Основные Компоненты, определения и принципы

> Кликайте по стрелкам ниже, чтобы узнать о каждом компоненте.











<a id="Routes"></a>
<Details>
<Summary>Маршруты (Routes)</Summary>
<br>

Маршруты являются первыми получателями HTTP-запросов.

Маршруты отвечают за сопоставление всех входящих HTTP-запросов со своим контроллером.

Файлы Маршрутов содержат конечные точки (Endpoints) (шаблоны URL - адресов, идентифицирующие входящий запрос).

Когда в ваше приложение поступает HTTP-запрос, конечные точки (Endpoints) совпадают с шаблоном URL-адреса и вызывают соответствующую функцию контроллера.


#### Принципы:
- Существует три типа маршрутов: Маршруты API, Web-Маршруты и Маршруты CLI.
- Файлы Маршрутов API ДОЛЖНЫ быть отделены от файлов Web-маршрутов, каждый в своей отдельной папке.
- Папка Web-маршрутов будет содержать только Web Endpoints (доступные веб-браузерам); А папка API-Маршрутов будет содержать только API Endpoints (доступные любому пользовательскому приложению).
- У каждого Контейнера ДОЛЖНЫ быть свои Маршруты.
- Каждый файл Маршрута ДОЛЖЕН содержать одну конечную точку (Endpoint).
- Задача конечной точки (Endpoint) состоит в том, чтобы вызвать функцию в соответствующем Контроллере после выполнения запроса любого типа. (НЕ ДОЛЖЕНО делаться ничего другого).

***

</Details>










<a id="Controllers"></a>
<Details>
<Summary>Контроллеры</Summary>
<br>

Контроллеры несут ответственность за валидацию запроса, обслуживание данных запроса и построение ответа. *Проверка и ответ происходят в отдельных классах, но запускаются контроллером*.

Концепция контроллеров такая же, как и в MVC (это C в MVC), но с ограниченными и предопределенными обязанностями.

#### Принципы:
- Контроллеры НЕ МОГУТ ничего знать о бизнес-логике или о каких-либо бизнес-объектах.
- Контроллер ДОЛЖЕН выполнять только следующие работы:
   1. Чтение данных Запроса (Request) (ввод пользователя)
   2. Вызов Действия (Action) (и передача ему данных запроса)
   3. Создание Ответа (Response) (обычно ответ создается на основе данных, собранных в результате вызова Действия (Action))
- Контроллеры НЕ МОГУТ иметь какую-либо бизнес-логику. (НУЖНО вызывать Действие (Action) для выполнения бизнес-логики).
- Контроллеры НЕ МОГУТ вызывать Задачи (Tasks) Контейнера. Они МОГУТ вызывать только Действия (Actions). (И потом Действия (Actions) могут вызывать Задачи (Tasks) Контейнера).
- Контроллеры МОГУТ быть вызваны только конечными точками (Endpoints) Маршрутов.
- Каждая папка пользовательского интерфейса (UI) Контейнера (Web, API, интерфейс командной строки (CLI)) будет иметь свои собственные контроллеры.

Вы можете спросить, зачем нам Контроллер! когда мы можем напрямую вызвать действие из маршрута. 
Слой контроллера помогает сделать действие повторно используемым в нескольких пользовательских интерфейсах (Web-интерфейс и API), 
поскольку он не создает ответа, и это уменьшает количество дублирования кода в разных пользовательских интерфейсах.
 
Вот пример ниже:

- UI (Web): Route `W-R1` -> Controller `W-C1` -> Action `A1`.
- UI (API): Route `A-R1` -> Controller `A-C1` -> Action `A1`.

Как вы можете видеть в приведенном выше примере, действие A1 использовалось обоими маршрутами W-R1 и A-R1 с помощью слоя Контроллера, который находится в каждом пользовательском интерфейсе.

***

</Details>










<a id="Requests"></a>
<Details>
<Summary>Запросы (Requests)</Summary>
<br>

Запросы в основном служат для ввода данных пользователем в приложение. И они очень полезны для автоматического применения правил проверки и авторизации.

Запросы - лучшее место для применения валидации, поскольку правила валидации будут связаны с каждым запросом. 
Запросы также могут проверять Авторизацию, например проверить, есть ли у этого пользователя доступ к этой функции контроллера. 
*(Пример: проверить, владеет ли конкретный пользователь продуктом перед его удалением, или проверить, является ли этот пользователь администратором, чтобы что-то редактировать).*

#### Принципы:
- Запрос МОЖЕТ содержать правила Валидации / Авторизации.
- Запросы ДОЛЖНЫ внедряться только в Контроллеры. После внедрения они автоматически проверяют, соответствуют ли данные запроса правилам проверки, и если ввод запроса не валидный, будет выброшено Исключение.
- Запросы МОГУТ также использоваться для авторизации, они могут проверять, авторизован ли пользователь для выполнения запроса.

***

</Details>










<a id="Actions"></a>
<Details>
<Summary>Действия (Actions)</Summary>
<br>

Действия (Actions) представляют собой сценарии использования (Use Cases) Приложения *(действия, которые могут быть предприняты Пользователем или Программным обеспечением в Приложении).*

Действия МОГУТ содержать бизнес-логику или / и организуют Задачи (Tasks) для выполнения бизнес-логики.

Действия (Actions) принимают структуры данных в качестве входных данных, манипулируют ими в соответствии с бизнес-правилами внутри компании или с помощью некоторых Задач (Tasks), а затем выводят новые структуры данных.

Действия (Actions) НЕ ДОЛЖНЫ заботиться о том, как собираются Данные или как они будут представлены.

Просто взглянув на папку «Действия» (Actions) Контейнера, вы можете определить, какие сценарии использования (функции) предоставляет ваш Контейнер. 
И глядя на все Действия (Actions), вы можете сказать, что может делать Приложение.

#### Принципы:
- Каждое Действие (Action) ДОЛЖНО отвечать за выполнение одного варианта использования в Приложении.
- Действие МОЖЕТ извлекать данные из Задач (Tasks) и передавать данные другой Задаче (Task).
- Действие МОЖЕТ вызывать несколько Задач (Tasks). (Они даже могут вызывать Задачи (Tasks) из других Контейнеров!).
- Действия МОГУТ возвращать данные Контроллеру.
- Действия НЕ ДОЛЖНЫ возвращать ответ. (Задача Контроллера - вернуть ответ).
- Действие НЕ ДОЛЖНО вызывать другое Действие (если вам нужно повторно использовать большой кусок бизнес-логики из нескольких Действий, 
и этот фрагмент вызывает несколько Задач, вы можете создать ПодДействие (SubAction). См. Раздел SubAction ниже.
- Действия в основном используются из Контроллеров. Однако их можно использовать из Слушивателей событий (Listeners), Команд и / или других Классов. Но их НЕ СЛЕДУЕТ использовать из Задач.
- Каждое действие ДОЛЖНО иметь только одну функцию с именем run ().
- Основная функция Действия `run ()` может принимать объект Запроса в параметре. (что странно, прим. перев.)
- Действия (Actions) несут ответственность за обработку всех ожидаемых Исключений (Exceptions).

***

</Details>










<a id="Tasks"></a>
<Details>
<Summary>Задачи (Tasks)</Summary>
<br>

Задачи (Tasks) - это классы, которые содержат общую бизнес-логику для нескольких Действий (Actions) в разных Контейнерах.

Каждая Задача (Task) отвечает за небольшую часть логики.

Задачи (Tasks) не являются обязательными, но в большинстве случаев они вам нужны.

Пример: если у вас есть Действие (Action) 1, которому нужно найти запись по ее идентификатору в БД, потом запускается Событие. 
И у вас есть Действие 2, которому необходимо найти ту же запись по ее идентификатору, а затем выполнить вызов внешнего API. 
Поскольку оба Действия выполняют логику «найти запись по идентификатору», мы можем взять эту бизнес-логику и поместить ее в собственный класс, этот класс является Задачей (Task). 
Эту Задачу (Task) теперь можно использовать в обоих Действиях (Actions), а также и в любых других Действиях, которые вы можете создать в будущем.

Правило состоит в том, что всякий раз, когда вы видите возможность повторного использования фрагмента кода из действия, 
вы должны поместить этот фрагмент кода в Задачу (Task). Не создавайте Задачи вслепую для всего, вы всегда можете начать с написания всей бизнес-логики в Действии (Action), 
и только когда вам нужно повторно использовать ее, создайте для нее специальную Задачу. (Рефакторинг необходим для адаптации к росту кода).

#### Принципы:
- Каждая Задача (Task) ДОЛЖНА иметь единственную ответственность (работу).
- Задача (Task) МОЖЕТ получать и возвращать данные. (Задача НЕ ДОЛЖНА возвращать ответ (response), задача Контроллера - вернуть ответ).
- Задача НЕ ДОЛЖНА вызывать другую задачу. Потому что это вернет нас к архитектуре Служб (Services Architecture), а это большой беспорядок.
- Задача НЕ ДОЛЖНА вызывать Действие (Action). Потому что тогда ваш код не будет иметь никакого логического смысла!
- Задачи (Tasks) ДОЛЖНЫ вызываться только из Действий (Actions). (Они также могут было вызваны из Действий других Контейнеров!).
- Задачи (Tasks) обычно имеют одну функцию `run ()`. Однако при необходимости у них может быть больше функций с явными именами. 
*Заставить класс Task заменить уродливую концепцию флагов функций.* 
Пример: `FindUserTask` может иметь 2 функции `byId` и `byEmail`, **все внутренние функции ДОЛЖНЫ вызывать функцию `run`.** 
В этом примере `run` может быть вызван в конце обеих функций после добавления Критериев в репозиторий.
- Задача НЕ МОЖЕТ вызываться из Контроллера. Потому что это приводит к появлению недокументированных функций в вашем коде. 
Совершенно нормально иметь много Действий (Actions), "например: `FindUserByIdAction` и `FindUserByEmailAction`, где оба действия вызывают одну и ту же задачу", 
а также совершенно нормально иметь одно действие `FindUserAction`, принимающее решение, какая Задача (Task) должна быть вызывана.
- Задача (Task) НЕ ДОЛЖНА принимать объект Запроса (Request) ни в одной из своих функций. Она может принимать любые параметры функций, но не объект Request. 
Она (Задача) может свободно использоваться везде, и её можно будет протестировать независимо.

***

</Details>










<a id="Models"></a>
<Details>
<Summary>Модели (Models)</Summary>
<br>

Модели (Models) обеспечивают абстракцию данных, они представляют данные в базе данных. (Это M в MVC).

Модели несут ответственность за то, как следует обрабатывать данные. Они следят за тем, чтобы данные поступали правильно в внутреннее хранилище (например, в базу данных).

#### Принципы:
- Модель НЕ ДОЛЖНА содержать бизнес-логику, она может содержать только код и данные, которые представляют себя. 
*(это отношения с другими Моделями, скрытые поля, имя таблицы, заполняемые атрибуты, ...)*
- Один Контейнер МОЖЕТ содержать несколько моделей.
- Модель МОЖЕТ определять Отношения между собой и любыми другими Моделями (в случае, если связь существует).

***

</Details>










<a id="Views"></a>
<Details>
<Summary>Представления (Views)</Summary>
<br>

Представления (Views) содержат HTML-код, обслуживаемый вашим приложением.

Их основная цель - отделить логику приложения от логики представления. (Это V в MVC).

#### Принципы:
- Представления можно использовать только из Web Контроллеров (Web Controllers).
- Представления ДОЛЖНЫ быть разделены на несколько файлов и папок в зависимости от того, что они отображают.
- Один Контейнер МОЖЕТ содержать несколько файлов Представлений.

***

</Details>










<a id="Transformers"></a>
<Details>
<Summary>Преобразователи (Transformers)</Summary>
<br>

Преобразователи (Transformers) (это сокращенное название от Responses Transformers).

Они эквивалентны Представлениям, но для ответов JSON. В то время как Views принимает данные и представляет их в HTML, Transformers принимает данные и представляет их в JSON.

Преобразователи (Transformers) - это классы, отвечающие за преобразование Моделей в массивы.

Преобразователи (Transformers) берут модель или группу моделей «коллекцию» и преобразует ее в форматированный сериализуемый массив.

#### Принципы:
- Все ответы API ДОЛЖНЫ быть отформатированы с помощью Преобразователей.
- Каждая Модель (которая возвращается вызовом API) ДОЛЖНА иметь Преобразователь.
- В одном Контейнере МОЖЕТ быть несколько Преобразователей.
- Обычно у каждой Модели есть Преобразователь.

***

</Details>










<a id="Exceptions"></a>
<Details>
<Summary>Исключения (Exceptions)</Summary>
<br>

Исключения (Exceptions) - это также форма вывода, которую следует ожидать (например, исключение API) и которая должна быть четко определена.

#### Принципы:
- Существуют Исключения Контейнеров (находятся в Контейнерах) и Общие Исключения (находятся в Корабле).
- Задачи (Tasks), Подзадачи (Sub-Tasks), Модели и любой класс в целом могут вызывать очень конкретное Исключение.
- Вызывающий ДОЛЖЕН обрабатывать все ожидаемые Исключения от вызываемого класса.
- Действия (Actions) ДОЛЖНЫ обрабатывать все Исключения и следить за тем, чтобы они не просачивались в Компоненты верхнего уровня и не вызывали неожиданное поведение.
- Имена исключений ДОЛЖНЫ быть как можно более конкретными и ДОЛЖНЫ иметь четкие описательные сообщения.

***

</Details>










<a id="Sub-Actions"></a>
<Details>
<Summary>Sub-Actions</Summary>
<br>

SubActions are designed to eliminate code duplication in Actions. Don't get confused! SubActions do not replace Tasks.

While Tasks allows Actions to share a piece of functionality. SubActions allows Actions to share a sequence of Tasks.

The SubActions are created to solve a problem. The problem is:
Sometimes you need to reuse a big chunk of business logic in multiple Actions. That chunk of code is already calling some Tasks. *(Remember a Task SHOULD NOT call other Tasks)* so how shall you reuse that chunk of code without creating a Task! The solution is create a SubAction.

Detailed Example: assuming an Action `A1` is calling Task1, Task2 and Task3. And another Action `A2` is calling Task2, Task3, Task4 and Task5. Notice both Actions are calling Tasks 2 and 3. To eliminate code duplication we can create a SubAction that contains all the common code between both Actions.

#### Principles:
- Sub-Actions MUST call Tasks. If a Sub-Actions is doing all the business logic, without the help of at least 1 Tasks, it probably shouldn't be a Sub-Action but a Task instead.
- A Sub-Action MAY retrieves data from Tasks and pass data to another Task.
- A Sub-Action MAY call multiple Tasks. (They can even call Tasks from other Containers as well!).
- Sub-Actions MAY return data to the Action.
- Sub-Action SHOULD NOT return a response. (the Controller job is to return a response).
- Sub-Action SHOULD NOT call another Sub-Action. (try to avoid that as much as possible).
- Sub-Action SHOULD be used from Actions. However, they can be used from Events, Commands and/or other Classes. But they SHOULD NOT be used from Controllers or Tasks.
- Every Sub-Action SHOULD have only a single function named `run()`.

***

</Details>


<br>










<a id="Optional-Components"></a>
## 2) Optional Components

You can add these Components when you need them, based on your App needs, however some of them are highly recommended:

Tests - Events - Listeners - Commands - Migrations - Seeders - Factories - Middlewares - Repositories - Criteria - Policies - Service Providers - Contracts - Traits - Jobs - Values - Transporters - Mails - Notifications...


<br>









<a id="Typical-Container-Structure"></a>
## Typical Container Structure

> A Container with a list of Main and Optional Components.

```
Container
	├── Actions
	├── Tasks
	├── Models
	├── Values
	├── Events
	├── Listeners
	├── Policies
	├── Exceptions
	├── Contracts
	├── Traits
	├── Jobs
	├── Notifications
	├── Providers
	├── Configs
	├── Mails
	│   ├── Templates	
	├── Data
	│   ├── Migrations
	│   ├── Seeders
	│   ├── Factories
	│   ├── Criteria
	│   ├── Repositories
	│   ├── Validators
	│   ├── Transporters
	│   └── Rules
	├── Tests
	│   ├── Unit
	│   └── Traits
	└── UI
	    ├── API
	    │   ├── Routes
	    │   ├── Controllers
	    │   ├── Requests
	    │   ├── Transformers
	    │   └── Tests
	    │       └── Functional
	    ├── WEB
	    │   ├── Routes
	    │   ├── Controllers
	    │   ├── Requests
	    │   ├── Views
	    │   └── Tests
	    │       └── Acceptance
	    └── CLI
	        ├── Routes
	        ├── Commands
	        └── Tests
	            └── Functional
```










<a id="Quality-Attributes"></a>
## Porto Quality Attributes

> The benefits of using Porto.










<Details>
<Summary>Modularity & Reusability</Summary>
<br>

In Porto, your application business logic lives in Containers. Porto Containers are similar in nature to the Modules *(from the Modular architecture)* and Domains *(from the DDD architecture)*.

Containers can depend on other Containers, similar to how a layer can depend on other layers in a layered architecture.

Porto's rules and guidelines minimizes and defines the dependecies directions between Containers, to avoid circular references between them.

And it allows the grouping of related Containers into sections, in order to reuse them together in different projects. *(each Section contains a reusable portion of your application business logic).*

In terms of dependency management, the developer is free to move each Container to its own repository or keep all Containers together under single repository.

***

</Details>










<Details>
<Summary>Maintainability & Scalability</Summary>
<br>

Porto aim to reduce maintance cost by saving developers time.
It's structured in a way to insure code decoupling, and forces consistency which all contribute to its maintainability.

Having a single function per class to describe a functionality, makes adding and removing features an easy process.

Porto has a very organized code base and a zero code decoupling. In addition to clear development workflow with predefined data flow and dependencies directions. That all contributes to its scalability.

***

</Details>










<Details>
<Summary>Testability & Debuggability</Summary>
<br>

Extremely adhering to the single responsibility principle by having single function per class, results in having super slim classes, which leads to easier testability.

In Porto each component expect the same type of input and output, which makes testing, mocking and stabbing very simple.

The Porto structure itself makes writing automated tests a smooth process. As it has a `tests` folder at the root of each Container for contaning the unit tests of your Tasks.
And a `tests` folder in each UI folder for contaning the functional tests (for testing each UI's separately).

The secret of making the testing and debugging easy, is not only in the organization of the tests and pre defined responsiblity of the components but also in the decoupling of your code.

***

</Details>










<Details>
<Summary>Adaptability & Evolvability</Summary>
<br>

With Porto you can easily accommodate future changes with the least amount of efforts.

Let's assume you have a web app that serves HTML and recently you decided that you need to have a Mobile app, hence you need an API.

Porto has pluggable UI's (WEB, API & CLI) and this allows writting the business logic of your application first, then implementing a UI to interact with your code. 

This gives the flexibility to adding interfaces whenever needed and adapting to future changes, with the least effort possible. 

it is all possible because the Actions are the central organizing principle "not the controller" which are shared across multiple UI's.
And the UI's are separated from the application business logic and separated from each others within each Container.

***

</Details>










<Details>
<Summary>Usability & Learnability</Summary>
<br>

Porto makes it super easy to locate any feature/functionality. And to understand what's happening inside it.

That due to the usage of the domain expert language when naming the classes "components". As well as the single function per class golden rule. Which allows you to find any Use Case (`Action`) in your code by just browsing the files.

Porto promises that you can find any feature implementation in less than 3 seconds! (example: if you are looking for where the user address is being validated - just go to the Address Container, open the list of Actions and search for ValidateUserAddressAction).

***

</Details>










<Details>
<Summary>Extensibility & Flexibility</Summary>
<br>

Porto's takes future growth into consideration and it ensures your code remains maintainable no matter what the project size becomes. 

It achieves this by its modular structure, separation of concerns and the organized coupling between the internal classes "Components".

This allows modifications to be made without undesirable side effects.

***

</Details>










<Details>
<Summary>Agility & Upgradability</Summary>
<br>

Porto gives the ability to move quickly and easily.

It's easy to make framework upgrades due to the complete separation between the App and the framework code through the Ship layer.

***

</Details>


<br>










<a id="Implementations-Projects"></a>

# Implementations

> Feel free to list your implementation here.

List of projects implementing the Porto architecture.

- **PHP**
	- **Laravel** 
		- [**Apiato**](http://apiato.io/) **(By the Porto creator)** A PHP Framework for building scalable API's on top of Laravel.
		- [**Laravel Large Project**](https://github.com/stasyanko/laravel-large-project) An example project to show how to build large projects with Porto.
	- **Zend Expressive**
		- [**Expressive Porto**](https://github.com/lpj145/expressive-porto) An implementation of the Porto architecture with Zend Expressive.
- **JavaScript**
- **Python**
- **Ruby**
- **Java**
- **C#**
- ...











<a id="Feedback"></a>
# Get in Touch

> Your feedback is important.

For feedbacks, questions, or suggestions? We are on [**Slack**](https://slackin-mezlsumyvc.now.sh/).

[![](https://s19.postimg.cc/h7pvzy9ar/Slack-i_OS-icon.png)](https://slackin-mezlsumyvc.now.sh/)











<a id="Author"></a>
## Author

<table>
  <tbody>
     <tr>
        <td align="center" valign="top">
            <img width="125" height="125" src="https://github.com/mahmoudz.png?s=150">
            <br>
            <strong>Mahmoud Zalt</strong>
            <br>
            Twitter: <a href="https://github.com/Mahmoudz">@mahmoudz</a>
            <br>
            Site: <a href="http://zalt.me">zalt.me</a>
        </td>
     </tr>
  </tbody>
</table>











<a id="Donations"></a>
## Donations

Become a [Github Sponsor](https://github.com/sponsors/Mahmoudz).
<br>
Direct donation via [Paypal](https://paypal.me/mzmmzz).
<br>
Become a [Patreon](https://www.patreon.com/zalt).










<a name="License"></a>
## License

[MIT](https://opensource.org/licenses/MIT) © Mahmoud Zalt
