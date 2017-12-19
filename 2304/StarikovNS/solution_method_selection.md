## Выбор метода решения
Решение представляет собой библиотеку работающую в интегрированной среде разработки Unity, позволяющий взаимодействовать с библиотекой OpenCV. Было решено для демонстрации создать прототип использующий алгоритмы распознования лиц. При помощи библиотеки OpenCV, используя алгоритм lbp каскадов, распознаётся и вырезается лицо, и передаеться в Unity. 

Решение должно быть удобным в использовании, правильно реагировать на ситуации когда не удаеться обноружить ни одного лица или когда лиц несколько. 

Таким образом, ключевые требования можно описать следующим образом:
* Удобное использование;
* Обработка ситуация когда не было обнаружено лиц;
* Обработка ситуация с множеством лиц;
* Достаточно высокую скорость обработки и передачи данных в Unity.

Разработка решения взаимодействия компьютерного зрения с виртуальным окружениеи производилась в три этапа.  
* На первом этапе был разработан метод использования OpenCV для взаимодействия с Unity.
* На втором этапе была создана библиотека сохраняющая распознанное лицо.
* На третем этапе разработанано решение передающее изображение в в текстуру объектов. 	