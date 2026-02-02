# Android Coding Quiz App - Architecture Diagrams

## 🏗️ Overall System Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         USER DEVICE                          │
│                                                               │
│  ┌─────────────────────────────────────────────────────┐    │
│  │              PRESENTATION LAYER                      │    │
│  │  ┌──────────┐  ┌──────────┐  ┌──────────────┐      │    │
│  │  │Activities│  │Fragments │  │  ViewModels  │      │    │
│  │  │          │  │          │  │              │      │    │
│  │  │ -Splash  │  │ -Home    │  │ LiveData/    │      │    │
│  │  │ -Login   │  │ -Progress│  │ StateFlow    │      │    │
│  │  │ -SignUp  │  │ -Settings│  │              │      │    │
│  │  │ -Main    │  │          │  │              │      │    │
│  │  │ -Question│  │          │  │              │      │    │
│  │  └──────────┘  └──────────┘  └──────────────┘      │    │
│  └─────────────────────────────────────────────────────┘    │
│                           ↕                                  │
│  ┌─────────────────────────────────────────────────────┐    │
│  │               DOMAIN LAYER                           │    │
│  │  ┌──────────────────┐  ┌──────────────────┐         │    │
│  │  │   Use Cases      │  │  Domain Models   │         │    │
│  │  │                  │  │                  │         │    │
│  │  │ -LoginUseCase    │  │ -Question        │         │    │
│  │  │ -GetQuestionUse  │  │ -User            │         │    │
│  │  │  Case            │  │ -UserProgress    │         │    │
│  │  │ -SubmitAnswerUse │  │ -ProgrammingLang │         │    │
│  │  │  Case            │  │                  │         │    │
│  │  └──────────────────┘  └──────────────────┘         │    │
│  │                                                       │    │
│  │  ┌──────────────────────────────────────┐           │    │
│  │  │    Repository Interfaces              │           │    │
│  │  │  -QuestionRepository                  │           │    │
│  │  │  -UserRepository                      │           │    │
│  │  │  -ProgressRepository                  │           │    │
│  │  └──────────────────────────────────────┘           │    │
│  └─────────────────────────────────────────────────────┘    │
│                           ↕                                  │
│  ┌─────────────────────────────────────────────────────┐    │
│  │                DATA LAYER                            │    │
│  │  ┌──────────────────────────────────────┐           │    │
│  │  │    Repository Implementations         │           │    │
│  │  └──────────────────────────────────────┘           │    │
│  │            ↙                    ↘                    │    │
│  │  ┌─────────────────┐    ┌─────────────────┐        │    │
│  │  │  Local Data     │    │  Remote Data    │        │    │
│  │  │                 │    │                 │        │    │
│  │  │ ┌─────────────┐ │    │ ┌─────────────┐ │        │    │
│  │  │ │ Room DB     │ │    │ │Firebase Auth│ │        │    │
│  │  │ │ -Questions  │ │    │ │             │ │        │    │
│  │  │ │ -Progress   │ │    │ └─────────────┘ │        │    │
│  │  │ └─────────────┘ │    │                 │        │    │
│  │  │                 │    │ ┌─────────────┐ │        │    │
│  │  │ ┌─────────────┐ │    │ │ Firestore   │ │        │    │
│  │  │ │ DataStore   │ │    │ │ -Users      │ │        │    │
│  │  │ │ -Preferences│ │    │ │ -Questions  │ │        │    │
│  │  │ └─────────────┘ │    │ │ -Progress   │ │        │    │
│  │  └─────────────────┘    │ └─────────────┘ │        │    │
│  │                         └─────────────────┘        │    │
│  └─────────────────────────────────────────────────────┘    │
│                                                               │
│  ┌─────────────────────────────────────────────────────┐    │
│  │         NOTIFICATION SYSTEM (Background)             │    │
│  │  ┌──────────────────────────────────────┐           │    │
│  │  │        WorkManager                    │           │    │
│  │  │  ┌──────────────────────────────┐    │           │    │
│  │  │  │  NotificationWorker          │    │           │    │
│  │  │  │  - Runs periodically         │    │           │    │
│  │  │  │  - Fetches random question   │    │           │    │
│  │  │  │  - Shows notification        │    │           │    │
│  │  │  └──────────────────────────────┘    │           │    │
│  │  └──────────────────────────────────────┘           │    │
│  └─────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────┘
                              ↕
                    ┌─────────────────────┐
                    │   Firebase Cloud    │
                    │  ┌───────────────┐  │
                    │  │ Authentication│  │
                    │  └───────────────┘  │
                    │  ┌───────────────┐  │
                    │  │   Firestore   │  │
                    │  │   Database    │  │
                    │  └───────────────┘  │
                    │  ┌───────────────┐  │
                    │  │   Analytics   │  │
                    │  └───────────────┘  │
                    └─────────────────────┘
