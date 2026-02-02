# Android Coding Quiz App - Detailed Implementation Timeline

## 📅 15-Day MVP Development Plan

---

## **DAY 1: Project Foundation**

### Morning (4 hours)
- [ ] Create new Android Studio project
  - Template: Empty Activity
  - Package name: `com.codequiz`
  - Language: Kotlin
  - Minimum SDK: API 24
  - Build configuration: Kotlin DSL
  
- [ ] Set up version control
  - Initialize Git repository
  - Create `.gitignore` for Android
  - First commit: "Initial project setup"
  - Create GitHub repository (optional)

### Afternoon (4 hours)
- [ ] Configure `build.gradle.kts` (project level)
  - Add Hilt plugin
  - Add Google Services plugin
  - Add Kotlin kapt plugin

- [ ] Configure `build.gradle.kts` (app level)
  - Add ALL dependencies from the plan
  - Enable ViewBinding
  - Set Java compatibility to 17
  - Sync project

- [ ] Set up Firebase
  - Create Firebase project
  - Add Android app to Firebase
  - Download `google-services.json`
  - Place in `app/` directory
  - Enable Authentication (Email/Password)
  - Create Firestore database (test mode initially)

**End of Day 1 Deliverable:** Buildable project with all dependencies configured

---

## **DAY 2: Architecture Setup**

### Morning (4 hours)
- [ ] Create complete package structure
  ```
  com.codequiz/
  ├── di/
  ├── data/local/database/dao/
  ├── data/local/database/entities/
  ├── data/local/datastore/
  ├── data/remote/
  ├── data/repository/
  ├── domain/model/
  ├── domain/repository/
  ├── domain/usecase/auth/
  ├── domain/usecase/question/
  ├── domain/usecase/preference/
  ├── domain/usecase/progress/
  ├── presentation/ui/splash/
  ├── presentation/ui/auth/
  ├── presentation/ui/main/
  ├── presentation/ui/question/
  ├── presentation/adapter/
  ├── presentation/common/
  ├── notification/
  └── util/
  ```

- [ ] Create Application class
  ```kotlin
  @HiltAndroidApp
  class CodeQuizApp : Application()
  ```

- [ ] Update `AndroidManifest.xml`
  - Add Application class
  - Add necessary permissions
  - Add notification permission (Android 13+)

### Afternoon (4 hours)
- [ ] Create all Hilt modules (empty implementations)
  - `di/AppModule.kt`
  - `di/DatabaseModule.kt`
  - `di/RepositoryModule.kt`
  - `di/NetworkModule.kt`

- [ ] Create base classes
  - `presentation/common/BaseActivity.kt`
  - `presentation/common/BaseViewModel.kt`
  - `presentation/common/ViewState.kt`

- [ ] Create Constants file
  - `util/Constants.kt`
  - Define all app constants

**End of Day 2 Deliverable:** Complete project structure with Hilt configured

---

## **DAY 3: Domain Models & Entities**

### Morning (4 hours)
- [ ] Create all domain models
  - `domain/model/Question.kt`
  - `domain/model/User.kt`
  - `domain/model/UserProgress.kt`
  - `domain/model/ProgrammingLanguage.kt` (enum)
  - `domain/model/QuestionDifficulty.kt` (enum)

- [ ] Create Room entities
  - `data/local/database/entities/QuestionEntity.kt`
  - `data/local/database/entities/UserProgressEntity.kt`

- [ ] Create mapper extensions
  - `QuestionEntity.toQuestion()`
  - `Question.toEntity()`
  - `UserProgressEntity.toUserProgress()`

### Afternoon (4 hours)
- [ ] Create DAOs
  - `data/local/database/dao/QuestionDao.kt`
    - `getQuestionsByLanguages()`
    - `getQuestionById()`
    - `getRandomQuestion()`
    - `insertQuestions()`
    
  - `data/local/database/dao/UserProgressDao.kt`
    - `insertProgress()`
    - `getRecentProgress()`
    - `getCorrectAnswersCount()`
    - `getTotalAnswersCount()`
    - `getActiveDays()`

