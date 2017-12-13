## Введение

Проблему одновременной локализации и построения карты группой роботов (MultiRobotSLAM) можно определить, как проблему непрерывной оценки и уточнения карты окружающего пространства на основе данных, получаемых с подвижного сенсора (робота), и в то же время, определение положения этого сенсора на карте. В данном контексте речь идет о любых подвижных роботах, перемещающихся по плоской поверхности и снабженных собственным вычислительным устройством и сенсорами, позволяющими получать информацию из внешнего мира – лазерным дальномером (LIDAR), видеокамерой, одометром, камерой глубины и др.  

Уже сейчас существует множество эффективных и применяемых на практике алгоритмов, решающих задачу SLAM для одиночного робота. Однако, ввиду расширения сферы применения роботов, стремительного развития технических средств и эволюции программного обеспечения изучение данного вопроса становится все более актуальным, применительно к группам мобильных роботов. Как правило, группа представлена достаточно независимыми для автономной работы роботами, которые объединены в единую сеть и кооперируются для достижения поставленной цели.  

Как показывает практика, на данный момент не существует какого-либо общепризнанного, достаточно надежного, проверенного алгоритма навигации для групп роботов. В то же время, ввиду заинтересованности научного сообщества данной темой, постоянно появляются новые разработки. Большая часть попыток решения проблемы SLAM не находит широкого распространения, поскольку зачастую авторы либо не раскрывают в своих статьях подробности реализации их алгоритмов и исходные коды, либо потому что эти алгоритмы сами по себе недостаточно проработаны, чтобы претендовать на роль универсального решения. Эти трудности, наряду с разобщенностью групп исследователей в данной области, отсутствием единой теоретической и программной базы, существенно замедляет прогресс.  

Настоящая работа призвана привнести свой вклад в данном направлении. Ее целью является исследование, анализ и классификация существующих методов SLAM групп роботов, а также выявление их преимуществ и недостатков, сравнительная оценка. Следующим шагом должна стать реализация выбранных наиболее перспективных методов на свободной и доступной платформе для построения роботов Robot Operation System (ROS), и далее - объективная оценка и сравнение из работы в различных условиях на основе реальных экспериментов.  

## Принципы работы методов SLAM
 
Основными требованиями к роботу при решении задачи SLAM является мобильность и способность получать информацию об окружающей среде с помощью сенсоров. Общую схему SLAM для одиночного робота можно представить в три этапа [1][2]:  

1. Робот под воздействием сигнала управления передвигается в новые координаты. При этом ввиду наличия шумов в данных с сенсора движения, пробуксовки колес и ошибок округления повышается неопределенность его положения. Данный процесс описывается моделью движения, которая зависит от типа сенсора, типа платформы и способа передвижения робота и представляется нелинейной функцией:  
![s_t = f(s_{t-1}, u_t) + N(0, \Lambda_u))](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20s_t%20%3D%20f%28s_%7Bt-1%7D%2C%20u_t%29%20+%20N%280%2C%20%5CLambda_u%29%29)  
Данная запись означает, что новое положение робота ![s_t](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20s_t) зависит от предыдущего положения ![s_{t-1}](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20s_%7Bt-1%7D), сигнала управления ![u_t](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20u_t) и Гауссового шума ![N](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20N) с заданным математическим ожиданием и дисперсией.  

2. Робот считывает новые данные с сенсора.  Данный этап описывается моделью прямых наблюдений, которая зависит от типа сенсора и его характеристик, и также представляется нелинейной функцией:  
![z_{t,i} = h(s_t, \Theta_{c_{t,i}}) + N(0, \Lambda_z)](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20z_%7Bt%2Ci%7D%20%3D%20h%28s_t%2C%20%5CTheta_%7Bc_%7Bt%2Ci%7D%7D%29%20+%20N%280%2C%20%5CLambda_z%29)  
Другими словами, измерение ![z_{t,i}](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20z_%7Bt%2Ci%7D) с сенсора зависит от положения робота ![s_t](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20s_t), положения ориентиров ![\Theta_{c_{t,i}}](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20%5CTheta_%7Bc_%7Bt%2Ci%7D%7D) (где ![c_{t,i}](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20c_%7Bt%2Ci%7D) - ориентир с номером i на шаге t) и Гауссового шума ![N](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20N).  

