%%markdown
# 1. Определение задачи Named Entity Recognition (NER)

**Описание задачи.**  
Named Entity Recognition (NER) — задача автоматического обнаружения и классификации именованных сущностей в тексте. В нашем случае извлекаем из 9 русскоязычных новостных документов про Brexit сущности типов:
- **PER** (person) — имена людей  
- **ORG** (organization) — организации и компании  
- **LOC** (location) — географические объекты  
- **EVT** (event) — события  
- **PRO** (product) — продукты и услуги  

Результат: список триплетов `(begin, end, type)` или последовательность токен+метка (BIO/IOB).

## Классические методы
1. **Rule‑based**: регулярные выражения, gazetteers.  
2. **HMM**: скрытые марковские модели.  
3. **CRF**: условные случайные поля.  
4. **Token‑classification**: SVM/logistic regression на ручных признаках.

## Подходы с LLM
- **Prompt‑based**: few‑shot или zero‑shot промпты для извлечения сущностей.  
- **Token‑classification API**: маркировка токенов BIO через API.  
- **Chain‑of‑Thought**: двухэтапный запрос (сбор кандидатов → классификация).  
- **Fine‑tuning**: дообучение модели на размеченных данных.

## Метрики оценки
- **Precision** = TP/(TP+FP)  
- **Recall** = TP/(TP+FN)  
- **F1** = 2·Precision·Recall/(Precision+Recall)  
**Strict** (точное совпадение границ и типа) vs. **Partial** (частичное совпадение).  
Усреднение: micro‑F1 vs. macro‑F1.
