# Анализ сетевых взаимодействий и тематического разнообразия выпускных работ студентов Факультета Социальных наук НИУ ВШЭ за 2022-2023 годы

Проект состоит из двух частей:  

• Папка SNA содержит анализ сетевой структуры научных руководителей ВКР в R

• Папка NLP содержит тематический анализ названий ВКР в Jupyter Notebook

**Цель**: Исследовать сетевую структуру научных руководитлей и академические интересы выпускников Факультета социальных наук НИУ ВШЭ, с упором на влияние центральности научных руководителей и анализ тематик, связанных с COVID-19 и событиями на Украине.

#### Гипотезы:
**H1**: Студенты, научные руководители которых имеют высокую степень центральности в сети, склонны получать более высокие оценки за свои дипломные работы.  

**H2**: Руководители с высокой межпосреднической центральностью оказывают более широкое влияние на итоговую оценку студентов, что потенциально может привести к более разнообразным оценкам за диплом.  

**H3**: Научные руководители, работающие с большим количеством студентов, имеют более высокую степень входящих связей в сети. Это может влиять на структуру сети, увеличивая вероятность наличия связей между студентами этих научных руководителей.  

**H4**: Среди тем выпускных квалификационных работ студентов Факультета социальных наук НИУ ВШЭ наблюдается тенденция выбора тем, акцентирующих внимание на пандемии COVID-19 и её последствиях и на Специальной военной операции на Украине, а также связанных с ней событиях.  

**Задачи**:  
• Сбор и анализ данных о выпускных работах студентов за последние годы для создания обширной базы данных.  

• Использовать методы анализа сетей для изучения структуры сети научных руководителей и студентов, рассчитать различные меры центральности и проанализировать диадические и триадические отношения.  

• Применить методы сетевого анализа и статистических моделей для проверки гипотез, связанных с социальным отбором и влиянием.  

• Воспользоваться методам топик-моделирования для определения основных тематических направлений в выпускных работах.  

• Выявить тенденции в тематиках научных работ, связанных с текущими социальными и политическими событиями (COVID-19 и события в Украине) с помощью LDA, LSI, ruBERT, YandexGPT API


**Этапы работы**:

1. Сбор данных: Сбор данных с сайта НИУ ВШЭ о выпускных работах студентов, включая названия работ, авторов, оценки и научных руководителей.
2. Предобработка данных: Очистка и структурирование собранных данных, включая удаление несущественной информации и форматирование для анализа.
3. Создание сети: Использование собранных данных для построения сети взаимоотношений между студентами и научными руководителями.
4. Анализ сети: Вычисление ключевых метрик сети, такие как плотность, центральность и степени связности участников.
5. Топик-моделирование: Применение методов, таких как LDA, для выявления основных тем в выпускных работах.
6. Статистический анализ: Анализирование взаимосвязи между центральностью научных руководителей в сети и итоговыми оценками ВКР.
7. Интерпретация и визуализация: Оформление результатов в виде графиков, таблиц и схем для наглядности.
8. Формулирование выводов: Синтезирование результатов анализа и формулирование общих выводов о сетевых взаимодействиях и тематиках ВКР.

## SNA

### Описание данных SNA-части исследования

Открытая база данных всех выпускных квалификационных работ (ВКР) студентов НИУ ВШЭ позволила с лёгкостью собрать для дальнейшего анализа названия ВКР выпускников Факультета социальных наук за 2022 и 2023 годы, авторов работ, их научных руководителей и оценки за работы. Для этого на самом сайте были поставлены соответствующие фильтры, а потом с помощью библиотек beautifulsoup4 и requests был произведен парсинг данных с помощью цикла, который прошелся по всем станицам. К сожалению, не удалось имплементировать в изначальный цикл сбор данных за 2023 год, поэтому просто были созданы два соответствующих цикла для каждого года. Далее база данных за 2022 год была объединена с базой за 2023, в итоговом датасете собраны 289 строк, то есть ВКР с оценками. Было принято решение сохранить данные в формате csv для ускорения последующего анализа.
Итоговая база данных представляет собой csv-файл, где для каждой ВКР есть столбцы с названием работы, ФИО автора, полученной по выпуску ступенью образования (бакалавриат/магистратура), годом выпуска, оценкой и ФИО научного руководителя. Для последующего анализа из базы данных был сделан edge list, где каждая строка описывает отношение между студентом и научным руководителем, а также включает дополнительную информацию об оценке за выпускную работу: 

