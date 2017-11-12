# Spring Security

## <a name="toc"></a> Содержание:
- [Создание проекта](#CreateProject)
- [Настройка проекта](#Configuration)
- [Подключаем SpringBoot](#SpringBoot)
- [Добавляем ThymeLeaf](#ThymeLeaf)
- [Подключаем Spring Security](#SpringSecurity)
- [Spring Security подробнее](#SpringSecurityDeeper)

## <a name="CreateProject"></a> Создание проекта
Для начала нам необходимо создать проект.
В этом нам поможет система сборки Gradle.
Переходим на [сайт Gradle](https://gradle.org) и переходим в раздел [документации](https://gradle.org/docs/). Нас, как пользователей, интересует **User Manual**.
Нас интересует глава, посвящённая **"Build Init Plugin"**.
Находим описание **java-application** и выполняем как указано:
```gradle init --type java-application```
Если всё хорошо, то нам напишут, что **BUILD SUCCESSFUL**.

## <a name="Configuration"></a> Настройка проекта
Для редактирования будем использовать [Sublime Text 3](https://www.sublimetext.com/3).
Запускаем SublimeText, выбираем ```File → Open Folder```.
Выбираем каталог, в котором мы выполнили Gradle Init Plugin.

Открываем на редактирование Build Script - файл с названием build.gradle.
Оставляем java плагин (подробнее см. [документацию gradle](https://docs.gradle.org/4.3.1/userguide/java_plugin.html)):
```apply plugin: 'java'```

Оставляем блок с репозиторием, перенаправив его на Maven Central:
```
repositories {
    mavenCentral()
}
```
Добавим указание на версию Java:
```
sourceCompatibility = 1.8
targetCompatibility = 1.8
```
Оставляем блок с зависимостями, он нам сейчас понадобится:
```
dependencies {
    testCompile 'junit:junit:4.12'
}
```
Ну и можем уточнить название для артефакта:
```
jar {
    baseName = 'securing-web'
    version =  '0.1.0'
}
```

## <a name="SpringBoot"></a> Подключаем Spring Boot
Перейдём на сайт Spring, в верхнем меню выберем Projects и выберем [SpringBoot](https://projects.spring.io/spring-boot/).
В правой части окна находим метку **current** и для неё открываем **Reference** документ.
Открытая ссылка будет иметь примерно следующий вид:
```
https://docs.spring.io/spring-boot/docs/1.5.8.RELEASE/reference/htmlsingle/
```
И тут есть небольшой хитрый ход. Версию, в данном примере 1.5.8.RELEASE, можно заменить словом current. Например, это может быть полезно, если сохраняем ссылку на документацию, т.к. она всегда будет актуальна.

В оглавлении нас интересует глава: [4. Working with Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#_working_with_spring_boot).
В списке находим **Gradle**, что перекинет нас на главу [13.3 Gradle](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-gradle).
В самом верху нашего Build скрипт убираем **apply plugin** и заменяем его на пример из документации:
```
plugins {
    id 'org.springframework.boot' version '1.5.8.RELEASE'
    id 'java'
}
```
Так же заменяем содержимое блока **dependencies** так, чтобы оно соответствовало примеру из документации.

Далее обратим внимание на главу [14.1 Using the “default” package](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-using-the-default-package). Указано, что необходимо избегать default package. А именно в таком пакете создаётся первый класс Gradle Initi Plugin'ом. Следовательно, создадим новый пакет и класс в нём.
В Sublime Text выбираем каталог **src/main/java** и через контекстное меню выбираем **New Folder**.
Т.к. каталог - название пакета. Так что соблюдаем [Code Convention](http://www.oracle.com/technetwork/java/codeconventions-150003.pdf), называем маленькими буквами. Например, learnspring.

Далее через контекстное меню каталога выбираем **New File**.
Сразу можно сохранить его, чтобы Sublime Text понял тип файла. Нажимаем **Ctrl+S**.
Сделаем как в разделе [14.2 Locating the main application class](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-locating-the-main-class) документации.
Назовём его **Application.java**. Базовой Java конструкцией опишем класс:
```java
package learnspring;

public class Application {

	public static void main(String[] args) {

	}
}
```
Как мы видим, мы выполнили рекомендацию:
``
We generally recommend that you locate your main application class in a root package above other classes.
``
Теперь мы можем удалить класс App.java, который был сегенрирован Gradle. И удалим тест AppTest из src/test/java (через пункт Delete File контекстного меню).

Далее, нас интересует аннотация **@SpringBootApplication**.
Про это написано в главе [18. Using the @SpringBootApplication annotation](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-using-springbootapplication-annotation).
Там же есть пример того, как нужно написать наш Main класс:
```java
package learnspring;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```
Ну и перед началом нужно убедиться, что наше приложение стартует без ошибок.
Запуск Spring Boot приложения описан в документации, в главе [19. Running your application](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-running-your-application).
В подглаве [19.4 Using the Gradle plugin](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-running-with-the-gradle-plugin) сказано, что при использовании Gradle мы можем использовать для запуска плагин - **spring-boot-gradle-plugin**.

Так как этот скрипт будет использован, чтобы запускать приложение, следовательно он будет использован вне процесса сборки. Следовательно, такие плагины объявляются в секции Gradle Build скрипта под названием [buildscript](https://docs.gradle.org/current/userguide/organizing_build_logic.html#sec:build_script_external_dependencies):
```
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.5.8.RELEASE")
    }
}
```
Данный блок должен идти самым первым. Подробнее про плагин можно прочитать в [документации](https://docs.spring.io/spring-boot/docs/current/reference/html/build-tool-plugins-gradle-plugin.html).

Теперь, настроив Spring Boot, проверяем запуск. Открываем в командной строке корневой каталог нашего проекта (тот, в котором мы выполняли gradle init) и выполним команду:
```gradle bootRun```

Если всё было выполнено правильно, то мы увидим что-то вроде этого:
```
INFO 4660 --- [main] s.b.c.e.t.TomcatEmbeddedServletContainer : Tomcat started on port(s): 8080 (http)
INFO 4660 --- [main] learnspring.Application: Started Application in 15.327 seconds (JVM running for 15.996)
```
Что говорит о том, что SpringBoot запустил томкат, который слушает на порту 8080 обращения по http.
Так же говорит, что приложение запущено. Теперь мы готовы к подвигам.

## <a name="ThymeLeaf"></a> Добавляем ThymeLeaf
ThymeLeaf - это движок шаблонов, позволяющий более просто реализовывать паттерн MVC при помощи View Templates.
Подробнее можно прочитать здесь: [Понимание View Templates](http://spring-projects.ru/understanding/view-templates/)

В Spring функциональность добавляется при помощи стартеров. Про Starters можно прочитать в документации Spring Boot, в главе: [13.5 Starters](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter).
Там же ниже приведён список стартеров. Из этого списка видно, что для использования ThymeLeaf необходимо использовать следующи стартер: **spring-boot-starter-thymeleaf**.

Поэтому, в файле build.gradle в блоке dependencies добавляем:
``` compile("org.springframework.boot:spring-boot-starter-thymeleaf") ```
Интересно, что в главе [74.9 Use Thymeleaf 3](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-use-thymeleaf-3) сказано, что по умолчанию, spring-boot-starter-thymeleaf используетм версию ThymeLeaf 2.1.
Немного загуглив будут найдены варианты, как можно указать 3 версию ThymeLeaf:
Например, [тут](https://medium.com/@dmarko484/spring-boot-spring-boot-1-4-x-thymeleaf-3-x-configuration-b920b1d92ba0).
На сайте [ThymeLeaf](http://www.thymeleaf.org/download.html) узнаём последнюю версию. Например: "Thymeleaf 3.0.9.RELEASE is the current stable version".
На сайте [mvnrepository](https://mvnrepository.com/artifact/nz.net.ultraq.thymeleaf/thymeleaf-layout-dialect/2.2.2) можем узнать последнюю версию thymeleaf-layout-dealect'а.
Следовательно, указываем:
```
ext["thymeleaf.version"] = "3.0.9.RELEASE"
ext["thymeleaf-layout-dialect.version"] = "2.2.2"
```

Теперь, нам нужно понять, как использовать шаблоны.
В этом нам могут помочь Spring Guides:
[Serving Web Content with Spring MVC](https://spring.io/guides/gs/serving-web-content/)
и
[Securing a Web Application](https://spring.io/guides/gs/securing-web/)

Сначала, нам нужно описать View, т.е. представление страницы. Т.е. описание того, каким образом пользователь должен видеть страницы.
По умолчанию, их следует располагать в каталоге **src/main/resources/templates**.
У Spring'а есть ViewResolver'ы, которые будут сопоставлять имя View с шаблоном. Подробнее можно прочитать в [74.8 Customize ViewResolvers](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#howto-customize-view-resolvers).

Создадим через Sublime Text 3 каталог src/main/resources. А в нём каталог templates.
Далее из примера Securing Web перенесём 2 View Template: home.html и hello.html

Пока что про них Spring не знает. Это просто файлы. Нужно познакомить с ними ViewResolver.
Для этого создадим каталог: **src/main/java/learnspring/config**.
После этого создадим в нём новый файл. Возьмём пример из [Securing a Web Application](https://spring.io/guides/gs/securing-web/).
Подробнее можно узнать из документации: [27.1.1 Spring MVC auto-configuration](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-spring-mvc-auto-configuration).
При помощи классов, аннотированных @Configuration, мы описываем конфигурацию Spring java кодом. Это относится к Spring Framework Core. Найти про это можно в соответствующей документации, глава [1.12. Java-based container configuration](https://docs.spring.io/spring/docs/current/spring-framework-reference/core.html#beans-java).
Про наследование от **WebMvcConfigurerAdapter** указано непосредственно в Spring Boot, в главе [27.1.1 Spring MVC auto-configuration](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-spring-mvc-auto-configuration).

View Controller'ы являются частью Web MVC, что так же относится к Spring Framework Core. Поэтому, читаем об этом в документации Spring Framework, раздел Web Servlet, глава [1.11.8. View Controllers](https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-config-view-controller).

Теперь мы можем запустить наше приложение из корневого каталога проекта через как:
```gradle bootRun```
Таким образом мы запускаем на выполнение плагин, который мы ранее подключили. Он делает всю работу: Запускает Embedded сервер, деплоит (разворачивает) на него наше приложение.

После запуска достаточно просто перейти на **http://127.0.0.1:8080/** и нам откроется наша страничка. Никакой магии, только Spring Boot )

## <a name="SpringSecurity"></a> Подключаем Spring Security
Как и любая другая функциональность, Spring Security добавляется стартером.
Как и ранее, в документации к Spring Boot переходим к разделу [13.5 Starters](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#using-boot-starter) и находим, какой стартер нас интересует для Spring Security.
Нас интересует стартер: **spring-boot-starter-security**. Добавим в блок dependencies нашего Gradle Build скрипта новую зависимость:
```
compile("org.springframework.boot:spring-boot-starter-security")
```

Spring Security - это отдельный проект. Поэтому, смотрим его собственный раздел: https://projects.spring.io/spring-security/. Нас интересует версия, помеченная как GA. Так же можем исправить в документации конкретную версию на current. Получаем ссылку:
https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/
Для начала, можем ознакомиться с главой [5. Java Configuration](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/#jc).
И узнаём, что чтобы задать конфигурацию Java кодом, мы должны унаследоваться от WebSecurityConfigurerAdapter и аннотировать класс как @EnableWebSecurity:
```java
package learnspring.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;

@Configuration
@EnableWebSecurity
public class WebSecurityConfig extends WebSecurityConfigurerAdapter {

}
```
Таким образом Spring узнаёт, что у нас есть класс, который хочет что-то настроить в Spring Security + данный класс уже предоставляет нам набор некоторых базовых конфигураций.

Непосредственно саму настройку возьмём из Guide: [Securing a Web Application](https://spring.io/guides/gs/securing-web/).
Так же теперь нам нужна View для логина. Возьмём её так же из данного Guide.

Так же Guide приводит интересный пример того, как можно изменить View hello.html.
Заменяем содержимое body:
```html
<body>
	<h1 th:inline="text">Hello [[${#httpServletRequest.remoteUser}]]!</h1>
    	<form th:action="@{/logout}" method="post">
            <input type="submit" value="Sign Out"/>
        </form>
</body>
```

Теперь можем перезапустить приложение и посмотреть, как это выглядит со стороны пользователя.
Логин и пароль пока что у нас жёстко забиты. Поэтому, вводим то, что надо:
user = user, password = password
Как мы видим, всё работает. Теперь, пора погружаться глубже.

## <a name="SpringSecurityDeeper"></a> Spring Security подробнее
Теперь, необходимо разобраться, как всё это дело настраивать.
Ну и мы не хотим жёстко забивать конкретный логин и пароль. Это совсем странно )
В этом нам поможет следующая документация:
Spring Boot: глава [28. Security](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#boot-features-security)

Spring security: [Spring Security Reference](https://docs.spring.io/spring-security/site/docs/current/reference/htmlsingle/)
