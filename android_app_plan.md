I'll create a comprehensive, executable plan for building this Android coding quiz app. Let me analyze the requirements and make specific technical decisions.

# COMPREHENSIVE ANDROID CODING QUIZ APP - EXECUTABLE PLAN

## 1. TECHNOLOGY STACK DECISION

### **RECOMMENDATION: Native Android with Kotlin**

**Justification:**

| Criteria | Native Android (Kotlin) | React Native | Flutter |
|----------|------------------------|--------------|---------|
| **Notification Support** | ✅ Excellent (WorkManager, native APIs) | ⚠️ Requires native modules | ⚠️ Requires platform channels |
| **Development Speed** | ⚠️ Moderate (single platform optimization) | ✅ Fast (if cross-platform needed) | ✅ Fast (if cross-platform needed) |
| **Performance** | ✅ Excellent | ⚠️ Good | ✅ Excellent |
| **Offline Support** | ✅ Excellent (Room, DataStore) | ✅ Good | ✅ Good |
| **Android-Only Focus** | ✅ Best (no overhead) | ❌ Wasted cross-platform features | ❌ Wasted cross-platform features |
| **Background Tasks** | ✅ Native WorkManager | ⚠️ Complex setup | ⚠️ Complex setup |
| **Community/Resources** | ✅ Excellent for Android | ✅ Good | ✅ Good |

**Winner: Native Android with Kotlin** - Since this is Android-only, native development provides the best notification support, performance, and eliminates cross-platform overhead.

---

## 2. ARCHITECTURE DESIGN

### **Architecture Pattern: MVVM + Clean Architecture**

```
┌─────────────────────────────────────────────────────────┐
│                    PRESENTATION LAYER                    │
│  ┌──────────┐  ┌──────────┐  ┌──────────┐              │
│  │ Activity │  │ Fragment │  │ViewModel │              │
│  └──────────┘  └──────────┘  └──────────┘              │
│       │              │              │                    │
└───────┼──────────────┼──────────────┼────────────────────┘
        │              │              │
┌───────┼──────────────┼──────────────┼────────────────────┐
│       │              │              ▼                     │
│       │              │        ┌──────────┐               │
│       │              │        │ Use Cases│               │
│       │              │        └──────────┘               │
│       │              │              │                     │
│       │         DOMAIN LAYER        │                     │
│       │              │              ▼                     │
│       │              │        ┌──────────┐               │
│       │              │        │Repository│               │
│       │              │        │Interface │               │
│       │              │        └──────────┘               │
└───────┼──────────────┼──────────────┼────────────────────┘
        │              │              │
┌───────┼──────────────┼──────────────┼────────────────────┐
│       │              │              ▼                     │
│       │              │     ┌─────────────────┐           │
│       │              │     │RepositoryImpl   │           │
│       │              │     └─────────────────┘           │
│       │         DATA LAYER          │                     │
│       │              │         ┌────┴─────┐              │
│       │              │         ▼          ▼              │
│       │              │   ┌─────────┐ ┌─────────┐        │
│       │              │   │  Room   │ │Firebase │        │
│       │              │   │Database │ │ Remote  │        │
│       │              │   └─────────┘ └─────────┘        │
└───────┴──────────────┴──────────────────────────────────┘
```

### **Data Flow:**

1. **User Interaction** → Activity/Fragment
2. **UI Events** → ViewModel
3. **Business Logic** → Use Cases
4. **Data Operations** → Repository
5. **Data Source** → Room DB / Firebase
6. **Response** → Flow back through layers to UI

### **Notification System Architecture:**

```
┌──────────────────────────────────────────┐
│         WorkManager Scheduler            │
│  (Schedules periodic notifications)      │
└──────────────────┬───────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────┐
│      NotificationWorker                  │
│  - Fetches next question                 │
│  - Creates notification                  │
│  - Schedules next notification           │
└──────────────────┬───────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────┐
│    NotificationManager                   │
│  - Shows notification with question      │
│  - Handles notification actions          │
└──────────────────────────────────────────┘
                   │
                   ▼
┌──────────────────────────────────────────┐
│         User Taps Notification           │
│    → Opens QuestionActivity              │
└──────────────────────────────────────────┘
```

### **Local vs Remote Data Storage:**

| Data Type | Storage | Reason |
|-----------|---------|--------|
| **Questions Database** | Local (Room) + Remote Sync | Offline access, sync updates periodically |
| **User Preferences** | Local (DataStore) | Fast access, privacy |
| **User Progress** | Local (Room) + Remote Backup | Offline tracking, cloud backup |
| **Authentication** | Firebase Auth | Industry standard, secure |
| **Analytics** | Firebase Analytics | User insights |
| **Remote Config** | Firebase Remote Config | A/B testing, feature flags |

---

## 3. PROJECT STRUCTURE

