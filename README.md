# English Vocabulary App (Flutter + Flask + Snowflake)

## Overview
This is a **Flutter-based** English vocabulary learning app with a **Flask** backend and **Snowflake** as the database. The app allows users to learn new words and track progress while fetching and storing data securely.

## Prerequisites
Before starting, ensure you have the following installed:

### Backend (Flask + Snowflake)
- Python 3.x
- Flask
- Snowflake Connector for Python
- Snowflake account
- Postman (for API testing, optional)

### Frontend (Flutter)
- Flutter SDK
- Dart
- Visual Studio Code / Android Studio
- Windows Developer Mode (for Windows apps)

---

## Installation Steps

### 1️⃣ Set Up Snowflake Database
1. Sign in to your **Snowflake** account.
2. Create a **database** and 2 **tables** for storing vocabulary words and user progress:
   ```sql
      create or replace TABLE VOCAB_DB.PUBLIC.USER_PROGRESS (
      	USER_ID VARCHAR(16777216),
      	WORD_ID NUMBER(38,0),
      	LEARNED BOOLEAN DEFAULT FALSE,
      	QUIZ_SCORE NUMBER(38,0) DEFAULT 0,
      	LAST_REVIEWED TIMESTAMP_NTZ(9) DEFAULT CURRENT_TIMESTAMP(),
      	foreign key (WORD_ID) references VOCAB_DB.PUBLIC.WORDS(ID)
      );
      create or replace TABLE VOCAB_DB.PUBLIC.WORDS (
      	ID NUMBER(38,0) NOT NULL autoincrement start 1 increment 1 noorder,
      	WORD VARCHAR(50),
      	MEANING VARCHAR(16777216),
      	EXAMPLE_SENTENCE VARCHAR(16777216),
      	DIFFICULTY_LEVEL VARCHAR(20),
      	AUDIO_URL VARCHAR(16777216),
      	IMAGE_URL VARCHAR(16777216),
      	CREATED_AT TIMESTAMP_NTZ(9) DEFAULT CURRENT_TIMESTAMP(),
      	primary key (ID)
      );
   ```
3. Get your **Snowflake connection credentials** (account, username, password, warehouse, etc.).

### 2️⃣ Set Up Backend (Flask API)
1. Create a project folder and a virtual environment:
   ```sh
   mkdir english_vocab_backend
   cd english_vocab_backend
   python -m venv venv
   source venv/bin/activate  # On Windows use: venv\Scripts\activate
   ```
2. Install dependencies:
   ```sh
   pip install flask flask-cors snowflake-connector-python
   ```
3. Create `backend.py` with the following content:
   ```python
   from flask import Flask, jsonify
   import snowflake.connector
   from flask_cors import CORS

   app = Flask(__name__)
   CORS(app)

   def get_snowflake_connection():
       return snowflake.connector.connect(
           user='YOUR_USER',
           password='YOUR_PASSWORD',
           account='YOUR_ACCOUNT',
           warehouse='YOUR_WAREHOUSE',
           database='vocab_db',
           schema='public'
       )

   @app.route('/words', methods=['GET'])
   def get_words():
       conn = get_snowflake_connection()
       cursor = conn.cursor()
       cursor.execute("SELECT word, definition, example FROM words")
       words = cursor.fetchall()
       return jsonify(words)

   if __name__ == '__main__':
       app.run(debug=True)
   ```
4. Run the Flask server:
   ```sh
   python backend.py
   ```
   - The server should be running at **http://127.0.0.1:5000**.
   - Test API: **http://127.0.0.1:5000/words** (use Postman or a browser).

### 3️⃣ Set Up Flutter Frontend
1. Install **Flutter**:
   ```sh
   flutter doctor
   ```
   Ensure that Flutter is properly installed and that all dependencies are configured correctly.
2. Create a Flutter project:
   ```sh
   flutter create english_vocab_app
   cd english_vocab_app
   ```
3. Install dependencies:
   ```sh
   flutter pub add http
   ```
4. Open `lib/main.dart` and update it to fetch data from Flask API:
   ```dart
   import 'package:flutter/material.dart';
   import 'package:http/http.dart' as http;
   import 'dart:convert';

   void main() {
     runApp(VocabApp());
   }

   class VocabApp extends StatefulWidget {
     @override
     _VocabAppState createState() => _VocabAppState();
   }

   class _VocabAppState extends State<VocabApp> {
     List words = [];

     @override
     void initState() {
       super.initState();
       fetchWords();
     }

     Future<void> fetchWords() async {
       final response = await http.get(Uri.parse('http://127.0.0.1:5000/words'));
       if (response.statusCode == 200) {
         setState(() {
           words = json.decode(response.body);
         });
       }
     }

     @override
     Widget build(BuildContext context) {
       return MaterialApp(
         home: Scaffold(
           appBar: AppBar(title: Text('English Vocabulary')),
           body: ListView.builder(
             itemCount: words.length,
             itemBuilder: (context, index) {
               return ListTile(
                 title: Text(words[index][0]),
                 subtitle: Text(words[index][1]),
               );
             },
           ),
         ),
       );
     }
   }
   ```
5. Run the Flutter app:
   ```sh
   flutter run -d windows  # Or -d chrome for Web version
   ```

---

## Deployment

### Deploy Flask Backend
- Use **Heroku** or **Render** for hosting.
- Example (using Render):
  1. Create a **Render.com** account.
  2. Push your backend to GitHub.
  3. Connect Render to your GitHub repo and deploy it.
  4. Update the API URL in `main.dart`.

### Deploy Flutter App
- **Web**: Run `flutter build web` and host on Firebase/GitHub Pages.
- **Windows**: Run `flutter build windows` and create an installer.

---

## Future Improvements
- User authentication
- Advanced quiz features
- Speech-to-text integration
- Cloud-based Snowflake access

---

## Author
Developed by **[Your Name]** 🚀

