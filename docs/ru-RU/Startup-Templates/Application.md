# Стартовый iаблон приложения

## Вступление 


Этот шаблон обеспечивает многоуровневую структуру приложения, основанную на практике [Domain Driven Design](../Domain-Driven-Design.md) (DDD).

В этом документе подробно объясняется **структура решения** и проекты. Если вы хотите начать быстро, следуйте приведенным ниже инструкциям: 

* [The getting started document](../Getting-Started-With-Startup-Templates.md) объясняет, как создать новое приложение за несколько минут. 
* [The application development tutorial](../Tutorials/Part-1) пошаговая разработка приложения. 

## С чего начать? 

Вы можете использовать [ABP CLI](../CLI.md) для создания нового проекта с использованием этого стартового шаблона. Или вы можете напрямую создать и загрузить шаблон на странице [Начало работы](https://abp.io/get-started). Далее используется подход CLI. 

Сначала установите ABP CLI, если вы еще не устанавливали его: 

````bash
dotnet tool install -g Volo.Abp.Cli
````
Затем используйте команду `abp new` в пустой папке, чтобы создать новое решение: 

````bash
abp new Acme.BookStore -t app
````

* `Acme.BookStore` это имя решения, например *YourCompany.YourProduct*. Вы можете использовать одноуровневое, двухуровневое или трехуровневое именование. 
* В этом примере указано имя шаблона (`-t` or `--template` option). Однако, `app`  уже является шаблоном по умолчанию, если вы его не укажете. 

### Выберете фреймворк пользовательского интерфейса

Этот шаблон предоставляет несколько фреймворков пользовательского интерфейса: 

* `mvc`: Пользовательский интерфейс ASP.NET Core MVC с Razor Pages (по умолчанию) 
* `blazor`: Blazor UI
* `angular`: Angular UI

Используйте параметр -u или --ui, чтобы указать конкретный фреймворк пользовательского интерфейса: 

````bash
abp new Acme.BookStore -u angular
````

### Укажите провайдера базы данных

Поддерживаемые типы провайдеров базы данных:

- `ef`: Entity Framework Core (по умолчанию)
- `mongodb`: MongoDB

Используйте опцию `-d` (или `--database-provider`) для указания конкретного типа провайдера базы данных:

````bash
abp new Acme.BookStore -d mongodb
````

### Укажите платформу мобильного приложения 

Этот шаблон поддерживает следующие платформы мобильных приложений:

- `react-native`: React Native

Используйте опцию `-m` (or `--mobile`) для указания конкретной платформы мобильного приложения:

````bash
abp new Acme.BookStore -m react-native
````

Если опция не указана, мобильное приложение создано не будет.

## Структура решения

В зависимости от указанных вами вариантов вы можете получить различную структуру решения.

### Структура по умолчанию

Если вы не укажете никаких дополнительных опций, у вас будет решение, подобное показанному ниже:

![bookstore-visual-studio-solution-v3](../images/bookstore-visual-studio-solution-v3.png)

Проекты организованы в папки `src` и `test`. Папка `src` содержит фактическое приложение, которое разбито на уровни на основе принципов [DDD](../Domain-Driven-Design.md), как упоминалось ранее.

На схеме ниже показаны уровни и зависимости приложения от проекта:

![layered-project-dependencies](../images/layered-project-dependencies.png)

Каждый раздел ниже объясняет связанный проект и его зависимости.

#### Проект .Domain.Shared

Этот проект содержит константы, перечисления и другие объекты, которые на самом деле являются частью уровня домена, но должны использоваться всеми слоями/проектами в решении.

Перечисление `BookType` и класс `BookConsts` (которые могут иметь некоторые постоянные поля для сущности `Book`, например `MaxNameLength`) являются хорошими кандидатами для этого проекта.

* Этот проект не зависит от других проектов в решении. Все остальные проекты прямо или косвенно зависят от этого.

#### Проект .Domain

Это доменный слой решения. В основном он содержит [entities, aggregate roots](../Entities.md), [службы домена] (../ Domain-Services.md), [domain services](../Domain-Services.md), [value objects](../Value-Objects.md), [repository interfaces](../Repositories.md) и другие объекты домена.

Сущность `Book`, доменная служба `BookManager` и интерфейс `IBookRepository` - хорошие элементы для этого проекта.

* Зависит от `.Domain.Shared`, потому что он использует константы, перечисления и другие объекты, определенные в этом проекте.

#### Проект .Application.Contracts

Этот проект в основном содержит [application service](../Application-Services.md) **интерфейсы** и [Data Transfer Objects](../Data-Transfer-Objects.md) (DTO) уровня приложения. Он существует для разделения интерфейса и реализации прикладного уровня. Таким образом, проект интерфейса может быть предоставлен клиентам как пакет контракта.

Интерфейс `IBookAppService` и класс `BookCreationDto` являются хорошими кандидатами для этого проекта.

* Зависит от `.Domain.Shared`, потому что он может использовать константы, перечисления и другие общие объекты этого проекта в интерфейсах служб приложений и DTO.

#### Проект .Application

Данный проект [application service](../Application-Services.md) **Имплементирует** интерфейсы определенных в проекте `.Application.Contracts`.

Класс `BookAppService` - хороший кандидат для этого проекта.

* Зависит от проекта `.Application.Contracts`, чтобы иметь возможность реализовывать интерфейсы и использовать DTO.

* Зависит от проекта `.Domain`, чтобы иметь возможность использовать объекты домена (сущности, интерфейсы репозитория ... и т. Д.) Для выполнения логики приложения.

#### Проект .EntityFrameworkCore

Это проект интеграции с EF Core. Он определяет `DbContext` и реализует интерфейсы репозитория, определенные в проекте .Domain.

* Зависит от проекта `.Domain`, чтобы иметь возможность ссылаться на сущности и интерфейсы репозитория. 

>Этот проект доступен, только если вы используете EF Core в качестве поставщика базы данных. Если вы выберете другого поставщика базы данных, его имя будет другим.

#### Проект .EntityFrameworkCore.DbMigrations

Содержит миграции базы данных EF Core для решения. Он имеет отдельный `DbContext`, предназначенный для управления миграциями.

ABP - это модульный фреймворк с идеальным дизайном, каждый модуль имеет свой собственный класс DbContext. Здесь в игру вступает миграция `DbContext`, которая объединяет все конфигурации` DbContext` в единую модель для поддержки единой схемы базы данных. Для более сложных сценариев у вас может быть несколько баз данных (каждая содержит одну или несколько таблиц модулей) и множественный перенос `DbContext`s (каждая поддерживает свою схему базы данных).

Обратите внимание, что миграция `DbContext` используется только для миграции базы данных и *не используется в runtime*.

* Зависит от проекта `.EntityFrameworkCore`, поскольку он повторно использует конфигурацию, определенную для` DbContext` приложения.

> Этот проект доступен, только если вы используете EF Core в качестве поставщика базы данных.
>
> Смотрите [Entity Framework Core Migrations Guide](../Entity-Framework-Core-Migrations.md), чтобы понять этот проект более подробно.

#### Проект .DbMigrator 

Это консольное приложение, которое упрощает выполнение миграции базы данных в средах разработки и на боевом сервере. Когда вы запускаете это приложение, оно;

* При необходимости создает базу данных.
* Применяет незавершенные миграции базы данных.
* При необходимости засевает исходные данные.

> У этого проекта есть собственный файл `appsettings.json`. Итак, если вы хотите изменить строку подключения к базе данных, не забудьте изменить и этот файл.

На этом этапе особенно важен инициализация исходных данных. ABP имеет модульную инфраструктуру с иначальной структурой данных. См. [its documentation](../Data-Seeding.md) для получения дополнительной информации о заполнении данных.

Хотя создание базы данных и применение миграции необходимо только для реляционных баз данных, эти проекты появляются, даже если вы выбираете поставщика базы данных NoSQL (например, MongoDB). В этом случае он по-прежнему сохраняет исходную структуру, необходимые для приложения.

* Зависит от `.EntityFrameworkCore.DbMigrations` проекта (для EF Core), поскольку ему требуется доступ к миграциям.
* Зависит от проекта `.Application.Contracts`, чтобы иметь возможность доступа к определениям разрешений, поскольку начальная инициализация данных требует разрешения в качестве роли администратора.

#### Проект .HttpApi

This project is used to define your API Controllers.

Most of time you don't need to manually define API Controllers since ABP's [Auto API Controllers](../API/Auto-API-Controllers.md) feature creates them automagically based on your application layer. However, in case of you need to write API controllers, this is the best place to do it.

* Depends on the `.Application.Contracts` project to be able to inject the application service interfaces.

#### Проект .HttpApi.Client

This is a project that defines C# client proxies to use the HTTP APIs of the solution. You can share this library to 3rd-party clients, so they can easily consume your HTTP APIs in their Dotnet applications (For other type of applications, they can still use your APIs, either manually or using a tool in their own platform)

Most of time you don't need to manually create C# client proxies, thanks to ABP's [Dynamic C# API Clients](../API/Dynamic-CSharp-API-Clients.md) feature.

`.HttpApi.Client.ConsoleTestApp` project is a console application created to demonstrate the usage of the client proxies.

* Depends on the `.Application.Contracts` project to be able to share the same application service interfaces and DTOs with the remote service.

> You can delete this project & dependencies if you don't need to create C# client proxies for your APIs.

#### Проект .Web

This project contains the User Interface (UI) of the application if you are using ASP.NET Core MVC UI. It contains Razor pages, JavaScript files, CSS files, images and so on...

This project contains the main `appsettings.json` file that contains the connection string and other configuration of the application.

* Depends on the `.HttpApi` since UI layer needs to use APIs and application service interfaces of the solution.

> If you check the source code of the `.Web.csproj` file, you will see the references to the `.Application` and the `.EntityFrameworkCore.DbMigrations` projects.
>
> These references are actually not needed while coding your UI layer, because UI layer normally doesn't depend on the EF Core or the Application layer's implementation. This startup templates are ready for the tiered deployment, where API layer is hosted in a separate server than the UI layer.
>
> However, if you don't choose the `--tiered` option, these references will be in the .Web project to be able to host the Web, API and application layers in a single application endpoint.
>
> This gives you to ability to use domain entities & repositories in your presentation layer. However, this is considered as a bad practice according to the DDD.

#### Проект Test

The solution has multiple test projects, one for each layer:

* `.Domain.Tests` is used to test the domain layer.
* `.Application.Tests` is used to test the application layer.
* `.EntityFrameworkCore.Tests` is used to test EF Core configuration and custom repositories.
* `.Web.Tests` is used to test the UI (if you are using ASP.NET Core MVC UI).
* `.TestBase` is a base (shared) project for all tests.

In addition, `.HttpApi.Client.ConsoleTestApp` is a console application (not an automated test project) which demonstrate the usage of HTTP APIs from a .NET application.

Test projects are prepared for integration testing;

* It is fully integrated to ABP framework and all services in your application.
* It uses SQLite in-memory database for EF Core. For MongoDB, it uses the [Mongo2Go](https://github.com/Mongo2Go/Mongo2Go) library.
* Authorization is disabled, so any application service can be easily used in tests.

You can still create unit tests for your classes which will be harder to write (because you will need to prepare mock/fake objects), but faster to run (because it only tests a single class and skips all initialization process).

#### How to Run?

Set `.Web` as the startup project and run the application. Default username is `admin` and password is `1q2w3E*`.

See [Getting Started With the ASP.NET Core MVC Template](../Getting-Started-AspNetCore-MVC-Template.md) for more information.

### Tiered Structure

If you have selected the ASP.NET Core UI and specified the `--tiered` option, the solution created will be a tiered solution. The purpose of the tiered structure is to be able to **deploy Web application and HTTP API to different servers**:

![bookstore-visual-studio-solution-v3](../images/tiered-solution-servers.png)

* Browser runs your UI by executing HTML, CSS & JavaScript.
* Web servers hosts static UI files (CSS, JavaScript, image... etc.) & dynamic components (e.g. Razor pages). It performs HTTP requests to the API server to execute the business logic of the application.
* API Server hosts the HTTP APIs which then use application & domain layers of the application to perform the business logic.
* Finally, database server hosts your database.

So, the resulting solution allows a 4-tiered deployment, by comparing to 3-tiered deployment of the default structure explained before.

> Unless you actually need to such a 4-tiered deployment, its suggested to go with the default structure which is simpler to develop, deploy and maintain.

The solution structure is shown below:

![bookstore-visual-studio-solution-v3](../images/bookstore-visual-studio-solution-tiered.png)

As different from the default structure, two new projects come into play: `.IdentityServer` & `.HttpApi.Host`.

#### Проект .IdentityServer

This project is used as an authentication server for other projects. `.Web` project uses OpenId Connect Authentication to get identity and access tokens for the current user from the IdentityServer. Then uses the access token to call the HTTP API server. HTTP API server uses bearer token authentication to obtain claims from the access token to authorize the current user.

![tiered-solution-applications](../images/tiered-solution-applications.png)

ABP uses the open source [IdentityServer4](https://identityserver.io/) framework for the authentication between applications. See [IdentityServer4 documentation](http://docs.identityserver.io) for details about the IdentityServer4 and OpenID Connect protocol.

It has its own `appsettings.json` that contains database connection and other configurations.

#### Проект .HttpApi.Host

This project is an application that hosts the API of the solution. It has its own `appsettings.json` that contains database connection and other configurations.

#### Проект .Web

Just like the default structure, this project contains the User Interface (UI) of the application. It contains razor pages, JavaScript files, style files, images and so on...

This project contains an `appsettings.json` file, but this time it does not have a connection string because it never connects to the database. Instead, it mainly contains endpoint of the remote API server and the authentication server.

#### Pre-requirements

* [Redis](https://redis.io/): The applications use Redis as as distributed cache. So, you need to have Redis installed & running.

#### How to Run?

You should run the application with the given order:

* First, run the `.IdentityServer` since other applications depends on it.
* Then run the `.HttpApi.Host` since it is used by the `.Web` application.
* Finally, you can run the `.Web` project and login to the application (using `admin` as the username and `1q2w3E*` as the password).

### Angular UI

If you choose `Angular` as the UI framework (using the `-u angular` option), the solution is being separated into two folders:

* `angular` folder contains the Angular UI application, the client-side code.
* `aspnet-core` folder contains the ASP.NET Core solution, the server-side code.

The server-side is similar to the solution described above. `*.HttpApi.Host` project serves the API, so the `Angular` application consumes it.

Angular application folder structure looks like below:

![angular-folder-structure](../images/angular-folder-structure.png)


Each of ABP Commercial modules is an NPM package. Some ABP modules are added as a dependency in `package.json`. These modules install with their dependencies. To see all ABP packages, you can run the following command in the `angular` folder:

```bash
yarn list --pattern abp
```

Angular application module structure:

![Angular template structure diagram](../images/angular-template-structure-diagram.png)

#### AppModule

`AppModule` is the root module of the application. Some of ABP modules and some essential modules imported to the `AppModule`.

ABP Config modules also have imported to `AppModule`  for initially requirements of lazy-loadable ABP modules.

#### AppRoutingModule

There are lazy-loadable ABP modules in the `AppRoutingModule` as routes.

> Paths of ABP Modules should not be changed.

You should add `routes` property in the `data` object to add a link on the menu to redirect to your custom pages.

```js
{
   path: 'dashboard',
   loadChildren: () => import('./dashboard/dashboard.module').then(m => m.DashboardModule),
   canActivate: [AuthGuard, PermissionGuard],
   data: {
      routes: {
         name: 'ProjectName::Menu:Dashboard',
         order: 2,
         iconClass: 'fa fa-dashboard',
         requiredPolicy: 'ProjectName.Dashboard.Host'
      } as ABP.Route
   }
}
```
In the above example;
*  If the user is not logged in, AuthGuard blocks access and redirects to the login page.
*  PermissionGuard checks the user's permission with `requiredPolicy` property of the `rotues` object. If the user is not authorized to access the page, the 403 page appears.
*  `name` property of `routes` is the menu link label. A localization key can be defined .
*  `iconClass` property of `routes` object is the menu link icon class.
*  `requiredPolicy` property of `routes` object is the required policy key to access the page.

After the above `routes` definition, if the user is authorized, the dashboard link will appear on the menu.

#### Shared Module

The modules that may be required for all modules have imported to the `SharedModule`. You should import the `SharedModule` to all modules.

See the [Sharing Modules](https://angular.io/guide/sharing-ngmodules) document.

#### Environments

The files under the `src/environments` folder has the essential configuration of the application.

#### Home Module

Home module is an example lazy-loadable module that loads on the root address of the application.

#### Styles

The required style files added to `styles` array in the `angular.json`. `AppComponent` loads some style files lazily via `LazyLoadService` after the main bundle is loaded to shorten the first rendering time.

#### Testing

You should create your tests in the same folder as the file file you want to test.

See the [testing document](https://angular.io/guide/testing).

#### Depended Packages

* [NG Bootstrap](https://ng-bootstrap.github.io/) is used as UI component library.
* [NGXS](https://www.ngxs.io/) is used as state management library.
* [angular-oauth2-oidc](https://github.com/manfredsteyer/angular-oauth2-oidc) is used to support for OAuth 2 and OpenId Connect (OIDC).
* [Chart.js](https://www.chartjs.org/) is used to create widgets.
* [ngx-validate](https://github.com/ng-turkey/ngx-validate) is used for dynamic validation of reactive forms.

### React Native

if `-m react-native` option is spesified in new project command, the solution includes the [React Native](https://reactnative.dev/) application in the `react-native` folder.

The server-side is similar to the solution described above. `*.HttpApi.Host` project serves the API, so the React Native application consumes it.

The React Native application was generated with [Expo](https://expo.io/). Expo is a set of tools built around React Native to help you quickly start an app and, while it has many features.

React Native application folder structure as like below:

![react-native-folder-structure](../images/react-native-folder-structure.png)

* `App.js` is bootstrap component of the application.
* `Environment.js` file has the essential configuration of the application. `prod` and `dev` configurations defined in this file. 
* [Contexts](https://reactjs.org/docs/context.html) are created in the `src/contexts` folder.
* [Higher order components](https://reactjs.org/docs/higher-order-components.html) are created in the`src/hocs` folder.
* [Custom hooks](https://reactjs.org/docs/hooks-custom.html#extracting-a-custom-hook) are created in the`src/hooks`.
* [Axios interceptors](https://github.com/axios/axios#interceptors) are created in the `src/interceptors` folder.
* Utility functions are exported from `src/utils` folder.

#### Components

Components that can be used on all screens are created in the `src/components` folder. All components have created as a function that able to use [hooks](https://reactjs.org/docs/hooks-intro.html).

#### Screens

![react-native-navigation-structure](../images/react-native-navigation-structure.png)

Screens are created by creating folders that separate their names in the `src/screens` folder. Certain parts of some screens can be split into components.

Each screen is used in a navigator in the `src/navigators` folder.

#### Navigation

[React Navigation](https://reactnavigation.org/) is used as a navigation library. Navigators are created in the `src/navigators`. A [drawer](https://reactnavigation.org/docs/drawer-based-navigation/) navigator and several [stack](https://reactnavigation.org/docs/hello-react-navigation/#installing-the-stack-navigator-library) navigators have created in this folder. See the [above diagram](#screens) for navigation structure.

#### State Management

[Redux](https://redux.js.org/) is used as state management library. [Redux Toolkit](https://redux-toolkit.js.org/) library is used as a toolset for efficient Redux development.

Actions, reducers, sagas, selectors are created in the `src/store` folder. Store folder as like below:

![react-native-store-folder](../images/react-native-store-folder.png)

* [**Store**](https://redux.js.org/basics/store) is defined in the `src/store/index.js` file.
* [**Actions**](https://redux.js.org/basics/actions/) are payloads of information that send data from your application to your store.
* [**Reducers**](https://redux.js.org/basics/reducers) specify how the application's state changes in response to actions sent to the store. 
* [**Redux-Saga**](https://redux-saga.js.org/) is a library that aims to make application side effects (i.e. asynchronous things like data fetching and impure things like accessing the browser cache) easier to manage. Sagas are created in the `src/store/sagas` folder.
* [**Reselect**](https://github.com/reduxjs/reselect) library is used to create memoized selectors. Selectors are created in the `src/store/selectors` folder.

#### APIs

[Axios](https://github.com/axios/axios) is used as an HTTP client library. An Axios instance has exported from  `src/api/API.js` file to make HTTP calls with the same config. `src/api` folder also has the API files that have been created for API calls.

#### Theming

[Native Base](https://nativebase.io/) is used as UI components library. Native Base components can customize easily. See the [Native Base customize](https://docs.nativebase.io/Customize.html#Customize) documentation. We followed the same way.

* Native Base theme variables are in the `src/theme/variables` folder.
* Native Base component styles are in the `src/theme/components` folder. These files have been generated with Native Base's `ejectTheme` script.
* Styles of components override with the files under the `src/theme/overrides` folder.

#### Testing

Unit tests will be created.

See the [Testing Overview](https://reactjs.org/docs/testing.html) document.

#### Depended Libraries

* [Native Base](https://nativebase.io/) is used as UI components library.
* [React Navigation](https://reactnavigation.org/) is used as navigation library.
* [Axios](https://github.com/axios/axios) is used as HTTP client library.
* [Redux](https://redux.js.org/) is used as state management library.
* [Redux Toolkit](https://redux-toolkit.js.org/) library is used as a toolset for efficient Redux development.
* [Redux-Saga](https://redux-saga.js.org/) is used to manage asynchronous processes.
* [Redux Persist](https://github.com/rt2zz/redux-persist) is used as state persistance.
* [Reselect](https://github.com/reduxjs/reselect) is used to create memoized selectors.
* [i18n-js](https://github.com/fnando/i18n-js) is used as i18n library.
* [expo-font](https://docs.expo.io/versions/latest/sdk/font/) library allows loading fonts easily.
* [Formik](https://github.com/jaredpalmer/formik) is used to build forms.
* [Yup](https://github.com/jquense/yup) is used for form validations.

## Social / External Logins

If you want to configure social/external logins for your application, please follow the [Social/External Logins](../Authentication/Social-External-Logins.md) document.

## What's Next?

- [The getting started document](../Getting-Started.md) explains how to create a new application in a few minutes.
- [The application development tutorial](../Tutorials/Part-1.md) explains step by step application development.