```
app/
├── src/
│   ├── main/
│   │   ├── java/com/codequiz/
│   │   │   ├── di/                          # Dependency Injection
│   │   │   │   ├── AppModule.kt
│   │   │   │   ├── DatabaseModule.kt
│   │   │   │   ├── RepositoryModule.kt
│   │   │   │   └── UseCaseModule.kt
│   │   │   │
│   │   │   ├── data/                        # Data Layer
│   │   │   │   ├── local/
│   │   │   │   │   ├── database/
│   │   │   │   │   │   ├── AppDatabase.kt
│   │   │   │   │   │   ├── dao/
│   │   │   │   │   │   │   ├── QuestionDao.kt
│   │   │   │   │   │   │   ├── UserProgressDao.kt
│   │   │   │   │   │   │   └── UserPreferenceDao.kt
│   │   │   │   │   │   └── entities/
│   │   │   │   │   │       ├── QuestionEntity.kt
│   │   │   │   │   │       ├── UserProgressEntity.kt
│   │   │   │   │   │       └── UserPreferenceEntity.kt
│   │   │   │   │   └── datastore/
│   │   │   │   │       └── PreferencesManager.kt
│   │   │   │   │
│   │   │   │   ├── remote/
│   │   │   │   │   ├── FirebaseAuthManager.kt
│   │   │   │   │   ├── FirebaseQuestionSync.kt
│   │   │   │   │   └── dto/
│   │   │   │   │       └── QuestionDto.kt
│   │   │   │   │
│   │   │   │   └── repository/
│   │   │   │       ├── QuestionRepositoryImpl.kt
│   │   │   │       ├── UserRepositoryImpl.kt
│   │   │   │       └── ProgressRepositoryImpl.kt
│   │   │   │
│   │   │   ├── domain/                      # Domain Layer
│   │   │   │   ├── model/
│   │   │   │   │   ├── Question.kt
│   │   │   │   │   ├── User.kt
│   │   │   │   │   ├── UserProgress.kt
│   │   │   │   │   ├── ProgrammingLanguage.kt
│   │   │   │   │   └── QuestionDifficulty.kt
│   │   │   │   │
│   │   │   │   ├── repository/
│   │   │   │   │   ├── QuestionRepository.kt
│   │   │   │   │   ├── UserRepository.kt
│   │   │   │   │   └── ProgressRepository.kt
│   │   │   │   │
│   │   │   │   └── usecase/
│   │   │   │       ├── auth/
│   │   │   │       │   ├── LoginUseCase.kt
│   │   │   │       │   ├── SignUpUseCase.kt
│   │   │   │       │   └── LogoutUseCase.kt
│   │   │   │       ├── question/
│   │   │   │       │   ├── GetRandomQuestionUseCase.kt
│   │   │   │       │   ├── GetQuestionByIdUseCase.kt
│   │   │   │       │   ├── SubmitAnswerUseCase.kt
│   │   │   │       │   └── SyncQuestionsUseCase.kt
│   │   │   │       ├── preference/
│   │   │   │       │   ├── GetUserPreferencesUseCase.kt
│   │   │   │       │   ├── UpdateLanguagePreferencesUseCase.kt
│   │   │   │       │   └── UpdateNotificationSettingsUseCase.kt
│   │   │   │       └── progress/
│   │   │   │           ├── GetUserStatsUseCase.kt
│   │   │   │           ├── RecordAnswerUseCase.kt
│   │   │   │           └── GetStreakUseCase.kt
│   │   │   │
│   │   │   ├── presentation/                # Presentation Layer
│   │   │   │   ├── ui/
│   │   │   │   │   ├── auth/
│   │   │   │   │   │   ├── LoginActivity.kt
│   │   │   │   │   │   ├── LoginViewModel.kt
│   │   │   │   │   │   ├── SignUpActivity.kt
│   │   │   │   │   │   ├── SignUpViewModel.kt
│   │   │   │   │   │   └── LanguageSelectionFragment.kt
│   │   │   │   │   │
│   │   │   │   │   ├── main/
│   │   │   │   │   │   ├── MainActivity.kt
│   │   │   │   │   │   ├── MainViewModel.kt
│   │   │   │   │   │   └── fragments/
│   │   │   │   │   │       ├── HomeFragment.kt
│   │   │   │   │   │       ├── HomeViewModel.kt
│   │   │   │   │   │       ├── ProgressFragment.kt
│   │   │   │   │   │       ├── ProgressViewModel.kt
│   │   │   │   │   │       ├── SettingsFragment.kt
│   │   │   │   │   │       └── SettingsViewModel.kt
│   │   │   │   │   │
│   │   │   │   │   ├── question/
│   │   │   │   │   │   ├── QuestionActivity.kt
│   │   │   │   │   │   ├── QuestionViewModel.kt
│   │   │   │   │   │   └── QuestionResultDialog.kt
│   │   │   │   │   │
│   │   │   │   │   └── splash/
│   │   │   │   │       ├── SplashActivity.kt
│   │   │   │   │       └── SplashViewModel.kt
│   │   │   │   │
│   │   │   │   ├── adapter/
│   │   │   │   │   ├── LanguageSelectionAdapter.kt
│   │   │   │   │   └── ProgressHistoryAdapter.kt
│   │   │   │   │
│   │   │   │   └── common/
│   │   │   │       ├── BaseActivity.kt
│   │   │   │       ├── BaseViewModel.kt
│   │   │   │       └── ViewState.kt
│   │   │   │
│   │   │   ├── notification/                # Notification System
│   │   │   │   ├── NotificationWorker.kt
│   │   │   │   ├── NotificationScheduler.kt
│   │   │   │   ├── NotificationHelper.kt
│   │   │   │   └── NotificationReceiver.kt
│   │   │   │
│   │   │   └── util/                        # Utilities
│   │   │       ├── Constants.kt
│   │   │       ├── Extensions.kt
│   │   │       ├── DateTimeUtil.kt
│   │   │       ├── CodeHighlighter.kt
│   │   │       └── NetworkUtil.kt
│   │   │
│   │   ├── res/
│   │   │   ├── layout/
│   │   │   │   ├── activity_splash.xml
│   │   │   │   ├── activity_login.xml
│   │   │   │   ├── activity_signup.xml
│   │   │   │   ├── activity_main.xml
│   │   │   │   ├── activity_question.xml
│   │   │   │   ├── fragment_home.xml
│   │   │   │   ├── fragment_progress.xml
│   │   │   │   ├── fragment_settings.xml
│   │   │   │   ├── fragment_language_selection.xml
│   │   │   │   ├── item_language_chip.xml
│   │   │   │   ├── item_progress_history.xml
│   │   │   │   └── dialog_question_result.xml
│   │   │   │
│   │   │   ├── values/
│   │   │   │   ├── colors.xml
│   │   │   │   ├── strings.xml
│   │   │   │   ├── themes.xml
│   │   │   │   └── styles.xml
│   │   │   │
│   │   │   ├── drawable/
│   │   │   ├── menu/
│   │   │   │   └── bottom_navigation_menu.xml
│   │   │   └── navigation/
│   │   │       └── nav_graph.xml
│   │   │
│   │   └── AndroidManifest.xml
│   │
│   └── test/
│       └── java/com/codequiz/
│           ├── domain/usecase/
│           ├── data/repository/
│           └── presentation/viewmodel/
│
└── build.gradle.kts
```

---

## 4. TECHNOLOGY COMPONENTS

### **Core Dependencies (build.gradle.kts):**

