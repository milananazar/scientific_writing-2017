# Проблематика производительности многоагентных фреймворков для разработки ПО автономных роботов

## Аннотация

Данная работа выделяет ряд проблем и задач, выполняемых таким промежуточным ПО, как фреймворки для разработки робототехнических приложений для автономных роботов. Данная статья делает упор на обнаружение критических для производительности мест в наиболее пригодной архитектуры для рассматриваемых фреймворков: многоагентной. Так же были выделены для исслдеования ряд существующих и наиболее используемых фреймворков для рассмотрения способов решения проблем разработки такой архитектуры, выделяя подходы и технологии, которые стоит протестировать на предмет различных характеристик производительности распределенной системы.

## Введение

Объектом исследования данной статья являются многоагентные фреймворки для разработки ПО для автономных роботов (далее "фреймворки") на предмет критичных для производительности аспектов многоагентных робототехнических фреймворков.

Под фреймворками понимается набор промежуточного (middleware) ПО, находящегося по уровню абстракции между ОС и прикладными приложениями, предназначенного для управления неоднородностью аппаратного обеспечения с целью упрощения и снижения стоимости разработки ПО.
Кроме того, в состав фреймворков идет набор библиотек и, опционально, инструментов, для разработки прикладных программ, обслуживающих систему, в данном случае - автономного робота.

На рынке доступны различные версии фреймворков для автономных роботов с разными принципами работы, написанные на разных языках программирования и под разные платформы. В связи с тем, что появляются новые разработки, возникают новые задачи для автономных роботов, а так же технологии для разработки ПО для них - возникает желание рассмотреть доступные и развивающиеся в данный момент решения и проанализировать на предмет производительности, чтобы разработчики могли обосновывать свой выбор при разработки приложений для автономных роботов. При этом стоит учитывать как соответствие фреймворков возможным общим критериям (лицензия, статус разработки), так и важные для конкретной области: разработки ПО для роботов. Для выполнения тестирования, следует определиться с тем, какие задачи, выполняемые фреймворком, являются значимыми для производительности системы в целом.

Целью данной статью является получения списка проблем и задач, решаемых при разработке фреймворков, которые являются критичными для производительности робототехнической системы в целом.

## Обзор предметной области 

Для обсуждения конкретных решений требуется из всего множества существующих фреймворков выбрать наиболее подходящие для данного исследования.

### Критерии сравнения аналогов

#### Наличие открытого исходного кода

Для лучшего понимания работы фреймворка, а так же лучшего понимания результатов тестирования и возможного улучшения тестовых задач желательно иметь возможность прочитать исходный код реализации функционала, влияющего на результат тестирования. Это может быть, например, реализация коммуникации между приложениями, которые были написаны при помощи исследуемого фреймворка.

#### Наличие документации

Написание обоснованных и корректных тестовых задач под конкретный фреймворк очень затруднительно, если нету инструкций по его использованию. Без наличия документации разработка приложений становится слишком сложной и корректность их выполнения не гарантируется.

#### Текущий статус разработки

Проекты, которые больше не поддерживаются разработчиками вполне могут рассматриваться для исследования, но в них скорее-всего исползуются устаревшие подходы, что приведет к низким показателям производительности.

#### Архитектура фреймворка

Разработка робототехнических систем налагает определенные ограничения и требования. Отдельным и самым важным критерием является надежность и устойчивость к ошибкам. [1] Поскольку любой программный продукт, даже хорошо протестированный, содержит какие-либо ошибки, включая те, которые приводят к неожиданному завершению. В случае завершения работы всего программного комплекса из-за ошибки отдельного модуля, роботу теряет работоспособность. Это особенно критично в таком приложении робототехнических фреймворков, как программирования команд или роя автономных роботов: потеря работоспособности ключевых узлов команды приводит к остановке выполнения поставленной ими задачи. Примером может являться исследование множетсвом роботов местности или пожаротушение командой из разнородных, выполняющих различные задачи, автономных роботов.

Таким образом, требуется как минимум распределенная архитектура. Чем ближе архитектура будет к P2P (peer-to-peer, одноранговая сеть) и многоагентным системам, тем устойчивее будет все робототехническое ПО. Для данного исследования будут рассматриваться гибридные P2P фреймворки и близкие к чистым P2P.

