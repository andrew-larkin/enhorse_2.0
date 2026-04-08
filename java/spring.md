[Назад](javamenu.md)

# Spring
### Spring Core
+ [Что такое IoC (инверсия управления)?](#что-такое-IoC-инверсия-управления)
+ [Что такое внедрение зависимостей?](#что-такое-внедрение-зависимостей)
+ [Как IoC реализуется?](#Как-IoC-реализуется)
+ [Что такое Spring](#что-такое-spring)
+ [Что такое Spring Bean?](#что-такое-spring-bean)
+ [Как IoC реализуется в Spring?](#Как-IoC-реализуется-в-Spring)
+ [Как происходит запуск IoC-контейнера Spring?](#Как-происходит-запуск-IoC-контейнера-Spring)
+ [Что такое Bean Scopes?](#что-такое-bean-scopes)
+ [Примеры использования bean scopes?](#Примеры-использования-bean-scopes)
+ [Аннотации `@Configuration` `@Bean` `@Import` `@ComponentScan`?](#Аннотации-Configuration-Bean-Import-ComponentScan)
+ [Аннотации `@Autowired`, `@Resource` и `@Inject`?](#Аннотации-Autowired-Resource-и-Inject)
+ [Аннотации `@Value`, `@ConfigurationProperties` и `@PropertySource`?](#Аннотации-Value-ConfigurationProperties-и-PropertySource)
+ [Аннотации `@Primary`, `@Qualifier` и `@Named`?](#Аннотации-Primary-Qualifier-и-Named)
+ [`@Component` и его стереотипные версии?](#Component-и-его-стереотипные-версии)
+ [`@Profile` и аннотации группы `@ConditionalOn...`?](#Profile-и-аннотации-группы-ConditionalOn)
+ [Тестирование Spring приложений?](#Тестирование-Spring-приложений)

### Spring MVC
+ [Где должны располагаться статические (css, js, html) ресурсы в Spring MVC приложении?](#где-должны-располагаться-статические-css-js-html-ресурсы-в-spring-mvc-приложении)
+ [Можно ли передать в запросе один и тот же параметр несколько раз?](#можно-ли-передать-в-запросе-один-и-тот-же-параметр-несколько-раз)
+ [В чем разница между `ModelMap` и `ModelAndView`?](#в-чем-разница-между-modelmap-и-modelandview)
+ [В чем разница между `model.put()` и `model.addAttribute()`?](#в-чем-разница-между-modelput-и-modeladdattribute)
+ [Что можете рассказать про Form Binding?](#что-можете-рассказать-про-form-binding)
+ [В чем разница между Filters, Listeners and Interceptors?](#в-чем-разница-между-filters-listeners-and-interceptors)

### Spring AOP
+ [В чем разница между Сквозной Функциональностью (Cross Cutting Concerns) и АОП (аспектно ориентированное программирование)?](#в-чем-разница-между-сквозной-функциональностью-cross-cutting-concerns-и-аоп-аспектно-ориентированное-программирование)
+ [Почему возвращаемое значение при применении аспекта `@Around` может потеряться? Назовите причины](#почему-возвращаемое-значение-при-применении-аспекта-around-может-потеряться-назовите-причины)

### Spring Data
+ [Почему мы используем Hibernate Validator?](#почему-мы-используем-hibernate-validator)



## Что такое IoC (инверсия управления)?
Инверсия управления — это принцип в разработке программного обеспечения, который передает управление объектами или частями 
программы контейнеру или фреймворку.
Инверсию управления можно разделить на 2 типа:
+ Dependency lookup (поиск зависимостей)
  + Dependency pull (загрузка зависимостей)
  + Contextualized dependency lookup (CDL) (поиск зависимостей в контексте)
+ Dependency injection (внедрение зависимостей)
  + Constructor dependency injection (внедрение через конструктор)
  + Setter dependency injection (внедрение через сеттер)

[к оглавлению](#spring-core)

## Что такое внедрение зависимостей?

Внедрение зависимостей (DI) - это концепция, которая определяет, как должно быть связано несколько классов.
Это один из примеров Инверсии контроля. Вам не нужно явно подключать службы и компоненты в коде при использовании
внедрения зависимостей. Вместо этого вы описываете службы, необходимые каждому компоненту, в файле конфигурации XML
(или с помощью аннотаций) и разрешаете контейнеру IOC автоматически подключать их.

[к оглавлению](#spring-core)

## Как IoC реализуется?
+ Dependency pull (загрузка зависимостей):
```java
public class HelloWorldSpringAnnotated {
    public static void main(String... args) {
        ApplicationContext ctx = new AnnotationConfigApplicationContext(HelloWorldConfiguration.class);
        MessageRenderer mr = ctx.getBean("renderer", MessageRenderer.class);
        mr.render();
    }
}
```
+ Contextualized dependency lookup (CDL):
```java
public interface ManagedComponent {
    void performLookup(Container container);
}

public interface Container {
    Object getDependency(String key);
}

interface MessageRenderer extends ManagedComponent {
    void render();
}

class StandardOutMessageRenderer implements MessageRenderer {
    private MessageProvider messageProvider;
    @Override
    public void performLookup(Container container) {
        this.messageProvider = (MessageProvider) container.getDependency("provider");
    }
}
```
+ Constructor dependency injection (внедрение через конструктор):
```java
class StandardOutMessageRenderer implements MessageRenderer {
    private final MessageProvider messageProvider;
    
    public StandardOutMessageRenderer(MessageProvider messageProvider) {
        this.messageProvider = messageProvider;
    }
}
```
+ Setter dependency injection (внедрение через сеттер):
```java
class StandardOutMessageRenderer implements MessageRenderer {
    private final MessageProvider messageProvider;

    public void setMessageProvider(MessageProvider messageProvider) {
        this.messageProvider = messageProvider;
    }
}
```

Во многих случаях тип IoC определяется используемым контейнером. Например, если используется EJB 2.1 или более ранние версии, 
необходимо использовать Dependency lookup (через JNDI) для получения EJB из контейнера JEE. В Spring компоненты и их 
зависимости всегда (кроме initial bean lookups) связываются друг с другом с помощью Dependency injection.

[к оглавлению](#spring-core)

## Что такое Spring?

__Spring Framework (или коротко Spring)__ - универсальный фреймворк с открытым исходным кодом для Java-платформы.
Центральной частью Spring является контейнер Inversion of Control, который предоставляет средства конфигурирования и
управления объектами Java с помощью рефлексии. Контейнер отвечает за управление жизненным циклом объекта: создание объектов,
вызов методов инициализации и конфигурирование объектов путём связывания их между собой.
Spring имеет множество дочерних под-проектов, в том числе:

+ _Data_ - для работы с хранилищами данных.
+ _MVC_ - для создания веб приложений.
+ _Boot_ - для быстрой компоновки и создания приложения на основе других Spring проектов.
+ _Cloud_ - для создания распределённой приложений.

[к оглавлению](#spring-core)

## Что такое Spring Bean?

Это объекты, составляющие основу пользовательского приложения, управляются контейнером Spring IoC.
Бины создаются, настраиваются, подключаются и управляются контейнером IoC. Бины создаются с использованием метаданных
конфигурации, которые пользователи предоставляют контейнеру (посредством XML или аннотациями (java annotations configurations)).

[к оглавлению](#spring-core)


## Как IoC реализуется в Spring?
Пакеты org.springframework.beans и org.springframework.context являются основой для контейнера IoC 
в Spring Framework. Центральным элементом контейнера IoC в Spring является интерфейс 
org.springframework.beans.factory.BeanFactory. Реализации этого интерфейса в Spring отвечают за 
управление компонентами, включая их зависимости, а также жизненные циклы.

Также существует интерфейс ApplicationContext - это, по сути, расширение BeanFactory. Оно предоставляет другие услуги:
+ Интеграция с Spring AOP
+ Обработка message resources для интернационализации
+ обработка событий приложения
+ доп контексты (web, security и т.д.)

При разработке Spring-приложений рекомендуется использовать именно ApplicationContext.
Spring поддерживает инициализацию ApplicationContext либо вручную (создание экземпляра вручную и загрузка соответствующей конфигурации), 
либо в среде веб-контейнера через ContextLoaderListener.

[к оглавлению](#spring-core)

## Как происходит запуск IoC-контейнера Spring?

1. Подготовка - prepareRefresh()
   1.  Установка времени запуска: Фиксируется startupDate
   2. Активация флагов: Контекст помечается как «активный» (active = true)
   3. Инициализация PropertySources: Загружаются переменные окружения и системные свойства (Environment)
2. Создание и подготовка BeanFactory
   1. obtainFreshBeanFactory() - Создаёт или переиспользует внутреннюю DefaultListableBeanFactory, создает BeanDefinitions на основании XML или аннотаций
   2. prepareBeanFactory(beanFactory) - Настраивает стандартные бины: environment, systemProperties, systemEnvironment; Добавляет ApplicationContextAwareProcessor (чтобы бины могли получить ссылку на контекст); Регистрирует стандартные BeanPostProcessor'ы.
   3. postProcessBeanFactory(beanFactory) - Пустой метод — для переопределения в подклассах (например, в Spring Boot здесь регистрируются дополнительные процессоры)
   4. invokeBeanFactoryPostProcessors(beanFactory) - Вызываются все BeanFactoryPostProcessor — они могут менять определения бинов до их создания. Пример: ConfigurationClassPostProcessor парсит @Configuration, @Bean, @Import, @ComponentScan
3. Регистрация BeanPostProcessors - registerBeanPostProcessors(beanFactory). Spring находит и регистрирует классы, которые будут «перехватывать» процесс создания бинов позже. Это «контролеры», которые будут обрабатывать бины (например, оборачивать их в прокси для работы @Transactional или @Async).
4. Инициализация MessageSource и EventMulticaster
   1. initMessageSource(): Настройка интернационализации (i18n).
   2. initApplicationEventMulticaster(): Подготовка механизма событий. После этого этапа контекст готов рассылать уведомления другим компонентам.
5. OnRefresh (Специфика Web) - Этот метод пустой в стандартном контексте, но в Spring Boot или веб-приложениях именно здесь поднимается встроенный веб-сервер (Tomcat, Jetty или Netty).
6. Регистрация Listeners - registerListeners(). Регистрируются слушатели событий (бины, реализующие ApplicationListener).
7. Финализация: Инстанцирование бинов - finishBeanFactoryInitialization(beanFactory). Для каждого бина на основании BeanDefinition:
   1. Проверка зависимостей - Если бин зависит от других (depends-on) — сначала создаются они.
   2. Создание экземпляра - Выбор стратегии: через конструктор (обычно), фабричный метод, или через дефолтный конструктор (Зависимости не внедряются пока что)
   3. Заполнение свойств
      1. Внедрение зависимостей через поля (@Autowired), сеттеры или конфигурацию.
      2. Если зависимость ещё не создана — Spring создаёт её рекурсивно (если она singleton).
      3. Круговая зависимость: Spring может использовать раннюю ссылку (Early Bean Reference) через трёхуровневый кэш:
         1. Кэш 1: singletonObjects — полностью готовые бины.
         2. Кэш 2: earlySingletonObjects — недоделанные, но уже созданные объекты (без зависимостей).
         3. Кэш 3: singletonFactories — фабрики для получения ранней ссылки.
         4. Если A зависит от B, а B от A — Spring помещает A в кэш 3, создаёт B, внедряет туда A (раннюю ссылку), затем завершает A.
      4. Применение BeanPostProcessor перед инициализацией - postProcessBeforeInitialization - например, проверка @PostConstruct (оборачивается в InitDestroyAnnotationBeanPostProcessor).
      5. Инициализация бина
         1. Вызов @PostConstruct метода.
         2. Вызов afterPropertiesSet() от InitializingBean.
         3. Вызов init-method, заданного в конфигурации.
      6. Применение BeanPostProcessor после инициализации - postProcessAfterInitialization — здесь происходит AOP. Если бин должен быть обёрнут в прокси (например, @Transactional, @Cacheable), BeanPostProcessor (обычно AbstractAutoProxyCreator) создаёт прокси-объект вместо исходного.
      7. Регистрация готового бина - Готовый бин (или прокси) кладётся в singletonObjects (кэш 1).
8. Завершение refresh() - finishRefresh() - 
   1. Очищается кэш ResourceLoader; 
   2. Инициализируется LifecycleProcessor (вызывается start() для бинов, реализующих Lifecycle); 
   3. Публикуется событие ContextRefreshedEvent — все ApplicationListener его получают.

### Важные нюансы
1. Порядок создания бинов
 - Сначала создаются BeanFactoryPostProcessor и BeanPostProcessor (они нужны для создания остальных бинов).
 - Затем все не ленивые singleton-бины в порядке, определённом зависимостями.
 - @Lazy-бины создаются только при первом запросе getBean().
2. Роль BeanPostProcessor в AOP
 - Прокси AOP создаётся после инициализации бина. Это позволяет оригинальному бину жить внутри прокси. Пример: 
@Transactional — метод начинается/коммитится транзакцией, даже если вызван через прокси.
3. Круговая зависимость
 - Spring разрешает циклические зависимости для singleton-бинов (через трёхуровневый кэш).
 - Для prototype-бинов — бросает исключение.
 - Если используется внедрение через конструктор и есть цикл — тоже исключение (конструктор не позволяет внедрить раннюю ссылку).

[к оглавлению](#spring-core)


## Что такое bean scopes?

Scope определяет:
- Когда создаётся экземпляр бина
- Сколько экземпляров существует
- Как долго живёт бин
- Кто имеет доступ к этому экземпляру

1. Singleton: Один экземпляр на весь IoC-контейнер.
   1. Создаётся при старте контекста (если не @Lazy)
   2. Хранится в кэше singletonObjects
   3. Потокобезопасность нужно обеспечивать самостоятельно
   4. Все зависимости получают один и тот же экземпляр
2. Prototype: Новый экземпляр при каждом запросе.
   1. Создаётся только при вызове getBean() или внедрении
   2. Не управляется полностью контейнером после создания
   3. @PreDestroy не вызывается (Spring не следит за prototype-бинами)
   4. Нужно самостоятельно освобождать ресурсы
   5. Если внедрить prototype в singleton, то prototype "застывает" — всегда будет один экземпляр. Решение: использовать ObjectProvider
3. Request: Один экземпляр на HTTP-запрос.
   1. Создаётся при первом обращении в рамках HTTP-запроса
   2. Живёт пока обрабатывается запрос
   3. Разрушается после завершения запроса
   4. Требует прокси при внедрении в singleton
4. Session: Один экземпляр на HTTP-сессию.
   1. Создаётся при первом обращении в сессии
   2. Живёт пока активна сессия
   3. Уничтожается при инвалидации сессии
   4. Требует прокси при внедрении
5. Application: Один экземпляр на ServletContext (на всё веб-приложение). Отличие от singleton: В распределённых средах (несколько сервлет-контекстов) может быть несколько экземпляров.
6. Websocket: Один экземпляр на WebSocket-сессию.

Scoped Proxy Mode - свойство @Scope, создает прокси в необходимых случаях. Пример: Когда singleton-бин внедряет request/session-бин, возникает проблема - singleton создаётся один раз при старте, а request-бин должен быть разным для каждого запроса.

```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestData {
  private String requestId;
  // геттеры/сеттеры
}
```

[к оглавлению](#spring-core)


## Примеры использования bean scopes

Пример 1: Корзина покупок (session scope)
```java
@Component
@Scope(value = "session", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class ShoppingCart {
  private Map<Long, Integer> items = new HashMap<>();

  public void addItem(Long productId, int quantity) {
    items.merge(productId, quantity, Integer::sum);
  }

  public BigDecimal getTotal() {
    // расчёт суммы
  }
}

@RestController
public class CartController {
  @Autowired
  private ShoppingCart cart; // прокси для текущей сессии

  @PostMapping("/cart/add")
  public void addToCart(@RequestParam Long productId) {
    cart.addItem(productId, 1); // добавляется в корзину пользователя
  }
}
```

Пример 2: Логирование запроса (request scope)
```java
@Component
@Scope(value = "request", proxyMode = ScopedProxyMode.TARGET_CLASS)
public class RequestLogger {
  private String requestId;
  private List<String> messages = new ArrayList<>();

  public void log(String message) {
    messages.add(message);
    System.out.println("[" + requestId + "] " + message);
  }
}

@RestController
public class ApiController {
  @Autowired
  private RequestLogger logger;

  @GetMapping("/api/data")
  public Data getData() {
    logger.log("Fetching data");
    // логи будут привязаны к конкретному запросу
  }
}
```
Пример 3: Прототип для сложной бизнес-операции
```java
@Component
@Scope("prototype")
public class ComplexCalculation {
  private final Long operationId;
  private final Map<String, Object> intermediateResults = new HashMap<>();

  public ComplexCalculation() {
    this.operationId = System.nanoTime();
  }

  public Result calculate(Input input) {
    // сложная логика с сохранением промежуточных результатов
  }
}

@Service
public class CalculationService {
  @Autowired
  private ObjectProvider<ComplexCalculation> calculationProvider;

  public Result process(Input input) {
    ComplexCalculation calc = calculationProvider.getObject();
    return calc.calculate(input); // новый экземпляр для каждого вызова
  }
}
```

[к оглавлению](#spring-core)


## Аннотации @Configuration @Bean @Import @ComponentScan

1. @Configuration - помечает класс как источник определений бинов.
- Суть: Spring понимает, что внутри этого класса будут методы, создающие объекты.
- Особенность: Она не просто помечает класс, но и включает CGLIB-проксирование. Если вызовется один метод с @Bean внутри 
другого, Spring перехватит вызов и вернет уже существующий экземпляр (Singleton), а не создаст новый.

2. @Bean - ставится над методом внутри класса с @Configuration.
- Суть: Указывает, что результат выполнения этого метода должен быть зарегистрирован как бин в ApplicationContext.
- Когда использовать: Когда создается объект сторонней библиотеки (например, RestTemplate или DataSource), 
исходный код которой нельзя пометить аннотацией @Component.

3. @ComponentScan - указывает, в каких пакетах искать классы, помеченные как @Component, @Service, @Repository или @Controller.
- Суть: Это режим «автопоиска». Spring сканирует файлы, находит классы с нужными аннотациями и сам создает их экземпляры.
- По умолчанию: Если пакет не указан, сканирование начнется с того пакета, где находится сам класс с этой аннотацией.

4. @Import - Позволяет собирать конфигурацию из разных частей.
- Суть: Можно создать несколько конфигурационных классов (например, DbConfig, SecurityConfig) и подключить их к основной конфигурации.
- Зачем это нужно: Чтобы не раздувать один файл до гигантских размеров и логически разделять настройки приложения.

Пример того как они работают вместе:
```java
@Configuration
@ComponentScan("com.example.app") // Ищем @Component в этом пакете
@Import(ExternalLibraryConfig.class) // Подключаем другой конфиг
public class MainConfig {

    @Bean
    public ThirdPartyService thirdPartyService() {
        return new ThirdPartyService(); // Создаем вручную, если это не наш класс
    }
}
```

[к оглавлению](#spring-core)

## Аннотации @Autowired, @Resource и @Inject
Эти три аннотации делают одну и ту же работу — внедряют зависимости (Dependency Injection).

1. @Autowired (Spring)
- Логика поиска: Сначала ищет подходящий бин по типу (by type). Если типов несколько, пытается выбрать по имени поля/параметра.
- Особенность: По умолчанию зависимость обязательна. Если Spring не найдет бин, приложение упадет с ошибкой при старте. Это можно изменить через @Autowired(required = false).
- Где ставить: На поля, конструкторы или сеттеры.

2. @Inject (JSR-330)
- Логика поиска: Почти такая же, как у @Autowired — сначала по типу.
- Особенность: У неё нет параметра required. Если бин не найден — ошибка неизбежна. Чтобы сделать зависимость 
необязательной, придется использовать Optional или Provider.
- Зачем нужна: Чтобы код меньше зависел от конкретного фреймворка (Spring). Если перейти на Micronaut или Google Guice, @Inject продолжит работать.

3. @Resource (JSR-250)
- Логика поиска: Сначала ищет по имени (by name). Если по имени ничего не найдено, тогда ищет по типу.
- Особенность: Имя берется из названия поля или указывается явно: @Resource(name = "myService").
- Где ставить: Только на поля и сеттеры (на конструкторы нельзя).

[к оглавлению](#spring-core)


## Аннотации @Value, @ConfigurationProperties и @PropertySource

1. @Value - используется для внедрения внешних данных в поля бина. Она не ищет другие бины в контексте, а вытаскивает 
значения из конфигурационных файлов.
- Откуда берет данные: Из файлов application.properties, application.yml, переменных окружения или системных свойств.
- Синтаксис SpEL: Часто используется с выражением ${property.name}.
- Значения по умолчанию: Можно указать дефолтное значение через двоеточие: @Value("${app.timeout:1000}").

2. @ConfigurationProperties - Вместо того чтобы помечать каждое поле аннотацией @Value, можно создать отдельный класс, который отображает на себя целую ветку из application.yml или .properties.
- Типизация: Поддерживает вложенные объекты, списки (Lists) и карты (Maps).
- Валидация: Можно использовать @Validated и стандартные Bean Validation (например, @Min, @NotNull).
- Relaxed Binding: Понимает разные стили написания (например, my.property-name в конфиге привяжется к полю propertyName в Java).

3. @PropertySource - Добавляет указанный файл в Environment. После этого данные из него можно вытаскивать через ту же @Value или Environment.

[к оглавлению](#spring-core)


## Аннотации @Primary, @Qualifier и @Named

1. @Primary - помечает бин как «вариант по умолчанию».
- Суть: Если Spring видит несколько бинов одного типа, он выберет тот, над которым стоит @Primary.
- Когда использовать: Когда есть основная реализация (например, SqlDatabase) и вспомогательная для тестов или специфических 
случаев (InMemoryDatabase). Ставится @Primary на основную, чтобы везде по умолчанию использовалась она.

2. @Qualifier - способ указать «конкретный вариант» в месте внедрения.
- Суть: бину дается имя (метку) и при внедрении бина, @Qualifier над бином указывает Spring найти бин именно с этой меткой.
- Когда использовать: Когда нужно внедрить разные реализации одного интерфейса в разные места (например, в один 
сервис — FastEncoder, а в другой — SlowEncoder).

Как они работают вместе:

Если над одним бином стоит @Primary, но в коде написана @Autowired вместе с @Qualifier("otherBean"), 
Spring проигнорирует «главный» бин и внедрит именно otherBean. @Qualifier всегда имеет приоритет, потому что это явный выбор 
разработчика в конкретном месте.
```java
// Определение бинов
@Bean
@Primary
public Service mainService() { return new MainService(); }

@Bean
@Qualifier("special")
public Service specialService() { return new SpecialService(); }

// Внедрение
@Autowired
@Qualifier("special") // Внедрит specialService, несмотря на @Primary у другого
private Service service;
```

3. @Named (JSR-330) — стандартный Java-аналог спрингового @Component (если ставить над классом) и @Qualifier 
(если использовать при внедрении).
- Как замена @Component: Помечает класс как бин, который нужно положить в контекст.
- Как замена @Qualifier: Помогает Spring понять, какой именно бин внедрить, если их несколько одного типа.
```java
@Inject
@Named("fastEncoder") // Уточняем имя бина для внедрения
private Encoder encoder;
```

[к оглавлению](#spring-core)


## @Component и его стереотипные версии

@Component над классом является идентификатором для component scanning - чтобы во время сканирования из данного класса был 
создан бин и помещен в контекст. В Spring есть так называемые «стереотипные» аннотации. Это, по сути, тот же самый @Component, 
но с семантическим смыслом и дополнительными особенностями:

1. @Repository
- Роль: Слой доступа к данным (DAO).
- Особенность: Spring автоматически ловит исключения базы данных (например, SQL-ошибки) и переводит их в свои внутренние, более понятные DataAccessException.
2. @Service
- Роль: Бизнес-логика.
- Особенность: отсутствует, но она идеальна для вешания на неё @Transactional.
3. @Controller
- Роль: Обработка входящих запросов (чаще всего веб-запросы).
- Особенность: Позволяет методам класса маппиться на URL-адреса и возвращать View (HTML-страницы).
4. @RestController
- Роль: API в стиле REST (JSON/XML).
- Особенность: Это комбинация @Controller и @ResponseBody. Результат работы метода отправляется в тело ответа в виде JSON.

[к оглавлению](#spring-core)


## @Profile и аннотации группы @ConditionalOn...
@Profile — указывает, что бин (или конфиг) должен существовать только тогда, когда приложение запущено в определенном режиме.
- На классах: Над @Component или @Configuration. Если профиль не активен, Spring просто проигнорирует весь класс.
- На методах: Над @Bean внутри конфигурации. Позволяет создавать разные реализации одного интерфейса в зависимости от среды.

Включение профиля осуществляется следующими способами:
- В application.properties: spring.profiles.active=dev
- Через аргументы запуска: -Dspring.profiles.active=prod
- В тестах: С помощью аннотации @ActiveProfiles("test").

В Spring Boot профили работают на уровне файлов. Если создать файл application-dev.properties, он автоматически подгрузится 
в дополнение к основному, когда включен профиль dev. Это позволяет держать пароли от серверной БД в одном файле, а от локальной — в другом.

Более точечный выбор создания бинов осуществляется при помощи аннотаций @ConditionalOn...

1. @ConditionalOnProperty - Проверяет наличие или значение конкретного свойства в конфиге.
- Пример: Создавать бин, только если в application.yml написано feature.enabled=true.
- fallback: Можно указать, что делать, если свойства вообще нет (matchIfMissing = true).
2. @ConditionalOnClass / @ConditionalOnMissingClass - Проверяет, есть ли определенный класс в classpath (зависимостях).
- Суть: «Если в проекте лежит библиотека Jackson, то создай бин для работы с JSON». Если библиотеки нет — ошибки не будет, 
Spring просто проигнорирует этот бин.
3. @ConditionalOnBean / @ConditionalOnMissingBean - Проверяет, зарегистрирован ли уже такой бин в контексте.
- Очень важно для кастомизации: Часто используется в автоконфигурациях. Spring говорит: «Я создам этот бин по умолчанию, 
только если разработчик сам вручную не создал такой же». Это позволяет легко заменять стандартные компоненты своими.
4. @ConditionalOnWebApplication / @ConditionalOnNotWebApplication - Проверяет, является ли приложение веб-приложением (запущен ли сервер).
- Полезно для настройки специфических вещей вроде фильтров безопасности или контроллеров, которые не нужны в консольном приложении.
5. @ConditionalOnExpression - Позволяет написать условие на языке SpEL (Spring Expression Language).
- Пример: @ConditionalOnExpression("'${env}'.equals('prod') and ${mail.enabled:true}").

```java
@Configuration
public class SmsAutoConfiguration {

    @Bean
    // Создать, только если в конфиге есть sms.api-key
    @ConditionalOnProperty(name = "sms.api-key") 
    // И только если пользователь сам не создал свой сервис отправки
    @ConditionalOnMissingBean 
    public SmsService defaultSmsService() {
        return new DefaultSmsService();
    }
}
```

[к оглавлению](#spring-core)


## Тестирование Spring приложений

Spring предоставляет аннотации, специфичные для тестирования. Эти аннотации можно использовать в юнит-тестах, 
обеспечивая различные возможности, такие как упрощенная загрузка контекста, профилирование, 
измерение времени выполнения тестов и многое другое.

| Аннотация | Описание                                                                                                                                                                                                                                                  |
| ------------------- |-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| @BootstrapWith | Аннотация уровня класса, используемая для определения способа инициализации Spring TestContext                                                                                                                                                            |
| @ContextConfiguration | Аннотация на уровне класса используется для определения способа загрузки и настройки ApplicationContext для интеграционных тестов. При использовании JUnit Jupiter тестовые классы должны быть аннотированы с помощью @ExtendWith(SpringExtension.class). |
| @WebAppConfiguration | Аннотация уровня класса, используемая для указания того, что загруженный ApplicationContext должен быть WebApplicationContext.                                                                                                                            |
| @ContextHierarchy | Аннотация уровня класса, используемая для определения иерархии объектов ApplicationContext для интеграционных тестов                                                                                                                                      |
| @DirtiesContext | Аннотация на уровне класса, указывающая, какой профиль компонента должен быть активен.                                                                                                                                                                    |
| @TestPropertySource | Аннотация уровня класса, используемая для настройки расположения файлов свойств и встроенных свойств, которые будут добавлены в Environment набор PropertySources для ApplicationContext для интеграционных тестов.                                       |
| @DynamicPropertySource | Аннотация на уровне метода для интеграционных тестов, которым необходимо добавить свойства с динамическими значениями в Environment PropertySources.                                                                                                      |
| @TestExecutionListeners | Аннотация на уровне класса для настройки слушателей выполнения тестов (TestExecutionListeners), которые должны быть зарегистрированы в TestContextManager.                                                                                                |
| @RecordApplicationEvents | Аннотация уровня класса, используемая для записи всех событий приложения, публикуемых в ApplicationContext во время выполнения одного теста.                                                                                                              |
| @Commit | Аннотация на уровне класса и метода, используемая для указания того, что транзакция, управляемая тестом, должна быть зафиксирована после завершения выполнения тестового метода.                                                                          |
| @Rollback | Аннотация на уровне класса и метода, используемая для указания того, следует ли откатывать транзакцию, управляемую тестом, после завершения тестового метода. @Rollback(false) эквивалентна @Commit.                                                      |
| @BeforeTransaction | -                                                                                                                                                                                                                                                         |
| @AfterTransaction | -                                                                                                                                                                                                                                                         |
| @Sql | Аннотация на уровне класса и метода, используемая для настройки SQL-скриптов и операторов, которые будут выполняться в отношении данной базы данных во время интеграционных тестов                                                                        |
| @SqlConfig | Аннотация уровня класса и метода, используемая для указания способа анализа и выполнения SQL-скриптов, настроенных с помощью аннотации @Sql.                                                                                                              |
| @SqlMergeMode | Аннотация уровня класса и метода, используемая для указания того, объединяются ли объявления @Sql на уровне метода с объявлениями @Sql на уровне класса.                                                                                                  |
| @SqlGroup | -                                                                                                                                                                                                                                                         |
| @IfProfileValue | Аннотация на уровне класса и метода, используемая для указания того, что тестовый метод должен быть включен для определенного набора условий окружающей среды.                                                                                            |
| @ProfileValueSourceConfiguration | -                                                                                                                                                                                                                                                         |
| @Timed | Аннотация на уровне метода, используемая для указания того, что тест должен завершиться в указанный период времени.                                                                                                                                                                                                                                       |
| @Repeat | Аннотация на уровне метода, используемая для указания того, что аннотированный тестовый метод следует повторить указанное количество раз.                                                                                                                                                                                                                                       |


[к оглавлению](#spring-core)

## Что такое `BeanPostProcessor`?

Интерфейс `BeanPostProcessor` позволяет вклиниться в процесс настройки ваших бинов до того, как они попадут в контейнер. 
Интерфейс несет в себе несколько методов.

````java
public interface BeanPostProcessor {
    Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException;

    Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException;
}
````

Оба метода вызываются для каждого бина. У обоих методов параметры абсолютно одинаковые. Разница только в порядке их вызова. 
Первый вызывается до init-метода, второй, после. Важно понимать, что на данном этапе экземпляр бина уже создан и идет его 
дополнительная настройка. Тут есть два важных момента:

+ Оба метода в итоге должны вернуть бин. Если в методе вы вернете null, то при получении этого бина из контекста вы 
получите null, а поскольку через `BeanPostProcessor` проходят все бины, после поднятия контекста, при запросе любого бина 
вы будете получать фиг, в смысле null.
+ Если вы хотите сделать прокси над вашим объектом, то имейте ввиду, что это принято делать после вызова init метода, 
иначе говоря это нужно делать в методе `postProcessAfterInitialization`.

Порядок в котором будут вызваны BeanPostProcessor не известен, но мы точно знаем что выполнены они будут последовательно.

[к оглавлению](#spring)






## В чем разница между Сквозной Функциональностью (Cross Cutting Concerns) и АОП (аспектно ориентированное программирование)?

Сквозная Функциональность — функциональность, которая может потребоваться вам на нескольких различных уровнях —
логирование, управление производительностью, безопасность и т.д.
АОП — один из подходов к реализации данной проблемы

[к оглавлению](#spring)

## Почему возвращаемое значение при применении аспекта `@Around` может потеряться? Назовите причины

Метод, помеченный аннотацией @Around, должен возвращать значение, которое он (метод) получил из joinpoint.proceed()

````java
public class Main {
    @Around("trackTimeAnnotation()")
    public Object around(ProceedingJoinPoint joinPoint) throws Throwable {
        long startTime = System.currentTimeMillis();
        Object retVal = joinPoint.proceed();
        long timeTaken = System.currentTimeMillis() - startTime;
        logger.info("Time taken by {} is equal to {}", joinPoint, timeTaken);
        return retVal;
    }
}
````

[к оглавлению](#spring)



## В чем разница между Filters, Listeners and Interceptors?

Концептуально всё просто, фильтры сервлетов могут перехватывать только HTTPServlets. Listeners могут перехватывать специфические события. Как перехватить события которые относятся ни к тем не другим?

Фильтры и перехватчики делают по сути одно и тоже: они перехватывают какое-то событие, и делают что-то до или после.
Java EE использует термин Filter, Spring называет их Interceptors. Именно здесь AOP используется в полную силу,
благодаря чему возможно перехватывание вызовов любых объектов.

[к оглавлению](#spring)

## В чем разница между `ModelMap` и `ModelAndView`?

Model — интерфейс, ModelMap его реализация. ModelAndView является контейнером для пары, как ModelMap и View. Обычно я
люблю использовать ModelAndView. Однако есть так же способ когда мы задаем необходимые атрибуты в ModelMap, и возвращаем
название View обычной строкой из метода контроллера.

[к оглавлению](#spring)

## В чем разница между `model.put()` и `model.addAttribute()`?

Метод addAttribute отделяет нас от работы с базовой структурой hashmap. По сути addAttribute это обертка над put, где делается дополнительная проверка на null. Метод addAttribute в отличие от put возвращает modelmap.
```java

public class Controller {

    @PostMapping()
    public Model get() {
        Model model = new Model();
        model.addAttribute(attribute1,value1).addAttribute(attribute2,value2);
        return model; 
    }
}
```

[к оглавлению](#spring)

## Что можете рассказать про Form Binding?

Нам это может понадобиться, если мы, например, захотим взять некоторое значение с HTML страницы и сохранить его в БД.
Для этого нам надо это значение переместить в контроллер Spring. Если мы будем использовать Spring MVC form tags, Spring
автоматически свяжет переменные на HTML странице с бином Spring.

[к оглавлению](#spring)

## Почему мы используем Hibernate Validator?

Hibernate Validator никак не связан с БД. Это просто библиотека для валидации. Hibernate Validator версии 5.x является
эталонной реализацией Bean Validation 1.1

Так же если взглянуть по адресу <http://beanvalidation.org/2.0>, то Hibernate Validator является единственным, который
сертифицирован.

[к оглавлению](#spring)

## Где должны располагаться статические (css, js, html) ресурсы в Spring MVC приложении?

Расположение статических ресурсов можно настроить. В документации Spring Boot рекомендуется использовать /static, или
/public, или /resources, или /META-INF/resources.

[к оглавлению](#spring-mvc)

## Можно ли передать в запросе один и тот же параметр несколько раз?

Пример: `http://localhost:8080/login?name=Ranga&name=Ravi&name=Sathish`
Да, можно принять все значения, используя массив в методе контроллера

````java
public String method(@RequestParam(value="name") String[] names){
}
````

[к оглавлению](#spring)

# Источники

+ [Википедия](https://ru.wikipedia.org/)
+ [Хабрахабр](https://habr.com/ru/post/222579/)

[Вопросы для собеседования](README.md)