```kotlin
// Kotlin & Core
implementation("androidx.core:core-ktx:1.12.0")
implementation("androidx.appcompat:appcompat:1.6.1")
implementation("com.google.android.material:material:1.11.0")
implementation("androidx.constraintlayout:constraintlayout:2.1.4")

// Kotlin Coroutines
implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
implementation("org.jetbrains.kotlinx:kotlinx-coroutines-play-services:1.7.3")

// Lifecycle Components
implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0")
implementation("androidx.lifecycle:lifecycle-livedata-ktx:2.7.0")
implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.7.0")

// Navigation Component
implementation("androidx.navigation:navigation-fragment-ktx:2.7.6")
implementation("androidx.navigation:navigation-ui-ktx:2.7.6")

// Room Database
implementation("androidx.room:room-runtime:2.6.1")
implementation("androidx.room:room-ktx:2.6.1")
kapt("androidx.room:room-compiler:2.6.1")

// DataStore (Preferences)
implementation("androidx.datastore:datastore-preferences:1.0.0")

// WorkManager (Notifications)
implementation("androidx.work:work-runtime-ktx:2.9.0")

// Firebase
implementation(platform("com.google.firebase:firebase-bom:32.7.0"))
implementation("com.google.firebase:firebase-auth-ktx")
implementation("com.google.firebase:firebase-firestore-ktx")
implementation("com.google.firebase:firebase-analytics-ktx")
implementation("com.google.firebase:firebase-config-ktx")

// Dependency Injection - Hilt
implementation("com.google.dagger:hilt-android:2.50")
kapt("com.google.dagger:hilt-compiler:2.50")
implementation("androidx.hilt:hilt-work:1.1.0")
kapt("androidx.hilt:hilt-compiler:1.1.0")

// Code Highlighting
implementation("io.noties.markwon:core:4.6.2")
implementation("io.noties.markwon:syntax-highlight:4.6.2")

// UI Components
implementation("androidx.swiperefreshlayout:swiperefreshlayout:1.1.0")
implementation("androidx.cardview:cardview:1.0.0")

// Testing
testImplementation("junit:junit:4.13.2")
testImplementation("org.mockito:mockito-core:5.3.1")
testImplementation("org.jetbrains.kotlinx:kotlinx-coroutines-test:1.7.3")
testImplementation("androidx.arch.core:core-testing:2.2.0")
androidTestImplementation("androidx.test.ext:junit:1.1.5")
androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
```

### **Backend Components:**

| Component | Technology | Purpose |
|-----------|-----------|---------|
| **Authentication** | Firebase Auth | User sign-up, login, session management |
| **User Data Backup** | Firebase Firestore | Backup user progress, sync across devices |
| **Question Database** | Firebase Firestore + Local Room | Remote question storage, local caching |
| **Analytics** | Firebase Analytics | Track user engagement, question performance |
| **Remote Config** | Firebase Remote Config | Feature flags, notification frequency settings |
| **Cloud Functions** | Firebase Functions (optional) | Question validation, admin operations |

### **Notification Strategy:**

```
WorkManager (Recommended)
├── Advantages:
│   ├── Battery efficient
│   ├── Survives app restarts
│   ├── Respects Doze mode
│   ├── Guaranteed execution
│   └── Flexible scheduling
│
└── Implementation:
    ├── PeriodicWorkRequest (15 min minimum interval)
    ├── OneTimeWorkRequest (for immediate notifications)
    └── Constraints (requires device idle/charging for efficiency)
```

---

## 5. MVP FEATURE BREAKDOWN

### **Must-Have Features (MVP - Phase 1):**

**Priority 1 (Critical):**
1. ✅ User Registration/Login with email
2. ✅ Language selection during onboarding
3. ✅ Display random coding questions
4. ✅ Show code with syntax highlighting
5. ✅ Multiple choice answers (4 options)
6. ✅ Immediate feedback (correct/wrong)
7. ✅ Basic notification system (3-4 times daily)
8. ✅ Local question database (minimum 50 questions)
9. ✅ Simple progress tracking (questions answered)
10. ✅ Basic settings (notification on/off, frequency)

**Priority 2 (Important):**
11. ✅ Filter questions by selected languages only
12. ✅ Question difficulty levels (Easy, Medium, Hard)
13. ✅ Streak counter (consecutive days)
14. ✅ Daily goal (e.g., answer 5 questions/day)
15. ✅ Offline mode support
16. ✅ Question explanation after answering
17. ✅ Profile screen with statistics

### **Nice-to-Have Features (Phase 2):**

**Priority 3 (Enhancement):**
18. 🔲 Social features (share progress, leaderboard)
19. 🔲 Achievement badges
20. 🔲 Custom notification times
21. 🔲 Dark mode
22. 🔲 Question bookmarking
23. 🔲 Review incorrect answers
24. 🔲 Weekly reports
25. 🔲 More languages (10+ programming languages)
26. 🔲 Challenge mode (time-based)
27. 🔲 Topic-based filtering (arrays, loops, OOP, etc.)

**Priority 4 (Future):**
28. 🔲 User-submitted questions (moderated)
29. 🔲 Multi-language UI support
30. 🔲 Code playground (run code)
31. 🔲 Video explanations
32. 🔲 Study mode (no notifications)
33. 🔲 Widget for home screen

---

## 6. STEP-BY-STEP IMPLEMENTATION CHECKLIST

### **Phase 0: Project Setup (Days 1-2)**

**Complexity: Easy** ⭐

- [ ] **Step 1.1:** Create new Android Studio project
  - Template: Empty Activity
  - Language: Kotlin
  - Minimum SDK: API 24 (Android 7.0)
  - Build system: Gradle (Kotlin DSL)
  
- [ ] **Step 1.2:** Configure build.gradle.kts files
  - Add all dependencies listed in Section 4
  - Enable ViewBinding: `buildFeatures { viewBinding = true }`
  - Enable Kotlin kapt plugin
  
- [ ] **Step 1.3:** Set up Firebase project
  - Create Firebase project in console
  - Add Android app to Firebase
  - Download `google-services.json`
  - Add to `app/` directory
  - Configure Firebase Authentication (Email/Password)
  - Create Firestore database
  
- [ ] **Step 1.4:** Set up Hilt dependency injection
  - Create `@HiltAndroidApp` application class
  - Create DI modules (AppModule, DatabaseModule, etc.)
  
- [ ] **Step 1.5:** Create base package structure
  - Create all packages as shown in Section 3
  - Create empty placeholder files

**Dependencies:** None

---

### **Phase 1: Data Layer Implementation (Days 3-5)**

**Complexity: Medium** ⭐⭐

- [ ] **Step 2.1:** Define domain models
  ```kotlin
  // domain/model/Question.kt
  data class Question(
      val id: String,
      val language: ProgrammingLanguage,
      val difficulty: QuestionDifficulty,
      val codeSnippet: String,
      val question: String,
      val options: List<String>,
      val correctAnswerIndex: Int,
      val explanation: String,
      val topic: String?
  )
  
  // domain/model/ProgrammingLanguage.kt
  enum class ProgrammingLanguage {
      KOTLIN, JAVA, PYTHON, JAVASCRIPT, 
      CPP, CSHARP, GO, SWIFT, RUST, TYPESCRIPT
  }
  
  // domain/model/QuestionDifficulty.kt
  enum class QuestionDifficulty {
      EASY, MEDIUM, HARD
  }
  
  // domain/model/UserProgress.kt
  data class UserProgress(
      val questionId: String,
      val answeredAt: Long,
      val isCorrect: Boolean,
      val timeTaken: Int // seconds
  )
  
  // domain/model/User.kt
  data class User(
      val id: String,
      val email: String,
      val selectedLanguages: List<ProgrammingLanguage>,
      val notificationsEnabled: Boolean,
      val notificationFrequency: Int, // times per day
      val createdAt: Long
  )
  ```

