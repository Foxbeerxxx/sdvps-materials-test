# Домашнее задание к занятию "`СУБД`" - `Татаринцев Алексей`



### Задание 1

`Крупная строительная компания, которая также занимается проектированием и девелопментом, решила создать правильную архитектуру для работы с данными. Ниже представлены задачи, которые необходимо решить для каждой предметной области.`

1. `1.1. Бюджетирование проектов с дальнейшим формированием финансовых аналитических отчётов и прогнозирования рисков. СУБД должна гарантировать целостность и чёткую структуру данных`

Подходящая СУБД: Реляционная СУБД , такая как PostgreSQL или Microsoft SQL Server , так как ACID-транзакции: гарантируют целостность данных при финансовых операциях, мощный SQL- позволяет выполнять сложные аналитические запросы, необходимые для финансового анализа.Хорошая поддержка инструментов BI: Легко интегрируются с BI системами для создания отчетов и дашбордов

`1.1.* Хеширование стало занимать длительно время, какое API можно использовать для ускорения работы?`
Использование специализированных библиотек для хеширования или аппаратного ускорения 

`1.2. Под каждый девелоперский проект создаётся отдельный лендинг, и все данные по лидам стекаются в CRM к маркетологам и менеджерам по продажам. Какой тип СУБД лучше использовать для лендингов и для CRM? СУБД должны быть гибкими и быстрыми.`
`1.2.* Можно ли эту задачу закрыть одной СУБД? И если да, то какой именно СУБД и какой реализацией?`

Задачи требуют гибкости и высокой скорости работы с неструктурированными данными. Лендинги генерируют множество временных данных, а CRM нуждается в гибких запросах к данным о клиентах
Для лендингов:
	`Документоориентированные СУБД: MongoDB`
	`СУБД: Redis - Высокая производительность и поддержка временных данных`
Для CRM:
	Реляционные СУБД: PostgreSQL, MySQL. CRM требует структурированных данных о клиентах и связей между сущностями (клиентами, заказами, проектами).`
Эти две задачи можно закрыть одной мультимодельной СУБД, например:`
PostgreSQL: поддерживает реляционное хранение и JSON-документы, что позволит объединить лендинги (через JSON) и CRM-данные (в реляционной структуре) в одной базе.

`1.3. Отдел контроля качества решил создать базу по корпоративным нормам и правилам, обучающему материалу и так далее, сформированную согласно структуре компании. СУБД должна иметь простую и понятную структуру.`
`1.3.* Можно ли под эту задачу использовать уже существующую СУБД из задач выше и если да, то как лучше это реализовать?`

Задача предполагает хранение структурированного обучающего материала и регламентов. При этом простота структуры важнее производительности.
- Реляционные СУБД (PostgreSQL, MySQL):
  - Обеспечивают структурированность.
  - Удобны для текстовой информации.
- Если структура данных будет изменяться активно, возможна документоориентированная СУБД: CouchDB или MongoDB (например, для удобного хранения документов).
Да, для этой задачи можно использовать СУБД, настроенную под CRM (PostgreSQL или другую реляционную), создав отдельные таблицы или схемы для хранения нормативных материалов.

`1.4. Департамент логистики нуждается в решении задач по быстрому формированию маршрутов доставки материалов по объектам и распределению курьеров по маршрутам с доставкой документов. СУБД должна уметь быстро работать со связями.`
`1.4.* Можно ли к этой СУБД подключить отдел закупок или для них лучше сформировать свою СУБД в связке с СУБД логистов?`

Работа с маршрутами доставки и связями предполагает высокую производительность при обработке городов, точек доставки.
- Графовые СУБД (Graph DB):
- Идеальны для работы со связями, маршрутами и оптимизацией логистики.
- Если добавляются геопространственные данные:
- PostGIS (расширение для PostgreSQL) — поддерживает расчёт расстояний, координат, маршрутов.
Закупки тесно связаны с логистикой, поэтому можно подключить их к СУБД логистов, добавив новые узлы/связи в единую графовую модель. Если закупки требуют иной структуры данных (например, учёта поставщиков и контрактов), можно использовать реляционную СУБД с интеграцией в графовую через API.

