# CRYSTAL

**Never Miss What Matters.**

CRYSTAL is a completely offline, native Android productivity and reminder application built with Kotlin, Jetpack Compose, Material 3, Room, DataStore Preferences, Android `AlarmManager`, and Android Telecom (`ConnectionService`). No ads, no subscriptions, no paywalls, no accounts, and no `INTERNET` permission.

---

## Core Functionality

- **Task & Schedule Management:** Unlimited tasks with title, optional description, Material 3 date/time pickers, quick presets, priorities (`High`, `Medium`, `Low`), live category counters (`Upcoming`, `Overdue`, `Done`), search, and category/priority filtering.
- **Two Reminder Modes:**
  - **Normal Reminder:** High-priority local notification (`ReminderService` foreground `mediaPlayback`) with custom ringtone, vibration patterns, `Dismiss`, configurable `Snooze`, `Complete`, and `Play Voice` actions.
  - **Native Phone Call Reminder:** Official Android Telecom `PhoneAccount` (`CAPABILITY_CALL_PROVIDER`) routing reminders through the device's built-in Phone incoming-call screen (`CrystalConnectionService`) without replacing the default Phone app or placing cellular calls.
- **Custom Caller Details:** Per-reminder optional caller display name (falls back directly to task title with no `"Reminder:"` prefix), country code (default `+91`), and optional local display phone number via `setCallerDisplayName` and `setAddress`.
- **Offline Voice & Media Reminders:**
  - Strictly offline Android `TextToSpeech` (English & Hindi offline voices, customizable speech speed, pitch, custom spoken text, and 1x–3x repetition).
  - In-app `Record Audio` screen (`MediaRecorder`, pause/play preview, re-record, local private `filesDir/crystal_audio/` storage).
  - Storage Access Framework (SAF) audio attachments (`MP3`, `WAV`, `M4A`, `AAC`).
  - In Native Phone Call mode, voice/audio starts only after accepting the call and stops immediately on disconnect (with optional auto-end call after playback).
- **Recurring & Snooze Engine:** `Never`, `Daily`, `Weekly`, `Monthly`, `Yearly`, `Selected weekdays`, and custom intervals (`minutes`/`hours`/`days`/`weeks`/`months`), DST/month-end/leap-year handling, single-occurrence vs full-series editing/skipping, configurable snooze presets (`1m`–`1h` + custom `1–1440m`), plus reboot/time/timezone recovery via `ReminderBootReceiver` and `scheduled_reminders`.
- **Completion History, Statistics & Appearance:** Searchable/filterable `Completion History` with occurrence restoration; native Compose `Productivity Statistics` card inside Settings; `AMOLED Black`, `Clean White`, and `System` themes with an instant top-bar Sun/Moon theme toggle.
- **Offline ZIP/JSON Backup & Restore:** SAF export (`CRYSTAL_Backup_YYYY-MM-DD.zip`) bundling versioned `backup.json` (`CRYSTAL_BACKUP` v1), tasks, schedules, history, DataStore preferences, and local audio files; validated preview dialog with deduplicated **Merge** and confirmation-gated transactional **Replace**.

---

## Build Instructions

**Prerequisites:** JDK 17 (`JAVA_HOME`), Android SDK (`ANDROID_HOME`, API 34 compile/target SDK, minSdk 26), Gradle Wrapper (`8.7`).

```powershell
# Run JVM unit tests and build Debug APK (auto-installs via ADB if a device is connected)
.\gradlew.bat testDebugUnitTest assembleDebug --no-daemon

# Skip automatic ADB install when building locally
.\gradlew.bat assembleDebug -PskipAutoDeploy --no-daemon

# Build R8-optimized Release APK
.\gradlew.bat assembleRelease --no-daemon
```

### Release Signing Configuration

`app/build.gradle.kts` reads release signing credentials from either a gitignored `keystore.properties` file in the repository root or environment variables—never hardcoded:

- **Properties (`keystore.properties`):** `storeFile`, `storePassword`, `keyAlias`, `keyPassword`
- **Environment variables:** `CRYSTAL_KEYSTORE_PATH`, `CRYSTAL_KEYSTORE_PASSWORD`, `CRYSTAL_KEY_ALIAS`, `CRYSTAL_KEY_PASSWORD`

Do not install a release APK over a debug APK signed with a different certificate unless performing a fresh release installation.

---

## Android Permissions

All permissions are local and minimal (no `INTERNET`, `ACCESS_NETWORK_STATE`, `CALL_PHONE`, `SEND_SMS`, or `READ_CONTACTS` permissions):

| Permission | Purpose | When Needed |
|---|---|---|
| `SCHEDULE_EXACT_ALARM` | Fires exact reminder triggers (`AlarmClock`) in Doze | Normal & Native Call reminders |
| `POST_NOTIFICATIONS` | Displays reminder alerts, actions, and foreground service notice | Android 13+ alerts |
| `RECEIVE_BOOT_COMPLETED` | Restores active & snoozed reminders after device reboot | Always |
| `VIBRATE` | Plays configured vibration patterns | Normal Reminder vibration |
| `WAKE_LOCK` / `FOREGROUND_SERVICE` / `FOREGROUND_SERVICE_MEDIA_PLAYBACK` | Keeps active alarm playback reliable briefly when triggered | Active alarm playback only |
| `REQUEST_IGNORE_BATTERY_OPTIMIZATIONS` | Optional user shortcut for unrestricted background alarm delivery | Optional reliability card |
| `MANAGE_OWN_CALLS` / `READ_PHONE_STATE` | Official Telecom `ConnectionService` incoming call + active cellular call check | Native Phone Call mode |
| `RECORD_AUDIO` | Records personal voice notes in `Record Audio` | Requested only when tapping **Start Recording** |