- [ ] Create AppDatabase
  - `data/local/database/AppDatabase.kt`
  - Define version = 1
  - Add entities
  - Add abstract DAO methods

- [ ] Test database builds successfully

**End of Day 3 Deliverable:** All data models and Room database defined

---

## **DAY 4: Repositories & DataStore**

### Morning (4 hours)
- [ ] Create DataStore for preferences
  - `data/local/datastore/PreferencesManager.kt`
  - Define preference keys
  - Implement save/retrieve methods
  - Use Kotlin Flow for reactive updates

- [ ] Create repository interfaces
  - `domain/repository/QuestionRepository.kt`
  - `domain/repository/UserRepository.kt`
  - `domain/repository/ProgressRepository.kt`

### Afternoon (4 hours)
- [ ] Implement repositories
  - `data/repository/QuestionRepositoryImpl.kt`
    - Combine Room + Firestore
    - Cache-first strategy
    
  - `data/repository/UserRepositoryImpl.kt`
    - Firebase Auth integration
    - DataStore for preferences
    
  - `data/repository/ProgressRepositoryImpl.kt`
    - Room for local tracking
    - Firestore for backup (optional)

- [ ] Update Hilt modules to provide repositories
  - `di/DatabaseModule.kt` - provide DAOs
  - `di/RepositoryModule.kt` - provide repository implementations

**End of Day 4 Deliverable:** Complete data layer with repositories

---

## **DAY 5: Use Cases (Business Logic)**

### Morning (4 hours)
- [ ] Create authentication use cases
  - `domain/usecase/auth/LoginUseCase.kt`
  - `domain/usecase/auth/SignUpUseCase.kt`
  - `domain/usecase/auth/LogoutUseCase.kt`
  - `domain/usecase/auth/GetCurrentUserUseCase.kt`

- [ ] Create question use cases
  - `domain/usecase/question/GetRandomQuestionUseCase.kt`
  - `domain/usecase/question/GetQuestionByIdUseCase.kt`
  - `domain/usecase/question/SubmitAnswerUseCase.kt`

### Afternoon (4 hours)
- [ ] Create preference use cases
  - `domain/usecase/preference/GetUserPreferencesUseCase.kt`
  - `domain/usecase/preference/UpdateLanguagePreferencesUseCase.kt`
  - `domain/usecase/preference/UpdateNotificationSettingsUseCase.kt`

- [ ] Create progress use cases
  - `domain/usecase/progress/GetUserStatsUseCase.kt`
  - `domain/usecase/progress/RecordAnswerUseCase.kt`
  - `domain/usecase/progress/GetStreakUseCase.kt`

- [ ] Update Hilt to provide use cases

**End of Day 5 Deliverable:** Complete domain layer with all business logic

---

## **DAY 6: Notification System Foundation**

### Morning (4 hours)
- [ ] Create NotificationHelper
  - `notification/NotificationHelper.kt`
  - Create notification channel (Android O+)
  - `showQuestionNotification()` method
  - Handle PendingIntent to QuestionActivity

- [ ] Create NotificationWorker
  - `notification/NotificationWorker.kt`
  - Annotate with `@HiltWorker`
  - Inject dependencies
  - Implement `doWork()` method

### Afternoon (4 hours)
- [ ] Create NotificationScheduler
  - `notification/NotificationScheduler.kt`
  - `scheduleNotifications()` method
  - `cancelNotifications()` method
  - Calculate initial delay for next notification time
  - Handle 3x/day and 4x/day frequencies

- [ ] Update Hilt to provide notification components

- [ ] Test notification scheduling (without UI)
  - Use `adb shell dumpsys jobscheduler` to verify

**End of Day 6 Deliverable:** Working background notification system

---

## **DAY 7: Polish Notification System**

### Morning (4 hours)
- [ ] Add notification permission handling
  - Request permission in settings (Android 13+)
  - Handle permission result

