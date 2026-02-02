# Android Coding Quiz App - Quick Start Guide

## 🎯 Technology Decision

**CHOSEN: Native Android with Kotlin**

### Why Native Android?
- ✅ Best notification support (WorkManager)
- ✅ No cross-platform overhead (Android-only app)
- ✅ Superior performance and battery efficiency
- ✅ First-class offline support (Room)
- ✅ Direct access to all Android APIs

---

## 📱 Core Technologies Stack

| Component | Technology |
|-----------|-----------|
| **Language** | Kotlin |
| **Architecture** | MVVM + Clean Architecture |
| **Database** | Room (local) + Firestore (cloud sync) |
| **Notifications** | WorkManager |
| **DI** | Hilt |
| **Auth** | Firebase Auth |
| **UI** | Material Design 3 + ViewBinding |
| **Code Highlighting** | Markwon + Prism4j |
| **Min SDK** | API 24 (Android 7.0) |

---

## 🏗️ Architecture Overview

```
Presentation Layer (Activities, Fragments, ViewModels)
         ↓
Domain Layer (Use Cases, Repository Interfaces, Models)
         ↓
Data Layer (Repository Impl, Room DB, Firebase)
```

---

## 📂 Project Structure

```
com.codequiz/
├── di/                    # Hilt modules
├── data/
│   ├── local/            # Room DB, DataStore
│   ├── remote/           # Firebase
│   └── repository/       # Repository implementations
├── domain/
│   ├── model/            # Domain models
│   ├── repository/       # Repository interfaces
│   └── usecase/          # Business logic
├── presentation/
│   ├── ui/               # Activities, Fragments
│   ├── adapter/          # RecyclerView adapters
│   └── common/           # Base classes
├── notification/         # WorkManager notification system
└── util/                 # Utilities
```

---

## 🚀 Implementation Phases (15-17 Days)

### Phase 0: Setup (Days 1-2) ⭐ Easy
- [ ] Create Android Studio project (Empty Activity, Kotlin)
- [ ] Add dependencies to build.gradle.kts
- [ ] Set up Firebase (Auth + Firestore)
- [ ] Configure Hilt
- [ ] Create package structure

### Phase 1: Data Layer (Days 3-5) ⭐⭐ Medium
- [ ] Define domain models (Question, User, UserProgress)
- [ ] Create Room entities (QuestionEntity, UserProgressEntity)
- [ ] Create DAOs (QuestionDao, UserProgressDao)
- [ ] Create AppDatabase
- [ ] Create PreferencesManager (DataStore)
- [ ] Create repositories

### Phase 2: Domain Layer (Day 6) ⭐ Easy
- [ ] Create repository interfaces
- [ ] Create use cases (auth, question, preference, progress)

### Phase 3: Notifications (Days 7-8) ⭐⭐ Medium
- [ ] Create NotificationHelper
- [ ] Create NotificationWorker
- [ ] Create NotificationScheduler
- [ ] Request notification permissions

### Phase 4: Auth UI (Days 9-10) ⭐⭐ Medium
- [ ] Create SplashActivity
- [ ] Create LoginActivity + ViewModel
- [ ] Create SignUpActivity + ViewModel
- [ ] Create LanguageSelectionFragment

### Phase 5: Main App UI (Days 11-13) ⭐⭐⭐ Hard
- [ ] Create MainActivity with BottomNavigation
- [ ] Create HomeFragment + ViewModel
- [ ] Create QuestionActivity + ViewModel
- [ ] Create ProgressFragment + ViewModel
- [ ] Create SettingsFragment + ViewModel
- [ ] Create adapters

### Phase 6: Utilities (Day 14) ⭐ Easy
- [ ] Implement CodeHighlighter
- [ ] Create extension functions
- [ ] Create Constants file

### Phase 7: Seed Database (Day 15) ⭐⭐ Medium
- [ ] Create initial_questions.json (50-100 questions)
- [ ] Create DatabaseSeeder
- [ ] Test question loading

### Phase 8: Testing & Polish (Days 16-17) ⭐⭐ Medium
- [ ] Unit tests for ViewModels
- [ ] Unit tests for Use Cases
- [ ] UI tests for key flows
- [ ] Bug fixes and refinements

---

## 📦 Key Dependencies (build.gradle.kts)