- [ ] **Step 2.2:** Create Room database entities
  ```kotlin
  // data/local/database/entities/QuestionEntity.kt
  @Entity(tableName = "questions")
  data class QuestionEntity(
      @PrimaryKey val id: String,
      val language: String,
      val difficulty: String,
      val codeSnippet: String,
      val question: String,
      val optionsJson: String, // JSON array of options
      val correctAnswerIndex: Int,
      val explanation: String,
      val topic: String?,
      val createdAt: Long,
      val lastUpdated: Long
  )
  
  // data/local/database/entities/UserProgressEntity.kt
  @Entity(
      tableName = "user_progress",
      indices = [Index(value = ["questionId"])]
  )
  data class UserProgressEntity(
      @PrimaryKey(autoGenerate = true) val id: Int = 0,
      val questionId: String,
      val answeredAt: Long,
      val isCorrect: Boolean,
      val timeTaken: Int
  )
  ```

- [ ] **Step 2.3:** Create Room DAOs
  ```kotlin
  // data/local/database/dao/QuestionDao.kt
  @Dao
  interface QuestionDao {
      @Query("SELECT * FROM questions WHERE language IN (:languages)")
      fun getQuestionsByLanguages(languages: List<String>): Flow<List<QuestionEntity>>
      
      @Query("SELECT * FROM questions WHERE id = :id")
      suspend fun getQuestionById(id: String): QuestionEntity?
      
      @Query("SELECT * FROM questions WHERE language IN (:languages) AND difficulty = :difficulty ORDER BY RANDOM() LIMIT 1")
      suspend fun getRandomQuestion(languages: List<String>, difficulty: String): QuestionEntity?
      
      @Insert(onConflict = OnConflictStrategy.REPLACE)
      suspend fun insertQuestions(questions: List<QuestionEntity>)
      
      @Query("DELETE FROM questions")
      suspend fun deleteAll()
  }
  
  // data/local/database/dao/UserProgressDao.kt
  @Dao
  interface UserProgressDao {
      @Insert
      suspend fun insertProgress(progress: UserProgressEntity)
      
      @Query("SELECT * FROM user_progress ORDER BY answeredAt DESC LIMIT :limit")
      fun getRecentProgress(limit: Int): Flow<List<UserProgressEntity>>
      
      @Query("SELECT COUNT(*) FROM user_progress WHERE isCorrect = 1")
      fun getCorrectAnswersCount(): Flow<Int>
      
      @Query("SELECT COUNT(*) FROM user_progress")
      fun getTotalAnswersCount(): Flow<Int>
      
      @Query("SELECT DISTINCT DATE(answeredAt / 1000, 'unixepoch') as date FROM user_progress WHERE answeredAt >= :startTime")
      suspend fun getActiveDays(startTime: Long): List<String>
  }
  ```

- [ ] **Step 2.4:** Create AppDatabase
  ```kotlin
  // data/local/database/AppDatabase.kt
  @Database(
      entities = [QuestionEntity::class, UserProgressEntity::class],
      version = 1,
      exportSchema = false
  )
  abstract class AppDatabase : RoomDatabase() {
      abstract fun questionDao(): QuestionDao
      abstract fun userProgressDao(): UserProgressDao
  }
  ```

- [ ] **Step 2.5:** Create DataStore for preferences
  ```kotlin
  // data/local/datastore/PreferencesManager.kt
  class PreferencesManager @Inject constructor(
      @ApplicationContext private val context: Context
  ) {
      private val dataStore = context.createDataStore("user_preferences")
      
      val userPreferences: Flow<UserPreferences> = dataStore.data
          .catch { exception ->
              if (exception is IOException) {
                  emit(emptyPreferences())
              } else {
                  throw exception
              }
          }
          .map { preferences ->
              UserPreferences(
                  selectedLanguages = preferences[SELECTED_LANGUAGES]?.split(",") ?: emptyList(),
                  notificationsEnabled = preferences[NOTIFICATIONS_ENABLED] ?: true,
                  notificationFrequency = preferences[NOTIFICATION_FREQUENCY] ?: 3
              )
          }
      
      suspend fun updateSelectedLanguages(languages: List<String>) {
          dataStore.edit { preferences ->
              preferences[SELECTED_LANGUAGES] = languages.joinToString(",")
          }
      }
      
      companion object {
          private val SELECTED_LANGUAGES = stringPreferencesKey("selected_languages")
          private val NOTIFICATIONS_ENABLED = booleanPreferencesKey("notifications_enabled")
          private val NOTIFICATION_FREQUENCY = intPreferencesKey("notification_frequency")
      }
  }
  ```

- [ ] **Step 2.6:** Create repository interfaces (domain layer)
  ```kotlin
  // domain/repository/QuestionRepository.kt
  interface QuestionRepository {
      fun getQuestionsByLanguages(languages: List<ProgrammingLanguage>): Flow<List<Question>>
      suspend fun getRandomQuestion(languages: List<ProgrammingLanguage>, difficulty: QuestionDifficulty? = null): Question?
      suspend fun getQuestionById(id: String): Question?
      suspend fun syncQuestions(): Result<Unit>
  }
  
  // domain/repository/UserRepository.kt
  interface UserRepository {
      suspend fun login(email: String, password: String): Result<User>
      suspend fun signUp(email: String, password: String, selectedLanguages: List<ProgrammingLanguage>): Result<User>
      suspend fun logout()
      fun getCurrentUser(): Flow<User?>
      suspend fun updateLanguagePreferences(languages: List<ProgrammingLanguage>)
  }
  
  // domain/repository/ProgressRepository.kt
  interface ProgressRepository {
      suspend fun recordAnswer(questionId: String, isCorrect: Boolean, timeTaken: Int)
      fun getRecentProgress(limit: Int): Flow<List<UserProgress>>
      fun getStatistics(): Flow<ProgressStatistics>
      suspend fun getCurrentStreak(): Int
  }
  ```

