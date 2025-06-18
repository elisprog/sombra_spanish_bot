# Telegram Spanish Learning Bot "SombraBot"🇪🇸🤖

Телеграм-бот для изучения испанского языка с помощью видеофрагментов, заданий на заполнение пропусков и голосового распознавания.
Бот отправляет пользователю видеофрагменты из сериала Extr@ Español и предлагает:
- заполнить пропущенные слова в тексте,
- записать голосовое сообщение с оригинальным текстом,
- получить оценку качества произношения.

Примеры интерактивности:
- Кнопки навигации: «Назад», «Повторить», «Пропустить»
- Оценка речи: `📊 Произношение: 87.2%`

---

## Предобработка данных (до запуска бота)

Перед реализацией бота была проведена подготовка:

### 1. Распознавание речи из видео

С помощью модели [OpenAI Whisper](https://github.com/openai/whisper) был автоматически расшифрован звуковой ряд из оригинального видео:

```python
import whisper

def transcribe_audio(file_path, model_size="medium", language="es"):
    model = whisper.load_model(model_size)
    result = model.transcribe(file_path, language=language)
    return result["text"]
```

---

### 2. Выделение ключевых слов и биграмм

Для генерации заданий на заполнение пропусков были выбраны **ключевые слова** (по TF-IDF) и **частотные биграммы**:

```python
from sklearn.feature_extraction.text import TfidfVectorizer
from nltk import bigrams
from collections import Counter
import spacy

nlp = spacy.load("es_core_news_sm")
```

**Ключевые слова** выбирались по частоте и значимости (POS-фильтрация: NOUN, VERB, ADJ, ADV).

```python
def extract_keywords_by_pos(text, top_n=50):
    ...
    return top_keywords
```

**Биграммы** рассчитывались с фильтрацией имён собственных (например, `Ana`, `Pablo`):

```python
def extract_top_bigrams(text, top_n=50, exclude_words=None):
    ...
    return top_bigrams
```

Пример вывода:

```
Ключевые слова (TF-IDF):
['hablar', 'carta', 'ducha', 'quedar', ...]
Частотные биграммы:
['me llamo', 'el perro', 'de la', 'un poco', ...]
```

Результаты использовались для генерации заданий с пропущенными словами в боте.