#### Наличие инструментов для мониторинга и конфигурации системы

Для анализа промежуточного ПО и обслуживаемых им приложений требуются такие инструенты, как мониторы используемых ресурсов и системы журналирования. Кроме того, развертывание большого распределенного ПО для проведения тестов является трудозатраной задачей и крайне желательны инструменты конфигурации и развертывания системы на целевой ОС и аппаратном обеспечении.

#### Поддержка различных языков программирования

Несмотря на то, что в этой работе наибольший интерес представляет именно промежуточный уровень системы, наличие альтернатив между различными языками программирования является преимуществом, поскольку позволяет, в зависимости от задачи, выбрать между, например, низкоуровневым прогроммированием с возможным преимуществом в производительности и высокоуровневыми языками с наличием удобных для разработки интерфейсов и прикладных библиотек.

В данной работе не столь важны критерии:
- Поддержки ограничений системы реального времени, поскольку это относится к скорости работы, а к предсказуемости системы. Наличие данного пункта является преимуществом в целом, но относительно производительности оказывается не существенным.
- Поддержка конкретных операционных систем, поскольку рассматривается вопрос производительности слоя абстракции между, собственно, ОС и прикладным ПО.
- Наличие инструментов симуляции, инструментов с графическим пользовательским интерфейсом и набора прикладных библиотек с реализациями наиболее распространенных алгоритмов, поскольку это относится к функциональному уровню системы, ближе к прикладному. Вопросы производительности отдельных инструментов, которые могут требоваться при разработке, отладке и администрированию робототехнических систем не относятся к проблематике потребления ресурсов на поддержание каркаса, основы системы - фреймворка.

### Представленные для рассмотрения фреймворки

#### ROS

Наиболее распространенный фреймворк, имеется и обширная документация, и открытый исходный код, разработка продолжается, широко используется. Имеет гибридную архитектуру, средства мониторинга и автоматизации развертывания системы. Для разработки по-умолчанию есть возможность использовать C++ и Python 2.7.

#### MIRA

Этот проект активно разрабатывается, имеется открытый исходный код и обширная документация. Имеет децентрализованную архитектуру [2] , реализован на C++. Имеются возможности для ведения журналирования приложений, мониторы состояния коммуникаций и ресурсов системы. Для разработки предлагается использовать C++, Python и JavaScript.

##### MOOS

Развивающийся проект, имеется документация и исходный код. На данный момент разрабатывается бета-версия MOOS 10. Проблема обеих версий: клиент-серверная архитектура, которая является спорным решением для разработки робототехнических систем из-за проблем устойчивости ПО к ошибкам. По этой причине в данной работе этот фреймворк рассматриваться не будет.

#### RoboComp

Не очень популярный и разрабатывается медленно разрабатываемый небольшой группой разработчиков. Документация очень скудна и в целом разбираться с разработкой проблематично. Архитектура распределенная с возможностью использовать как модель клиент-сервер, так и издатель-подписчик. Разработка ведется на C++. Ввиду незрелости разработки, в данной работе этот фреймворк рассматриваться не будет.

### OROCOS/Rock

Очень распространенный фреймворк, один из немногих поддерживающих ограничения реального времени. Документация обширная, разработка в основном ведется над набором инструментов Rock. Используется гибридная децентрализованная архитектура. Разработка ведется на C++, для разработки прикладных программ предоставляются такие языки, как C++, Python, Simulink [1]. Имеются инструменты для развертывания и мониторинга системы.

#### ASEBA

Для программирования используется концепция языков программирования пятого поколения: GUI, блоки, коннекторы. Разработка является скорее обучающий продукт с коммерческой составляющей в виде конкретной модели робота thymio. Рассматриваться в данной работе не будет.

#### OPRoS (Open Platform for Robotic Services)

Сложный для понимания корейский фреймворк, исходный код найден не был. Последние записи документации на английском датированы 2013м годом, а для программирования используется подход языков программирования 5го поколения. Рассматриваться в данной работе не будет.

#### SmartSoft

Разрабатывающийся проект с обширной документацией. Для реализации компонентов используется C++. Практически не используется децентрализация, основной шаблон взаимодействия клиент-сервер. Кроме того, используется многопоточный подход, а не многопроцессный [3], что ставит под вопрос общую устойчивость всей системы. В данной работе рассматриваться не будет.

