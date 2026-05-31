# IkLog – Android File‑Based Logger

**A lightweight, zero‑dependency logging utility that writes timestamped logs directly to the public Downloads directory.**

No Jetpack. No external libraries. Just a single static class that mirrors `android.util.Log` and automatically stores every log line in a plain‑text file – ideal for debugging on remote devices, beta testing, or when Logcat isn't available.

---

## ✨ Features

- 🪵 **Mirrors `android.util.Log`** – use `IkLog.d(TAG, msg)`, `IkLog.e(TAG, msg, throwable)`, etc. exactly like the standard API.
- 📂 **Writes to Downloads** – logs are saved as `onwa_debug_log.txt` in the public Downloads folder, accessible without root or ADB.
- 🔁 **Automatic rotation** – when the log file exceeds 5 MB, the old file is deleted and a new one with the same name is created. Only one file exists at a time.
- 🧠 **Self‑initialising** – if you never call `IkLog.init(context)`, the first log line will obtain the application context via reflection and set itself up automatically.
- 🛑 **Disable support** – call `IkLog.disable()` to stop all file output (Logcat still works). Useful on release builds or when shutting down.
- ⚡ **Instant flush** – toggle `IkLog.setInstantFlush(true)` to force every log line to be written to disk immediately instead of being buffered.
- 📦 **Scoped‑storage ready** – uses `MediaStore` for file creation on API 29+ and falls back to `File` APIs on older devices.
- 🧵 **Background thread** – all file I/O runs on a dedicated `HandlerThread`, keeping the main thread free.
- 📝 **StackTrace support** – `IkLog.e(tag, msg, throwable)` automatically captures and formats the full stack trace.
- 🌍 **Zero dependencies** – built entirely on the Android SDK; no Jetpack, no Kotlin, no extra libraries.

---

## 📦 Installation

### Option 1 – JitPack (recommended)

Add the JitPack repository to your root `build.gradle` (or `settings.gradle`):

```gradle
allprojects {
    repositories {
        ...
        maven { url 'https://jitpack.io' }
    }
}
```

Then add the dependency:

```gradle
dependencies {
    implementation 'com.github.dav24wils:IkLog:Tag'
}
```

Replace Tag with the latest release tag (e.g., v1.0.0).

Option 2 – Copy the source file

IkLog is a single Java file (IkLog.java). Copy it into your project – no additional configuration required.

---

🚀 Quick Start

```java
// Optional – initialise explicitly (recommended in Application.onCreate)
IkLog.init(getApplicationContext());

// Log messages just like android.util.Log
IkLog.d("MyTag", "User logged in");
IkLog.i("MyTag", "Download started");
IkLog.w("MyTag", "Slow network detected");
IkLog.e("MyTag", "Failed to parse JSON", exception);

// When your app is about to be killed, call disable to flush and clean up
IkLog.disable();
```

That's it! Logs appear in Downloads/onwa_debug_log.txt on your device.

---

🛠️ Detailed Usage

Initialisation

```java
// Explicit (preferred)
IkLog.init(getApplicationContext());   // safe to call multiple times

// Automatic
// The first log line will auto‑initialise using reflection.  No init() required.
```

Logging levels

All standard log levels are available:

Method Priority
IkLog.v(tag, msg) VERBOSE
IkLog.d(tag, msg) DEBUG
IkLog.i(tag, msg) INFO
IkLog.w(tag, msg) WARN
IkLog.e(tag, msg) ERROR
IkLog.e(tag, msg, throwable) ERROR + stacktrace
IkLog.wtf(tag, msg) ASSERT

Every call is immediately printed to Logcat and then appended to the internal buffer for file writing.

Controlling file output

```java
// Disable file logging entirely (Logcat still works)
IkLog.disable();

// Enable instant flushing (every log line is written to disk immediately)
IkLog.setInstantFlush(true);
```

Reading the log file

Connect your device via USB and browse to /storage/emulated/0/Download/onwa_debug_log.txt, or use the Android file manager.

---

⚙️ How It Works

1. Buffer – log lines are accumulated in a StringBuilder.
2. Flush trigger – the buffer is flushed to disk either:
   · When sInstantFlush is true (immediately after each log), or
   · After the buffer reaches 20 lines (async), or
   · Every 1 second via a periodic Handler tick.
3. File writing – on API 29+ it uses ContentResolver.openOutputStream on a MediaStore‑inserted URI; on older APIs it writes directly to a File in the public Downloads directory.
4. Rotation – once the file exceeds 5 MB, the current file is deleted and a new one is created with the same name.

All disk I/O happens on a dedicated background thread, so logging never blocks the UI.

---

📄 License

```
MIT License

Copyright (c) 2025 David Wilson Okere

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```

---

👤 Author

David Wilson Okere

· GitHub: dav24wils
· Repository: IkLog

---

⭐ Show your support

If this project helped you, please consider giving it a ⭐ on GitHub – it really helps!