```kotlin
plugins {
    id("com.android.application")
    id("org.jetbrains.kotlin.android")
    id("kotlin-kapt")
    id("com.google.dagger.hilt.android")
    id("com.google.gms.google-services")
}

android {
    namespace = "com.codequiz"
    compileSdk = 34
    
    defaultConfig {
        applicationId = "com.codequiz"
        minSdk = 24
        targetSdk = 34
        versionCode = 1
        versionName = "1.0"
    }
    
    buildFeatures {
        viewBinding = true
    }
    
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
}

dependencies {
    // Core
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.11.0")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
    
    // Coroutines
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
    
    // Lifecycle
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0")
    implementation("androidx.lifecycle:lifecycle-livedata-ktx:2.7.0")
    
    // Navigation
    implementation("androidx.navigation:navigation-fragment-ktx:2.7.6")
    implementation("androidx.navigation:navigation-ui-ktx:2.7.6")
    
    // Room
    implementation("androidx.room:room-runtime:2.6.1")
    implementation("androidx.room:room-ktx:2.6.1")
    kapt("androidx.room:room-compiler:2.6.1")
    
    // DataStore
    implementation("androidx.datastore:datastore-preferences:1.0.0")
    
    // WorkManager
    implementation("androidx.work:work-runtime-ktx:2.9.0")
    
    // Firebase
    implementation(platform("com.google.firebase:firebase-bom:32.7.0"))
    implementation("com.google.firebase:firebase-auth-ktx")
    implementation("com.google.firebase:firebase-firestore-ktx")
    implementation("com.google.firebase:firebase-analytics-ktx")
    
    // Hilt
    implementation("com.google.dagger:hilt-android:2.50")
    kapt("com.google.dagger:hilt-compiler:2.50")
    implementation("androidx.hilt:hilt-work:1.1.0")
    kapt("androidx.hilt:hilt-compiler:1.1.0")
    
    // Code Highlighting
    implementation("io.noties.markwon:core:4.6.2")
    implementation("io.noties.markwon:syntax-highlight:4.6.2")
    
    // Testing
    testImplementation("junit:junit:4.13.2")
    androidTestImplementation("androidx.test.ext:junit:1.1.5")
    androidTestImplementation("androidx.test.espresso:espresso-core:3.5.1")
}
```

---

## 📊 Data Models

### Question Model
```kotlin
data class Question(
    val id: String,                          // "kotlin_001"
    val language: ProgrammingLanguage,       // KOTLIN, JAVA, PYTHON, etc.
    val difficulty: QuestionDifficulty,      // EASY, MEDIUM, HARD
    val codeSnippet: String,                 // Code with bug
    val question: String,                    // "What's wrong?"
    val options: List<String>,               // 4 options
    val correctAnswerIndex: Int,             // 0-3
    val explanation: String,                 // Why answer is correct
    val topic: String?                       // Optional: "arrays", "loops"
)

enum class ProgrammingLanguage {
    KOTLIN, JAVA, PYTHON, JAVASCRIPT, 
    CPP, CSHARP, GO, SWIFT, RUST, TYPESCRIPT
}

enum class QuestionDifficulty {
    EASY, MEDIUM, HARD
}
```

### User Model
```kotlin
data class User(
    val id: String,
    val email: String,
    val selectedLanguages: List<ProgrammingLanguage>,
    val notificationsEnabled: Boolean,
    val notificationFrequency: Int,          // 3 or 4 times/day
    val createdAt: Long
)
```

### UserProgress Model
```kotlin
data class UserProgress(
    val questionId: String,
    val answeredAt: Long,
    val isCorrect: Boolean,
    val timeTaken: Int                       // in seconds
)
```

---

## 🔔 Notification Strategy

### WorkManager Implementation
```kotlin
// Notification Times:
// 3x/day: 9 AM, 2 PM, 7 PM
// 4x/day: 9 AM, 12 PM, 3 PM, 7 PM

class NotificationScheduler {
    fun scheduleNotifications() {
        // Uses PeriodicWorkRequest
        // Interval: 24 hours / frequency
        // Constraints: No network required
        // Initial delay: Next scheduled time
    }
}
```

**Why WorkManager over AlarmManager?**
- ✅ Battery efficient (respects Doze mode)
- ✅ Survives app restarts
- ✅ Guaranteed execution
- ✅ No complex permission handling
- ✅ Handles edge cases automatically

---

## 🎨 UI Screens

### MVP Screens (Must-Have)
1. **SplashActivity** - App initialization
2. **LoginActivity** - Email/password login
3. **SignUpActivity** - Registration + language selection
4. **MainActivity** - Bottom navigation container
   - HomeFragment - Daily stats, "Start Quiz" button
   - ProgressFragment - Statistics, history
   - SettingsFragment - Notification toggle, frequency
5. **QuestionActivity** - Question display + answer submission

### Screen Flow
```
SplashActivity
    ↓
LoginActivity → SignUpActivity (with language selection)
    ↓
MainActivity (HomeFragment)
    ↓
QuestionActivity
    ↓
QuestionResultDialog
    ↓
MainActivity (updated stats)
```

---

## 📝 MVP Features Checklist

### Must-Have (Phase 1)
- [x] Email/password authentication
- [x] Language selection during signup
- [x] Random question display
- [x] Syntax-highlighted code
- [x] Multiple choice (4 options)
- [x] Immediate feedback
- [x] 3-4 daily notifications
- [x] Offline support
- [x] Progress tracking (streak, accuracy)
- [x] Settings (toggle, frequency)

