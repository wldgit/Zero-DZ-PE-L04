# 🤖 Локальный агент на Ollama (PE_L_04)

Простое консольное приложение с AI-агентом на базе LangChain/LangGraph и локальной модели Ollama (llama3.1).

![Python](https://img.shields.io/badge/Python-3.9+-blue.svg)
![Ollama](https://img.shields.io/badge/Ollama-llama3.1-green.svg)
![LangChain](https://img.shields.io/badge/LangChain-LangGraph-purple.svg)

## 📋 Описание

Приложение позволяет:
- **Запускать агента локально** — без облачных API, через [Ollama](https://ollama.ai) и модель llama3.1
- **Использовать инструменты** — агент умеет вызывать инструмент умножения двух чисел
- **Общаться в интерактивном режиме** — ввод с клавиатуры, ответ в консоль (например: «Сколько будет 7 умножить на 8?»)

Используется гибкая сборка агента: при наличии `langchain.agents.create_agent` — он, иначе — `langgraph.prebuilt.create_react_agent`.

## 🚀 Быстрый старт

### 1. Установка зависимостей

```bash
# Перейдите в каталог проекта
cd PE_L_04

# Создайте виртуальное окружение
python -m venv .venv

# Активируйте окружение
# Windows:
.venv\Scripts\activate
# Linux/Mac:
source .venv/bin/activate

# Установите зависимости
pip install -r requirements.txt
```

### 2. Настройка переменных окружения (по желанию)

Скопируйте пример и при необходимости измените модель или температуру:

```bash
# Windows (PowerShell):
copy env.example .env
# Linux/Mac:
cp env.example .env
```

Файл `.env` автоматически подхватывается при запуске. Если его нет, используются значения по умолчанию (модель `llama3.1`, temperature `0`). См. раздел [Смена модели Ollama](#-смена-модели-ollama).

### 3. Запуск Ollama и модели

Убедитесь, что установлена и запущена [Ollama](https://ollama.ai), и скачана модель `llama3.1`:

```bash
ollama list
ollama run llama3.1
```

При необходимости: `ollama pull llama3.1`

### 4. Запуск приложения

```bash
python main.py
```

В консоли появятся приветствие и подсказки. Вводите вопросы (например: «Умножь 12 на 5»), для выхода — `exit` или `quit`.

## 📁 Структура проекта

```
PE_L_04/
├── main.py           # Точка входа: LLM, инструмент multiply, сборка агента, цикл ввода
├── requirements.txt  # Зависимости Python
├── env.example       # Образец переменных окружения (скопировать в .env)
├── .env              # Локальные настройки (создаётся вручную из env.example)
└── README.md         # Этот файл
```

## 🔧 Функциональность

- **LLM**: `ChatOllama(model="llama3.1", temperature=0)`
- **Инструмент**: `multiply(a: int, b: int)` — умножение двух целых чисел
- **Агент**: ReAct-стиль (через LangChain или LangGraph), один тур диалога через `ask_agent(text)`
- **Интерактив**: цикл `input()` → вызов агента → вывод ответа или сообщения об ошибке

## 🛠️ Технологии

- **Python**: 3.9+
- **AI**: Ollama (локально), модель llama3.1
- **Фреймворк**: LangChain (langchain-ollama, langchain-core), LangGraph (create_react_agent)

## 🔄 Смена модели Ollama

Модель и «креативность» (temperature) задаются через файл `.env`, переменные окружения в терминале или правку кода.

### Через файл .env (удобно для постоянной настройки)

Скопируйте `env.example` в `.env` и отредактируйте:

```env
OLLAMA_MODEL=llama3.2
OLLAMA_TEMPERATURE=0.3
```

При запуске `python main.py` значения из `.env` подхватываются автоматически.

### Через переменные окружения в терминале

Перед запуском `python main.py`:

```bash
# Windows (PowerShell):
$env:OLLAMA_MODEL = "llama3.2"
$env:OLLAMA_TEMPERATURE = "0.3"
python main.py

# Windows (cmd):
set OLLAMA_MODEL=llama3.2
set OLLAMA_TEMPERATURE=0.3
python main.py

# Linux/Mac:
export OLLAMA_MODEL=llama3.2
export OLLAMA_TEMPERATURE=0.3
python main.py
```

По умолчанию: `OLLAMA_MODEL=llama3.1`, `OLLAMA_TEMPERATURE=0`.

### Через правку кода

В `main.py` измените константы (или логику их задания):

```python
_OLLAMA_MODEL = os.environ.get("OLLAMA_MODEL", "llama3.1")   # подставьте свою модель
_OLLAMA_TEMPERATURE = float(os.environ.get("OLLAMA_TEMPERATURE", "0"))
llm = ChatOllama(model=_OLLAMA_MODEL, temperature=_OLLAMA_TEMPERATURE)
```

### Примеры моделей в Ollama

| Модель        | Описание                          | Команда загрузки          |
|---------------|-----------------------------------|---------------------------|
| `llama3.1`    | Базовая модель по умолчанию       | `ollama pull llama3.1`    |
| `llama3.2`    | Более новая версия LLaMA          | `ollama pull llama3.2`    |
| `llama3.2:1b` | Облегчённая (1B)                  | `ollama pull llama3.2:1b` |
| `mistral`     | Альтернатива, хороший баланс      | `ollama pull mistral`     |
| `phi3`        | Компактная модель от Microsoft    | `ollama pull phi3`        |
| `qwen2.5`     | Многоязычная модель               | `ollama pull qwen2.5`     |
| `qwen3.5:4b ` | Многоязычная модель               | `ollama pull qwen3.5:4b`  |

Список установленных: `ollama list`. Каталог моделей: [ollama.com/library](https://ollama.com/library).

### Temperature

- `0` — детерминированные ответы (удобно для арифметики и точных задач).
- `0.3–0.7` — более разнообразные формулировки, возможны «галлюцинации».
- Выше `0.7` — креативнее, но менее предсказуемо.

## ⚠️ Требования

- **Python**: 3.10–3.13 (рекомендуется 3.11 или 3.12). Для **Python 3.14** совместимость пакетов `langchain-*` и `langgraph` может отставать — при ошибках импорта обновите пакеты до последних версий (`pip install -U langchain-ollama langchain-core langgraph`) или используйте Python 3.12/3.13.
- Установленная и запущенная [Ollama](https://ollama.ai) с нужной моделью (по умолчанию `llama3.1`).
- Для работы агента интернет не нужен (всё выполняется локально).

## 📝 Лицензия

MIT License
