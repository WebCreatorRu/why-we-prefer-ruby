# Почему мы используем Ruby и Ruby on Rails?

## 1) Понятный и компактный синтаксис

Синтаксис Ruby — это машинопонятный английский язык. Человек с базовыми знаниями английского языка сможет без проблем понять, _что_ делает код, не разбираясь в каждой синтаксической конструкции отдельно.

Код опытного программиста на Ruby — _самодокументируюшийся_.

Делать код читаемым позволяет полностью объектно-ориентированная архитектура языка, позволяющая создавать и использовать человекопонятные _методы-помощники_(`.month`, `.weeks`, `.between?` и др. в примерах ниже).

### Примеры

1. Обработка времени

  **Ruby**
  ```ruby
  start_time = Time.now - 1.month
  end_time = Time.now + 2.weeks
  Time.now.between?(start_time, end_time) # "?" - в конце обозначает, что метод возвращает ложь(false) или истину(true)
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
    attr_accessor :name
    attr_accessor :age
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

    При использовании встроенных в Ruby on Rails способов взаимодействия с базой данных, все параметры автоматически экарнируются и приводятся к нужному типу
    ```ruby
    Product.where(name: "' OR '1'='1").to_sql
    ```
    ```sql
    SELECT 'products'.* FROM 'products' WHERE 'products'.'name' = "'' OR ''1''=''1'"
    ```

  * XSS атаки

    Любой динамический контент считается HTML-небезопасным и все символы в нём экранируются, если разработчик напрямую не укажет обратного
  * Загрузка и исполнение вредоносных файлов

    Все загруженны пользователями файлы находятся в отдельной директории, в которой все файлы по умолчанию не исполняемые
  * CSRF атаки

    Фреймворк использует систему [CSRF-токенов](https://ru.wikipedia.org/wiki/%D0%9C%D0%B5%D0%B6%D1%81%D0%B0%D0%B9%D1%82%D0%BE%D0%B2%D0%B0%D1%8F_%D0%BF%D0%BE%D0%B4%D0%B4%D0%B5%D0%BB%D0%BA%D0%B0_%D0%B7%D0%B0%D0%BF%D1%80%D0%BE%D1%81%D0%B0) для защиты от атак данного типа
  * Подмены куки

    В RoR есть встроенные механизмы _подписи_ и _шифрования_ файлов куки для защиты от подмены и чтения соответственно.

Данные меры позволяют избежать случайных ошибок, приводящих к большим проблемам в безопасности.

Современные PHP-фреймворки имеют аналогичные механизмы защиты, но RoR — интрумент проверенный временем(_фреймворк существует больше 11 лет_) и крупными проектами([GitHub](https://github.com/),  [Airbnb](https://www.airbnb.com/), [Twitch](https://www.twitch.tv/))