- [ ] Test notifications thoroughly
  - Test immediate notification
  - Test scheduled notifications
  - Test notification tap → opens app
  - Test different frequencies (3x, 4x)

### Afternoon (4 hours)
- [ ] Handle edge cases
  - App killed → notifications still work
  - Airplane mode → graceful handling
  - No questions in database → show default message
  - Battery optimization → ensure WorkManager survives

- [ ] Debug and refine notification UX
  - Better notification text
  - Add notification icon
  - Test on different Android versions

**End of Day 7 Deliverable:** Robust, production-ready notification system

---

## **DAY 8: Authentication UI**

### Morning (4 hours)
- [ ] Create splash screen
  - `res/layout/activity_splash.xml`
  - `presentation/ui/splash/SplashActivity.kt`
  - `presentation/ui/splash/SplashViewModel.kt`
  - Check if user logged in
  - Navigate to Login or Main

- [ ] Create login screen
  - `res/layout/activity_login.xml`
    - Email EditText
    - Password EditText (password input type)
    - Login Button
    - "Sign Up" link
  - `presentation/ui/auth/LoginActivity.kt`
  - `presentation/ui/auth/LoginViewModel.kt`

### Afternoon (4 hours)
- [ ] Implement login functionality
  - Validate email format
  - Validate password length
  - Show loading indicator
  - Handle errors (wrong password, no internet, etc.)
  - Navigate to MainActivity on success

- [ ] Test login flow end-to-end

**End of Day 8 Deliverable:** Working splash and login screens

---

## **DAY 9: Sign Up & Language Selection**

### Morning (4 hours)
- [ ] Create sign up screen
  - `res/layout/activity_signup.xml`
    - Email, Password, Confirm Password
    - "Next" button
  - `presentation/ui/auth/SignUpActivity.kt`
  - `presentation/ui/auth/SignUpViewModel.kt`

- [ ] Implement sign up validation
  - Email format check
  - Password strength check
  - Passwords match check

### Afternoon (4 hours)
- [ ] Create language selection screen
  - `res/layout/fragment_language_selection.xml`
    - RecyclerView or ChipGroup
    - List of programming languages
    - "Complete" button
  
  - `presentation/ui/auth/LanguageSelectionFragment.kt`
  - `presentation/adapter/LanguageSelectionAdapter.kt`

- [ ] Implement sign up flow
  1. User enters email/password → Next
  2. Show language selection
  3. User selects languages → Complete
  4. Create Firebase Auth account
  5. Save preferences
  6. Navigate to MainActivity

**End of Day 9 Deliverable:** Complete authentication flow

---

## **DAY 10: Main Activity & Navigation**

### Morning (4 hours)
- [ ] Create MainActivity with bottom navigation
  - `res/layout/activity_main.xml`
    - BottomNavigationView
    - FragmentContainerView
  
  - `res/menu/bottom_navigation_menu.xml`
    - Home, Progress, Settings

  - `presentation/ui/main/MainActivity.kt`
  - Set up Navigation Component

### Afternoon (4 hours)
- [ ] Create navigation graph
  - `res/navigation/nav_graph.xml`
  - Define fragments: Home, Progress, Settings

- [ ] Create empty fragments
  - `res/layout/fragment_home.xml`
  - `presentation/ui/main/fragments/HomeFragment.kt`
  - `presentation/ui/main/fragments/HomeViewModel.kt`
  
  - `res/layout/fragment_progress.xml`
  - `presentation/ui/main/fragments/ProgressFragment.kt`
  - `presentation/ui/main/fragments/ProgressViewModel.kt`
  
  - `res/layout/fragment_settings.xml`
  - `presentation/ui/main/fragments/SettingsFragment.kt`
  - `presentation/ui/main/fragments/SettingsViewModel.kt`

**End of Day 10 Deliverable:** Main activity with navigation skeleton

---

## **DAY 11: Home Fragment**

