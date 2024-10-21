# Тестовое задание для Корсофт
[Java-бэкэнд разработчик](https://hh.ru/vacancy/108438531)  
[Требования](https://docs.google.com/document/d/15phWRzZN4yx0eu3c5yno0W6R7r1SHQ4y/edit)

# Описание решения
Решение состоит из двух микросервисов [UserManagementAPI](https://github.com/DenisKonev/UserManagementAPI) и [RoleManagementAPI
](https://github.com/DenisKonev/RoleManagementAPI)  
Для того чтобы протестировать его нужно запустить их оба. Как это сделать описано в ридми каждого из сервисов.

# High-Level Design (HLD)

## Общий обзор

Система будет построена на основе микросервисной архитектуры и будет состоять из двух независимых сервисов:

1. **Микросервис "Пользователи"**
2. **Микросервис "Роли"**

Каждый микросервис будет иметь свою собственную базу данных PostgreSQL и будет взаимодействовать с другими сервисами через REST API.

## Архитектура микросервисов

### 1. Микросервис "Пользователи"

- **Ответственность:**
    - Управление пользователями и их данными.
    - Назначение ролей пользователям.
    - Предоставление REST API для CRUD операций над пользователями и управления их ролями.

- **Структура:**

    - **Контроллеры (Controllers):**
        - Обрабатывают HTTP-запросы к API.
        - **Эндпоинты:**
            - `GET /api/v1/users` — получение списка всех пользователей.
            - `GET /api/v1/users/{id}` — получение пользователя по ID.
            - `POST /api/v1/users` — создание нового пользователя.
            - `PUT /api/v1/users/{id}` — обновление пользователя по ID.
            - `DELETE /api/v1/users/{id}` — удаление пользователя по ID.
            - `GET /api/v1/users/{userId}/roles` — получение списка ролей пользователя.
            - `POST /api/v1/users/{userId}/roles/{roleId}` — назначение роли пользователю.
            - `DELETE /api/v1/users/{userId}/roles/{roleId}` — удаление роли у пользователя.

    - **Сервисный слой (Services):**
        - Содержит бизнес-логику для управления пользователями и их ролями.
        - Обеспечивает валидацию данных, шифрование паролей и взаимодействие с микросервисом "Роли".

    - **Репозитории (Repositories):**
        - Используют Spring Data JPA для взаимодействия с базой данных PostgreSQL.
        - Управляют сущностями `User` и `UserRole`.

- **База данных:**

    - **Схема данных:**
        - **Таблица `User`**
            - `id` (Long)
            - `username` (String)
            - `email` (String)
            - `password` (String)
            - `createdAt` (LocalDateTime)
            - `updatedAt` (LocalDateTime)
        - **Таблица `UserRole`**
            - `userId` (Long)
            - `roleId` (Long)

    - **Особенности:**
        - Отдельная база данных для обеспечения независимости и безопасности данных.

- **Безопасность:**
    - Использование Spring Security для базовой аутентификации.
    - Шифрование паролей с помощью `BCryptPasswordEncoder`.

- **Логирование:**
    - Настройка логирования с помощью SLF4J и Logback.
    - Логирование входящих запросов и исходящих ответов.

- **Документация API:**
    - Интеграция Swagger (Springfox или OpenAPI) для автоматической генерации документации.

### 2. Микросервис "Роли"

- **Ответственность:**
    - Управление ролями пользователей.
    - Предоставление REST API для CRUD операций над ролями.

- **Структура:**

    - **Контроллеры (Controllers):**
        - **Эндпоинты:**
            - `GET /api/v1/roles` — получение списка всех ролей.
            - `GET /api/v1/roles/{id}` — получение роли по ID.
            - `POST /api/v1/roles` — создание новой роли.
            - `PUT /api/v1/roles/{id}` — обновление роли по ID.
            - `DELETE /api/v1/roles/{id}` — удаление роли по ID.

    - **Сервисный слой (Services):**
        - Содержит бизнес-логику для управления ролями.
        - Обеспечивает валидацию данных (уникальность названия роли).

    - **Репозитории (Repositories):**
        - Используют Spring Data JPA для взаимодействия с базой данных PostgreSQL.
        - Управляют сущностью `Role`.

- **База данных:**

    - **Схема данных:**
        - **Таблица `Role`**
            - `id` (Long)
            - `name` (String)
            - `description` (String)

    - **Особенности:**
        - Отдельная база данных для обеспечения независимости и безопасности данных.

- **Безопасность:**
    - Использование Spring Security для базовой аутентификации.

- **Логирование:**
    - Настройка логирования с помощью SLF4J и Logback.

- **Документация API:**
    - Интеграция Swagger для автоматической генерации документации.

## Взаимодействие между микросервисами

- **REST API вызовы:**
    - Микросервис "Пользователи" взаимодействует с микросервисом "Роли" через REST API.
    - При назначении роли пользователю, сервис "Пользователи" делает HTTP-запрос к сервису "Роли" для проверки существования роли.

- **Обработка ошибок:**
    - Обработка сценариев, когда роль не найдена или сервис "Роли" недоступен.
    - Реализация тайм-аутов и повторных попыток при неудачных запросах.

## Данные и их хранение

- **Изоляция данных:**
    - Каждому микросервису соответствует своя база данных.
    - Обеспечивает независимость сервисов и упрощает масштабирование.

- **Связь данных:**
    - Связь между пользователями и ролями осуществляется через внешние ключи и таблицу `UserRole` в базе данных микросервиса "Пользователи".

## Слои приложения

- **Контроллеры (Controllers):**
    - Отвечают за обработку входящих HTTP-запросов.
    - Возвращают соответствующие HTTP-ответы.

- **Сервисы (Services):**
    - Содержат бизнес-логику.
    - Обрабатывают данные и выполняют операции над ними.

- **Репозитории (Repositories):**
    - Предоставляют абстракцию для доступа к базе данных.
    - Используют ORM для работы с сущностями.

## Тестирование

- **Unit-тесты:**
    - Покрывают сервисный слой.
    - Используют JUnit и Mockito для создания моков и проверки логики.

- **Интеграционные тесты:**
    - Тестируют REST API эндпоинты.
    - Используют Spring Boot Test и MockMvc для имитации HTTP-запросов.

## Безопасность

- **Аутентификация:**
    - Реализация базовой аутентификации с использованием Spring Security.
    - Требует предоставления учетных данных для доступа к API.

- **Авторизация:**
    - Возможность расширения для реализации ролевой модели доступа.

## Логирование

- **Технологии:**
    - SLF4J в связке с Logback или Log4j2.

- **Практики:**
    - Логирование уровня INFO для общих событий.
    - Логирование уровня ERROR для исключений и ошибок.
    - Возможность настройки формата и уровня детализации логов.

## Документация API

- **Swagger/OpenAPI:**
    - Автоматическая генерация документации на основе аннотаций.
    - Предоставление интерактивного интерфейса для тестирования API.

## Архитектурные решения

- **Микросервисная архитектура:**
    - **Преимущества:**
        - Независимое развертывание и масштабирование сервисов.
        - Улучшенная отказоустойчивость системы.
        - Разделение ответственности между командами (при масштабировании проекта).

    - **Выбор обоснован:**
        - По заданию требуется микросервисная архитектура.
        - Облегчает понимание и поддержку кода.

- **Взаимодействие через REST API:**
    - **Преимущества:**
        - Стандартизованный протокол взаимодействия.
        - Простота реализации и отладки.

    - **Особенности:**
        - Необходимо учитывать сетевые задержки и возможные сбои в коммуникации.

- **Отдельные базы данных:**
    - **Преимущества:**
        - Улучшенная безопасность и контроль доступа к данным.
        - Возможность выбора разных технологий хранения данных в будущем.
        - Изоляция проблем с данными внутри одного сервиса.

## Технологический стек

- **Язык программирования:** Java
- **Фреймворк:** Spring Boot
- **Система сборки:** Gradle
- **База данных:** PostgreSQL
- **Безопасность:** Spring Security
- **Тестирование:** JUnit, Mockito, Spring Boot Test
- **Логирование:** SLF4J, Logback
- **Документация API:** Swagger (SpringDoc)

## Дополнительные соображения

## Дополнительные соображения

- **Масштабируемость:**
    - Возможность горизонтального масштабирования микросервисов.
    - Использование контейнеризации (например, Docker) для упрощения развертывания.

- **Управление конфигурацией:**
    - Использование Spring Cloud Config (в перспективе) для централизованного управления настройками.

- **Мониторинг и трассировка:**
    - Внедрение инструментов для мониторинга производительности и логирования запросов между сервисами (в рамках будущих улучшений).

