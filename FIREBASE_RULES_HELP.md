# Firestore Security Rules Fix

To fix the "Failed to load users" error, update your Firestore Rules in the Firebase Console:

1. Go to Firebase Console > Firestore Database > Rules
2. Paste this code:

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // 1. Transactions & Categories: Strictly private (Owner only)
    match /users/{userId}/transactions/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
    match /users/{userId}/categories/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }

    // 2. User Profiles: 
    // - Owner can Read/Write their own profile
    // - Admin (devsrihari@gmail.com) can Read ALL profiles
    match /users/{userId} {
      allow create: if request.auth != null; 
      allow write: if request.auth != null && request.auth.uid == userId;
      
      allow read: if request.auth != null && (
        request.auth.uid == userId || 
        request.auth.token.email == 'devsrihari@gmail.com'
      );
    }
  }
}
```