#### YARP

Активно разрабатывающийся и зрелый фреймворк с открытым исходным кодом. Имеется обширная и подробная документация. Является одним из немногих децентрализованных фреймворков, кроме того, поддерживает ограничения систем реального времени. Разрабатывается в основном на C++ и поддерживает такие языки как C++, Python, Java, Octave. Инструменты мониторинга и развертывания приложений имеются.

#### OpenRTM-aist

Распространенный фреймворк с открытым исходным кодом, является реализацией стандарта RT-middleware. Основная проблема: часть документации, при скромном содержании, на японском языке. Архитектура гибридная децентрализованная, имеются инструменты мониторинга, журналирования и развертывания, языки разработки: C++, Java, Python.

#### URBI

Данный фреймвор имеет много недостатков: приостановленная разработка, централизованная архитектура, скудная документация. Рассматриваться в данной работе не будет.



### Таблица сравнения по критериям

| Название     | Открытый код | Наличие понятной документации | Последние изменения | Архитектура        | Инструменты мониторинга | Поддержка ЯП              |
|--------------|--------------|-------------------------------|---------------------|--------------------|-------------------------|---------------------------|
| ROS          | Да           | Да                            | Недавно             | Гибридная          | Да                      | C++, Python               |
| MIRA         | Да           | Да                            | Недавно             | Децентрализованная | Да                      | C++, Python, JavaScript   |
| MOOS         | Да           | Да                            | Недавно             | Централизованная   | Да                      | C++, Java                 |
| RoboComp     | Да           | Нет                           | Недавно             | Распределенная     | Да                      | C++                       |
| OROCOS/Rock  | Да           | Да                            | 2016                | Гибридная          | Да                      | C++, Python, Simulink     |
| ASEBA        | Да           | Нет                           | Недавно             | Распределенная     | Да                      | Собственный язык          |
| OPRoS        | Нет          | Нет                           | 2013                | Распределенная     | Да                      | Неизвестно                |
| SmartSoft    | Да           | Да                            | Недавно             | Распределенная     | Да                      | C++                       |
| YARP         | Да           | Да                            | Недавно             | Децентрализованная | Да                      | C++, Python, Java, Octave |
| OpenRTM-aist | Да           | Нет                           | 2016                | Гибридная          | Да                      | C++, Java, Python         |
| URBI         | Да           | Нет                           | 2016                | Централизованная   | Да                      | C++, Java, urbiscript     |

Таким образом, в исследовании будут учавствовать следующие фреймворки:

- ROS
- MIRA
- OROCOS/Rock
- YARP
- OpenRTM-ist

## Выбор метода решения

Отобрав фреймворки с децентрализованной структурой, требуется определить те части, которые оказывают наибольшее влияние на производительность. Для этого требуется:
- Разобрать базовые проблемы децентрализованных систем.
- Выявить на какие аспекты производительности каждая из проблем влияет.
- Рассмотреть какие решения были приняты в выбранных для исследования фреймворках.

На основании полученных данных требуется выделить задачи и отслеживаемые характеристики для тестирования фреймворков. 

## Описание метода решения

### Проблемы децентрализованных систем

Для распределенной децентрализованной многоагентной системы важны следующие аспекты [1]:

- *Тип архитектуры*. Большая часть фреймворков использует гибридную архитектуру, поскольку такой подход позволяет избежать ряд проблем, а именно:
	- Не требуется инкапсулировать в каждый узел системы общие сервисы, как, например, службу поиска других узлов: эту задачу выполняет специально выделенный узел. Так же поступают и с агенствами - контейнерами, создающие, регистрирующие и хранящие узлы-агенты.
	- Уменьшение нагрузки на сеть коммуникаций, поскольку вся нагрузка по общим для всех узлов запросам смещается в сторону обработки на выделенных узлах. Это приводит к появлению критических узлов в системе, в случае отказа которых весь робот может оказаться неспособным продолжать выполнение поставленных задач. Таким образом, в таком подходе стоит отдельно рассматривать производительность таких критических узлов системы, как служба поиска узлов, служба именования и, возможно, другие выделенные сервисы.
	
	В случае, если используется максимально децентрализованная архитектура, то возникают следующие проблемы:
	- Увеличивается сложность узлов, а так же нагрузка на обработку данных между ними. Тем не менее, то, насколько эффективно используются возможности фреймворка по взаимодействию между узлами, зависит от разработчика на прикладном уровне.
	- Увеличивается нагрузка на систему коммуникации фреймворка. Под системой коммункации понимается набор технологий и протоколов, обеспечивающих обмен данными между узлами системы. Часто для обеспечения коммуникации используются отдельные узлы, создаваыемые самим фреймворком, будь то брокеры объектных запросов (ORB) при использовании CORBA или реализации шаблона взаимодействия "издатель-подписчик" при помощи топиков (от англ. topic - тема), которые имеют свобю внутреннюю реализацию.
	
