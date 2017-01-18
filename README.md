# Почему мы используем Ruby и Ruby on Rails?

## 1) Понятный и компактный синтаксис

Синтаксис Ruby — это машинопонятный английский язык. Человек с базовыми знаниями английского языка сможет без проблем понять, _что_ делает код, не разбираясь в каждой синтаксической конструкции отдельно.

Код опытного программиста на Ruby — _самодокументирующийся_.

Делать код читаемым позволяет полностью объектно-ориентированная архитектура языка, позволяющая создавать и использовать человекопонятные _методы-помощники_(`.month`, `.weeks`, `.between?` и др. в примерах ниже).

### Примеры

1. Обработка времени

  **Ruby**
  ```ruby
  start_time = Time.now - 1.month
  end_time = Time.now + 2.weeks
  Time.now.between?(start_time, end_time)
  # "?" - в конце обозначает, что метод возвращает истину(true) или ложь(false)
  ```
  **PHP**
  ```php
  <?php
  $start_time = time() - 3600 * 24 * 30;
  $end_time = time() + 3600 * 24 * 14;
  if (time() > $start_time && time() < $end_time) {
    return true;
  } else {
    return false;
  }
  ```

2. Объявление и изменение атрибутов объекта

  **Ruby**
  ```ruby
  class Person
    attr_accessor :name, :age
  end

  santa_clause = Person.new(name: 'Santa Clause', age: 74)
  santa_clause.age += 1 if Time.now > Time.parse('2016-07-28 00:00:00')

  puts santa_clause.age
  # => 75
  ```

  **PHP**
  ```php
  <?php
  class Person {
    private $name;
    private $age;

    public function __get($property) {
      if (property_exists($this, $property)) {
        return $this->$property;
      }
    }

    public function __set($property, $value) {
      if (property_exists($this, $property)) {
        $this->$property = $value;
      }

      return $this;
    }
  }

  $santa_clause = new Person;
  $santa_clause->name = 'Santa Clause';
  $santa_clause->age = 74;
  if (time() > date("H:i:s", strtotime('2016-07-28 00:00:00'))) {
    $santa_clause->age = $santa_clause->age + 1;
  }

  echo $santa_clause->age
  // => 75
  ```

## 2) Безопасность

