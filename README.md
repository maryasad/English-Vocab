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
from flask import Flask
from app.routes import api_blueprint

app = Flask(__name__)
app.register_blueprint(api_blueprint)  # Register API routes

if __name__ == '__main__':
    app.run(debug=True)
```

**Database Connection (`db.py`)**
```python
import snowflake.connector

def get_db_connection():
    conn = snowflake.connector.connect(
        user='YOUR_USER',
        password='YOUR_PASSWORD',
        account='YOUR_ACCOUNT',
        database='VOCAB_DB',
        schema='PUBLIC'
    )
    return conn
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
python backend.py
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