```

---

## 📱 User Flow Diagram

```
START
  │
  ▼
┌─────────────────┐
│ SplashActivity  │──────► Check if user logged in?
└─────────────────┘              │
                                 ├─── NO ──► ┌──────────────┐
                                 │           │LoginActivity │
                                 │           └──────┬───────┘
                                 │                  │
                                 │                  │ New User?
                                 │                  │
                                 │                  ▼
                                 │           ┌──────────────┐
                                 │           │SignUpActivity│
                                 │           │              │
                                 │           │ 1. Email/Pass│
                                 │           │ 2. Select    │
                                 │           │    Languages │
                                 │           └──────┬───────┘
                                 │                  │
                                 ▼                  │
                          ┌─────────────────────────┘
                          │
                          ▼
                   ┌──────────────┐
                   │MainActivity  │
                   │              │
                   │ Bottom Nav:  │
                   │ - Home       │
                   │ - Progress   │
                   │ - Settings   │
                   └──────┬───────┘
                          │
         ┌────────────────┼────────────────┐
         │                │                │
         ▼                ▼                ▼
  ┌────────────┐  ┌────────────┐  ┌────────────┐
  │   Home     │  │  Progress  │  │  Settings  │
  │  Fragment  │  │  Fragment  │  │  Fragment  │
  │            │  │            │  │            │
  │ - Stats    │  │ - Total Q  │  │ - Toggle   │
  │ - Streak   │  │ - Accuracy │  │   Notif.   │
  │ - Start    │  │ - History  │  │ - Freq.    │
  │   Quiz Btn │  │            │  │ - Logout   │
  └────┬───────┘  └────────────┘  └────────────┘
       │
       │ Click "Start Quiz"
       │
       ▼
  ┌──────────────────┐
  │ QuestionActivity │
  │                  │
  │ 1. Show code     │
  │ 2. Show question │
  │ 3. 4 options     │
  │ 4. Submit button │
  └────┬─────────────┘
       │
       │ Submit Answer
       │
       ▼
  ┌──────────────────┐
  │  Result Dialog   │
  │                  │
  │ ✓ Correct/Wrong  │
  │ ✓ Explanation    │
  │ ✓ Next Question  │
  └────┬─────────────┘
       │
       │ Close Dialog
       │
       ▼
  Back to MainActivity (updated stats)


NOTIFICATION FLOW (Background):
  
  ┌──────────────────┐
  │  WorkManager     │
  │  Triggers at:    │
  │  9 AM, 2 PM,     │
  │  7 PM (3x/day)   │
  └────┬─────────────┘
       │
       ▼
  ┌──────────────────┐
  │NotificationWorker│
  │ - Get random Q   │
  │ - Create notif   │
  └────┬─────────────┘
       │
       ▼
  ┌──────────────────┐
  │  Notification    │
  │  appears on      │
  │  device          │
  └────┬─────────────┘
       │
       │ User taps notification
       │
       ▼
  Opens QuestionActivity
  with specific question ID
```

---

## 💾 Data Flow Diagram

```
USER ACTION: "Submit Answer"
  │
  ▼
┌─────────────────────┐
│ QuestionActivity    │
│ btnSubmit.onClick() │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│  QuestionViewModel  │
│  submitAnswer()     │
└──────────┬──────────┘
           │
           ▼
