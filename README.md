# 📚 English Vocabulary Learning App

A vocabulary learning app that helps users practice and improve their English through interactive quizzes and progress tracking. Built with **Flutter (Frontend), Flask (Backend), and Snowflake (Database).**

---

## 📁 Project Structure

```
english_vocab_app/
│── backend/                  # Backend (Flask API)
│   ├── venv/                 # Virtual environment (optional but recommended)
│   ├── app/                  # Flask application
│   │   ├── __init__.py       # Initializes Flask app
│   │   ├── routes.py         # API routes for handling requests
│   │   ├── models.py         # Database models (SQLAlchemy)
│   │   ├── config.py         # Configurations (Snowflake credentials)
│   │   ├── db.py             # Database connection setup
│   ├── backend.py            # Main entry point to run Flask API
│   ├── requirements.txt      # Python dependencies
│── frontend/                 # Frontend (Flutter App)
│   ├── lib/                  # Main Flutter project files
│   │   ├── main.dart         # Main Dart file
│   │   ├── screens/          # UI screens
│   │   ├── widgets/          # Reusable UI components
│   │   ├── services/         # API integration (Flask backend)
│   │   ├── models/           # Data models for vocabulary
│   ├── pubspec.yaml          # Flutter dependencies
│── data/                     # Sample dataset
│   ├── seed_data.sql         # SQL script to insert sample words
│── README.md                 # Documentation
│── .gitignore                # Git ignore file
│── config.example.json       # Example Snowflake credentials (for env setup)
```

---

## 🚀 Setup Guide

### 1️⃣ Backend (Flask) Setup

#### Install Dependencies
```bash
cd english_vocab_app
mkdir backend && cd backend
python -m venv venv   # Create virtual environment
source venv/bin/activate  # Mac/Linux
venv\Scripts\activate  # Windows
pip install flask flask-cors snowflake-connector-python
```

#### Create and Configure Flask Backend

**Create `backend.py` (Main API File)**
```python
from app import create_app

app = create_app()

if __name__ == '__main__':
    app.run(debug=True)

```
**Create app/__init__.py (Initialize Flask App)**
```python
from flask import Flask
from app.routes import api
from app.db import init_db

def create_app():
    app = Flask(__name__)

    # Initialize database connection
    init_db()

    # Register API routes
    app.register_blueprint(api)

    return app
```
**app/config.py (Store Configurations)**
```python
import os

# Snowflake Database Configuration
SNOWFLAKE_CONFIG = {
    "user": "your_username",
    "password": "your_password",
    "account": "your_snowflake_account",
    "warehouse": "your_warehouse",
    "database": "VOCAB_DB",
    "schema": "PUBLIC",
}
```

**app/db.py (Connect to Snowflake)**
```python
import snowflake.connector
from app.config import SNOWFLAKE_CONFIG

def init_db():
    try:
        conn = snowflake.connector.connect(**SNOWFLAKE_CONFIG)
        print("✅ Connected to Snowflake!")
        return conn
    except Exception as e:
        print(f"❌ Database connection failed: {e}")
        return None

def get_words():
    """Fetch words from the Snowflake database."""
    conn = init_db()
    if conn:
        cur = conn.cursor()
        cur.execute("SELECT * FROM WORDS")
        words = cur.fetchall()
        cur.close()
        conn.close()
        return words
    return []
```
**app/models.py (Database Models)**
```python
class Word:
    def __init__(self, id, word, meaning, example_sentence, difficulty_level, audio_url, image_url):
        self.id = id
        self.word = word
        self.meaning = meaning
        self.example_sentence = example_sentence
        self.difficulty_level = difficulty_level
        self.audio_url = audio_url
        self.image_url = image_url

    def to_dict(self):
        return {
            "id": self.id,
            "word": self.word,
            "meaning": self.meaning,
            "example_sentence": self.example_sentence,
            "difficulty_level": self.difficulty_level,
            "audio_url": self.audio_url,
            "image_url": self.image_url,
        }

```
**app/routes.py  (API Endpoints)**
```python
from flask import Blueprint, jsonify
from app.db import get_words
from app.models import Word

api = Blueprint('api', __name__)

@api.route('/words', methods=['GET'])
def get_all_words():
    words_data = get_words()
    words = [Word(*word).to_dict() for word in words_data]
    return jsonify(words)
```

### 2️⃣ Frontend (Flutter) Setup

#### Install Flutter & Create Project
```bash
cd english_vocab_app
flutter create frontend
cd frontend
flutter pub add http
```

#### Fetch Data from Flask API

**Modify `lib/services/api_service.dart`**
```dart
import 'package:http/http.dart' as http;
import 'dart:convert';

class ApiService {
  static const String baseUrl = "http://127.0.0.1:5000";

  Future<List<dynamic>> fetchWords() async {
    final response = await http.get(Uri.parse('$baseUrl/words'));
    if (response.statusCode == 200) {
      return json.decode(response.body);
    } else {
      throw Exception('Failed to load words');
    }
  }
}
```

---

## 🎯 Running the App

### Backend (Flask API)
```bash
cd backend
source venv/bin/activate  # Activate virtual environment
python -m backend

```

### Frontend (Flutter UI)
```bash
cd frontend
flutter run -d windows  # Or use `flutter run -d chrome` for web
```

---

## 🎓 Database Schema (Snowflake)

```sql
CREATE OR REPLACE TABLE VOCAB_DB.PUBLIC.USER_PROGRESS (
    USER_ID VARCHAR(16777216),
    WORD_ID NUMBER(38,0),
    LEARNED BOOLEAN DEFAULT FALSE,
    QUIZ_SCORE NUMBER(38,0) DEFAULT 0,
    LAST_REVIEWED TIMESTAMP_NTZ(9) DEFAULT CURRENT_TIMESTAMP(),
    FOREIGN KEY (WORD_ID) REFERENCES VOCAB_DB.PUBLIC.WORDS(ID)
);

CREATE OR REPLACE TABLE VOCAB_DB.PUBLIC.WORDS (
    ID NUMBER(38,0) NOT NULL AUTOINCREMENT START 1 INCREMENT 1 NOORDER,
    WORD VARCHAR(50),
    MEANING VARCHAR(16777216),
    EXAMPLE_SENTENCE VARCHAR(16777216),
    DIFFICULTY_LEVEL VARCHAR(20),
    AUDIO_URL VARCHAR(16777216),
    IMAGE_URL VARCHAR(16777216),
    CREATED_AT TIMESTAMP_NTZ(9) DEFAULT CURRENT_TIMESTAMP(),
    PRIMARY KEY (ID)
);
```

---

## 🔥 Features  
✅ Vocabulary Database (Words, Meanings, Examples)  
✅ User Progress Tracking (Snowflake)  
✅ Flutter UI for Interactive Learning  
✅ Flask API for Data Retrieval  
✅ Cross-Platform (Windows, Web, Mobile)

---

## 🤝 Contributing
Feel free to submit issues, feature requests, or pull requests!

---

## 📜 License
This project is licensed under the MIT License.

---

🚀 **Happy Learning!**

