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











### Ship Structure

The Ship layer, contains the following types of codes:

- **The Core Code**: is the engine of the ship, that auto-register and auto-load all your Container's Components to boot your Application. It contains most of the magical code that handles everything that is not part of your business logic. And mostly contains code that facilitate the development by extending the framework features.
- **The Containers shared code**
	- **Parents Classes**: the base classes of each Component in your Container. (Adding functions to the parent classes makes them available in every Container). Parents are made to contain shared code between your Containers.
	- **Generic Classes**: the reusable features and classes which can be used by every Container. Such as, Global Exceptions, Application Middleware's, Global Config files, etc.

Note: All the Container's Components MUST extend or inherit from the Ship layer *(in particular the Parent's folder)*.

When separating the **Core** to an external package, the Ship Parents should extend from the Core Parents (can be named Abstract, since most of the them supposed to be Abstract Classes).
The Ship Parents holds your custom Application shared business logic, while the Core Parents (Abstracts) holds your framework common code, basically anything that is not business logic should be hidden from the actual Application being developed.


<br>









<a id="Containers-Layer"></a>

## 2) Containers Layer

Porto manages the complexity of a problem by breaking it down to smaller manageable Containers.

The Containers layer is where the Application specific business logic lives *(Application features/functionalities)*. You will spend 90% of your time at this layer, creating Components.

Here's an example of how Containers are generated:

"In a TODO App, the 'Task', 'User' and 'Calendar' objects each would live in a different Container, were each has its own Routes, Controllers, Models, Exceptions, etc. And each Container is responsible for receiving requests and returning responses from whichever supported UI (Web, API..)."

It's advised to use a Single Model per Container, however in some cases you may need more than a single Model and that's totally fine. 
(Even if you have a single Model you could also have Values "AKA Value Objects" (Values are similar to Models but that do not get represented in the DB on their own tables but as data on the Models) these objects get built automatically after their data is fetched from the DB such as Price, Location, Time...)

Just keep in mind two Models means two Repositories, two Transformers, etc.
Unless you want to use both Models always together, do split them into 2 Containers.

Note: if you have high dependecies between two containers by nature, than placing them in the same Section would make reusing them easier in other projects.










<a id="Containers-Structure"></a>
### Basic Containers Structure

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
### Containers Communications & Relations

- A Container MAY depends on one or many other Containers. *(Preferably wihin the same section.)*
- A Controller MAY call Tasks from another Container.
- A Model MAY have a relationship with a Model from another Containers.
- Other forms of communications are also possible, such as Listening to Events fired by other Containers.

*If you use Event based communcations between containers, you could use the same mechanism after spliting your code base into multi services.*

> Note: If you're not familiar with separating your code into Modules/Domains, or for some reason you don't prefer that approach. You can create your entire Application in a single Container. (Not recommended but absolutely possible).











<a id="Containers-Sections"></a>
### Containers Sections

Container `Sections` are another very important aspect in the Porto architecture.

*Think of a Section as a rows of containers on a cargo ship. Well organized containers in rows, speeds up the loading and unloading of related containers for a specific customer.*

To avoid having tens of containers on the root of your containers folder, you can group related Containers into Sections.

The basic definition of a Section is a folder that contains related Containers. However the benifits are huge. Think of Sections as bounded context, where each section represents a portion of your system. 

Example: if you're building a racing game like Need for Speed, you may have the following two sections: the Race Section and the Lobby Section, where each section contains a Car Container and a Car Model inside it, but with different properties and functions. 
In this example the Car Model of the Race section can contain the business logic for accelerating and controlling the car, while the Car Model of the Lobby Section contains the business logic for customizing the car before the race.

Sections allows separating large Model into smaller ones. And they can provide boundaries for different Models in your system.

If you prefer simplicity or you have only single team working on the project, you can have no Sections at all (where all Containers live in the containers folder) which means your project is a single section. In this case if the project grew quickly and you decided you need to start using sections, you can make a new project also with a single section, this is known as Micro-Services. In Micro-Services each section "project portion" live in its own project (repository) and they can communicate over the network usually using the HTTP protocol.

<br>










<a id="Components"></a>
# Components

In the Container layer there's a set of `Components` "Classes" with predefined responsibilities. 

Every single piece of code you write should live in a Component (class function). Porto defines a huge list of those Components for you, with a set guidelines to follow when using them, to keep the development process smooth.

Components ensures consistency and make your code easier to maintain as you already know where each piece of code should be found.


<a id="Components-Types"></a>
### Components Types

Every Container consists of a number of Components, in **Porto** the Components are split into two Types:
`Main Components` and `Optional Components`.











<a id="Main-Components"></a>
## 1) Main Components

You must use these Components as they are essential for almost all types of Web Apps:

Routes - Controllers - Requests - Actions - Tasks - Models - Views - Transformers.

> **Views:** should be used in case the App serves HTML pages.
> <br>
> **Transformers:** should be used in case the App serves JSON or XML data.











<a id="Components-Interaction-Diagram"></a>
### 1.1) Main Components Interaction Diagram

![](/assets/porto_container_interactions.png)











