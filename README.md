# MedReminder — AI Medicine Schedule Assistant

> A complete Flutter Android application for managing medicine schedules,
> tracking doses, and receiving local reminders.

---

## Medical Disclaimer
This app is a **reminder and scheduling tool only**.
It does not provide medical advice, diagnose conditions, recommend medicines,
or adjust prescribed dosages. Always consult a qualified healthcare provider.

---

## Features
| # | Feature | Status |
|---|---|---|
| A | Home dashboard with today's schedule | ✅ |
| B | Add / edit / delete medicines | ✅ |
| C | Local notifications with recurrence | ✅ |
| D | Offline SQLite database | ✅ |
| E | Dose tracking (taken / skipped / missed) | ✅ |
| F | OCR medicine label scanner (ML Kit) | ✅ |
| G | Stock & refill reminders | ✅ |
| H | AI/ML adherence insights | ✅ |
| I | Settings & privacy | ✅ |

---

## Tech Stack
- **Flutter 3.x** · Dart 3.x
- **sqflite** — offline SQLite storage
- **flutter_local_notifications** — recurring Android notifications
- **google_mlkit_text_recognition** — on-device OCR
- **image_picker** — camera & gallery access
- **table_calendar** — schedule calendar view
- **Python 3.11 + scikit-learn** — ML prototype (standalone, offline)

---

## Project Structure
```
med_reminder/
├── lib/
│   ├── main.dart                        # App entry point + splash + shell
│   ├── theme/app_theme.dart             # Material 3 colour & typography
│   ├── models/
│   │   ├── medicine.dart               # Medicine data model
│   │   └── dose_record.dart            # Dose event model
│   ├── database/database_service.dart  # SQLite layer (singleton)
│   ├── services/
│   │   ├── notification_service.dart   # Local notification scheduling
│   │   ├── ocr_service.dart            # ML Kit OCR wrapper
│   │   └── ml_insights_service.dart    # Rule-based adherence insights
│   ├── screens/
│   │   ├── home_screen.dart
│   │   ├── medicine_list_screen.dart
│   │   ├── medicine_form_screen.dart
│   │   ├── schedule_screen.dart
│   │   ├── history_screen.dart
│   │   ├── scanner_screen.dart
│   │   └── settings_screen.dart
│   └── widgets/
│       ├── medicine_card.dart
│       ├── reminder_card.dart
│       └── empty_state.dart
├── ml/                                  # Python ML prototype (standalone)
│   ├── train_model.py
│   ├── sample_data.csv
│   ├── requirements.txt
│   └── README.md
├── android/app/src/main/
│   └── AndroidManifest.xml
├── pubspec.yaml
└── README.md
```

---

## Getting Started

### Prerequisites
- Flutter SDK 3.x ([install guide](https://docs.flutter.dev/get-started/install))
- Android Studio with API 34 SDK
- A physical Android device or emulator (API 21+)

### Run the app

```bash
# 1. Get dependencies
flutter pub get

# 2. Check setup
flutter doctor

# 3. Run on connected device / emulator
flutter run
```

### Build a release APK

```bash
flutter build apk --release
# Output: build/app/outputs/flutter-apk/app-release.apk
```

Transfer the APK to an Android device and install it, or use:
```bash
flutter install
```

---

## Android Notification Restrictions
- **Android 12+ (API 31+):** Requires `SCHEDULE_EXACT_ALARM` permission.
  On some devices this may need to be granted manually in
  *Settings → Apps → MedReminder → Alarms & Reminders*.
- **Android 13+ (API 33+):** Also requires `POST_NOTIFICATIONS` permission —
  the app requests this at first launch.
- **Battery optimisation:** Some manufacturers (Xiaomi, Huawei, Samsung) aggressively
  kill background processes. Users may need to whitelist MedReminder in
  battery settings for reliable notification delivery.
- **Exact vs inexact alarms:** When exact alarm permission is not granted the OS
  may delay notifications by several minutes. This is an OS limitation.

---

## Python ML Module
See [`ml/README.md`](ml/README.md) for setup and usage.

---

## Architecture Overview (B.Tech Viva)

```
┌─────────────────────────────────────────────┐
│              Flutter UI Layer               │
│  Screens ─ Widgets ─ Theme ─ Navigation     │
└───────────────────┬─────────────────────────┘
                    │
┌───────────────────▼─────────────────────────┐
│           Service / Business Logic          │
│  NotificationService  OcrService            │
│  MlInsightsService    DatabaseService       │
└───────────┬───────────────────┬─────────────┘
            │                   │
┌───────────▼──────┐   ┌────────▼──────────────┐
│  SQLite (sqflite)│   │  Android OS APIs       │
│  med_reminder.db │   │  AlarmManager / Camera │
│  medicines table │   │  ML Kit (on-device)    │
│  dose_records    │   └───────────────────────┘
└──────────────────┘

Python ML Prototype (standalone, offline)
  sample_data.csv → train_model.py → adherence_model.pkl
```

**Data flow for a reminder:**
1. User adds medicine → `DatabaseService.insertMedicine()`
2. `NotificationService.scheduleMedicineReminders()` calls `plugin.zonedSchedule()`
3. Android `AlarmManager` fires at scheduled time
4. User opens notification → taps action → dose record updated in SQLite
5. `MlInsightsService` reads history → returns non-clinical pattern insights

---

## Limitations
- Notification delivery is not guaranteed on all Android devices due to
  manufacturer battery optimisations.
- OCR accuracy depends on image quality and label font.
- ML insights require ≥7 recorded doses to generate; the Python model
  requires ≥50 for reliable accuracy.
- The app is offline-first; no cloud sync is implemented.

## Future Enhancements
- [ ] Caregiver mode (with explicit consent)
- [ ] Cloud backup via Firebase (opt-in)
- [ ] Wear OS companion
- [ ] Pill identification from image (requires curated dataset)
- [ ] FastAPI endpoint serving the Python model for richer insights
- [ ] iOS support

---

## Privacy
All data is stored locally on the device using SQLite.
No personal health data is transmitted to any server.
Users can delete all data from Settings → Delete All My Data.
