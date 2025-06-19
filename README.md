# Telegram-бот для интерактивной практики испанского языка "sombra_spanish_bot" 🇪🇸🤖

Учебные задания автоматически формируются на основе видеоматериала: аудио распознаётся с помощью модели Whisper от OpenAI, текст проходит предобработку с использованием методов лингвистического анализа (токенизация, выделение частей речи POS, TF-IDF, частотные биграммы), после чего формируются упражнения на заполнение пропусков и задания на произношение с оценкой его качества. Бот умеет:

- отправлять видеофрагменты пользователю
- генерировать задания с пропущенными словами по ключевым словам и частотным биграммам
- принимать голосовые ответы и оценивать их, используя расстояние Левенштейна (Levenshtein distance)
- учитывать уровень совпадения и предлагать повтор или продолжение

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

Результаты использовались для генерации заданий с пропущенными словами.

```
Ключевые слова (TF-IDF):
['hablar', 'carta', 'ducha', 'quedar', ...]
Частотные биграммы:
['me llamo', 'el perro', 'de la', 'un poco', ...]
```
<img>: <img src="https://github.com/user-attachments/assets/9fb5ccaf-af19-47cd-9a14-32ee07433f66" alt="sombra_bot" width="170" height="370">
