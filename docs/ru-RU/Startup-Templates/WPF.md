# Стартовый шаблон WPF приложения

Этот шаблон используется для создания минималистичного проекта приложения WPF. 

## How to Start With?

Сперва установите [ABP CLI](../CLI.md) если вы установили ранее:

````bash
dotnet tool install -g Volo.Abp.Cli
````

Используйте команду `abp new` в пустом каталоге для создания нового решения:

````bash
abp new Acme.MyWpfApp -t wpf
````

`Acme.MyWpfApp` это имя решения, например *YourCompany.YourProduct*. Вы можете использовать одноуровневое, двухуровневое или трехуровневое именование. 

## Структура решения 

После того, как вы воспользуетесь указанной выше командой для создания решения, у вас будет решение, подобное показанному ниже: 

![basic-wpf-application-solution](../images/basic-wpf-application-solution.png)

* `HelloWorldService` - это пример службы, которая реализует интерфейс `ITransientDependency` для регистрации этой службы в системе [dependency injection](../Dependency-Injection.md). 
