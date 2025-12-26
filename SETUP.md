# FriendSpaces Alpha - Setup Guide 🚀

Complete guide to building and deploying your own FriendSpace (v1.1).

---

## 📋 Prerequisites

Before you begin, make sure you have:
- A Google account (for Firebase)
- A github account (for hosting)

---

## 📦 Step 1: Get the Code

Download the latest release and place them in a directory for use with Github Pages or Firebase Hosting.

---

## 🔥 Step 2: Firebase Project Setup

### 2.1 Create Firebase Project

1. Go to [Firebase Console](https://console.firebase.google.com/)
2. Click **"Add project"** or **"Create a project"**
3. Enter project name: `FriendSpaces` (or your choice)
4. Click **Continue**
5. **Disable** Google Analytics (not needed) or keep it if you want
6. Click **"Create project"**
7. Wait for creation, then click **"Continue"**

### 2.2 Register Web App

1. In your Firebase project dashboard, click the **Web icon** (`</>`) to add an app.
2. Register app nickname: `FriendSpaces Web`
3. ✅ **Check** "Also set up Firebase Hosting"
4. Click **"Register app"**
5. **COPY YOUR API** - You'll see a text containing text like this:

```javascript
const firebaseConfig = {
  apiKey: "AIzaSyXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project-id",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123456789012",
  appId: "1:123456789012:web:abcdef123456"
};
```

6. **Copy the API Key** -  and save it, you'll need it in Step 3
7. Click **"Continue to console"**

---

## 🔐 Step 3: Enable Authentication

1. In the left sidebar, click **"Authentication"** inside of Build.
2. Click **"Get started"**
3. Click on **"Email/Password"** under Sign-in providers
4. **Enable** the first toggle (Email/Password)
5. Leave "Email link (passwordless sign-in)" **disabled**
6. Click **"Save"**

---

## 💾 Step 4: Create Firestore Database

### 4.1 Create Database

1. In the left sidebar, click **"Firestore Database"** inside of Build.
2. Click **"Create database"**
3. Select **"Start in production mode"** (we'll add rules next)
4. Click **"Next"**
5. Choose your location (pick closest to your users)
6. Click **"Enable"**
7. Wait for database creation

### 4.2 Set Up Security Rules

1. Still in Firestore Database, click the **"Rules"** tab
2. **Delete everything** in the editor
3. **Paste these rules:**

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Helper functions
    function isSignedIn() {
      return request.auth != null;
    }
    
    function isMember(spaceId) {
      return isSignedIn() && 
             exists(/databases/$(database)/documents/spaces/$(spaceId)/members/$(request.auth.uid));
    }
    
    function getMemberData(spaceId) {
      return get(/databases/$(database)/documents/spaces/$(spaceId)/members/$(request.auth.uid)).data;
    }
    
    function isOwnerOrCoOwner(spaceId) {
      let memberData = getMemberData(spaceId);
      return memberData.role == 'owner' || memberData.role == 'co-owner';
    }
    
    function isActive(spaceId) {
      let memberData = getMemberData(spaceId);
      return memberData.status == 'active' || !('status' in memberData);
    }
    
    function isNotBanned(spaceId) {
      let memberData = getMemberData(spaceId);
      return memberData.status != 'banned';
    }
    
    // Spaces collection
    match /spaces/{spaceId} {
      // Anyone signed in can read a space
      allow read: if isSignedIn();
      
      // Only authenticated users can create spaces
      allow create: if isSignedIn();
      
      // Only owner/co-owner can update or delete
      allow update: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
      allow delete: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
      
      // Members subcollection
      match /members/{userId} {
        // Anyone signed in can read members
        allow read: if isSignedIn();
        
        // Users can create their own member document when joining
        allow create: if isSignedIn() && request.auth.uid == userId;
        
        // Owner/co-owner can update any member, users can update their own
        allow update: if isSignedIn() && (
          request.auth.uid == userId || 
          (isMember(spaceId) && isOwnerOrCoOwner(spaceId))
        );
        
        // Only owner/co-owner can delete members
        allow delete: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
      }
      
      // Tables subcollection
      match /tables/{tableId} {
        // Members can read tables
        allow read: if isMember(spaceId);
        
        // Owner/co-owner can manage tables
        allow create, update, delete: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
        
        // Messages subcollection
        match /messages/{messageId} {
          // Members can read messages
          allow read: if isMember(spaceId);
          
          // Active members can create messages
          allow create: if isMember(spaceId) && isActive(spaceId) && isNotBanned(spaceId);
          
          // Owner/co-owner can update/delete messages (for moderation)
          allow update, delete: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
        }
      }
      
      // Reports subcollection
      match /reports/{reportId} {
        // Only owner/co-owner can read reports
        allow read: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
        
        // Owner/co-owner can create and update reports
        allow create, update: if isMember(spaceId) && isOwnerOrCoOwner(spaceId);
        
        // No deletion of reports
        allow delete: if false;
      }
      
      // Presence subcollection
      match /presence/{userId} {
        // Anyone in the space can read presence
        allow read: if isMember(spaceId);
        
        // Users can only update their own presence
        allow create, update: if isSignedIn() && request.auth.uid == userId;
        
        // Users can delete their own presence
        allow delete: if isSignedIn() && request.auth.uid == userId;
      }
    }
  }
}
```

4. Click **"Publish"**

---

## ⚙️ Step 5: Configure Your Code

### 5.1 Add Firebase Config

1. Open `script.js` in your text editor
2. Find lines 3-10 (the Firebase config section)
3. Replace with YOUR config from Step 2.2:

```javascript
const firebaseConfig = {
    apiKey: "YOUR_API_KEY_HERE",
    authDomain: "YOUR_PROJECT_ID.firebaseapp.com",
    projectId: "YOUR_PROJECT_ID",
    storageBucket: "YOUR_PROJECT_ID.appspot.com",
    messagingSenderId: "YOUR_SENDER_ID",
    appId: "YOUR_APP_ID"
};
```

4. **Save the file**

---

## 🌐 Step 6: Deploy Your FriendSpace

You have two deployment options:

### Option A: Firebase Hosting (Recommended)

#### 6.1 Install Firebase CLI

Open Terminal/Command Prompt and run:

```bash
npm install -g firebase-tools
```

If you don't have Node.js/npm, [download it first](https://nodejs.org/).

#### 6.2 Login to Firebase

```bash
firebase login
```

This will open your browser - sign in with your Google account.

#### 6.3 Initialize Firebase

Navigate to your FriendSpaces folder:

```bash
cd /path/to/FriendSpaces
```

Initialize Firebase:

```bash
firebase init hosting
```

Answer the prompts:
- **Use existing project** → Select your project
- **Public directory?** → Press Enter (use current directory `.`)
- **Configure as single-page app?** → `Yes`
- **Overwrite index.html?** → `No` (important!)
- **Set up automatic builds?** → `No`

#### 6.4 Deploy

```bash
firebase deploy --only hosting
```

Wait for deployment... Done! ✅

Your FriendSpace is now live at: `https://YOUR_PROJECT_ID.web.app`