┌──────────────────────┐
│ SubmitAnswerUseCase  │
│ - Validate answer    │
│ - Calculate result   │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│  ProgressRepository  │
│  recordAnswer()      │
└──────────┬───────────┘
           │
           ├──────────────────────────┐
           │                          │
           ▼                          ▼
┌──────────────────┐        ┌──────────────────┐
│  Room Database   │        │    Firestore     │
│  INSERT INTO     │        │  (Background     │
│  user_progress   │        │   Sync)          │
└──────────────────┘        └──────────────────┘
           │
           │ Result (Success/Failure)
           │
           ▼
┌──────────────────────┐
│ SubmitAnswerUseCase  │
│ return isCorrect     │
└──────────┬───────────┘
           │
           ▼
┌─────────────────────┐
│  QuestionViewModel  │
│  _answerResult.value│
│  = result           │
└──────────┬──────────┘
           │
           ▼
┌─────────────────────┐
│ QuestionActivity    │
│ observe answerResult│
│ showResultDialog()  │
└─────────────────────┘
           │
           ▼
       USER SEES RESULT
```

---

## 🔔 Notification System Flow

```
APP STARTUP / SETTINGS CHANGE
  │
  ▼
┌───────────────────────────┐
│  NotificationScheduler    │
│  scheduleNotifications()  │
└────────────┬──────────────┘
             │
             │ Check user preferences
             │ (enabled? frequency?)
             │
             ▼
┌────────────────────────────┐
│   WorkManager              │
│   Schedule periodic work:  │
│   - Interval: 8h (3x/day)  │
│   - Flex: 15 min           │
│   - Constraints: None      │
└────────────┬───────────────┘
             │
             │ ... time passes ...
             │
             ▼
┌────────────────────────────┐
│   TRIGGER TIME             │
│   (9 AM, 2 PM, or 7 PM)    │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│  NotificationWorker        │
│  doWork()                  │
└────────────┬───────────────┘
             │
             ├─────► Check if notifications enabled
             │       (if disabled, return Success)
             │
             ├─────► Get user's selected languages
             │
             ├─────► Query Room DB for random question
             │       WHERE language IN (userLanguages)
             │
             ▼
┌────────────────────────────┐
│  Question Retrieved        │
│  {id, language, code, ...} │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│  NotificationHelper        │
│  showQuestionNotification()│
└────────────┬───────────────┘
             │
             │ Create PendingIntent
             │ → QuestionActivity(questionId)
             │
             ▼
┌────────────────────────────┐
│  NotificationCompat        │
│  .Builder                  │
│  - Title: "Coding Challenge│
│  - Text: Question preview  │
│  - Intent: Open app        │
│  - Icon, Sound, Vibration  │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│  NotificationManager       │
│  notify(NOTIFICATION_ID)   │
└────────────┬───────────────┘
             │
             ▼
┌────────────────────────────┐
│  NOTIFICATION APPEARS      │
│  ON USER'S DEVICE          │
└────────────┬───────────────┘
             │
             │ USER TAPS NOTIFICATION
             │
             ▼
