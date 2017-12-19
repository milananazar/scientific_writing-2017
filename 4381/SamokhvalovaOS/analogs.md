Аналогичные научные статьи, которые так же исследовали рекомендательные алгоритмы для социальных сетей


## Сравнение аналогов
### РАЗРАБОТКА РЕКОМЕНДАТЕЛЬНОЙ СИСТЕМЫ НА ОСНОВЕ ДАННЫХ ИЗ ПРОФИЛЯ СОЦИАЛЬНОЙ СЕТИ «ВКОНТАКТЕ»

В статье рассматривается проблема автоматизации процесса веб-сёрфинга и фильтрации контента, а также,
 описано проектирование и реализация мультиагентной рекомендательной системы «EZSurf»,
  обеспечивающей анализ интересов и предоставление рекомендаций пользователям социальной сети «ВКонтакте»
   на основе данных из профиля конкретного пользователя.
   
### РЕКОМЕНДАЦИИ ТРЕКОВ В СОЦИАЛЬНЫХ СЕТЯХ

В этой работе исследована применимость алгоритма Random Walk with Restarts в
крупных социальных сетях для рекомендации музыкальных треков.
Предложены модификации оригинального алгоритма, позволяющие
улучшить качество рекомендаций. 


### АЛЬТЕРНАТИВНАЯ РЕКОМЕНДАТЕЛЬНАЯ СИСТЕМА ДЛЯ ПОЛЬЗОВАТЕЛЕЙ STEAM
Статья посвящена разработке рекомендательной системы для пользователей интернет-сервиса
 Steam, специализирующемся на продаже компьютерных игр.
  Разрабатываемая система использует гибридную модель фильтраций,
   состоящую из «collaboration filtration» (коллаборативная фильтрация),
    а также «content-based» (фильтрация, основанная на контенте).

## Критерии сравнения аналогов

### Доступность приложения

Основной идеей было создание простого рабочего приложения: любой пользователь сможет без проблем  с ним взаимодейтвовать. 

### Скорость построения рекомендаций

Предполагается, что для пользователя имеет значение время, требуемое для решения поставленной задачи.
Это говорит о том, что одной из главных целей должна быть быстрота работы алгоритмов рекомендаций.


### Простота алгоритма

Будем считать, что для поставленной задачи необходимо найти наиболее оптимальный вариант алгоритма.
Под простотой подразумевается низкая сложность вычислений, производимых данным алгоритмом.

 

## Таблица сравнения по критериям

<table>
<tr><th>Название статьи</th><th>Доступность приложениям</th><th>Скорость построения рекомендаций</th><th>Простота алгоритма</th></tr> <!--ряд с ячейками заголовков-->
  <tr><td>Построение рекомендаций сообществ социальной сети «Вконтакте»</td>
    <td>+</td><td>Алгоритм срабатывает за считанные секунды, но загрузка данных занимает несколько минут.</td><td>Примитивный алгоритм. Основная сложность заключается в запросах к базе Neo4j.</td></tr>
  <tr><td>РАЗРАБОТКА РЕКОМЕНДАТЕЛЬНОЙ СИСТЕМЫ НА ОСНОВЕ ДАННЫХ ИЗ ПРОФИЛЯ СОЦИАЛЬНОЙ СЕТИ «ВКОНТАКТЕ»</td>
    <td>-</td><td>-</td><td>Сложный мультиагентный механизм</td></tr>
  <tr><td>РЕКОМЕНДАЦИИ ТРЕКОВ В СОЦИАЛЬНЫХ СЕТЯХ</td>
    <td>-</td><td>Требует значительных временных ресурсов</td><td>Алгоритм несет в себе нетривиальные расчеты</td></tr>
  <tr><td>АЛЬТЕРНАТИВНАЯ РЕКОМЕНДАТЕЛЬНАЯ СИСТЕМА ДЛЯ ПОЛЬЗОВАТЕЛЕЙ STEAM</td>
     <td>-</td><td>-</td><td>Алгоритм несет в себе нетривиальные расчеты</td></tr>
</table>

## Выводы по итогам сравнения

Рассмотренные нами рекомендательные системы используют более сложные алгоритмы, из чего можно предположить, что время работы этих алгоритмов значительно выше
значений, полученных нами (В одной из статей автор указывает на то, что алгоритм не справляется за приемлемое для пользователя время). Надо отметить, что не было найдено
аналогов, связанных с построением рекомендаций именно сообществ сети ВКонтакте. 

## Источники

https://elibrary.ru/item.asp?id=22092035&
http://web.iis.nsk.su/files/dzyuba_m_thesis.pdf
https://elibrary.ru/item.asp?id=28154004