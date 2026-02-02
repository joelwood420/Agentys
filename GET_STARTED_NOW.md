# 🚀 START BUILDING IN 30 MINUTES

This guide gets you from zero to coding in 30 minutes. Follow these exact steps.

---

## ✅ PRE-REQUISITES (15 minutes)

### 1. Install Android Studio
- Download: https://developer.android.com/studio
- Version: Latest stable (Hedgehog 2023.1.1 or newer)
- During installation, include Android SDK and Android Virtual Device

### 2. Create Firebase Project
1. Go to https://console.firebase.google.com
2. Click "Add project"
3. Project name: **CodeQuiz** (or your choice)
4. Disable Google Analytics (optional for MVP)
5. Click "Create project"

### 3. Set Up Firebase Services
**Enable Authentication:**
- In Firebase Console → Build → Authentication
- Click "Get started"
- Click "Email/Password" → Enable → Save

**Create Firestore Database:**
- In Firebase Console → Build → Firestore Database
- Click "Create database"
- Start in **test mode** (we'll secure it later)
- Choose location closest to your users
- Click "Enable"

---

## 📱 CREATE ANDROID PROJECT (5 minutes)

### Step 1: New Project in Android Studio

1. **Open Android Studio** → "New Project"

2. **Template**: Select "Empty Activity"

3. **Configure Project**:
   ```
   Name:                 CodeQuiz
   Package name:         com.codequiz
   Save location:        [Your choice]
   Language:             Kotlin
   Minimum SDK:          API 24 (Android 7.0)
   Build configuration:  Kotlin DSL (build.gradle.kts)
   ```

4. Click **Finish**

### Step 2: Connect Firebase to Android

1. In Firebase Console → Project Overview → "Android" icon

2. **Register app**:
   ```
   Android package name: com.codequiz
   App nickname: CodeQuiz (optional)
   Debug signing certificate: [Leave empty for now]
   ```

3. Click "Register app"

4. **Download google-services.json**:
   - Click "Download google-services.json"
   - Move file to: `YourProject/app/` folder
   - Confirm it's in the right place (same level as build.gradle.kts)

5. Click "Next" → "Next" → "Continue to console"

---

## 🔧 CONFIGURE BUILD FILES (10 minutes)

### Step 3: Project-level build.gradle.kts

Open `build.gradle.kts` (Project level) and add:

```kotlin
// Top-level build file
plugins {
    id("com.android.application") version "8.2.0" apply false
    id("org.jetbrains.kotlin.android") version "1.9.20" apply false
    id("com.google.dagger.hilt.android") version "2.50" apply false
    id("com.google.gms.google-services") version "4.4.0" apply false
}
```

### Step 4: App-level build.gradle.kts

Open `app/build.gradle.kts` and replace entire content with:

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

        testInstrumentationRunner = "androidx.test.runner.AndroidJUnitRunner"
    }

    buildTypes {
        release {
            isMinifyEnabled = false
            proguardFiles(
                getDefaultProguardFile("proguard-android-optimize.txt"),
                "proguard-rules.pro"
            )
        }
    }
    
    compileOptions {
        sourceCompatibility = JavaVersion.VERSION_17
        targetCompatibility = JavaVersion.VERSION_17
    }
    
    kotlinOptions {
        jvmTarget = "17"
    }
    
    buildFeatures {
        viewBinding = true
    }
}

dependencies {
    // Core Android
    implementation("androidx.core:core-ktx:1.12.0")
    implementation("androidx.appcompat:appcompat:1.6.1")
    implementation("com.google.android.material:material:1.11.0")
    implementation("androidx.constraintlayout:constraintlayout:2.1.4")
    
    // Lifecycle components
    implementation("androidx.lifecycle:lifecycle-viewmodel-ktx:2.7.0")
    implementation("androidx.lifecycle:lifecycle-livedata-ktx:2.7.0")
    implementation("androidx.lifecycle:lifecycle-runtime-ktx:2.7.0")
    
    // Coroutines
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-android:1.7.3")
    implementation("org.jetbrains.kotlinx:kotlinx-coroutines-play-services:1.7.3")
    
    // Activity & Fragment KTX
    implementation("androidx.activity:activity-ktx:1.8.2")
    implementation("androidx.fragment:fragment-ktx:1.6.2")
    
    // Navigation
    implementation("androidx.navigation:navigation-fragment-ktx:2.7.6")
    implementation("androidx.navigation:navigation-ui-ktx:2.7.6")
    
    // Room Database
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
    
    // Hilt Dependency Injection
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
}