<a id="Request-Life-Cycle"></a>
### 1.2) The Request Life Cycle

*A basic API call scenario, navigating through the main components:*

1. **User** calls an `Endpoint` in a `Route` file.
2. `Endpoint` calls a `Middleware` to handle the Authentication.
3. `Endpoint` calls its `Controller` function.
4. `Request` injected in the `Controller` automatically applies the request validation & authorization rules.
5. `Controller` calls an `Action` and pass each `Request` data to it.
6. `Action` do the business logic, *OR can call as many `Tasks` as needed to do the reusable subsets of the business logic*.
7. `Tasks` do a reusable subsets of the business logic (A `Task` can do a single portion of the main Action).
8. `Action` prepares data to be returned to the `Controller`, *some data can be collected from the `Tasks`*.
9. `Controller` builds the response using a `View` (or `Transformer`) and send it back to the **User**.










<a id="Components-Details"></a>
### 1.3) Main Components Definitions & Principles

> Click on the arrows below to read about each component.











<a id="Routes"></a>
<Details>
<Summary>Routes</Summary>
<br>

Routes are the first receivers of the HTTP requests.

The Routes are responsible for mapping all the incoming HTTP requests to their controller's functions.

The Routes files contain Endpoints (URL patterns that identify the incoming request).

When an HTTP request hits your Application, the Endpoints match with the URL pattern and make the call to the corresponding Controller function.

#### Principles:
- There are three types of Routes, API Routes, Web Routes and CLI Routes.
- The API Routes files SHOULD be separated from the Web Routes files, each in its own folder.
- The Web Routes folder will contain only the Web Endpoints, (accessible by Web browsers); And the API Routes folder will contain only the API Endpoints, (accessible by any consumer App).
- Every Container SHOULD have its own Routes.
- Every Route file SHOULD contain a single Endpoint.
- The Endpoint job is to call a function on the corresponding Controller once a request of any type is made. (It SHOULD NOT do anything else).

***

</Details>










<a id="Controllers"></a>
<Details>
<Summary>Controllers</Summary>
<br>

Controllers are responsible for validating the request, serving the request data and building a response. *Validation and response, happens in separate classes, but triggered from the Controller*.

The Controllers concept is the same as in MVC *(They are the C in MVC)*, but with limited and predefined responsibilities.

#### Principles:
- Controllers SHOULD NOT know anything about the business logic or about any business object.
- A Controller SHOULD only do the following jobs:
   1. Reading a Request data (user input)
   2. Calling an Action (and passing request data to it)
   3. Building a Response (usually build response based on the data collected from the Action call)
- Controllers SHOULD NOT have any form of business logic. (It SHOULD call an Action to perform the business logic).
- Controllers SHOULD NOT call Container Tasks. They MAY only call Actions. (And then Actions can call Container Tasks).
- Controllers CAN be called by Routes Endpoints only.
- Every Container UI folder (Web, API, CLI) will have its own Controllers.

You may wonder why we need the Controller! when we can directly call the Action from the Route. The Controller layer helps making the Action reusable in multiple UI's (Web & API), since it doesn't build a response, and that reduces the amount of code duplication across different UI's.

Here's an example below:

- UI (Web): Route `W-R1` -> Controller `W-C1` -> Action `A1`.
- UI (API): Route `A-R1` -> Controller `A-C1` -> Action `A1`.

As you can see in the example above the Action `A1` was used by both routes `W-R1` and `A-R1`, with the help of the Controllers layer that lives in each UI.

***

</Details>










<a id="Requests"></a>
<Details>
<Summary>Requests</Summary>
<br>

Requests mainly serve the user input in the application. And they are very useful to automatically apply the Validation and Authorization rules.

Requests are the best place to apply validations, since the validations rules will be related to every request.
Requests can also check the Authorization, e.g. check if this user has access to this controller function.
*(Example: check if a specific user owns a product before deleting it, or check if this user is an admin to edit something).*

#### Principles:
- A Request MAY hold the Validation / Authorization rules.
- Requests SHOULD only be injected in Controllers. Once injected they automatically check if the request data matches the validation rules, and if the request input is not valid an Exception will be thrown.
- Requests MAY also be used for authorization, they can check if the user is authorized to make a request.

***

</Details>










<a id="Actions"></a>
<Details>
<Summary>Actions</Summary>
<br>

Actions represent the Use Cases of the Application *(the actions that can be taken by a User or a Software in the Application)*.

Actions CAN hold business logic or/and they orchestrate the Tasks to perform the business logic.

Actions take data structures as inputs, manipulates them according to the business rules internally or through some Tasks, then output a new data structures.

Actions SHOULD NOT care how the Data is gathered, or how it will be represented.

By just looking at the Actions folder of a Container, you can determine what Use Cases (features) your Container provides.
And by looking at all the Actions you can tell what an Application can do.