source: ФИО студента. Это поле представляет "источник" в отношении между студентом и научным руководителем. В контексте направленного графа, ребро исходит от студента.

target: ФИО научного руководителя. Это поле представляет "цель" или "пункт назначения" отношения. Ребро ведёт к научному руководителю.

weight: Оценка, которую студент получил за выпускную работу. Это поле может быть использовано для представления "веса" ребра в взвешенном графе, где вес может указывать на силу или важность отношения.

### Результаты SNA-части исследования

В рамках нашего исследования мы погрузились в анализ сети выпускников Факультета социальных наук НИУ ВШЭ и их научных руководителей. И вот что нам удалось выяснить:

**Гипотеза 1 (H1)**: Мы предположили, что оценки за дипломные работы студентов будут выше, если их научные руководители занимают центральное положение в сети. И, оказывается, мы были правы! Существует заметная связь между центральностью руководителя и оценками выпускников. Но стоит отметить, что модель объясняет только 12% разницы в оценках, так что, вероятно, есть и другие факторы, которые стоит учитывать.

**Гипотеза 2 (H2)**: Мы также рассматривали возможность того, что руководители с высоким уровнем межпосреднической центральности могут оказывать более разнообразное влияние на итоговые оценки студентов. Однако, к сожалению, все участники сети оказались на одном уровне межпосреднической центральности, и мы не смогли проверить эту гипотезу.

**Гипотеза 3 (H3)**: Ну и наконец, мы предположили, что руководители, которые работают с большим количеством студентов, занимают более важное место в сети. И это подтвердилось! Эти "влиятельные" руководители действительно играют ключевую роль, укрепляя связи между студентами и способствуя формированию более плотной и взаимосвязанной структуры сети.

Таким образом, наше исследование позволило нам лучше понять, как устроена сеть взаимоотношений между студентами и их руководителями на ФСН НИУ ВШЭ. Мы выявили некоторые ключевые тенденции и паттерны, хотя для более глубокого анализа и понимания всех аспектов этой сети потребуются дополнительные исследования.

## NLP

### Описание данных NLP-части исследования

Основная идея работы заключается в том, чтобы с помощью методов обработки естественного языка выделить главные тренды среди тем выпускных квалификационных работы студентов ФСН НИУ ВШЭ. В качестве гипотезы представляется следующие: лидирующими темами будут те, которые в названии включают упоминание COVID-19, СВО или новые регионы РФ. Иными словами, гипотеза звучит так: 
Среди тем выпускных квалификационных работ студентов Факультета социальных наук НИУ ВШЭ наблюдается тенденция выбора тем, акцентирующих внимание на пандемии COVID-19 и её последствиях и на Специальной военной операции на Украине, а также связанных с ней событиях.
Открытая база данных всех выпускных квалификационных работ (ВКР) студентов НИУ ВШЭ позволила с лёгкостью собрать для дальнейшего анализа названия ВКР выпускников Факультета социальных наук за 2022 и 2023 годы. Для этого на самом сайте были поставлены соответствующие фильтры, а потом с помощью библиотек beautifulsoup4 и requests был произведен парсинг данных с помощью цикла, который прошелся по всем станицам. К сожалению, не удалось имплементировать в изначальный цикл сбор данных за 2023 год, поэтому просто были созданы два соответствующих цикла для каждого года. Далее база данных за 2022 год была объединена с базой за 2023, в итоговом датасете собраны 2340 строк, то есть тем ВКР. Было принято решение сохранить данные в формате csv для ускорения последующего анализа.
Итоговая база данных представляет собой csv-файл, где для каждой ВКР есть столбцы с названием работы, ФИО автора, полученной по выпуску ступенью образования (бакалавриат/магистратура) и годом выпуска. Для последующего анализа база данных была обновлена, был оставлен только столбец с темами ВКР.

### Результаты NLP-части исследования

**Гипотеза 4 (H4)**: Гипотеза частично подтвердилась. Среди тем выпускных квалификационных работ студентов Факультета социальных наук НИУ ВШЭ наблюдается тенденция выбора тем, акцентирующих внимание на пандемии COVID-19 и её последствиях. Однако стоит отметить, не были выделины обобщенные темы относительно Специальной военной операции на Украине, а также связанных с ней событиях. 

В качестве методологического вывода по работе можно отметить, что YandexGPT и ruBERT оказались наиболее удачными инструментами в рамках выделения основных тем в тематиках ВКР.