┌────────────────────────────┐
│  QuestionActivity          │
│  onCreate()                │
│  - Get questionId from     │
│    intent extras           │
│  - Load & display question │
└────────────────────────────┘
```

---

## 🗄️ Database Schema Diagram

```
┌─────────────────────────────────────────────┐
│           ROOM DATABASE (Local)              │
│                                               │
│  ┌────────────────────────────────────┐     │
│  │  TABLE: questions                  │     │
│  ├────────────────────────────────────┤     │
│  │ id (PK)              VARCHAR       │     │
│  │ language             VARCHAR       │     │
│  │ difficulty           VARCHAR       │     │
│  │ codeSnippet          TEXT          │     │
│  │ question             TEXT          │     │
│  │ optionsJson          TEXT          │     │
│  │ correctAnswerIndex   INTEGER       │     │
│  │ explanation          TEXT          │     │
│  │ topic                VARCHAR       │     │
│  │ createdAt            BIGINT        │     │
│  │ lastUpdated          BIGINT        │     │
│  └────────────────────────────────────┘     │
│                                               │
│  ┌────────────────────────────────────┐     │
│  │  TABLE: user_progress              │     │
│  ├────────────────────────────────────┤     │
│  │ id (PK, autoincr)    INTEGER       │     │
│  │ questionId (FK)      VARCHAR       │◄────┤─ Indexed
│  │ answeredAt           BIGINT        │     │
│  │ isCorrect            BOOLEAN       │     │
│  │ timeTaken            INTEGER       │     │
│  └────────────────────────────────────┘     │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│      DATASTORE (Local Preferences)           │
│                                               │
│  - userId                 String             │
│  - selectedLanguages      List<String>       │
│  - notificationsEnabled   Boolean            │
│  - notificationFrequency  Int                │
│  - dailyGoal              Int                │
│  - lastSyncTime           Long               │
└─────────────────────────────────────────────┘

┌─────────────────────────────────────────────┐
│       FIRESTORE (Cloud Backup)               │
│                                               │
│  COLLECTION: users                           │
│  ┌────────────────────────────────────┐     │
│  │ DOCUMENT: {userId}                 │     │
│  ├────────────────────────────────────┤     │
│  │ email                String         │     │
│  │ selectedLanguages    Array          │     │
│  │ notificationsEnabled Boolean        │     │
│  │ notificationFrequency Int           │     │
│  │ createdAt            Timestamp      │     │
│  └────────────────────────────────────┘     │
│                                               │
│  COLLECTION: userProgress                    │
│  ┌────────────────────────────────────┐     │
│  │ DOCUMENT: {userId}                 │     │
│  ├────────────────────────────────────┤     │
│  │ progress             Array          │     │
│  │   - questionId       String         │     │
│  │   - answeredAt       Timestamp      │     │
│  │   - isCorrect        Boolean        │     │
│  │   - timeTaken        Int            │     │
│  └────────────────────────────────────┘     │
│                                               │
│  COLLECTION: questions (Master Data)         │
│  ┌────────────────────────────────────┐     │
│  │ DOCUMENT: {questionId}             │     │
│  ├────────────────────────────────────┤     │
│  │ [Same fields as Room questions]    │     │
│  └────────────────────────────────────┘     │
└─────────────────────────────────────────────┘
```

---

## 🔄 Dependency Injection Graph (Hilt)

```
┌────────────────────────┐
│   @HiltAndroidApp      │
│   CodeQuizApp          │
│   Application          │
└───────────┬────────────┘
            │
            │ provides
            │
┌───────────▼────────────┐
│   @InstallIn           │
│   SingletonComponent   │
└───────────┬────────────┘
            │
            ├─────► AppModule
            │       - Context
            │       - Application
            │
            ├─────► DatabaseModule
            │       - AppDatabase
            │       - QuestionDao
            │       - UserProgressDao
            │       - PreferencesManager
            │
            ├─────► RepositoryModule
            │       - QuestionRepository
            │       - UserRepository
            │       - ProgressRepository
            │
            ├─────► NetworkModule
            │       - FirebaseAuth
            │       - FirebaseFirestore
            │
            └─────► NotificationModule
                    - NotificationHelper
                    - NotificationScheduler

┌────────────────────────┐
│   @HiltViewModel       │
│   ViewModels           │
└───────────┬────────────┘
            │
            │ @Inject
            │ constructor
            │
            ├─────► LoginViewModel
            │       - LoginUseCase
            │       - UserRepository
            │
            ├─────► SignUpViewModel
            │       - SignUpUseCase
            │       - UpdateLanguagePreferencesUseCase
            │
            ├─────► HomeViewModel
            │       - GetUserStatsUseCase
            │       - GetStreakUseCase
            │
            ├─────► QuestionViewModel
            │       - GetRandomQuestionUseCase
            │       - SubmitAnswerUseCase
            │
            └─────► SettingsViewModel
                    - UpdateNotificationSettingsUseCase
                    - NotificationScheduler

