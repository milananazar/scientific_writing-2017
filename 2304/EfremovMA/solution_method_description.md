## Описание метода решения

### Проблемы децентрализованных систем

Для распределенной децентрализованной многоагентной системы важны следующие аспекты:

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
| ROS   [1][3]       | Сервисы поиска, именования, сервис параметров | Топики, параметры, сервисы         | TCP, UDP, собственный протокол rosserial                     | Бинарный                     |
| MIRA    [2]     | Нет                                           | Топики, RPC                        | Внутрипроцессное взаимодействие, TCP                         | Бинарный, XML, JSON          |
| OROCOS/Rock [1][2][3][4]  | Сервисы поиска, именования                    | Порты, сервисы, события, параметры | CORBA, TCP, UDP, SSL,UNIX Sockets, MQueue, EtherCAT, CanOPEN | Сериализация на основе CORBA |
| YARP    [1][2]     | Сервис имен                                   | Порты, топики                      | ACE, TCP, UDP, внутрипроцессное взаимодействие               | Бинарный                     |
| OpenRTM-aist [1][3][5][6] | Сервис имен                                   | Порты, сервисы, параметры          | TCP, UDP, SSL,UNIX Sockets, CORBA                            | Сериализация на основе CORBA |

### Рассматриваемые области тестирования

- Реализация централизованных сервисов, если они имеются во фреймворке на предмет их быстродействия, устойчивости, потребления ресурсов памяти.
- Реализация способов взаимодействия между узлами на предмет задержки передачи сообщений между узлами, возможных затрат ресурсов на обработку сообщений.
- Реализация коммуникации на предмет пропускной способности, затрат вычислительных ресурсов на передачу данных. При использовании CORBA и ACE - объем потребляемой памяти на поддержку коммуникации этими методами. При использовании шифрования - задержку передачи сообщения и затраты вычислительнх ресурсов на преобразование данных.
- Реализация форматов хранения данных на предмет скорости сериализации и десериализации, объема передаваемых данных, скорость извлечения данных в случае, если они сжимаются.


### Список литературы

1. Robotics software frameworks for multi-agent robotic systems development
2. MIRA - Middleware for Robotic Applications
3. Robotics Middleware: A Comprehensive Literature Survey and Attribute-Based Bibliography
4. Open Robot Control Software: the OROCOS project
5. YARP: Yet Another Robot Platform
6. Middleware for Robotics: A Survey 