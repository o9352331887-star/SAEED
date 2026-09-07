# راهنمای تبدیل ZIP به APK

## توضیح فرآیند

تبدیل فایل ZIP اندروید به APK یک فرآیند چند مرحله‌ای است:

1. **استخراج ZIP** - محتویات فایل ZIP استخراج می‌شود
2. **تحقق ساختار** - بررسی وجود فایل‌های لازم (Manifest، کلاس‌های Java و...)
3. **کامپایل** - تبدیل کد Java به Dalvik Bytecode
4. **بسته‌بندی** - ایجاد فایل APK

## نیازمندی‌های سیستم

```bash
# Linux/macOS
sudo apt-get install openjdk-11-jdk  # Ubuntu/Debian
brew install openjdk@11              # macOS

# Download Android SDK
wget https://developer.android.com/studio
```

## راه‌های استفاده

### ✨ روش 1: Python (ترجیحی)

```bash
# نصب وابستگی‌ها (اگر نیاز باشد)
pip3 install -r requirements.txt

# اجرا
chmod +x zip_to_apk.py
python3 zip_to_apk.py my_app.zip my_app.apk
```

**مزایا:**
- ✅ عملکرد خودکار
- ✅ پیام‌های خطای واضح
- ✅ کراس‌پلتفرم (Windows, macOS, Linux)

---

### 🔧 روش 2: Bash Script

```bash
# اعطا دسترسی اجرایی
chmod +x build_apk.sh

# اجرا
./build_apk.sh my_app.zip my_app.apk
```

**مزایا:**
- ✅ سرعت بیشتر
- ✅ مناسب برای Linux/macOS

---

### 📦 روش 3: Gradle مستقیم

```bash
# فرض: پروژه Gradle دارید

# ساخت Debug APK
gradle assembleDebug

# ساخت Release APK
gradle assembleRelease

# APK در این مسیر یافت می‌شود:
# app/build/outputs/apk/debug/app-debug.apk
# app/build/outputs/apk/release/app-release.apk
```

---

### 🐳 روش 4: Docker

```dockerfile
FROM openjdk:11

# نصب Android SDK
RUN apt-get update && apt-get install -y android-sdk

# کپی فایل‌ها
COPY . /app
WORKDIR /app

# ساخت
CMD ["gradle", "build"]
```

```bash
docker build -t zip-to-apk .
docker run -v $(pwd):/app zip-to-apk
```

---

## ساختار فایل ZIP صحیح

ZIP شما باید ساختار زیر را داشته باشد:

```
my_app.zip
├── AndroidManifest.xml          ✅ الزامی
├── res/
│   ├── layout/
│   │   └── activity_main.xml
│   ├── drawable/
│   │   └── ic_launcher.png
│   └── values/
│       └── strings.xml
├── src/
│   └── main/
│       └── java/
│           └── com/example/app/
│               └── MainActivity.java
└── build.gradle                  (اختیاری)
```

## بررسی‌لیست

قبل از تبدیل:

- [ ] فایل ZIP معتبر است؟
- [ ] `AndroidManifest.xml` موجود است؟
- [ ] فایل‌های `.java` موجود هستند؟
- [ ] پوشه `res` دارای منابع است؟
- [ ] تمام مسیرها بدون فاصله و کاراکتر خاص هستند؟

## خطاهای رایج و حل‌ها

### ❌ خطا: "SDK Path not found"

```bash
# تنظیم مسیر SDK
export ANDROID_SDK_ROOT=$HOME/Android/Sdk
export PATH=$PATH:$ANDROID_SDK_ROOT/tools/bin
```

### ❌ خطا: "Gradle not found"

```bash
# نصب Gradle
brew install gradle        # macOS
sudo apt install gradle    # Linux

# یا دانلود مستقیم
wget https://services.gradle.org/distributions/gradle-7.6-bin.zip
```

### ❌ خطا: "Invalid Android Manifest"

```bash
# بررسی فایل Manifest
xmllint --noout AndroidManifest.xml

# یا استفاده از template ما
cp AndroidManifest.xml path/to/your/zip/
```

### ❌ خطا: "Compilation failed"

```bash
# بررسی نسخه Java
java -version

# نیاز به Java 11+
sudo update-alternatives --install /usr/bin/java java /usr/lib/jvm/java-11-openjdk-amd64/bin/java 1
```

---

## نمونه کامل

```bash
# 1. فایل ZIP را دانلود/آماده کنید
cd ~/Desktop
ls my_app.zip

# 2. اسکریپت Python را اجرا کنید
python3 zip_to_apk.py my_app.zip output.apk

# 3. نتیجه را بررسی کنید
ls -lh output.apk
file output.apk

# 4. نصب و تست
adb install -r output.apk
```

---

## اشکال‌زدایی

```bash
# لیست تمام فایل‌های ZIP
unzip -l my_app.zip | head -20

# استخراج و بررسی ساختار
unzip -q my_app.zip -d temp/
tree temp/

# بررسی Manifest
cat temp/AndroidManifest.xml

# بررسی فایل‌های Java
find temp/ -name "*.java"

# بررسی متا‌داده APK
aapt dump badging output.apk
```

---

## منابع و لینک‌های مفید

- 📖 [Android Studio Docs](https://developer.android.com/studio)
- 📖 [Gradle for Android](https://developer.android.com/build)
- 📖 [APK Format](https://en.wikipedia.org/wiki/Android_application_package)
- 🔗 [Android SDK Manager](https://developer.android.com/studio/command-line/sdkmanager)

---

## پشتیبانی

اگر مشکل داشتید:

1. لاگ‌های خطا را بررسی کنید
2. فایل‌های موقتی را حذف کرده دوباره تلاش کنید
3. اسکریپت را با `-v` برای Verbose Mode اجرا کنید
4. Issues را در GitHub باز کنید

---

**موفق باشید! 🚀**
