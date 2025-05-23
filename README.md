# Тестовое задание: Извлечение сущностей из новостных текстов

В этом репозитории находится решение тестового задания для стажёров команды Data Science (IDP).  
Цель — извлечь сущности типов PER, ORG, LOC, EVT, PRO из девяти русскоязычных новостных документов о Brexit.

---

## Вопросы по заданию

1. Определение задачи с точки зрения NLP  
2. Чтение датасета в pandas DataFrame  
3. Функция для формирования промпта LLM  
4. Сбор ответов GigaChat и сохранение в DataFrame  
5. Реализация функции `score_fn` и unit‑тесты  
6. Подсчёт метрик, агрегация и визуализация результатов  
7. Анализ зависимости метрик от длины документа  
8. Анализ ошибок и рекомендации по улучшению  
9. Выводы по проделанной работе  

---

## 1. Определение задачи Named Entity Recognition (NER)

**Что такое NER?**  
Named Entity Recognition (NER) — это одна из базовых задач обработки естественного языка (NLP), цель которой — найти в тексте упоминания конкретных объектов (сущностей) и определить их тип. В нашем проекте мы работаем с русскоязычными новостями про Brexit и выделяем пять категорий:

- **PER** (Person) — имена и фамилии людей (например, «Тереза Мэй», «Борис Джонсон»).  
- **ORG** (Organization) — названия организаций и компаний (например, «Конференция партии тори», «Европейская комиссия»).  
- **LOC** (Location) — географические объекты и места (например, «Лондон», «Альбион»).  
- **EVT** (Event) — события и процедуры (например, «конференция», «процедура Brexit»).  
- **PRO** (Product) — продукты, услуги, документы (например, «доклад», «соглашение»).

В результате мы либо получаем список триплетов `(begin_index, end_index, label)`, либо последовательность токенов с метками в формате BIO (B‑начало, I‑внутри, O‑вне сущности).

---

### Классические подходы

1. **Rule‑based / шаблонные методы**  
   Прямой поиск сущностей по заранее заданным правилам, словарям (gazetteers) и регулярным выражениям: всё, что попало под правило или в словарь, помечается как сущность.

2. **Статистические модели (HMM, CRF)**  
   Модели, обученные на размеченных данных, которые предсказывают метку каждого токена с учётом вероятностей и зависимостей между соседними метками.

3. **Классификация токенов (SVM, логистическая регрессия)**  
   Каждый токен кодируется набором признаков (n‑граммы, POS‑теги, положение в тексте и т. д.), а классический алгоритм ML обучается присваивать токену метку BIO.

---

### Подходы на основе LLM

1. **Prompt‑based NER**  
   Использует zero‑ или few‑shot промпты: в подсказке приводятся примеры «текст → разметка», и модель по аналогии извлекает сущности без дополнительного обучения.

2. **Token‑classification API**  
   Прямая подача текста в API, который возвращает BIO‑метки для каждого токена, автоматически сегментируя и классифицируя сущности.

3. **Chain‑of‑Thought**  
   Многошаговый подход: сначала запросом выявляем все возможные фрагменты‑кандидаты без типов, затем отдельным запросом классифицируем каждый фрагмент по типу сущности.

4. **Fine‑tuning**  
   Дообучение предобученной трансформерной модели (например, BERT или RuBERT) на размеченном наборе NER‑данных, чтобы модель научилась извлекать нужные сущности напрямую.

---

### Метрики оценки качества

#### Precision (точность)

- **Что измеряет:** долю правильных предсказаний среди всех предсказанных моделью сущностей.  
- **Формула:**
$\mathrm{Precision} = \frac{TP}{TP + FP} $
- **Интерпретация:** если Precision = 0.8, значит 80 % найденных моделью сущностей действительно верны.



#### Recall (полнота)

- **Что измеряет:** долю правильно найденных сущностей среди всех истинных сущностей.  
- **Формула:**  
$\mathrm{Recall} = \frac{TP}{TP + FN} $  
- **Интерпретация:** если Recall = 0.7, значит модель нашла 70 % всех реальных сущностей.



#### F1‑score

- **Что измеряет:** гармоническое среднее Precision и Recall, балансируя между ними.  
- **Формула:**  
$F_{1} = 2 \times \frac{\mathrm{Precision} \times \mathrm{Recall}}{\mathrm{Precision} + \mathrm{Recall}} $  
- **Интерпретация:** F1 = 1 только когда и Precision, и Recall равны 1; низкое значение указывает, что модель либо много пропускает, либо много «находит лишнего».

---


### Усреднение результатов

- **Micro‑F1:**  
  1. Суммируем TP, FP и FN по всем классам.  
  2. Считаем Precision, Recall и F1 на этих суммарных значениях.  
  - Используется, когда важен общий результат и учёт всех примеров.

- **Macro‑F1:**  
  1. Считаем Precision, Recall и F1 для каждого класса (PER, ORG, LOC, …) отдельно.  
  2. Берём среднее F1 по всем классам.  
  - Все классы получают одинаковый вес, даже если некоторые встречаются реже.

> Далее переходим к практической части: чтение данных, генерация промптов, сбор ответов и вычисление метрик.

---


## 9. Выводы по проделанной работе

Работа была интересная, скорее всего в ближайшее время сделаю для нее автоматизацию, чтобы не 9 тестов вручную а все автоматически. Попробовал для себя применение нескольких метрик на практике, попробовал поработать с гигачатом, к сожалению с подключением к api возникли проблемы.