- [ ] **Step 2.7:** Implement repositories
  ```kotlin
  // data/repository/QuestionRepositoryImpl.kt
  class QuestionRepositoryImpl @Inject constructor(
      private val questionDao: QuestionDao,
      private val firebaseFirestore: FirebaseFirestore
  ) : QuestionRepository {
      
      override fun getQuestionsByLanguages(languages: List<ProgrammingLanguage>): Flow<List<Question>> {
          val languageStrings = languages.map { it.name }
          return questionDao.getQuestionsByLanguages(languageStrings)
              .map { entities -> entities.map { it.toDomain() } }
      }
      
      override suspend fun getRandomQuestion(
          languages: List<ProgrammingLanguage>,
          difficulty: QuestionDifficulty?
      ): Question? {
          val languageStrings = languages.map { it.name }
          val difficultyString = difficulty?.name ?: QuestionDifficulty.EASY.name
          return questionDao.getRandomQuestion(languageStrings, difficultyString)?.toDomain()
      }
      
      override suspend fun syncQuestions(): Result<Unit> = withContext(Dispatchers.IO) {
          try {
              val snapshot = firebaseFirestore.collection("questions").get().await()
              val questions = snapshot.documents.mapNotNull { it.toQuestionEntity() }
              questionDao.insertQuestions(questions)
              Result.success(Unit)
          } catch (e: Exception) {
              Result.failure(e)
          }
      }
  }
  ```

**Dependencies:** Phase 0 complete

---

### **Phase 2: Domain Layer - Use Cases (Day 6)**

**Complexity: Easy** ⭐

- [ ] **Step 3.1:** Create authentication use cases
  ```kotlin
  // domain/usecase/auth/LoginUseCase.kt
  class LoginUseCase @Inject constructor(
      private val userRepository: UserRepository
  ) {
      suspend operator fun invoke(email: String, password: String): Result<User> {
          return userRepository.login(email, password)
      }
  }
  
  // domain/usecase/auth/SignUpUseCase.kt
  class SignUpUseCase @Inject constructor(
      private val userRepository: UserRepository
  ) {
      suspend operator fun invoke(
          email: String,
          password: String,
          selectedLanguages: List<ProgrammingLanguage>
      ): Result<User> {
          // Validation
          if (email.isBlank() || !android.util.Patterns.EMAIL_ADDRESS.matcher(email).matches()) {
              return Result.failure(Exception("Invalid email"))
          }
          if (password.length < 6) {
              return Result.failure(Exception("Password must be at least 6 characters"))
          }
          if (selectedLanguages.isEmpty()) {
              return Result.failure(Exception("Select at least one language"))
          }
          
          return userRepository.signUp(email, password, selectedLanguages)
      }
  }
  ```

- [ ] **Step 3.2:** Create question use cases
  ```kotlin
  // domain/usecase/question/GetRandomQuestionUseCase.kt
  class GetRandomQuestionUseCase @Inject constructor(
      private val questionRepository: QuestionRepository,
      private val preferencesManager: PreferencesManager
  ) {
      suspend operator fun invoke(difficulty: QuestionDifficulty? = null): Question? {
          val preferences = preferencesManager.userPreferences.first()
          val languages = preferences.selectedLanguages.map { ProgrammingLanguage.valueOf(it) }
          return questionRepository.getRandomQuestion(languages, difficulty)
      }
  }
  
  // domain/usecase/question/SubmitAnswerUseCase.kt
  class SubmitAnswerUseCase @Inject constructor(
      private val progressRepository: ProgressRepository
  ) {
      suspend operator fun invoke(
          questionId: String,
          selectedAnswerIndex: Int,
          correctAnswerIndex: Int,
          timeTaken: Int
      ): Boolean {
          val isCorrect = selectedAnswerIndex == correctAnswerIndex
          progressRepository.recordAnswer(questionId, isCorrect, timeTaken)
          return isCorrect
      }
  }
  ```

- [ ] **Step 3.3:** Create progress use cases
  ```kotlin
  // domain/usecase/progress/GetUserStatsUseCase.kt
  class GetUserStatsUseCase @Inject constructor(
      private val progressRepository: ProgressRepository
  ) {
      operator fun invoke(): Flow<ProgressStatistics> {
          return progressRepository.getStatistics()
      }
  }
  
  // domain/usecase/progress/GetStreakUseCase.kt
  class GetStreakUseCase @Inject constructor(
      private val progressRepository: ProgressRepository
  ) {
      suspend operator fun invoke(): Int {
          return progressRepository.getCurrentStreak()
      }
  }
  ```

**Dependencies:** Phase 1 complete

---

### **Phase 3: Notification System (Days 7-8)**

**Complexity: Medium** ⭐⭐

- [ ] **Step 4.1:** Create NotificationHelper
  ```kotlin
  // notification/NotificationHelper.kt
  class NotificationHelper @Inject constructor(
      @ApplicationContext private val context: Context
  ) {
      private val notificationManager = context.getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
      
      init {
          createNotificationChannel()
      }
      
      private fun createNotificationChannel() {
          if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
              val channel = NotificationChannel(
                  CHANNEL_ID,
                  "Coding Questions",
                  NotificationManager.IMPORTANCE_HIGH
              ).apply {
                  description = "Daily coding questions and reminders"
                  enableLights(true)
                  enableVibration(true)
              }
              notificationManager.createNotificationChannel(channel)
          }
      }
      
      fun showQuestionNotification(question: Question) {
          val intent = Intent(context, QuestionActivity::class.java).apply {
              putExtra("QUESTION_ID", question.id)
              flags = Intent.FLAG_ACTIVITY_NEW_TASK or Intent.FLAG_ACTIVITY_CLEAR_TOP
          }
          
          val pendingIntent = PendingIntent.getActivity(
              context, 0, intent,
              PendingIntent.FLAG_UPDATE_CURRENT or PendingIntent.FLAG_IMMUTABLE
          )
          
          val notification = NotificationCompat.Builder(context, CHANNEL_ID)
              .setSmallIcon(R.drawable.ic_code)
              .setContentTitle("Time for a coding challenge!")
              .setContentText("Can you spot the bug in this ${question.language.name} code?")
              .setStyle(NotificationCompat.BigTextStyle()
                  .bigText("Question: ${question.question}"))
              .setPriority(NotificationCompat.PRIORITY_HIGH)
              .setAutoCancel(true)
              .setContentIntent(pendingIntent)
              .build()
          
          notificationManager.notify(NOTIFICATION_ID, notification)
      }
      
      companion object {
          const val CHANNEL_ID = "coding_questions_channel"
          const val NOTIFICATION_ID = 1001
      }
  }
  ```

- [ ] **Step 4.2:** Create NotificationWorker
  ```kotlin
  // notification/NotificationWorker.kt
  @HiltWorker
  class NotificationWorker @AssistedInject constructor(
      @Assisted context: Context,
      @Assisted params: WorkerParameters,
      private val getRandomQuestionUseCase: GetRandomQuestionUseCase,
      private val notificationHelper: NotificationHelper,
      private val preferencesManager: PreferencesManager
  ) : CoroutineWorker(context, params) {
      
      override suspend fun doWork(): Result {
          return try {
              // Check if notifications are enabled
              val preferences = preferencesManager.userPreferences.first()
              if (!preferences.notificationsEnabled) {
                  return Result.success()
              }
              
              // Get random question
              val question = getRandomQuestionUseCase() ?: return Result.failure()
              
              // Show notification
              notificationHelper.showQuestionNotification(question)
              
              Result.success()
          } catch (e: Exception) {
              Log.e("NotificationWorker", "Error showing notification", e)
              Result.retry()
          }
      }
  }
  ```