### Morning (4 hours)
- [ ] Design Home Fragment UI
  - Welcome message with user email
  - Today's stats card
    - Questions answered today: X/5
    - Progress bar
  - Current streak card
    - 🔥 X days
  - Large "Start Quiz" button

- [ ] Implement HomeViewModel
  - Load daily stats
  - Load streak
  - Expose as LiveData/StateFlow

### Afternoon (4 hours)
- [ ] Implement Home Fragment
  - Observe ViewModel data
  - Update UI
  - Handle "Start Quiz" click
    - Navigate to QuestionActivity

- [ ] Polish UI
  - Add Material Design components
  - Add animations
  - Add icons

**End of Day 11 Deliverable:** Beautiful, functional home screen

---

## **DAY 12: Question Activity (Core Feature)**

### Morning (4 hours)
- [ ] Design Question Activity UI
  - `res/layout/activity_question.xml`
    - Language badge (e.g., "Kotlin")
    - Difficulty badge (e.g., "Easy")
    - Question text
    - Code snippet view (will add syntax highlighting later)
    - RadioGroup with 4 options
    - Submit button

- [ ] Create QuestionActivity
  - `presentation/ui/question/QuestionActivity.kt`
  - `presentation/ui/question/QuestionViewModel.kt`

### Afternoon (4 hours)
- [ ] Implement question loading
  - Get questionId from intent (if from notification)
  - Otherwise, get random question
  - Display question details
  - Show 4 options as radio buttons

- [ ] Implement answer submission
  - Get selected option
  - Call SubmitAnswerUseCase
  - Record start/end time
  - Show result dialog

**End of Day 12 Deliverable:** Working question display and submission

---

## **DAY 13: Question Result & Progress Fragment**

### Morning (4 hours)
- [ ] Create result dialog
  - `res/layout/dialog_question_result.xml`
    - ✓ or ✗ icon (correct/wrong)
    - Correct answer text
    - Explanation text
    - "Next Question" button
    - "Back to Home" button

  - `presentation/ui/question/QuestionResultDialog.kt`
  - Show dialog after answer submission

### Afternoon (4 hours)
- [ ] Implement Progress Fragment
  - `res/layout/fragment_progress.xml`
    - Total questions answered
    - Correct answers
    - Accuracy percentage
    - Recent activity RecyclerView

  - `presentation/adapter/ProgressHistoryAdapter.kt`
  - Display recent questions answered
  - Show correct/wrong icon
  - Show timestamp

**End of Day 13 Deliverable:** Complete question flow with progress tracking

---

## **DAY 14: Settings & Code Highlighting**

### Morning (4 hours)
- [ ] Implement Settings Fragment
  - Notifications toggle (SwitchCompat)
  - Notification frequency (3x or 4x per day)
  - Manage languages (edit selections)
  - Logout button

- [ ] Connect settings to NotificationScheduler
  - Toggle notifications → schedule/cancel
  - Change frequency → reschedule

### Afternoon (4 hours)
- [ ] Implement code syntax highlighting
  - `util/CodeHighlighter.kt`
  - Use Markwon + Prism4j
  - Support Kotlin, Java, Python, JavaScript, etc.
  - Apply to code snippets in QuestionActivity

- [ ] Create utility functions
  - `util/Extensions.kt`
  - `util/DateTimeUtil.kt`
  - Toast extensions
  - View visibility extensions
  - Date formatting

**End of Day 14 Deliverable:** Complete settings and beautiful code display

---

## **DAY 15: Database Seeding**

### Morning (4 hours)
- [ ] Create initial questions JSON
  - `assets/initial_questions.json`
  - Minimum 50 questions across languages
  - Mix of easy, medium, hard
  - Covering common bugs and gotchas

- [ ] Create DatabaseSeeder
  - `data/local/database/DatabaseSeeder.kt`
  - Read JSON from assets
  - Parse and insert into Room DB
  - Run on first app launch only