Ruby on Rails имеет встроенные механизмы защиты от большинства известных типов атак на веб-приложения:

  * SQL инъекции

    При использовании встроенных в Ruby on Rails способов взаимодействия с базой данных, все параметры автоматически экранируются и приводятся к нужному типу
    ```ruby
    Product.where(name: "' OR '1'='1").to_sql
    ```
    ```sql
    SELECT 'products'.* FROM 'products' WHERE 'products'.'name' = "'' OR ''1''=''1'"
    ```

  * XSS атаки

    Любой динамический контент считается HTML-небезопасным и все символы в нём экранируются, если разработчик напрямую не укажет обратного
  * Загрузка и исполнение вредоносных файлов

    Все загруженные пользователями файлы находятся в отдельной директории, в которой все файлы по умолчанию не исполняемые
  * CSRF атаки

    Фреймворк использует систему [CSRF-токенов](https://ru.wikipedia.org/wiki/%D0%9C%D0%B5%D0%B6%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D0%BF%D0%BE%D0%B4%D0%B4%D0%B5%D0%BB%D0%BA%D0%B0_%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%B0) для защиты от атак данного типа
  * Подмены куки

    В RoR есть встроенные механизмы _подписи_ и _шифрования_ файлов куки для защиты от подмены и чтения соответственно.

Данные меры позволяют избежать случайных ошибок, приводящих к большим проблемам в безопасности.

Современные PHP-фреймворки имеют аналогичные механизмы защиты, но RoR — инструмент проверенный временем(_фреймворк существует более 11 лет_) и крупными проектами([GitHub](https://github.com/),  [Airbnb](https://www.airbnb.com/), [Twitch](https://www.twitch.tv/))

## 3) Возможности для расширения функционала языка и создания DSL*
\* англ. Domain-specific language, DSL — «язык, специфический для предметной области»

Создание DSL позволяет абстрагироваться от деталей языка программирования для решения конкретной задачи(маршрутизация запросов, миграции базы данных).

### Примеры

* Маршрутизация запросов

  **Ruby on Rails**
  ```ruby
  root 'pages#show'
  get 'search/:q', to: 'pages#search', as: :search
  get 'information', to: redirect('information/shops')

  namespace :admin do
    get '/', to: redirect('admin/static')

    resources :static, except: [:show]
  end

  get '*path', to: 'pages#show'
  ```
  **PHP**

  В стандартном подходе PHP используте маршрутизацию с помощью файлов `.htacces`  ([пример](https://github.com/WebCreatorRu/why-we-prefer-ruby/blob/master/examples/.htaccess))

  Современные фреймворки используют чуть более многословную Rails-подобную маршрутизацию

  ```php
  // Laravel routes.php
  <?php
  Route::group(['prefix' => '/', 'middleware' => ['marketing', 'actionpay']], function () {
    Route::get('/{gender?}', ['as' => 'page.index', 'uses' => 'PageController@index'])->where('gender', '(men|women)');

    Route::get('admin', ['as' => 'admin.index', 'uses' => 'AdminController@index', 'middleware' => 'admin']);
    Route::get('admin/login', ['as' => 'admin.login.form', 'uses' => 'AdminController@login']);
    Route::post('admin/login', ['as' => 'admin.login', 'uses' => 'Admin\AuthController@login']);
    Route::get('admin/logout', ['as' => 'admin.logout', 'uses' => 'Admin\AuthController@logout']);
  });

  ```

* Миграции базы данных

  **Ruby on Rails**
  ```ruby
  # Создание таблицы
  def change
    create_table :products do |t|
      t.integer :category_id
      t.string :name
      t.string :collection

      t.jsonb :metadata, null: false, default: {}
      t.timestamps
    end

    # Добавление индекса
    add_index :products, :metadata, using: :gin

    # Переименование колонки
    rename_column :pages, :main_object_kind, :main_object_type

    # Изменение установок колонки
    change_column :items, :price, null: false, default: 0
  end
  ```

  **PHP**

  В PHP проектах часто механизмы миграций отсутствуют. Миграции производятся вручную или с помощью SQL-файлов. Современные фреймворки имеют схожие с Rails механизмы миграции.

* Расширение функционала языка

  Ruby on Rails добавляет набор полезных функций в стандартные классы Ruby

  ```ruby
  # Получение объекта времени из строки
  '2016-07-28 00:00:00'.to_time # => 2016-07-28 00:00:00 +0300

  # "Очистка" строки от пробельных символов
  " \n  foo\n\r \t bar \n".squish # => "foo bar"

  # Преобразование хеш-таблицы в XML
  {"foo" => 1, "bar" => 2}.to_xml
  # =>
  # <?xml version="1.0" encoding="UTF-8"?>
  # <hash>
  #   <foo type="integer">1</foo>
  #   <bar type="integer">2</bar>
  # </hash>

  # Сумма элементов в массиве
  [1, 2, 3].sum # => 6

  # Получение значений элементов массива по ключу
  [{ name: "David" }, { name: "Rafael" }, { name: "Aaron" }].pluck(:name)
  # => ["David", "Rafael", "Aaron"]
  ```

## 4) Активное сообщество

В экосистеме Ruby существуют удобные инструменты: менеджер пакетов Ruby Gems и менеджер зависимостей [Bundler](https://github.com/bundler/bundler). Они позволяют безболезненно подключать сторонние библиотеки к своим проектам.

Благодаря этим инструментам и активному сообществу для Ruby есть много надёжных библиотек, решающих типовые задачи:
  * [Работа с JSON](https://github.com/flori/json)
  * [Хеширование паролей](https://github.com/pbhogan/scrypt)
  * Адаптеры баз данных [PostgreSQL](https://github.com/ged/ruby-pg), [MySQL](https://github.com/brianmario/mysql2)
  * [Парсинг XML и HTML](https://github.com/sparklemotion/nokogiri)
  * [Древовидные структуры в SQL(nested sets)](https://github.com/collectiveidea/awesome_nested_set)
  * [Поддержка русского языка](https://github.com/yaroslav/russian)

## License

This work is licensed under a
[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International Public License](https://creativecommons.org/licenses/by-nc-sa/4.0/)

© 2017, Web Creator