- [ ] **Step 4.3:** Create NotificationScheduler
  ```kotlin
  // notification/NotificationScheduler.kt
  class NotificationScheduler @Inject constructor(
      @ApplicationContext private val context: Context,
      private val preferencesManager: PreferencesManager
  ) {
      private val workManager = WorkManager.getInstance(context)
      
      suspend fun scheduleNotifications() {
          val preferences = preferencesManager.userPreferences.first()
          
          if (!preferences.notificationsEnabled) {
              cancelNotifications()
              return
          }
          
          // Schedule notifications based on frequency
          val frequency = preferences.notificationFrequency
          schedulePeriodicNotifications(frequency)
      }
      
      private fun schedulePeriodicNotifications(timesPerDay: Int) {
          // Cancel existing work
          workManager.cancelAllWorkByTag(NOTIFICATION_WORK_TAG)
          
          // Calculate interval (in hours)
          val intervalHours = 24L / timesPerDay
          
          val constraints = Constraints.Builder()
              .setRequiredNetworkType(NetworkType.NOT_REQUIRED)
              .build()
          
          val notificationWork = PeriodicWorkRequestBuilder<NotificationWorker>(
              intervalHours, TimeUnit.HOURS,
              15, TimeUnit.MINUTES // Flex interval
          )
              .setConstraints(constraints)
              .addTag(NOTIFICATION_WORK_TAG)
              .setInitialDelay(calculateInitialDelay(timesPerDay), TimeUnit.MILLISECONDS)
              .build()
          
          workManager.enqueueUniquePeriodicWork(
              NOTIFICATION_WORK_NAME,
              ExistingPeriodicWorkPolicy.REPLACE,
              notificationWork
          )
      }
      
      private fun calculateInitialDelay(timesPerDay: Int): Long {
          // Schedule first notification for 9 AM, 2 PM, or 7 PM
          val calendar = Calendar.getInstance()
          val currentHour = calendar.get(Calendar.HOUR_OF_DAY)
          
          val notificationTimes = when (timesPerDay) {
              3 -> listOf(9, 14, 19) // 9 AM, 2 PM, 7 PM
              4 -> listOf(9, 12, 15, 19) // 9 AM, 12 PM, 3 PM, 7 PM
              else -> listOf(9, 14, 19)
          }
          
          val nextTime = notificationTimes.firstOrNull { it > currentHour } ?: notificationTimes.first()
          calendar.set(Calendar.HOUR_OF_DAY, nextTime)
          calendar.set(Calendar.MINUTE, 0)
          calendar.set(Calendar.SECOND, 0)
          
          if (nextTime <= currentHour) {
              calendar.add(Calendar.DAY_OF_MONTH, 1)
          }
          
          return calendar.timeInMillis - System.currentTimeMillis()
      }
      
      fun cancelNotifications() {
          workManager.cancelAllWorkByTag(NOTIFICATION_WORK_TAG)
      }
      
      companion object {
          private const val NOTIFICATION_WORK_TAG = "coding_question_notification"
          private const val NOTIFICATION_WORK_NAME = "periodic_question_notification"
      }
  }
  ```

- [ ] **Step 4.4:** Request notification permissions (Android 13+)
  ```kotlin
  // Add to relevant Activity
  private fun requestNotificationPermission() {
      if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.TIRAMISU) {
          if (ContextCompat.checkSelfPermission(this, Manifest.permission.POST_NOTIFICATIONS)
              != PackageManager.PERMISSION_GRANTED) {
              ActivityCompat.requestPermissions(
                  this,
                  arrayOf(Manifest.permission.POST_NOTIFICATIONS),
                  NOTIFICATION_PERMISSION_CODE
              )
          }
      }
  }
  ```

**Dependencies:** Phase 2 complete

---

### **Phase 4: UI - Authentication (Days 9-10)**

**Complexity: Medium** ⭐⭐

- [ ] **Step 5.1:** Create SplashActivity
  ```kotlin
  // presentation/ui/splash/SplashActivity.kt
  @AndroidEntryPoint
  class SplashActivity : AppCompatActivity() {
      private val viewModel: SplashViewModel by viewModels()
      
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          
          lifecycleScope.launch {
              delay(1500) // Splash duration
              
              if (viewModel.isUserLoggedIn()) {
                  startActivity(Intent(this@SplashActivity, MainActivity::class.java))
              } else {
                  startActivity(Intent(this@SplashActivity, LoginActivity::class.java))
              }
              finish()
          }
      }
  }
  ```

- [ ] **Step 5.2:** Create LoginActivity with ViewModel
  ```kotlin
  // presentation/ui/auth/LoginActivity.kt
  @AndroidEntryPoint
  class LoginActivity : AppCompatActivity() {
      private lateinit var binding: ActivityLoginBinding
      private val viewModel: LoginViewModel by viewModels()
      
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          binding = ActivityLoginBinding.inflate(layoutInflater)
          setContentView(binding.root)
          
          setupObservers()
          setupClickListeners()
      }
      
      private fun setupObservers() {
          viewModel.loginState.observe(this) { state ->
              when (state) {
                  is ViewState.Loading -> showLoading()
                  is ViewState.Success -> {
                      hideLoading()
                      navigateToMain()
                  }
                  is ViewState.Error -> {
                      hideLoading()
                      showError(state.message)
                  }
                  is ViewState.Idle -> hideLoading()
              }
          }
      }
      
      private fun setupClickListeners() {
          binding.btnLogin.setOnClickListener {
              val email = binding.etEmail.text.toString()
              val password = binding.etPassword.text.toString()
              viewModel.login(email, password)
          }
          
          binding.tvSignUp.setOnClickListener {
              startActivity(Intent(this, SignUpActivity::class.java))
          }
      }
  }
  
  // presentation/ui/auth/LoginViewModel.kt
  @HiltViewModel
  class LoginViewModel @Inject constructor(
      private val loginUseCase: LoginUseCase
  ) : ViewModel() {
      
      private val _loginState = MutableLiveData<ViewState<User>>()
      val loginState: LiveData<ViewState<User>> = _loginState
      
      fun login(email: String, password: String) {
          viewModelScope.launch {
              _loginState.value = ViewState.Loading
              
              val result = loginUseCase(email, password)
              
              _loginState.value = if (result.isSuccess) {
                  ViewState.Success(result.getOrNull()!!)
              } else {
                  ViewState.Error(result.exceptionOrNull()?.message ?: "Login failed")
              }
          }
      }
  }
  ```