3. Робот уточняет свое положение и карту местности, путем интеграции уже имеющихся и новых собранных данных. Данный этап является ключевым и описывается конкретным алгоритмом, вид которого существенно зависит от лежащего в основе механизма обработки и интеграции данных с датчика и отсеивания шумов.  

Таким образом полную задачу SLAM для отдельного робота можно представить рекурсивным вероятностным выражением:  
![p(s_t, m | z_{1:t}, u_{1:t}) = p(z_t | s_t, m) \int p(s_t | s_{t-1}, u_t) p(s_{t-1}, m | z_{1:t-1}, u_{1:t-1})ds_{t-1}](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20p%28s_t%2C%20m%20%7C%20z_%7B1%3At%7D%2C%20u_%7B1%3At%7D%29%20%3D%20p%28z_t%20%7C%20s_t%2C%20m%29%20%5Cint%20p%28s_t%20%7C%20s_%7Bt-1%7D%2C%20u_t%29%20p%28s_%7Bt-1%7D%2C%20m%20%7C%20z_%7B1%3At-1%7D%2C%20u_%7B1%3At-1%7D%29ds_%7Bt-1%7D)  
Где ![m=\sum^n_{i=0} c_i](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20m%3D%5Csum%5En_%7Bi%3D0%7D%20c_i) - карта, представленная набором ориентиров на местности или ячеек.  
Это показывает, что оценка карты и положения робота на каждом шаге может быть получена на основе новых данных с сенсора и сигнала управления и оценки, учитывающей все предыдущие шаги.  

Методы SLAM для групп роботов являются, по сути, модификациями таковых для одиночного робота. Эти модификации нацелены на решение ряда возникающих в новом контексте вопросов, среди которых:  
* По каким каналам роботы должны обмениваться информацией.  
* Какую топологию должна иметь сеть, связывающая роботов группы.  
* Какой информацией о карте и своем состоянии должны обмениваться роботы.  
* Как должна происходить интеграция полученных от других роботов данных.  
В случае успешного решения этих проблем можно добиться ряда преимуществ:  
* Большая скорость построения карты по сравнению с одиночным роботом, ввиду наличия нескольких вычислительных устройств и сенсоров.  
* Большая точность алгоритма ввиду наличия нескольких независимых источников данных и оценок карты.  
* Устойчивость алгоритма к воздействию отрицательных факторов среды на отдельных роботов группы и к их неисправностям.  

Математически задача SLAM для группы роботов может быть представлена выражением [12]:  
![p(s_t^{1:k}, m | z_t^{1:k}, u_t^{1:k}) = \prod^k_{i=0} (p(z_t^i | s_t^i, m) \int p(s_t^i | s_{t-1}^i, u_t^{1:k}) p(s_{t-1}^i, m | z_{1:t-1}^{1:k}, u_{1:t-1}^{1:k})ds_{t-1}^i)](https://latex.codecogs.com/png.download?%5Cdpi%7B120%7D%20p%28s_t%5E%7B1%3Ak%7D%2C%20m%20%7C%20z_t%5E%7B1%3Ak%7D%2C%20u_t%5E%7B1%3Ak%7D%29%20%3D%20%5Cprod%5Ek_%7Bi%3D0%7D%20%28p%28z_t%5Ei%20%7C%20s_t%5Ei%2C%20m%29%20%5Cint%20p%28s_t%5Ei%20%7C%20s_%7Bt-1%7D%5Ei%2C%20u_t%5E%7B1%3Ak%7D%29%20p%28s_%7Bt-1%7D%5Ei%2C%20m%20%7C%20z_%7B1%3At-1%7D%5E%7B1%3Ak%7D%2C%20u_%7B1%3At-1%7D%5E%7B1%3Ak%7D%29ds_%7Bt-1%7D%5Ei%29)  
Таким образом, оценка общей карты и положений всех роботов группы на каждом шаге может быть получена на основе новых данных с сенсоров всех роботов и оценки, учитывающей все предыдущие шаги.  