#### Principles:
- Every Action SHOULD be responsible for doing a single Use Case in the Application.
- An Action MAY retrieves data from Tasks and pass data to another Task.
- An Action MAY call multiple Tasks. (They can even call Tasks from other Containers as well!).
- Actions MAY return data to the Controller.
- Actions SHOULD NOT return a response. (The Controller's job is to return a response).
- An Action SHOULD NOT call another Action (If you need to reuse a big chunk of business logic in multiple Actions, and this chunk is calling some Tasks, you can create a SubAction). See the SubAction section below.
- Actions are mainly used from Controllers. However, they can be used from Events Listeners, Commands and/or other Classes. But they SHOULD NOT be used from Tasks.
- Every Action SHOULD have only a single function named `run()`.
- The Action main function `run()` can accept a Request Object in the parameter.
- Actions are responsible of handling all expected Exceptions.

***

</Details>










<a id="Tasks"></a>
<Details>
<Summary>Tasks</Summary>
<br>

The Tasks are the classes that hold the shared business logic between multiple Actions accross different Containers.

Every Task is responsible for a small part of the logic.

Tasks are optional, but in most cases you find yourself in need for them.

Example: if you have Action 1 that needs to find a record by its ID from the DB, then fires an Event.
And you have an Action 2 that needs to find the same record by its ID, then makes a call to an external API.
Since both actions are performing the "find a record by ID" logic, we can take that business logic and put it in it's own class, that class is the Task. This Task is now reusable by both Actions and any other Action you might create in the future.

The rule is, whenever you see the possibility of reusing a piece of code from an Action, you should put that piece of code in a Task. Do not blindly create Tasks for everything, you can always start with writing all the business logic in an Action and only when you need to reuse it, create an a dedicated Task for it. (Refactoring is essential to adapt to the code growth).

#### Principles:
- Every Task SHOULD have a single responsibility (job).
- A Task MAY receive and return Data. (Task SHOULD NOT return a response, the Controller's job is to return a response).
- A Task SHOULD NOT call another Task. Because that will takes us back to the Services Architecture and it's a big mess.
- A Task SHOULD NOT call an Action. Because your code wouldn't make any logical sense then!
- Tasks SHOULD only be called from Actions. (They could be called from Actions of other Containers as well!).
- Tasks usually have a single function `run()`. However, they can have more functions with explicit names if needed. *Making the Task class replace the ugly concept of function flags.* Example: the `FindUserTask` can have 2 functions `byId` and `byEmail`, **all internal functions MUST call the `run` function**. In this example the `run` can be called at the end of both funtions, after appending Criteria to the repository.
- A Task SHOULD NOT be called from Controller. Because this leads to non-documented features in your code. It's totally fine to have a lot of Actions "example: `FindUserByIdAction` and `FindUserByEmailAction` where both Actions are calling the same Task" as well as it's totally fine to have single Action `FindUserAction` making a decision to which Task it should call.
- A Task SHOULD NOT accept a Request object in any of its functions. It can take anything in its funtions parameters but never a Request object. This will keep free to use from anwyhere, and can be tested independently.

***

</Details>










<a id="Models"></a>
<Details>
<Summary>Models</Summary>
<br>

The Models provide an abstraction for the data, they represent the data in the database. *(They are the M in MVC)*.

Models are responsible for how the data should be handled. They make sure that data arrives properly into the backend store (e.g. Database).

#### Principles:
- A Model SHOULD NOT hold business logic, it can only hold the code and data that represents itself. *(it's relationships with other models, hidden fields, table name, fillable attributes,...)*
- A single Container MAY contain multiple Models.
- A Model MAY define the Relations between itself and any other Models (in case a relation exist).

***

</Details>










<a id="Views"></a>
<Details>
<Summary>Views</Summary>
<br>

Views contain the HTML served by your application.

Their main goal is to separate the application logic from the presentation logic. *(They are the V in MVC)*.

#### Principles:
- Views can only be used from the Web Controllers.
- Views SHOULD be separated into multiple files and folders based on what they display.
- A single Container MAY contain multiple Views files.

***

</Details>










<a id="Transformers"></a>
<Details>
<Summary>Transformers</Summary>
<br>

Transformers (are the short name for Responses Transformers).

They are equivalent to Views but for JSON Responses. While Views takes data and represent it in HTML, Transformers takes data and represent it in JSON.

Transformers are classes responsible for transforming Models into Arrays.

Transformers takes a Model or a group of Models "Collection" and converts it to a formatted serializable Array.

#### Principles:
- All API responses MUST be formatted via Transformers.
- Every Model (that gets returned by an API call) SHOULD have a Transformer.
- A single Container MAY have multiple Transformers.
- Usually every Model would have a Transformer.

***

</Details>










<a id="Exceptions"></a>
<Details>
<Summary>Exceptions</Summary>
<br>

Exceptions are also a form of output that should be expected (like an API exception) and well defined.

#### Principles:
- There are container Exceptions (live in Containers) and general Exceptions (live in Ship).
- Tasks, Sub-Tasks, Models and any class in general can throw a very specific Exception.
- The caller MUST handle all expected Exceptions from the called class.
- Actions MUST handle all Exceptions, and making sure they don't leak to upper Components, and cause unexpected behaviors.
- Exceptions names SHOULD be as specific as possible and they SHOULD have a clear descriptive messages.

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