- [ ] **Step 5.3:** Create SignUpActivity with language selection
  ```kotlin
  // presentation/ui/auth/SignUpActivity.kt
  @AndroidEntryPoint
  class SignUpActivity : AppCompatActivity() {
      private lateinit var binding: ActivitySignupBinding
      private val viewModel: SignUpViewModel by viewModels()
      
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          binding = ActivitySignupBinding.inflate(layoutInflater)
          setContentView(binding.root)
          
          setupLanguageSelection()
          setupObservers()
          setupClickListeners()
      }
      
      private fun setupLanguageSelection() {
          // Show fragment for language selection
          supportFragmentManager.beginTransaction()
              .replace(R.id.fragmentContainer, LanguageSelectionFragment())
              .commit()
      }
  }
  
  // presentation/ui/auth/LanguageSelectionFragment.kt
  class LanguageSelectionFragment : Fragment() {
      private val selectedLanguages = mutableSetOf<ProgrammingLanguage>()
      
      override fun onCreateView(/* ... */): View {
          // Create chips for each language
          ProgrammingLanguage.values().forEach { language ->
              val chip = createLanguageChip(language)
              binding.chipGroup.addView(chip)
          }
      }
      
      private fun createLanguageChip(language: ProgrammingLanguage): Chip {
          return Chip(requireContext()).apply {
              text = language.name
              isCheckable = true
              setOnCheckedChangeListener { _, isChecked ->
                  if (isChecked) {
                      selectedLanguages.add(language)
                  } else {
                      selectedLanguages.remove(language)
                  }
              }
          }
      }
      
      fun getSelectedLanguages(): List<ProgrammingLanguage> {
          return selectedLanguages.toList()
      }
  }
  ```

- [ ] **Step 5.4:** Create layout files
  - `activity_splash.xml` - Simple logo/branding
  - `activity_login.xml` - Email, password fields, login button
  - `activity_signup.xml` - Email, password, confirm password, fragment container
  - `fragment_language_selection.xml` - ChipGroup for language selection

**Dependencies:** Phase 3 complete

---

### **Phase 5: UI - Main App (Days 11-13)**

**Complexity: Hard** ⭐⭐⭐

- [ ] **Step 6.1:** Create MainActivity with bottom navigation
  ```kotlin
  // presentation/ui/main/MainActivity.kt
  @AndroidEntryPoint
  class MainActivity : AppCompatActivity() {
      private lateinit var binding: ActivityMainBinding
      private val viewModel: MainViewModel by viewModels()
      
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          binding = ActivityMainBinding.inflate(layoutInflater)
          setContentView(binding.root)
          
          setupBottomNavigation()
          requestNotificationPermission()
          
          // Schedule notifications on first launch
          viewModel.scheduleNotifications()
      }
      
      private fun setupBottomNavigation() {
          val navHostFragment = supportFragmentManager
              .findFragmentById(R.id.nav_host_fragment) as NavHostFragment
          val navController = navHostFragment.navController
          
          binding.bottomNavigation.setupWithNavController(navController)
      }
  }
  ```

- [ ] **Step 6.2:** Create HomeFragment
  ```kotlin
  // presentation/ui/main/fragments/HomeFragment.kt
  @AndroidEntryPoint
  class HomeFragment : Fragment() {
      private lateinit var binding: FragmentHomeBinding
      private val viewModel: HomeViewModel by viewModels()
      
      override fun onCreateView(/* ... */): View {
          binding = FragmentHomeBinding.inflate(inflater, container, false)
          return binding.root
      }
      
      override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
          super.onViewCreated(view, savedInstanceState)
          
          setupObservers()
          setupClickListeners()
          
          viewModel.loadTodayStats()
      }
      
      private fun setupObservers() {
          viewModel.dailyStats.observe(viewLifecycleOwner) { stats ->
              binding.tvQuestionsToday.text = "${stats.answeredToday}/${stats.dailyGoal}"
              binding.tvStreak.text = "${stats.currentStreak} days"
              binding.progressBar.progress = (stats.answeredToday * 100 / stats.dailyGoal)
          }
      }
      
      private fun setupClickListeners() {
          binding.btnStartQuiz.setOnClickListener {
              startActivity(Intent(requireContext(), QuestionActivity::class.java))
          }
      }
  }
  
  // presentation/ui/main/fragments/HomeViewModel.kt
  @HiltViewModel
  class HomeViewModel @Inject constructor(
      private val getUserStatsUseCase: GetUserStatsUseCase,
      private val getStreakUseCase: GetStreakUseCase
  ) : ViewModel() {
      
      private val _dailyStats = MutableLiveData<DailyStats>()
      val dailyStats: LiveData<DailyStats> = _dailyStats
      
      fun loadTodayStats() {
          viewModelScope.launch {
              getUserStatsUseCase().collect { stats ->
                  val streak = getStreakUseCase()
                  _dailyStats.value = DailyStats(
                      answeredToday = stats.answeredToday,
                      dailyGoal = 5, // Default goal
                      currentStreak = streak
                  )
              }
          }
      }
  }
  ```