┌────────────────────────┐
│   @HiltWorker          │
└───────────┬────────────┘
            │
            └─────► NotificationWorker
                    - GetRandomQuestionUseCase
                    - NotificationHelper
                    - PreferencesManager
```

---

## 📊 State Management Flow

```
USER INTERACTION
  │
  ▼
┌─────────────────────┐
│   View (Fragment/   │
│    Activity)        │
│  - Click button     │
│  - Enter text       │
└──────────┬──────────┘
           │
           │ Call ViewModel method
           │
           ▼
┌──────────────────────┐
│   ViewModel          │
│  - Business logic    │
│  - Call use case     │
└──────────┬───────────┘
           │
           │ Launch coroutine
           │ viewModelScope.launch
           │
           ▼
┌──────────────────────┐
│   Use Case           │
│  - Domain logic      │
│  - Call repository   │
└──────────┬───────────┘
           │
           │ suspend function
           │
           ▼
┌──────────────────────┐
│   Repository         │
│  - Data operations   │
│  - Combine sources   │
└──────────┬───────────┘
           │
           ├──────► Local (Room/DataStore)
           │
           └──────► Remote (Firebase)
           │
           │ Return result
           │
           ▼
┌──────────────────────┐
│   Use Case           │
│  - Process result    │
│  - Return to VM      │
└──────────┬───────────┘
           │
           ▼
┌──────────────────────┐
│   ViewModel          │
│  - Update LiveData/  │
│    StateFlow         │
│  _state.value = ...  │
└──────────┬───────────┘
           │
           │ Observer notified
           │
           ▼
┌──────────────────────┐
│   View               │
│  - Observe state     │
│  - Update UI         │
│  - Show data         │
└──────────────────────┘
```

---

## 🧪 Testing Pyramid

```
                    ▲
                   ╱ ╲
                  ╱   ╲
                 ╱     ╲
                ╱  UI   ╲
               ╱  Tests  ╲        ← Few, Slow, Expensive
              ╱───────────╲         (Espresso)
             ╱             ╲
            ╱  Integration  ╲     ← Some, Medium Speed
           ╱     Tests       ╲      (Room, Firebase)
          ╱─────────────────  ╲
         ╱                     ╲
        ╱      Unit Tests       ╲  ← Many, Fast, Cheap
       ╱    (ViewModels,         ╲   (JUnit, Mockito)
      ╱    UseCases, Utils)       ╲
     ╱───────────────────────────  ╲
    ▕═══════════════════════════════▏

UI Tests (10%):
  - Login flow
  - Question answering flow
  - Navigation

Integration Tests (20%):
  - Room database queries
  - Repository implementations
  - WorkManager notification scheduling

Unit Tests (70%):
  - ViewModel logic
  - Use case business rules
  - Data model transformations
  - Utility functions
```

---

## 🚀 Build & Deployment Flow

```
┌────────────────────┐
│  Developer writes  │
│  code in Android   │
│  Studio            │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Git commit &      │
│  push to GitHub    │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  CI/CD Pipeline    │
│  (GitHub Actions)  │
└─────────┬──────────┘
          │
          ├─────► Run unit tests
          ├─────► Run lint checks
          ├─────► Build debug APK
          └─────► Build release APK
          │
          ▼
┌────────────────────┐
│  Tests pass?       │
└─────────┬──────────┘
          │
     YES  │  NO ─────► Notify developer
          │            Stop pipeline
          ▼
┌────────────────────┐
│  Sign release APK  │
│  with keystore     │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Upload to Google  │
│  Play Console      │
│  (Internal Track)  │
└─────────┬──────────┘
          │
          ▼
┌────────────────────┐
│  Internal testing  │
│  (Team members)    │
└─────────┬──────────┘
          │
          │ Approved?
          ▼
┌────────────────────┐
│  Promote to Beta   │
│  or Production     │
└────────────────────┘
```

---

These diagrams provide a visual overview of the entire Android app architecture, data flows, and system interactions. Refer to these when implementing the app to maintain consistency with the planned architecture.
