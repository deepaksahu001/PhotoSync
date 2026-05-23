# PhotoSync — Android App

Register room photos ko original resolution mein Google Drive pe store karta hai.
Duplicate detection built-in hai (SHA-256 hash se).

---

## Features

- Google Sign-In se secure login
- Camera se directly photo click karo
- Gallery se multiple photos select karo
- Photos **original resolution** mein upload hoti hain (koi compression nahi)
- **Duplicate detection** — same photo dobara upload nahi hogi
- Google Drive folder structure: `PhotoSync_Project / UserName / Date / Photos`
- Har user ka apna alag folder aur `hashes.json`
- Gallery screen — Drive mein saved photos dekhein

---

## Project Structure

```
app/src/main/java/com/photosync/
├── data/
│   └── drive/
│       └── DriveService.kt        ← Drive API, upload, folder creation, hash management
├── ui/
│   ├── login/
│   │   └── LoginActivity.kt       ← Google Sign-In screen
│   ├── home/
│   │   ├── HomeActivity.kt        ← Main screen, camera/gallery picker
│   │   ├── HomeViewModel.kt       ← Upload logic, duplicate detection
│   │   └── UploadItemAdapter.kt   ← Upload progress list
│   └── gallery/
│       ├── GalleryActivity.kt     ← Shows Drive photos
│       └── GalleryAdapter.kt      ← Grid adapter
└── util/
    ├── HashUtil.kt                ← SHA-256 hash generator
    └── SessionManager.kt          ← Login session storage
```

---

## Setup Steps

### Step 1 — Google Cloud Console

1. [console.cloud.google.com](https://console.cloud.google.com) par jao
2. Naya project banao: **PhotoSync**
3. **APIs & Services → Enable APIs** mein yeh enable karo:
   - Google Drive API
   - Google Sign-In API
4. **Credentials → Create Credentials → OAuth 2.0 Client ID** banao
   - Application type: **Android**
   - Package name: `com.photosync`
   - SHA-1 fingerprint: apni keystore se nikalo (neeche command hai)

```bash
keytool -list -v -keystore ~/.android/debug.keystore -alias androiddebugkey -storepass android -keypass android
```

5. `google-services.json` download karo aur `app/` folder mein dalo

### Step 2 — Android Studio mein import karo

1. **File → Open** → `PhotoSync` folder select karo
2. Gradle sync hone do
3. `app/google-services.json` file dalo (Step 1 se)

### Step 3 — Run karo

1. Physical Android device connect karo (Camera ke liye)
2. **Run → Run 'app'**

---

## Drive Folder Structure

```
My Drive/
└── PhotoSync_Project/              ← Shared root (admin share kar sakta hai)
    ├── Amit_Kumar/
    │   ├── 2025-05-23/
    │   │   ├── PHOTO_001.jpg       ← Original resolution
    │   │   └── PHOTO_002.jpg
    │   └── hashes.json             ← Duplicate tracking
    ├── Priya_Singh/
    │   ├── 2025-05-23/
    │   └── hashes.json
    └── Rahul_Verma/
        └── ...
```

---

## Duplicate Detection Logic

1. Photo select hone par uska **SHA-256 hash** generate hota hai
2. User ke `hashes.json` se compare hota hai (Drive se download hota hai)
3. Match mila → **"Duplicate — skip kar diya"** message
4. Match nahi mila → Original resolution mein upload → Hash save hota hai

**Note:** WhatsApp compressed photo aur original photo ka hash alag hoga, isliye agar koi dono bheje to dono upload hongi. Same exact file dobara bheje to duplicate detect hoga.

---

## Dependencies

- Kotlin + Coroutines
- Google Sign-In (`play-services-auth`)
- Google Drive API v3
- Glide (image loading)
- Material Components
- ViewBinding

---

## Admin Setup (Multi-user ke liye)

1. Ek Google account se pehli baar login karo — `PhotoSync_Project` folder automatically ban jayega
2. Us folder ko team ke sabhi Google accounts ke saath **Editor** permission se share karo:
   - Google Drive → `PhotoSync_Project` → Share → email add karo
3. Ab har user apna account use karke login karega — unka apna subfolder ban jayega

---

## Troubleshooting

| Problem | Solution |
|---|---|
| Sign-in fail ho raha hai | SHA-1 fingerprint Google Console mein sahi hai? |
| Drive upload nahi ho raha | Drive API enabled hai Google Console mein? |
| `google-services.json` error | File `app/` folder mein hai? |
| Camera kaam nahi kar raha | Physical device use karo (emulator mein camera nahi hota) |
