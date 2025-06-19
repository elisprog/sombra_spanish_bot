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
### 3. Был проведён кластерный анализ (тематическое моделирование) методом латентного размещения Дирихле LDA, в результате которого было выделено 5 кластеров

<img width="500" height="270" alt="Снимок экрана 2025-06-19 в 16 41 46" src="https://github.com/user-attachments/assets/4b027999-9caf-4cd8-9640-5a71cddd3fcd" />
<img width="500" height="270" alt="Снимок экрана 2025-06-19 в 16 41 52" src="https://github.com/user-attachments/assets/10358258-e561-4d2a-a2f4-185def5cecbb" />
<img width="500" height="270" alt="Снимок экрана 2025-06-19 в 16 41 56" src="https://github.com/user-attachments/assets/56fc6c10-009e-46c2-8157-13c3a43b51c6" />
<img width="500" height="270" alt="Снимок экрана 2025-06-19 в 16 41 59" src="https://github.com/user-attachments/assets/d26bae04-8006-484b-b42a-4e94d0f0f2a0" />
<img width="500" height="270" alt="Снимок экрана 2025-06-19 в 16 42 04" src="https://github.com/user-attachments/assets/55efe759-27ed-4eff-ae32-21bb165c83d6" />







<img>: <img src="https://github.com/user-attachments/assets/9fb5ccaf-af19-47cd-9a14-32ee07433f66" alt="sombra_bot" width="150" height="270">
