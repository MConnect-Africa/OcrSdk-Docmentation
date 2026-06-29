# Isocel ID OCR SDK

The Isocel ID OCR SDK is a powerful Android library designed for real-time identification card detection and data extraction. It supports multiple generations of ID cards and provides a seamless integration for camera-based scanning.

## Features

- **Real-time Card Detection**: Automatically detects ID cards in camera frames.
- **Card Cropping**: Intelligently crops and rectifies the card image.
- **Data Extraction**: Extracts key fields such as ID number, full names, dates, and more.
- **Face Extraction**: Automatically extracts the portrait from the ID card.

## Installation

### 1. Add GitHub Packages Repository

Add the GitHub Packages repository to your project's `settings.gradle.kts` or `build.gradle` file:

```kotlin
// In settings.gradle.kts
dependencyResolutionManagement {
   repositories {
      // Other repositories...
      maven {
         name = "GitHubPackages"
         url = uri("maven_package_url")
         credentials {
            username = project.findProperty("gpr.user") as String? ?: System.getenv("USERNAME")
            password = project.findProperty("gpr.key") as String? ?: System.getenv("TOKEN")
         }
      }
   }
}
```
### 2. Add Authentication Credentials

Add your provided username and password to your `local.properties` file:

```properties
gpr.user=PROVIDED_USERNAME
gpr.key=PROVIDED_PASSWORD
```

### 3. Add the Dependency

Add the dependency to your app's `build.gradle.kts` file:

```kotlin
dependencies {
   implementation("co.isocel:idocr:<version>") // 1.0.1
}
```

## Setup

Place the `isocel_config.json` file in your application's `assets` directory located at:

`src/main/assets`

The `assets` directory should be at the same level as the `java` (or `kotlin`) and `res` directories under `src/main`.

Example project structure:

```
src/
└── main/
    ├── java/
    ├── res/
    ├── assets/
    │   └── isocel_config.json
    └── AndroidManifest.xml
```


## Initialization

Initialize the SDK in your `Application` class:

```kotlin

class MyApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        IdOcr.initialize(this)
    }
}
```

## Usage

### 1. Real-time Scanning with CameraX

The SDK provides `CardAnalyzer`, which integrates with Android's CameraX library for real-time processing.

```kotlin
val analysis = ImageAnalysis.Builder()
    .setTargetResolution(Size(1280, 720))
    .setBackpressureStrategy(ImageAnalysis.STRATEGY_KEEP_ONLY_LATEST)
    .setOutputImageFormat(ImageAnalysis.OUTPUT_IMAGE_FORMAT_RGBA_8888)
    .build()
    .also {
        it.setAnalyzer(executor, CardAnalyzer(
            onCardCropped = { idFields ->
                // Handle extracted data
            },
            onHintUpdated = { hint ->
                // Update UI with ScanHint (e.g., "Searching", "Tilt Left")
            },
            onError = { message ->
                // Handle errors (e.g., license verification failure)
            },
            onNoCard = {
                // Handle case where no card is detected
            }
        ))
    }
```

## Data Model: IdFields

The result of the OCR process is returned as an `IdFields` object containing:

- `idNumber`: The unique ID card number.
- `fullNames`: Full name as it appears on the card.
- `surname`: Extracted surname.
- `givenNames`: Extracted given names.
- `sex`: Gender (M/F).
- `dateOfBirth`: Date of birth.
- `nationality`: Nationality.
- `placeOfBirth`: Place of birth.
- `dateOfIssue`: Card issue date.
- `dateOfExpiry`: Card expiry date.
- `faceBitmap`: Extracted portrait image.
- `idCardBitmap`: Cropped image of the full card.

## Requirements

- Minimum SDK: 24
- Target SDK: 35+