- *Тип взаимодействия между узлами*. При использовании распределенной архитектуры используется несколько разных способов взаимодействия:
	- Порты, реализующий независимое чтение из входных портов, запись во входные и взаимодействие "один ко многим".
	- Топики, реализующие шаблон "издатель-подписчик" и взаимодействие "многие ко многим".
	- События, реализующие шаблон "наблюдатель" и ассинхронное взаимодействие "один ко многим".
	- Сервисы, предоставление реактивного поведения узлам: возможность отправлять запрос от узла-клиента на узел-сервис, реализуя взаимодействие выполнения удаленных процедур.
	- Свойства, предоставляющие возможность менять состояние узлов при помощи модификаторов параметров агента (get/set). Реализация может отличаться в зависимости от архитектуры: гибридная архитектура представляет выделенный узел-сервис - службу свойств, децентрализованная инкапсулирует свойства узлов непосредственно в агентах системы.
	
	Большинство фреймворков дает выбор: какой тип взаимодействия между узлами использовать, но, в случае с реализацией сервисов может возникнуть простой системы до тех пор, пока узел, предоставлявший требуемый функционал, не будет восстановлен. Реализация отдельных типов взаимодействий влияет практически на все аспекты производительности: задержку, вычислительные ресурсы, использование памяти, энергопотребление. Для каждого из фреймворков следует тестировать каждый из способов взаимодействия по всем показателям производительности.
	
- *Механизмы коммуникации*. Для доставки сообщений обычно используются различные подходы и протоколы. Обычно разработчики пишут свои решения на основе стека TCP/IP с некоторыми оптимизациями, например реализация узлов как отдельных потоков внутри процесса-агенства. Это позволяет использовать общую память процесса и ускорить взаимодействие между узлами внутри процесса-агенства, которые могут распологаться в пределах одного вычислительного устройства. Это влечет за собой возможность потерять доступ ко всем узлам агенства в случае неисправностей из-за ошибок, допущенных при реализации агентов. Эта проблема решается репликацией агенств на вычислительной системе. Кроме того для доставки сообщений между узлами используются как более высокоуровневые технологии (CORBA, ICE, ACE), так и приближенные к аппаратной части (EtherCAT, I2C, CANBus). Выбор механизма доставки данных между узлами влияет на задержку получения информации, на пропускную способность между узлами, целостность данных, а так же потребление ресурсов вычислительной системы: чем более высокоуровневая технология, тем больше. Дополнительный функционал на уровне коммуникации, например QoS или шифрование так же влияет на производительность: при наличии такого функционала, его следует тестировать отдельно.

- *Тип сообщений*. Сообщения в зависимости от их структуры влияют на производительность. Бинарные типы сообщений имеют меньший объем, а следовательно на их передачу требуется меньше энергии и времени. Структурированные типы, вроде XML и JSON, хорошо поддаются анализу для разработчика, но могут занимать больше времени на обработку своиз данных, а так же требуют больше времени на передачу данных между узлами.

- *Способ взаимодействия с аппаратурой*. Существует два основных способа предоставить интерфейс от аппаратной части системы к прикладному ПО:
	- Инкапсулировать взаимодействие с аппаратурой в узлах, обращение к которым реализовать посредством RPC.
	- Динамически связывающиеся библиотеки.
	
	От способа обращения будет зависеть отзывчивость системы на внешнее взаимодействие. Кроме того, различные раелизации могет использовать различное количество ресурсов. Кроме того, в случае использования дополнительного слоя абстракции в виде узлов-сервисов создает критические для устойчивости всей системы части.

