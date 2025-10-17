# RAG Tutorial v2

System Retrieval-Augmented Generation (RAG) wykorzystujący LangChain, ChromaDB i Ollama do odpowiadania na pytania na podstawie dokumentów PDF.

## Opis

Ten projekt implementuje system RAG, który:
- Ładuje dokumenty PDF z katalogu `data/`
- Dzieli dokumenty na chunki i tworzy embeddingi
- Przechowuje embeddingi w bazie wektorowej ChromaDB
- Odpowiada na pytania wyszukując relewanetne fragmenty i generując odpowiedzi za pomocą modelu Ollama

## Wymagania

- Python 3.11+
- Ollama zainstalowane lokalnie
- Modele Ollama:
  - `gemma3:27b-it-qat` (główny model LLM)
  - `embeddinggemma:300m` (model do tworzenia embeddingów)

## Instalacja

### 1. Utworzenie wirtualnego środowiska Python

Zalecane jest używanie wirtualnego środowiska Python, aby izolować zależności projektu:

**Na Windows:**
```bash
# Utwórz wirtualne środowisko (in bash)
python -m venv venv

# Aktywuj środowisko (in cmd)
venv\Scripts\activate
```

**Na macOS/Linux:**
```bash
# Utwórz wirtualne środowisko
python3 -m venv venv

# Aktywuj środowisko
source venv/bin/activate
```

**Deaktywacja środowiska (na wszystkich systemach):**
```bash
deactivate
```

### 2. Instalacja zależności

Po aktywacji wirtualnego środowiska zainstaluj wymagane biblioteki:

```bash (or in cmd, on running venv)
pip install -r requirements.txt
```

### 3. Instalacja Ollama i modeli

Zainstaluj Ollama i pobierz wymagane modele (see whatsap TODO: update):
```bash
ollama pull gemma3:27b-it-qat
ollama pull embeddinggemma:300m
```

### 4. Przygotowanie danych

Umieść pliki PDF w katalogu `data/`

## Użycie

### 1. Załadowanie dokumentów do bazy danych

```bash
python populate_database.py
```

Opcje:
- `--reset` - czyści bazę danych

### 2. Zadawanie pytań

```bash
python query_data.py "Twoje pytanie"
```

Przykład:
```bash
python query_data.py "How much total money does a player start with in Monopoly?"
```

### 3. Uruchamianie testów

```bash
pytest test_rag.py -v
```

## Struktura plików

- `populate_database.py` - ładuje PDFy, tworzy chunki i embeddingi
- `query_data.py` - obsługuje zapytania do systemu RAG
- `get_embedding_function.py` - konfiguracja funkcji embeddingów
- `test_rag.py` - testy automatyczne z weryfikacją odpowiedzi
- `requirements.txt` - lista zależności Python
- `data/` - katalog na pliki PDF (aktualnie pusty)
- `chroma/` - baza danych wektorowa (tworzona automatycznie)

## Konfiguracja

### Modele

W plikach można zmienić używane modele:

**query_data.py:**
```python
LLM_MODEL = "gemma3:27b-it-qat"  # Model do generowania odpowiedzi
```

**get_embedding_function.py:**
```python
EMBEDDING_MODEL_NAME = "embeddinggemma:300m"  # Model do embeddingów
```

### Parametry chunkowania

W `populate_database.py`:
```python
chunk_size=800         # Rozmiar chunka
chunk_overlap=80       # Nakładanie się chunków
```

## Testowanie

System zawiera automatyczne testy w `test_rag.py` które:
- Zadają pytania testowe
- Porównują odpowiedzi z oczekiwanymi rezultatami
- Używają modelu LLM do oceny poprawności odpowiedzi

## Rozwiązywanie problemów

1. **Brak modeli Ollama** - upewnij się, że modele są pobrane lokalnie
2. **Pusty katalog data/** - dodaj pliki PDF do katalogu `data/`
3. **Błędy połączenia** - sprawdź czy Ollama działa lokalnie
4. **Problemy z bibliotekami** - upewnij się, że wirtualne środowisko jest aktywne przed instalacją zależności
5. **Ostrzeżenie "invalid option provided mirostat_eta"** - to ostrzeżenie pochodzi z biblioteki LangChain Community i jest bezpieczne do zignorowania. Kod został zmodyfikowany, aby jawnie ustawić parametry mirostat na `None`, co minimalizuje te ostrzeżenia

### Częste ostrzeżenia i ich znaczenie

**Ostrzeżenie mirostat_eta:**
```
time=2025-09-14T06:40:56.617+02:00 level=WARN source=types.go:654 msg="invalid option provided" option=mirostat_eta
```
To ostrzeżenie jest bezpieczne i nie wpływa na działanie systemu. Występuje, gdy LangChain próbuje przekazać parametry mirostat do nowszych wersji Ollama, które ich nie obsługują.
