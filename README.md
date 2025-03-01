# English Vocabulary App (Flutter + Flask)

This project is a **Flutter-based English Vocabulary App** with a **Flask backend** for managing data.

---
## **Prerequisites**
### **1. Install Required Tools**
- Install [Python](https://www.python.org/downloads/) (for Flask backend)
- Install [Flutter](https://flutter.dev/docs/get-started/install)
- Install [Visual Studio](https://visualstudio.microsoft.com/) with C++ development tools
- Install [Android Studio](https://developer.android.com/studio) (for mobile support)
- Install [Git](https://git-scm.com/downloads) (optional, but recommended)

### **2. Setup Flutter Environment**
```sh
flutter doctor
```
Fix any issues reported by `flutter doctor`, especially missing dependencies.

---
## **Step 1: Setup Flask Backend**
### **1. Create Backend Directory**
```sh
mkdir english_vocab_backend && cd english_vocab_backend
```

### **2. Setup Virtual Environment**
```sh
python -m venv venv
source venv/bin/activate  # On macOS/Linux
venv\Scripts\activate     # On Windows
```

### **3. Install Flask and Required Libraries**
```sh
pip install flask flask-cors
```

### **4. Create `backend.py`**
```python
from flask import Flask, jsonify
from flask_cors import CORS

app = Flask(__name__)
CORS(app)

@app.route("/words", methods=["GET"])
def get_words():
    words = ["Hello", "World", "Flutter", "Python"]
    return jsonify(words)

if __name__ == "__main__":
    app.run(debug=True)
```

### **5. Run the Flask Backend**
```sh
python backend.py
```
It should run at: **http://127.0.0.1:5000/words**

---
## **Step 2: Create Flutter App**
### **1. Create a New Flutter Project**
```sh
flutter create english_vocab_app
cd english_vocab_app
```

### **2. Add Dependencies**
Edit `pubspec.yaml`:
```yaml
dependencies:
  flutter:
    sdk: flutter
  http: ^0.13.3
```
Run:
```sh
flutter pub get
```

---
## **Step 3: Fetch Data from Flask API**
### **Modify `lib/main.dart`**
```dart
import 'package:flutter/material.dart';
import 'package:http/http.dart' as http;
import 'dart:convert';

void main() {
  runApp(MyApp());
}

class MyApp extends StatelessWidget {
  Future<List<String>> fetchWords() async {
    final response = await http.get(Uri.parse('http://127.0.0.1:5000/words'));
    if (response.statusCode == 200) {
      return List<String>.from(jsonDecode(response.body));
    } else {
      throw Exception('Failed to load words');
    }
  }

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      home: Scaffold(
        appBar: AppBar(title: Text('Vocabulary App')),
        body: FutureBuilder<List<String>>(
          future: fetchWords(),
          builder: (context, snapshot) {
            if (snapshot.connectionState == ConnectionState.waiting) {
              return Center(child: CircularProgressIndicator());
            } else if (snapshot.hasError) {
              return Center(child: Text('Error: ${snapshot.error}'));
            } else {
              return ListView.builder(
                itemCount: snapshot.data?.length ?? 0,
                itemBuilder: (context, index) {
                  return ListTile(
                    title: Text(snapshot.data![index]),
                  );
                },
              );
            }
          },
        ),
      ),
    );
  }
}
```

---
## **Step 4: Run Flutter App**
### **1. Run on Windows**
Ensure Windows desktop support is enabled:
```sh
flutter config --enable-windows-desktop
```
Then, run the app:
```sh
flutter run -d windows
```

### **2. Run on Web**
```sh
flutter run -d chrome
```

### **3. Run on Android Emulator**
Ensure an emulator is running, then execute:
```sh
flutter run -d emulator-5554
```

---
## **Step 5: Build and Deploy**
### **1. Build Windows App**
```sh
flutter build windows
```
Find the executable in `build/windows/runner/Release/`

### **2. Build Web App**
```sh
flutter build web
```
Deploy the files in `build/web/` to a hosting service.

### **3. Build APK for Android**
```sh
flutter build apk
```

---
## **Troubleshooting**
### **Fix: Enable Windows Developer Mode**
If you see **"Building with plugins requires symlink support"**, enable Developer Mode:
```sh
start ms-settings:developers
```

### **Fix: Missing C++ Dependencies**
If you get an error like **"atlstr.h: No such file or directory"**, install **ATL components** in Visual Studio:
- Open **Visual Studio Installer**
- Modify installation and enable **C++ ATL for v143 build tools**

---
## **Conclusion**
You have successfully built a **Flutter app with a Flask backend**! 🎉

- 🖥 **Run it on Windows, Web, and Android**
- 🚀 **Deploy as a standalone application**

Happy coding! 😊