`1.5.* Можно ли все перечисленные выше задачи решить, используя одну СУБД? Если да, то какую именно?`
Теоретически, можно использовать одну мультимодельную СУБД, поддерживающую реляционные, документоориентированные и графовые модели данных, например:
- ArangoDB: сочетает графовое, документоориентированное и реляционное хранение в одной базе. Подходит для всех задач (финансовая аналитика, лендинги, маршруты, корпоративные нормы).
- PostgreSQL: универсальная СУБД, расширения которой (PostGIS для маршрутов, JSON для лендингов) позволяют покрыть все задачи, однако графовые задачи обрабатываются не так эффективно, как в специализированной СУБД.
Хотя использование одной СУБД теоретически возможно, на практике лучше использовать специализированные базы под каждую задачу:
1. PostgreSQL/MySQL для финансов, CRM и корпоративных норм.
2. MongoDB/Redis для лендингов.
3. Neo4j/TigerGraph для маршрутов доставки.
Интеграция между системами может быть организована через API 

### Задание 2
2. `Транзакции`

`2.1. Пользователь пополняет баланс счёта телефона, распишите пошагово, какие действия должны произойти для того, чтобы транзакция завершилась успешно. Ориентируйтесь на шесть действий.`
`2.1.* Какие действия должны произойти, если пополнение счёта телефона происходило бы через автоплатёж?`
Пополнение баланса телефона (6 шагов):

1. Инициация транзакции: Пользователь вводит номер телефона и сумму пополнения в приложении (или на сайте). Нажимает кнопку "Пополнить".
2. Аутентификация и авторизация: Приложение отправляет запрос на сервер, содержащий данные пользователя, номер телефона, сумму и информацию о платежном методе (например, данные карты или привязанного счета). Сервер проверяет подлинность пользователя (аутентификация) и его право на выполнение данной операции (авторизация).
3. Резервирование средств: Сервер отправляет запрос в банк или платежную систему, чтобы зарезервировать указанную сумму на счету пользователя. Происходит проверка достаточности средств на счете. Платежная система блокирует необходимую сумму для совершения платежа.
4. Обработка платежа: Если резервирование средств прошло успешно, платежная система переводит деньги оператору связи. Это может происходить напрямую или через промежуточного агента (например, агрегатора платежей).
5. Обновление баланса телефона: Оператор связи получает информацию об успешном платеже и пополняет баланс указанного номера телефона.
6. Подтверждение транзакции: Сервер отправляет подтверждение об успешном пополнении баланса пользователю. Приложение отображает подтверждение пополнения баланса. Пользователь получает уведомление об успешном пополнении (например, SMS).

В случае с автоплатежом, процесс будет схожим, но с некоторыми отличиями в инициации и авторизации.

1. Триггер автоплатежа: Наступает событие, которое запускает автоплатеж (например, достижение баланса на телефоне ниже определенного уровня или наступление определенной даты). Это может быть системное событие или проверка по заданному расписанию на сервере оператора или в банке.
2. Авторизация транзакции: Сервер получает команду на выполнение платежа на основе ранее сохраненных данных об автоплатеже (номер телефона, сумма, платежные данные). Проверяется валидность этих данных и статус автоплатежа. Не требуется непосредственное участие пользователя в этот момент.
3. Резервирование средств: Сервер отправляет запрос в банк или платежную систему, чтобы зарезервировать указанную сумму на счету пользователя. Происходит проверка достаточности средств на счете. Платежная система блокирует необходимую сумму для совершения платежа.
4. Обработка платежа: Если резервирование средств прошло успешно, платежная система переводит деньги оператору связи.
5. Обновление баланса телефона: Оператор связи получает информацию об успешном платеже и пополняет баланс указанного номера телефона.
6. Подтверждение транзакции: Сервер отправляет подтверждение об успешном пополнении баланса пользователю (например, SMS). Также, может быть отправлено подтверждение в приложение, если оно используется.

