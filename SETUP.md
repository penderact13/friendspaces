# FriendSpaces Setup Guide 🚀

**Don't worry - this is easier than it looks!** We'll get you up and running in about 10 minutes.

This guide is written for **non-nerds** - if you can use Google Docs, you can do this.

---

## 📋 What You'll Need

- A Google account (you probably have one)
- A GitHub account (free - we'll show you how to make one)
- 10 minutes of your time
- That's it!

---

## 📦 Step 1: Download FriendSpaces

1. Go to the [FriendSpaces GitHub Releases](https://github.com/YOUR_USERNAME/friendspaces/releases)
2. Click on the latest release
3. Download the `.zip` file
4. **Unzip it** to a folder on your computer
   - Right-click → "Extract All" (Windows)
   - Double-click (Mac)

You should now have a folder called `FriendSpaces` with these files:
- `index.html`
- `script.js`
- `styles.css`
- `README.md`
- `SETUP.md` (this file!)
- `LICENSE`

✅ **Step 1 done!**

---

## 🔥 Step 2: Set Up Firebase (The Backend)

**What is Firebase?** It's Google's free service that stores your messages and handles logins. Don't worry, it's free for small groups!

### 2.1 Create Firebase Project

1. Go to [console.firebase.google.com](https://console.firebase.google.com/)
2. Sign in with your Google account
3. Click **"Add project"** (the big button)
4. Name it whatever you want (e.g., "My FriendSpace")
5. Click **Continue**
6. **Turn off** Google Analytics (we don't need it)
7. Click **"Create project"**
8. Wait a few seconds... done!

### 2.2 Get Your App Connected

1. On your Firebase dashboard, click the **Web icon** (looks like `</>`)
2. Give it a nickname (e.g., "FriendSpaces Web")
3. **Don't check** the Firebase Hosting box (we're using GitHub Pages!)
4. Click **"Register app"**
5. You'll see a bunch of code - **COPY EVERYTHING** that looks like this:

```javascript
const firebaseConfig = {
  apiKey: "AIza....",
  authDomain: "your-project.firebaseapp.com",
  projectId: "your-project",
  storageBucket: "your-project.appspot.com",
  messagingSenderId: "123...",
  appId: "1:123..."
};
```

6. **Paste this somewhere safe** (Notepad, Notes app, etc.) - you'll need it soon!
7. Click **"Continue to console"**

✅ **Step 2 done!**

---

## 🔐 Step 3: Turn On Logins

1. In Firebase (left sidebar), click **"Authentication"**
2. Click **"Get started"**
3. Click **"Email/Password"** 
4. Toggle the **first switch** to ON (it turns blue/purple)
5. Leave the second one (Email link) OFF
6. Click **"Save"**

✅ **Step 3 done! Logins are ready.**

---

## 💾 Step 4: Set Up the Database

**What's this?** This is where your messages, members, and settings are stored.

### 4.1 Create Database

1. In Firebase (left sidebar), click **"Firestore Database"**
2. Click **"Create database"**
3. Select **"Start in production mode"** (we'll add security next)
4. Click **"Next"**
5. Choose your location - pick the one closest to where most people will use it:
   - **North America** → `us-central`
   - **Europe** → `europe-west`
   - **Asia** → `asia-southeast`
6. Click **"Enable"**
7. Wait a few seconds...

### 4.2 Add Security Rules

**Important:** This keeps your space private and secure!

1. Click the **"Rules"** tab (at the top)
2. **Delete everything** in the box
3. Copy and paste these rules (don't worry about understanding them - they just work!):

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

## 🌐 Step 6: Put It Online with GitHub Pages

**Why GitHub Pages?** It's free, easy, and doesn't require installing anything!

### 6.1 Create a GitHub Account (if you don't have one)

1. Go to [github.com](https://github.com)
2. Click **"Sign up"**
3. Follow the steps (username, email, password)
4. Verify your email
5. Done!

### 6.2 Create Your Repository

**What's a repository?** Think of it as a folder for your website on GitHub.

1. On GitHub, click the **+** button (top right)
2. Click **"New repository"**
3. Name it: `friendspaces` (lowercase, no spaces)
4. Make it **Public** (this is required for free GitHub Pages)
5. **Don't check** any of the boxes (no README, no .gitignore, no license)
6. Click **"Create repository"**

### 6.3 Upload Your Files

1. On your new repository page, click **"uploading an existing file"** (the blue link)
2. **Drag and drop** your 3 main files from your computer:
   - `index.html`
   - `script.js`
   - `styles.css`
3. (Optional) Also upload `README.md`, `SETUP.md`, and `LICENSE` if you want
4. Scroll down and click **"Commit changes"** (the green button)

### 6.4 Turn On GitHub Pages

1. Click **"Settings"** (top of your repository)
2. Click **"Pages"** (in the left sidebar)
3. Under "Source", select **"main"** branch
4. Click **"Save"**
5. Wait about 1 minute...
6. Refresh the page
7. You'll see: **"Your site is live at https://YOUR-USERNAME.github.io/friendspaces/"**

✅ **Your FriendSpace is online!**

Copy that URL - that's your FriendSpace website!

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

**FriendSpaces Alpha v1.1 - Built with ❤️ by Hydration**