- [ ] **Step 6.3:** Create QuestionActivity
  ```kotlin
  // presentation/ui/question/QuestionActivity.kt
  @AndroidEntryPoint
  class QuestionActivity : AppCompatActivity() {
      private lateinit var binding: ActivityQuestionBinding
      private val viewModel: QuestionViewModel by viewModels()
      private var startTime: Long = 0
      
      override fun onCreate(savedInstanceState: Bundle?) {
          super.onCreate(savedInstanceState)
          binding = ActivityQuestionBinding.inflate(layoutInflater)
          setContentView(binding.root)
          
          startTime = System.currentTimeMillis()
          
          setupObservers()
          loadQuestion()
      }
      
      private fun setupObservers() {
          viewModel.question.observe(this) { question ->
              displayQuestion(question)
          }
          
          viewModel.answerResult.observe(this) { result ->
              showResult(result)
          }
      }
      
      private fun displayQuestion(question: Question) {
          binding.tvLanguage.text = question.language.name
          binding.tvDifficulty.text = question.difficulty.name
          binding.tvQuestionText.text = question.question
          
          // Syntax highlighted code
          binding.tvCodeSnippet.text = highlightCode(question.codeSnippet, question.language)
          
          // Setup options
          binding.radioGroup.removeAllViews()
          question.options.forEachIndexed { index, option ->
              val radioButton = RadioButton(this).apply {
                  text = option
                  id = index
              }
              binding.radioGroup.addView(radioButton)
          }
          
          binding.btnSubmit.setOnClickListener {
              val selectedId = binding.radioGroup.checkedRadioButtonId
              if (selectedId != -1) {
                  val timeTaken = ((System.currentTimeMillis() - startTime) / 1000).toInt()
                  viewModel.submitAnswer(selectedId, timeTaken)
              }
          }
      }
      
      private fun showResult(result: AnswerResult) {
          val dialog = QuestionResultDialog.newInstance(result)
          dialog.show(supportFragmentManager, "result")
      }
  }
  
  // presentation/ui/question/QuestionViewModel.kt
  @HiltViewModel
  class QuestionViewModel @Inject constructor(
      private val getRandomQuestionUseCase: GetRandomQuestionUseCase,
      private val submitAnswerUseCase: SubmitAnswerUseCase
  ) : ViewModel() {
      
      private val _question = MutableLiveData<Question>()
      val question: LiveData<Question> = _question
      
      private val _answerResult = MutableLiveData<AnswerResult>()
      val answerResult: LiveData<AnswerResult> = _answerResult
      
      fun loadQuestion() {
          viewModelScope.launch {
              val q = getRandomQuestionUseCase()
              _question.value = q
          }
      }
      
      fun submitAnswer(selectedIndex: Int, timeTaken: Int) {
          viewModelScope.launch {
              val q = _question.value ?: return@launch
              val isCorrect = submitAnswerUseCase(
                  q.id, selectedIndex, q.correctAnswerIndex, timeTaken
              )
              
              _answerResult.value = AnswerResult(
                  isCorrect = isCorrect,
                  correctAnswer = q.options[q.correctAnswerIndex],
                  explanation = q.explanation
              )
          }
      }
  }
  ```

- [ ] **Step 6.4:** Create ProgressFragment
  ```kotlin
  // presentation/ui/main/fragments/ProgressFragment.kt
  @AndroidEntryPoint
  class ProgressFragment : Fragment() {
      private lateinit var binding: FragmentProgressBinding
      private val viewModel: ProgressViewModel by viewModels()
      
      override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
          super.onViewCreated(view, savedInstanceState)
          
          setupRecyclerView()
          setupObservers()
          
          viewModel.loadProgress()
      }
      
      private fun setupObservers() {
          viewModel.statistics.observe(viewLifecycleOwner) { stats ->
              binding.tvTotalAnswered.text = stats.totalAnswered.toString()
              binding.tvCorrectAnswers.text = stats.correctAnswers.toString()
              binding.tvAccuracy.text = "${stats.accuracy}%"
          }
          
          viewModel.recentProgress.observe(viewLifecycleOwner) { history ->
              progressAdapter.submitList(history)
          }
      }
  }
  ```

- [ ] **Step 6.5:** Create SettingsFragment
  ```kotlin
  // presentation/ui/main/fragments/SettingsFragment.kt
  class SettingsFragment : Fragment() {
      private lateinit var binding: FragmentSettingsBinding
      private val viewModel: SettingsViewModel by viewModels()
      
      override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
          super.onViewCreated(view, savedInstanceState)
          
          setupSwitches()
          setupLanguageSelection()
      }
      
      private fun setupSwitches() {
          binding.switchNotifications.setOnCheckedChangeListener { _, isChecked ->
              viewModel.updateNotificationSettings(isChecked)
          }
          
          binding.btnLogout.setOnClickListener {
              viewModel.logout()
              // Navigate to login
              startActivity(Intent(requireContext(), LoginActivity::class.java))
              requireActivity().finish()
          }
      }
  }
  ```

- [ ] **Step 6.6:** Create layout files
  - `activity_main.xml` - NavHostFragment + BottomNavigationView
  - `fragment_home.xml` - Stats cards, start quiz button
  - `fragment_progress.xml` - Statistics, RecyclerView for history
  - `fragment_settings.xml` - Switches, buttons, language chips
  - `activity_question.xml` - Question display, code snippet, radio buttons
  - `dialog_question_result.xml` - Result dialog with explanation

**Dependencies:** Phase 4 complete

---

### **Phase 6: Code Highlighting & Utilities (Day 14)**

**Complexity: Easy** ⭐

- [ ] **Step 7.1:** Implement CodeHighlighter
  ```kotlin
  // util/CodeHighlighter.kt
  class CodeHighlighter {
      companion object {
          fun highlight(code: String, language: ProgrammingLanguage): Spanned {
              val markwon = Markwon.builder(context)
                  .usePlugin(SyntaxHighlightPlugin.create(Prism4j(GrammarLocator()), Prism4jThemeDarkula()))
                  .build()
              
              val markdown = "```${language.name.lowercase()}\n$code\n```"
              return markwon.toMarkdown(markdown)
          }
      }
  }
  ```

- [ ] **Step 7.2:** Create extension functions
  ```kotlin
  // util/Extensions.kt
  fun Context.showToast(message: String, duration: Int = Toast.LENGTH_SHORT) {
      Toast.makeText(this, message, duration).show()
  }
  
  fun View.visible() {
      visibility = View.VISIBLE
  }
  
  fun View.gone() {
      visibility = View.GONE
  }
  
  fun Long.toDateString(): String {
      val sdf = SimpleDateFormat("MMM dd, yyyy", Locale.getDefault())
      return sdf.format(Date(this))
  }
  ```

- [ ] **Step 7.3:** Create Constants file
  ```kotlin
  // util/Constants.kt
  object Constants {
      const val DATABASE_NAME = "code_quiz_db"
      const val PREFS_NAME = "user_preferences"
      
      const val DEFAULT_NOTIFICATION_FREQUENCY = 3
      const val MIN_PASSWORD_LENGTH = 6
      const val DAILY_GOAL_DEFAULT = 5
      
      const val NOTIFICATION_WORK_TAG = "question_notification"
  }
  ```

**Dependencies:** Phase 5 complete

---

### **Phase 7: Seed Question Database (Day 15)**

**Complexity: Medium** ⭐⭐

- [ ] **Step 8.1:** Create initial question JSON
  ```json
  // assets/initial_questions.json
  [
    {
      "id": "kotlin_001",
      "language": "KOTLIN",
      "difficulty": "EASY",
      "codeSnippet": "fun main() {\n    var x = 10\n    val y = 20\n    x = y\n    y = x\n    println(\"x=$x, y=$y\")\n}",
      "question": "What will this code print?",
      "options": [
        "x=20, y=20",
        "x=20, y=10",
        "Compilation error",
        "x=10, y=20"
      ],
      "correctAnswerIndex": 2,
      "explanation": "The code won't compile because 'y' is declared as 'val' (immutable), so the line 'y = x' will cause a compilation error. In Kotlin, 'val' creates a read-only variable that cannot be reassigned after initialization."
    }
  ]
}