### Option B: GitHub Pages

#### 6.1 Create GitHub Repository

1. Go to [GitHub](https://github.com)
2. Click **"New repository"**
3. Name it `friendspaces` (or your choice)
4. Make it **Public**
5. Click **"Create repository"**

#### 6.2 Upload Files

1. Click **"uploading an existing file"**
2. Drag and drop all your FriendSpaces files
3. Click **"Commit changes"**

#### 6.3 Enable GitHub Pages

1. Go to **Settings** → **Pages**
2. Under "Source", select **main** branch
3. Select **root** folder
4. Click **Save**

Your site will be live at: `https://YOUR_USERNAME.github.io/friendspaces`

---

## 🎉 Step 7: Create Your Space

1. Visit your deployed URL
2. Click **"Create Account"**
3. Enter:
   - Display name (your name)
   - Email
   - Password (min 6 characters)
4. Click **"Create Account"**

You'll be taken through the setup wizard:

### Step 1: Name Your Space
Enter a name like "The Squad" or "Study Group"

### Step 2: Choose Icon
Pick an emoji and background color

### Step 3: Create Tables
- "Main" is already there
- Add more like "Announcements", "Random", etc.
- Click **"+ Add Table"** for each one

### Step 4: Privacy
- **Public** - Anyone with URL can join
- **Private** - Need join code to join

### Step 5: Theme
Pick background and text colors (or use defaults)

### Step 6: Done!
If private, you'll see your join code - **save it!**

Click **"Enter Space"** and you're in! 🎊

---

## 👥 Step 8: Invite Friends

### For Public Spaces:
Just share the URL: `https://your-space.web.app`

### For Private Spaces:
Share the URL **and** the 10-character join code

They'll:
1. Visit the URL
2. Create account / sign in
3. Enter join code
4. Join your space!

---

## 🛠️ Troubleshooting

### "Permission denied" Error
**Problem**: Firestore security rules not set correctly

**Solution**:
1. Go to Firebase Console → Firestore → Rules
2. Make sure you pasted the FULL rules from Step 4.2
3. Click "Publish"
4. Refresh your app

### Can't Create Account
**Problem**: Email/Password auth not enabled

**Solution**:
1. Firebase Console → Authentication
2. Enable Email/Password sign-in method
3. Try again

### App Not Loading
**Problem**: Firebase config incorrect

**Solution**:
1. Check `script.js` lines 3-10
2. Make sure all values match your Firebase config
3. No extra quotes or missing commas

### Images Not Showing
**Problem**: Image URL format

**Solution**:
- URL must contain `.jpg`, `.png`, `.gif`, etc.
- Image must be publicly accessible
- Try a direct image URL from a site like Wikimedia Commons

### Not Showing as Online
**Problem**: Tab closed or presence not updating

**Solution**:
- Must keep tab open to show as online
- Check in Firebase Console → Firestore → presence collection
- Delete presence documents to reset

---

## 🔧 Advanced Configuration

### Custom Domain (Firebase Hosting)

1. Firebase Console → Hosting
2. Click "Add custom domain"
3. Enter your domain (e.g., `chat.yourdomain.com`)
4. Follow DNS setup instructions
5. Wait for SSL certificate (automatic)

### Message Auto-Deletion (Optional)

To auto-delete messages after 30 days, set up a Cloud Function:

1. Upgrade to Blaze plan (pay-as-you-go, still free for small usage)
2. Install Firebase Functions:
   ```bash
   firebase init functions
   ```
3. Add deletion function to `functions/index.js`
4. Deploy with `firebase deploy --only functions`

*Note: This is optional - spaces work fine without it*

### Multiple Spaces

Want to host multiple independent spaces?

**Option 1**: Use query parameters
- Space 1: `yoursite.com?space=friends`
- Space 2: `yoursite.com?space=work`

**Option 2**: Deploy multiple times
- Deploy to different domains/subdomains
- Each has independent Firebase project

---

## 📊 Monitoring Your Space

### View Members
Firebase Console → Firestore → spaces → [your-space-id] → members

### View Messages
Firebase Console → Firestore → spaces → [your-space-id] → tables → [table-id] → messages

### Check Reports
Firebase Console → Firestore → spaces → [your-space-id] → reports

### Monitor Usage
Firebase Console → Usage tab (check quota)

---

## 🔒 Security Best Practices

1. **Never share your Firebase config API key publicly** (it's in script.js)
2. **Use strong passwords** for owner/co-owner accounts
3. **Review Firestore rules** periodically
4. **Check reported messages** regularly
5. **Promote co-owners carefully** - they have significant power
6. **Use private spaces** for sensitive groups
7. **Enable 2FA** on your Google account (protects Firebase)

---

## 💰 Cost Expectations

Firebase free tier includes:
- **Storage**: 1 GB
- **Bandwidth**: 10 GB/month
- **Reads**: 50,000/day
- **Writes**: 20,000/day

For a 25-person space with moderate activity:
- **Expected cost**: $0/month (stays in free tier)
- **Heavy usage**: ~$1-5/month

---

## 🆘 Getting Help

### Self-Help Resources
1. Check this SETUP.md guide
2. Review [README.md](README.md) for feature explanations
3. Check browser console (F12) for errors
4. Review Firebase Console for data/rule issues

### Common Error Messages

**"Missing or insufficient permissions"**
→ Check Firestore rules (Step 4.2)

**"Error loading space: null is not an object"**
→ Make sure you're signed in before accessing space

**"Firebase: Error (auth/invalid-email)"**
→ Check email format is correct

**"Firebase: Error (auth/weak-password)"**
→ Password must be at least 6 characters

---

## ✅ Setup Complete!

You now have a fully functional FriendSpace Alpha (v1.1)! 🎉

**Next steps:**
1. Customize your space (tables, permissions, theme)
2. Invite friends
3. Set up moderation rules
4. Enjoy chatting!

**Questions?** Review the [README.md](README.md) for usage tips and tricks.

---

**FriendSpaces Alpha v1.1 - Built with ❤️ by Mustafa Reza**