### Afternoon (4 hours)
- [ ] Test complete app flow
  1. Install app → Splash → Login
  2. Sign up → Select languages
  3. Home screen → Start quiz
  4. Answer question → See result
  5. Check progress screen
  6. Go to settings → Toggle notifications
  7. Wait for notification → Tap → Answer question

- [ ] Fix any bugs found during testing

**End of Day 15 Deliverable:** Fully functional MVP app

---

## **DAY 16-17: Testing & Polish**

### Day 16 Morning (4 hours)
- [ ] Write unit tests
  - ViewModel tests
  - Use case tests
  - Repository tests (with mocks)

### Day 16 Afternoon (4 hours)
- [ ] Write UI tests
  - Login flow test
  - Sign up flow test
  - Question answering flow test

### Day 17 Morning (4 hours)
- [ ] Polish UI/UX
  - Consistent colors and typography
  - Smooth transitions
  - Loading states
  - Error states
  - Empty states

### Day 17 Afternoon (4 hours)
- [ ] Final bug fixes
- [ ] Performance optimization
- [ ] Prepare for deployment
  - Generate signed APK
  - Test on multiple devices
  - Create app icon
  - Write Play Store description

**End of Day 17 Deliverable:** Production-ready MVP app

---

## 📊 Progress Tracking

Use this table to track your progress:

| Day | Phase | Status | Notes |
|-----|-------|--------|-------|
| 1 | Project Setup | ⬜ | |
| 2 | Architecture | ⬜ | |
| 3 | Models & DB | ⬜ | |
| 4 | Repositories | ⬜ | |
| 5 | Use Cases | ⬜ | |
| 6 | Notifications 1 | ⬜ | |
| 7 | Notifications 2 | ⬜ | |
| 8 | Auth UI 1 | ⬜ | |
| 9 | Auth UI 2 | ⬜ | |
| 10 | Main Activity | ⬜ | |
| 11 | Home Screen | ⬜ | |
| 12 | Question UI | ⬜ | |
| 13 | Result & Progress | ⬜ | |
| 14 | Settings & Code | ⬜ | |
| 15 | Database Seed | ⬜ | |
| 16 | Testing | ⬜ | |
| 17 | Polish | ⬜ | |

---

## 🎯 Daily Checklist Template

Copy this for each day:

```
## Day X: [PHASE NAME]

Date: ________

### Morning Session (9 AM - 1 PM)
- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

**Blockers:**
- 

**Notes:**
- 

### Afternoon Session (2 PM - 6 PM)
- [ ] Task 1
- [ ] Task 2
- [ ] Task 3

**Blockers:**
- 

**Notes:**
- 

### End of Day Review
- [ ] All tasks completed?
- [ ] Code committed to Git?
- [ ] Tomorrow's tasks clear?

**What went well:**
- 

**What to improve:**
- 

**Questions for tomorrow:**
- 
```

---

## 🚨 Common Pitfalls to Avoid

1. **Day 1-2:** Don't skip dependency configuration. Verify ALL dependencies sync successfully.

2. **Day 3-5:** Don't skip repository layer. It's tempting to directly use DAOs in ViewModels, but this violates architecture.

3. **Day 6-7:** Test notifications EARLY. Don't wait until Day 15 to discover WorkManager issues.

4. **Day 8-9:** Don't hardcode Firebase credentials. Use `google-services.json`.

5. **Day 10-14:** Don't try to make UI perfect. Focus on functionality first, polish later.

6. **Day 15:** Don't manually insert questions. Use JSON + automated seeding.

7. **Day 16-17:** Don't skip testing. You'll regret it.

---

## 📞 Help Resources

If you get stuck:

1. **Kotlin:** https://kotlinlang.org/docs/
2. **Android:** https://developer.android.com/
3. **Hilt:** https://dagger.dev/hilt/
4. **Room:** https://developer.android.com/training/data-storage/room
5. **WorkManager:** https://developer.android.com/topic/libraries/architecture/workmanager
6. **Firebase:** https://firebase.google.com/docs/android

---

**Good luck! You've got this! 🚀**