// Allow references to generated code
kapt {
    correctErrorTypes = true
}
```

### Step 5: Sync Project

Click **"Sync Now"** in the top banner. Wait for Gradle sync to complete (2-3 minutes).

**Common Issues:**
- If sync fails, check internet connection
- Verify `google-services.json` is in `app/` folder
- Check Android Studio is using Java 17 (Settings → Build → Gradle → Gradle JDK)

---

## 📁 CREATE PROJECT STRUCTURE (5 minutes)

### Step 6: Create Package Structure

In `app/src/main/java/com/codequiz/`, create these packages:

```
Right-click on "com.codequiz" → New → Package
```

Create all of these:
- `di`
- `data.local.database.dao`
- `data.local.database.entities`
- `data.local.preferences`
- `data.remote`
- `data.repository`
- `domain.model`
- `domain.repository`
- `domain.usecase.auth`
- `domain.usecase.question`
- `domain.usecase.preference`
- `domain.usecase.progress`
- `presentation.ui.splash`
- `presentation.ui.auth`
- `presentation.ui.main`
- `presentation.ui.question`
- `presentation.adapter`
- `presentation.common`
- `notification`
- `util`

**Shortcut**: Create one package at a time, or use dots for nested packages:
```
Example: "data.local.database.dao" creates the full hierarchy
```

---

## 🎯 CREATE APPLICATION CLASS (5 minutes)

### Step 7: Create CodeQuizApp.kt

In package `com.codequiz`, create `CodeQuizApp.kt`:

```kotlin
package com.codequiz

import android.app.Application
import dagger.hilt.android.HiltAndroidApp

@HiltAndroidApp
class CodeQuizApp : Application() {
    override fun onCreate() {
        super.onCreate()
    }
}
```

### Step 8: Update AndroidManifest.xml

Open `app/src/main/AndroidManifest.xml` and update the `<application>` tag:

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <!-- Permissions -->
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.POST_NOTIFICATIONS" />
    <uses-permission android:name="android.permission.SCHEDULE_EXACT_ALARM" />

    <application
        android:name=".CodeQuizApp"
        android:allowBackup="true"
        android:dataExtractionRules="@xml/data_extraction_rules"
        android:fullBackupContent="@xml/backup_rules"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/Theme.CodeQuiz"
        tools:targetApi="31">
        
        <activity
            android:name=".MainActivity"
            android:exported="true">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
        
    </application>

</manifest>
```

---

## ✅ VERIFY SETUP

### Step 9: Build the Project

1. Click **Build → Rebuild Project**
2. Wait for build to complete (1-2 minutes)
3. Check "Build" tab at bottom - should say "BUILD SUCCESSFUL"

### Step 10: Run on Emulator

1. **Create emulator** (if you don't have one):
   - Tools → Device Manager
   - Create Virtual Device
   - Choose: Pixel 6 + Android 13 (API 33)
   - Finish

2. **Run app**:
   - Click green play button (▶️)
   - Select your emulator
   - App should launch showing empty activity

**Success!** You now have:
- ✅ Working Android project with Kotlin
- ✅ Firebase connected
- ✅ All dependencies installed
- ✅ Hilt configured
- ✅ Package structure created
- ✅ App builds and runs

---

## 🎯 WHAT'S NEXT?

You're now ready to start implementing features. Follow this order:

### Week 1: Data Foundation
**Day 1-2**: ✅ Complete (you just did this!)  
**Day 3**: Create domain models (Question, User, Progress)  
**Day 4**: Create Room database entities and DAOs  
**Day 5**: Implement repositories  

### Week 2: Core Features  
**Day 6**: Create use cases (business logic)  
**Day 7-8**: Build notification system with WorkManager  
**Day 9-10**: Create authentication UI (Login, SignUp)  

### Week 3: Main App  
**Day 11-13**: Build main app screens (Question display, Progress)  
**Day 14**: Add code syntax highlighting  
**Day 15**: Seed database with questions  

### Week 4: Polish  
**Day 16-17**: Testing, bug fixes, deployment prep  

---

## 📚 DETAILED NEXT STEPS

### Immediate Next Step: Create Domain Models (Day 3)

1. Open the detailed plan: `/tmp/android_app_plan.md`
2. Go to **Phase 1: Data Layer - Domain Models**
3. Start with creating `ProgrammingLanguage.kt`

**Pro Tip**: Keep `/tmp/IMPLEMENTATION_QUICK_START.md` open as a reference while coding.

---

## 🆘 NEED HELP?

**Common Issues:**

1. **Build fails with "Could not find google-services.json"**
   - Solution: Verify file is in `app/` folder (not `app/src/`)

2. **"Failed to resolve: firebase-bom"**
   - Solution: Check internet connection, sync project again

3. **"Hilt components not generated"**
   - Solution: Build → Clean Project → Rebuild Project

4. **Emulator won't start**
   - Solution: Tools → Device Manager → Wipe Data on emulator

---

## 🎉 YOU'RE READY!

You've completed the setup phase. Your development environment is fully configured.

**Estimated time to MVP**: 15-17 days following the detailed plan.

**Next**: Open `/tmp/PROJECT_TIMELINE.md` and start Day 3 tasks.

Good luck building! 🚀