### Решения проблем в различных реализациях фреймворков

Ниже приведена таблица, сопоставляющая возникающие проблемы и их решения для отобранных для исследования фреймворков.

|              | Централизованные сервисы                      | Взаимодействия между узлами        | Механизмы коммуникации                                       | Тип сообщений                |
|--------------|-----------------------------------------------|------------------------------------|--------------------------------------------------------------|------------------------------|
| ROS   [1][4]       | Сервисы поиска, именования, сервис параметров | Топики, параметры, сервисы         | TCP, UDP, собственный протокол rosserial                     | Бинарный                     |
| MIRA    [3]     | Нет                                           | Топики, RPC                        | Внутрипроцессное взаимодействие, TCP                         | Бинарный, XML, JSON          |
| OROCOS/Rock [1][3][4][5]  | Сервисы поиска, именования                    | Порты, сервисы, события, параметры | CORBA, TCP, UDP, SSL,UNIX Sockets, MQueue, EtherCAT, CanOPEN | Сериализация на основе CORBA |
| YARP    [1][3]     | Сервис имен                                   | Порты, топики                      | ACE, TCP, UDP, внутрипроцессное взаимодействие               | Бинарный                     |
| OpenRTM-aist [1][4][6][7] | Сервис имен                                   | Порты, сервисы, параметры          | TCP, UDP, SSL,UNIX Sockets, CORBA                            | Сериализация на основе CORBA |

### Рассматриваемые области тестирования

- Реализация централизованных сервисов, если они имеются во фреймворке на предмет их быстродействия, устойчивости, потребления ресурсов памяти.
- Реализация способов взаимодействия между узлами на предмет задержки передачи сообщений между узлами, возможных затрат ресурсов на обработку сообщений.
- Реализация коммуникации на предмет пропускной способности, затрат вычислительных ресурсов на передачу данных. При использовании CORBA и ACE - объем потребляемой памяти на поддержку коммуникации этими методами. При использовании шифрования - задержку передачи сообщения и затраты вычислительнх ресурсов на преобразование данных.
- Реализация форматов хранения данных на предмет скорости сериализации и десериализации, объема передаваемых данных, скорость извлечения данных в случае, если они сжимаются.


## Заключение

В ходе исследования были отобраны 5 фреймворков по различным критериям, среди которых самые важные: открытый исходный код, понятная и доступная документация, децентрализованная (возможно, гибридная) архитектура, наличие инструментов мониторинга.

В ходе исследования многоагентной архитектуры робототехнических фреймворков были выявлены 4 основных источника влияния на производительность данного промежуточного слоя системы:

- Наличие в системе централизованных узлов.
- Типы взаимодействия между узлами системы.
- Механизмы коммуницирования между узлами системы.
- Дополнительный функционал в системе коммуницирования, например шифрование или QoS.
- Типы сообщений, используемых для передачи информации между узлами системы.

Исходя из выявленных проблем была составлена таблица решений этих задач отобранными фреймворками, а так же сформулированы основные показатели производительности системы в зависимости от контекста рассматриваемой проблемы.

В данной работе и не рассматривалось некоторое количество фреймворков из-за централизованной архитектуры. В теории, опущенные из изучения разработки могут иметь интерес для тестирования производительности, но из-за разницы архитектур имеется большая разница в возникающих проблемах и задачах. Кроме того было показано, что сильно централизованные архитектуры плохо применимы к разработке робототехнических приложений для автономных роботов.

В дальнейшем, на основе полученной инфомрации, планируется провести тестирование отобранных фреймворков в контексте выявленных узких мест для многоагентных робототехнических фреймворков.

## Список литературы

1. Robotics software frameworks for multi-agent robotic systems development
2. MIRA - Middleware for Robotic Applications
3. http://www.servicerobotik-ulm.de/drupal/?q=node/19
4. Robotics Middleware: A Comprehensive Literature Survey and Attribute-Based Bibliography
5. Open Robot Control Software: the OROCOS project
6. YARP: Yet Another Robot Platform
7. Middleware for Robotics: A Survey 