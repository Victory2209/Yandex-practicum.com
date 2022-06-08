# Выбор локации для скважины

# Описание проекта и задачи 
Ддобывающей компании «ГлавРосГосНефть» нужно решить, где бурить новую скважину.
Шаги для выбора локации обычно такие:
- В избранном регионе собирают характеристики для скважин: качество нефти и объём её запасов;
- Строят модель для предсказания объёма запасов в новых скважинах;
- Выбирают скважины с самыми высокими оценками значений;
- Определяют регион с максимальной суммарной прибылью отобранных скважин.

Предоставлены пробы нефти в трёх регионах. Характеристики для каждой скважины в регионе уже известны. Задача - построить модель для определения региона, где добыча принесёт 
наибольшую прибыль. Проанализировать возможную прибыль и риски техникой Bootstrap.

# Описание данных 
Данные геологоразведки трёх регионов находятся в файлах.
- id — уникальный идентификатор скважины;
- f0, f1, f2 — три признака точек (неважно, что они означают, но сами признаки значимы);
- product — объём запасов в скважине (тыс. баррелей).

# Используемые библиотеки 
numpy   
pandas  
matplotlib  
sklearn  
scipy 