### Задание 3
3. `SQL vs NoSQL`
`3.1. Напишите пять преимуществ SQL-систем по отношению к NoSQL.`
`3.1.* Какие, на ваш взгляд, преимущества у NewSQL систем перед SQL и NoSQL.`

SQL-системы, как правило, основаны на реляционной модели данных и обладают рядом преимуществ, которые делают их подходящими для многих задач. Вот пять ключевых преимуществ по сравнению с NoSQL

1. ACID-транзакции: SQL-системы, как правило, обеспечивают ACID-свойства (Atomicity, Consistency, Isolation, Durability) транзакций, NoSQL часто жертвует полнотой ACID ради масштабируемости и производительности..
2. Строгая схема: SQL-базы данных требуют определения строгой схемы, которая задает структуру данных (таблицы, столбцы, типы данных, ограничения). NoSQL-системы, особенно документоориентированные, часто имеют гибкую или даже бессхемную структуру, что может привести к проблемам в долгосрочной перспективе и затруднить совместную работу.
3. Зрелость и развитость инструментов: SQL-технологии существуют уже несколько десятилетий, что привело к появлению большого количества проверенных инструментов, библиотек, фреймворков и знаний. Это облегчает разработку, администрирование и отладку SQL-систем. NoSQL-мир все еще активно развивается, и набор инструментов может быть менее зрелым и стабильным. 
4. Стандартный язык запросов (SQL): Язык SQL является стандартом де-факто для взаимодействия с реляционными базами данных. Это позволяет разработчикам быстро изучить и использовать SQL для различных баз данных, а также упрощает переносимость между разными SQL-системами. 
5. Оптимизация запросов: SQL-движки имеют мощные оптимизаторы запросов, которые анализируют SQL-запросы и выбирают наиболее эффективные планы их выполнения. Это позволяет SQL-системам обрабатывать сложные запросы и большие объемы данных с высокой производительностью. NoSQL-системы могут быть не так хороши в оптимизации сложных запросов и могут требовать больше усилий от разработчиков для оптимизации производительности.

`Преимущества NewSQL перед SQL и NoSQL:`
NewSQL-системы представляют собой попытку объединить лучшие черты SQL и NoSQL. Они стремятся предоставить масштабируемость и производительность, характерные для NoSQL, при этом сохраняя ACID-транзакции и согласованность данных, присущие SQL
Некоторые ключевые преимущества NewSQL:
-Масштабируемость и производительность
-ACID-транзакции с высокой производительностью
-Согласованность данных и структурирование

### Задание 4

4. `Критерии выбора СУБД`
1. Тип данных:
  - Структурированные данные (реляционные): Если у нас преимущественно структурированные данные, которые можно представить в виде таблиц, то нам понадобятся решения типа распределенной SQL СУБД. Это могут быть, например, Google Cloud Spanner, CockroachDB, TiDB, или даже решения на базе PostgreSQL с расширениями для распределенной работы.
  - Неструктурированные или полуструктурированные данные: Если у нас много неструктурированных данных (текст, JSON, логи) или полуструктурированных, то имеет смысл смотреть в сторону NoSQL баз данных. В этом случае нам подойдут распределенные хранилища документов (MongoDB, Couchbase), хранилища "ключ-значение" (Redis, Memcached, DynamoDB)
  - Комбинированный вариант: Очень часто встречаются гибридные варианты, когда часть данных структурирована, а часть – нет. В этом случае можно использовать комбинацию СУБД.
2. Характер вычислений:
- Транзакционная нагрузка 
- Аналитическая нагрузка
- Смешанная нагрузка 
3. Масштабируемость:
 - Горизонтальное масштабирование: Нам нужна СУБД, которая легко горизонтально масштабируется на 1000 машин. Практически все распределенные решения подходят под это требование.
 - Отказоустойчивость: Нам нужна высокая отказоустойчивость. Распределенные СУБД как раз обеспечивают отказоустойчивость за счет репликации и распределения данных.