### Nice-to-Have (Phase 2)
- [ ] Social features & leaderboards
- [ ] Achievement badges
- [ ] Dark mode
- [ ] Question bookmarking
- [ ] Topic-based filtering
- [ ] Weekly reports

---

## 🗄️ Database Schema

### Room Tables

**questions**
```sql
id (PK) | language | difficulty | codeSnippet | question | 
optionsJson | correctAnswerIndex | explanation | topic | 
createdAt | lastUpdated
```

**user_progress**
```sql
id (PK, autoincrement) | questionId | answeredAt | 
isCorrect | timeTaken
```

### Firebase Collections

**users**
```
{
  "userId": {
    "email": "user@example.com",
    "selectedLanguages": ["KOTLIN", "PYTHON"],
    "notificationsEnabled": true,
    "notificationFrequency": 3,
    "createdAt": 1234567890
  }
}
```

**userProgress** (optional cloud backup)
```
{
  "userId": {
    "progress": [
      {
        "questionId": "kotlin_001",
        "answeredAt": 1234567890,
        "isCorrect": true,
        "timeTaken": 45
      }
    ]
  }
}
```

---

## 🔑 Key Implementation Details

### 1. Question Selection Logic
```kotlin
// GetRandomQuestionUseCase
suspend fun invoke(): Question? {
    val userLanguages = getUserLanguages()
    return questionRepository.getRandomQuestion(
        languages = userLanguages,
        difficulty = getNextDifficulty()
    )
}
```

### 2. Streak Calculation
```kotlin
// GetStreakUseCase
suspend fun invoke(): Int {
    val activeDays = progressRepository.getActiveDays(last30Days)
    return calculateConsecutiveDays(activeDays)
}
```

### 3. Notification Scheduling
```kotlin
// Called on:
// - User signup
// - User enables notifications
// - User changes frequency
notificationScheduler.scheduleNotifications()
```

### 4. Code Highlighting
```kotlin
// Uses Markwon + Prism4j
val markwon = Markwon.builder(context)
    .usePlugin(SyntaxHighlightPlugin.create(Prism4j(), theme))
    .build()

val markdown = "```kotlin\nfun main() {}\n```"
markwon.setMarkdown(textView, markdown)
```

---

## 🧪 Testing Strategy

### Unit Tests
- ViewModels (all business logic)
- Use Cases (domain logic)
- Repositories (data operations)

### UI Tests
- Login flow
- Question answering flow
- Settings changes

### Integration Tests
- Room database operations
- Firebase authentication
- WorkManager scheduling

---

## 🚀 First Steps to Begin

1. **Create Android Studio Project**
   ```
   File → New → New Project
   Select: Empty Activity
   Language: Kotlin
   Minimum SDK: API 24
   ```

2. **Set up Firebase**
   - Go to https://console.firebase.google.com
   - Create new project
   - Add Android app
   - Download google-services.json
   - Enable Authentication (Email/Password)
   - Create Firestore database

3. **Add Dependencies**
   - Copy dependencies from above to build.gradle.kts
   - Add plugins at top of file
   - Sync project

4. **Create Application Class**
   ```kotlin
   @HiltAndroidApp
   class CodeQuizApp : Application()
   ```

5. **Follow Phase 0 Checklist**
   - Create all packages
   - Set up Hilt modules
   - Configure AndroidManifest.xml

---

## 📖 Additional Resources

- Full plan: `/tmp/android_app_plan.md` (1519 lines)
- Kotlin documentation: https://kotlinlang.org/docs/
- Android developer guide: https://developer.android.com/
- WorkManager guide: https://developer.android.com/topic/libraries/architecture/workmanager
- Room database guide: https://developer.android.com/training/data-storage/room

---

## 🎯 Success Criteria

### MVP is complete when:
- ✅ User can sign up and select languages
- ✅ User can log in
- ✅ Random questions appear from selected languages
- ✅ Code is syntax-highlighted
- ✅ User can answer and see if correct
- ✅ Notifications appear 3-4 times daily
- ✅ Progress is tracked and displayed
- ✅ User can disable notifications
- ✅ App works offline

### Definition of Done:
- All MVP features working
- No critical bugs
- App doesn't crash
- Notifications work reliably
- Database has 50+ questions
- Basic tests pass

---

## 💡 Pro Tips

1. **Start Small**: Build vertical slices (one feature end-to-end)
2. **Test Early**: Write tests as you build
3. **Use Fake Data**: Create mock questions initially
4. **Git Commits**: Commit after each phase
5. **Follow SOLID**: Keep code clean and modular
6. **Firebase Emulator**: Test without affecting production
7. **Debug Notifications**: Use WorkManager info in logcat
8. **Material Design**: Use Material components for consistency

---

## 📞 Next Steps

Would you like me to:
1. Generate the initial project structure with placeholder files?
2. Create sample question JSON for the database?
3. Start implementing Phase 0 step-by-step?
4. Show detailed code for a specific component?

**Ready to build? Start with Phase 0! 🚀**
