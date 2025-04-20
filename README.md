# NER Pipeline для новостных текстов (IDP Data Science Internship)

Это репозиторий с полным решением по извлечению сущностей из русскоязычных новостных документов (BREXIT‑семпл, 9 файлов). Всё собрано в одном Jupyter Notebook.

## Структура репозитория

- **README.md** — краткое описание проекта и инструкции по запуску.
- **requirements.txt** — список зависимостей Python.
- **ner_pipeline.ipynb** — основной ноутбук с решениями всех пунктов:
  1. Описание задачи NER и обзор подходов (rule‑based, HMM/CRF, LLM‑based).
  2. Загрузка данных в `pandas.DataFrame` с колонками `document_id`, `document_text`, `entity`, `gold_answer`.
  3. Функция для генерации промпта под LLM.
  4. Вызовы GigaChat / запись предсказаний в DataFrame.
  5. Реализация функции `score_fn(gold, pred) -> float` + юнит‑тесты.
  6. Расчёт метрик (precision, recall, F1) по сущностям и по документам + визуализация.
  7. Анализ зависимости качества от длины документа.
  8. Анализ ошибок и предложения по улучшению.
  9. Общие выводы.

- **predictions.csv** — итоговый CSV с колонками `document_id, entity, pred_answer, gold_answer`.

## Как запустить

1. Клонируйте репозиторий:
   ```bash
   git clone https://github.com/<ваш‑логин>/idp-ner-assignment.git
   cd idp-ner-assignment
