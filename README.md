# VTB-Data-Fusion-Goodsification
Receipt classification competition

__Private:19/265__

### Команда Students:  

[__Сивцов Данил__](https://github.com/svtdanny)

[__Тыцкий Владислав__  ](https://github.com/Tytskiy)

---
## Доразметка
1. Обучаем базовый SVM на tf-idf char-wb и предсказываем уникальные item_name из неразмеченных данных
2. Мерджим по итем нейму в таблицу(это существенно ускоряет доразметку)
3. Т.к. мы используем категориальные и числовые фичи, а агрегация доразмеченного датасета на для каждого уникального item_name заняла бы вечность 
мы сеплируем из датасета случайно N объектов и агрегируем фичи только по ним

---
## Построение алгоритма классификации

### Архитектура
#### Псевдолейблинг
1. Обучаем tfidf charwb + svm
2. Исключаем классы, которые svm очень плохо различает (71,84,204,79,83)
3. Берем метки с уверенностью больше 0.75
4. Произвольно выбираем из оставшихся 15000 и добаляем к изначальному датасету

Была также выделена небольшая валидационная выборка для тестирования доразметки. После подбора параметров стекинг обучался на всех данных

#### Далее работаем с полученными доразмеченными данными
#### Базовые модели
1. tfidf charwb + calibrated svm  
2. Numerical & categorical features + lgb  
3. tfidf char + naive bayes  

Взята часть категориальных фичей из публичного решения exotol create_features_part_2()

#### Стекинг - predict proba + svm

---
### Что не сработало:
1. KNN фичи на tfidf -> lgb (хотя в аналогичном [соревновании от Яндекс](https://www.youtube.com/watch?v=WSiPNCzShSY&t=691s&ab_channel=ODSAIRu) они улучшили модель)
2. Стекинг бустингом. Как на классах, так и на вероятностях
3. Добавление нелинейности для модели стекинга(SVM). Пытались использовать RBF ядро и RFF.
4. Стекинг моделей с различным tfidf: charwb и word
5. Увеличение и уменьшение порога доразметки и количество добавляемых элементов
6. Агрегация фич по чекам (в соревновании Яндекса это улучшило скор)
7. Разделение camel case чеков, добавление к исходному названию склееного название чеков (для нас это оказалось странным, но паблик падал на пару тысячных, решили убрать)

---
### Что мы не успели попробовать/догадаться
1. Использовать fastText
2. Семплировать с различными сидами и усреднять несколько стекингов
