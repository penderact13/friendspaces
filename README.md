# FriendSpaces Alpha (v1.1) 💬

Welcome to FriendSpaces! I'm **Hydration**, and I created FriendSpaces to solve a problem: **Discord is too open and kids aren't allowed to use it**, while **Google Chat is too closed and loses all the customization and feel**. So I made FriendSpaces.

This is the **Alpha version**.

## 🚀 How It Works

1. **Download** the latest release
2. **Publish** it as a website via GitHub Pages
3. **Follow** the setup in your website
4. **Done!**

To actually do this, go to the [**Setup Guide**](SETUP.md) - it's dumbed down for non-nerds and you'll be up and running with GitHub Pages in no time.

---

## 🌟 What is FriendSpaces?

FriendSpaces is a real-time group chat platform for small groups (2-50 people) with powerful moderation tools. It's **private, customizable, and simple** - the perfect middle ground between Discord and Google Chat.

**Built for:**
- Friend groups who want privacy
- Families who need a safe space for kids
- Study groups who want organization
- Small teams who need control

---

## ✨ Key Features

### The Perfect Middle Ground
- **Not too open** (like Discord) - Private spaces with join codes
- **Not too closed** (like Google Chat) - Full customization and control
- **Just right** - Safe for kids, powerful for everyone

### 💬 Core Chat Features
- **Real-time messaging** powered by Firestore
- **Multiple tables (channels)** - Organize conversations by topic
- **Emoji picker** with 1000+ emojis across all categories
- **Image preview** - Paste any image URL and it auto-displays
- **Clickable links** - All URLs become clickable
- **Enter to send, Shift+Enter for new line**
- **Auto-expanding message box** - Supports multi-line messages

### 👥 Member Management

**Five Rank System:**
1. **Owner** - Full control, can delete space
2. **Co-owner** - Manage members, moderate content
3. **Super Friend** - Honorary rank with special access
4. **Friend** - Base member level
5. **Guest** - New members

**Member Status:**
- 🟢 **Online** - Currently active
- ⚪ **Offline** - Not active
- 🚫 **Banned** - Permanently blocked
- 👢 **Kicked** - Temporarily removed
- ⏸️ **Temp Banned** - Can view but not send

**Avatar Colors:**
- Each member gets a persistent color from 8-color palette
- Colors auto-assigned and stored in Firestore
- Consistent across all messages and UI

### 🛡️ Advanced Moderation

**Report System:**
- Hover over any message → Click 🚨 to report
- Reported user is temp-banned (can view, can't send)
- All their messages in that table are hidden
- Co-owners/Owners review in Moderator Panel

**Moderator Panel:**
- Badge shows number of unresolved reports
- Review reported messages and users
- Three actions per report:
  - 👍 **Approve** - Restore messages, lift ban
  - 🦶 **Kick** - Remove user with custom reason
  - 💥 **Ban** - Permanently block user

**Kick System:**
- User sees kick reason on next login
- Can rejoin with "Come Back" button
- Prevents abuse while allowing redemption

**Ban System:**
- User cannot sign in to the space
- Email/password/display name remain reserved
- Co-owners and Owners can lift bans

### 🎨 Customization

**Space Setup:**
- Custom name and emoji icon (from 100+ options)
- Login screen displays your space's emoji
- Background and text color themes
- Public or private (with 10-character join code)

**Table Permissions:**
- Control who can **view** each table
- Control who can **send messages** in each table
- Perfect for announcements, private channels, or read-only info

### 🔔 Notifications
- Browser notifications for new messages
- Works when app is minimized or in background
- Cross-browser support (Chrome, Safari, Edge)

### 📱 Mobile Ready
- Fully responsive design
- Optimized for iPhone and tablets
- Touch-friendly interface
- Horizontal table tabs on mobile
- Prevents iOS zoom on inputs

### ⚡ Performance & Efficiency
- **Optimized Firebase structure** - Each message is one document
- **Cookie-based authentication** - Stay signed in
- **Real-time updates** - See messages instantly
- **Online status tracking** - Know who's active
- **50 member limit** - Keeps spaces intimate and fast

---

## 🏗️ How It Works

### Space Structure
Each FriendSpace is completely independent with:
- **One owner** who created it
- **Multiple co-owners** (optional) for shared management
- **Multiple tables** for organizing conversations
- **Custom theme** that applies to all members
- **Member list** with roles and status

### Database Architecture
```
Space
├── Settings (name, icon, theme, privacy)
├── Members (users with roles and status)
├── Tables (channels)
│   └── Messages (chat history)
├── Reports (moderation queue)
└── Presence (online status)
```

### Security
- Comprehensive Firestore security rules
- Email/password authentication
- Role-based permissions
- 50 member hard limit
- Protected owner actions

---

## 🎯 Perfect For

- **Friend Groups** - Keep your conversations private
- **Study Groups** - Organize by subject with tables
- **Gaming Clans** - Coordinate with powerful moderation
- **Family Chat** - Safe, controlled environment
- **Small Teams** - Alternative to Slack/Discord for small groups
- **Community Projects** - Manage contributors with roles

---

## 🚀 Getting Started

### For Users (Joining a Space)
1. Visit the FriendSpace URL (given by space creator)
2. Create an account or sign in
3. Enter join code if it's a private space
4. Start chatting!

### For Creators (Building Your Own)
**See the complete setup guide:** [**SETUP.md**](SETUP.md)

We recommend **GitHub Pages** - it's:
- ✅ Free
- ✅ Easy (even for non-nerds!)
- ✅ No credit card needed
- ✅ Takes about 10 minutes

Quick overview:
1. Download FriendSpaces files
2. Set up Firebase (free tier)
3. Upload to GitHub
4. Enable GitHub Pages
5. Create your space!

**Full step-by-step guide:** [**SETUP.md**](SETUP.md)

---

## 📊 Technical Stack

- **Frontend**: Vanilla JavaScript (no frameworks)
- **Backend**: Firebase (Auth + Firestore)
- **Hosting**: Firebase Hosting or GitHub Pages
- **Styling**: Pure CSS with mobile-first design
- **Notifications**: Browser Notification API

---

## 🔒 Privacy & Data

### What's Stored
- **Account info**: Email, display name (encrypted by Firebase)
- **Messages**: Text only, auto-deleted after 30 days*
- **Presence**: Online/offline status
- **Metadata**: Timestamps, roles, permissions

*30-day deletion requires Cloud Function setup (optional)

### What's NOT Stored
- **No tracking** or analytics
- **No third-party cookies**
- **No file uploads** (image URLs only)
- **No message editing history**

### Data Control
- **Owners can delete spaces** entirely
- **Users can delete accounts** anytime
- **All data stays in your Firebase** project
- **You control security rules**

---

## ⚠️ Known Limitations

### By Design
- No message editing (prevents abuse)
- No file uploads (keep it simple, use image URLs)
- No DMs (use private tables instead)
- No message search (privacy-focused)
- 50 member maximum (keeps spaces intimate)

### Technical
- Background notifications require app to be running
- Presence detection has 60-second accuracy
- Image previews require direct image URLs
- Mobile landscape mode has limited space

---

## 🔮 Why "Alpha"?

FriendSpaces Alpha is the **first complete release** of the platform. All core features are implemented and working:
- ✅ Real-time messaging with image previews
- ✅ Full member management (5 ranks, status tracking)
- ✅ Complete moderation system (report, kick, ban, temp-ban)
- ✅ Custom themes and space branding
- ✅ Table permissions and organization
- ✅ Mobile responsive design
- ✅ Persistent avatar colors
- ✅ 1000+ emoji selection

The "Alpha" designation indicates this is version 1.0 - stable, feature-complete, and ready for real use!

---

## 💡 Tips for Best Experience

1. **Use descriptive table names** - "Announcements", "General", "Off-topic"
2. **Promote trusted members to Co-owner** - Share moderation duties
3. **Set table permissions early** - Prevent accidental posts in wrong channels
4. **Use Super Friend rank** - Reward active community members
5. **Check Moderator panel regularly** - Stay on top of reports
6. **Keep spaces under 25 people** - Optimal performance and intimacy
7. **Use private spaces for sensitive groups** - Enable join codes
8. **Share image links, not files** - Faster and simpler

---

## 🎨 Customization Ideas

### Theme Combinations
- **Dark Ocean**: `#1a1a2e` background, `#eee` text
- **Forest**: `#2d4a2b` background, `#fff` text  
- **Sunset**: `#ff6b6b` background, `#fff` text
- **Professional**: `#f5f5f5` background, `#333` text

### Table Organization
- Main (general chat)
- Announcements (view only for most)
- Off-topic (casual conversations)
- Resources (links and info)
- Moderation (co-owners only)

### Role Strategy
- **Owner**: You (creator)
- **Co-owner**: 1-2 trusted moderators
- **Super Friend**: Active, helpful members (5-10)
- **Friend**: Regular members (auto-promote from Guest)
- **Guest**: New members (probation period)

---

## 📜 Version History

### Alpha v1.1 - December 2024
**Quality of life improvements!**

**New Features:**
- ✨ **Delete tables**: Owners/Co-owners can now delete tables (except Main) with ✕ button
- ⏱️ **Anti-spam protection**: 1-second cooldown between messages
- 🎨 **Custom emoji input**: Text box for any emoji/character from keyboard, with suggestions below
- 💡 **Color reminder**: Setup now reminds users to choose light background colors (text is black)

**Improvements:**
- Better table management UI
- Confirmation dialog before deleting tables
- Auto-switches to Main if current table is deleted
- Cleaner emoji selection interface

### Alpha v1.0 - December 2024
**First complete release!**

**Core Features:**
- Real-time messaging with Firestore
- 5-tier member ranking system (Owner, Co-owner, Super Friend, Friend, Guest)
- Full moderation suite (report/kick/ban/temp-ban with moderator panel)
- Multiple tables with granular permissions
- Custom themes (background & text colors applied to all chat elements)
- Public/private spaces with 10-character join codes
- Image URL auto-preview (supports .jpg, .png, .gif, .webp, .bmp)
- 1000+ emoji picker for chat
- 100+ emoji selection for space setup
- Persistent avatar colors (8-color palette, auto-assigned & stored)
- Login screen shows space's custom emoji
- Mobile responsive design (iPhone optimized)
- Online/offline status tracking (60-second accuracy)
- Browser notifications
- Shift+Enter for multi-line messages
- Auto-expanding message textarea
- Account deletion
- Space deletion (owner only)
- 50 member limit per space
- Cookie-based authentication persistence

**Technical:**
- Pure vanilla JavaScript (no frameworks)
- Firebase Authentication & Firestore
- Comprehensive security rules
- Optimized database structure
- Real-time listeners
- Cross-browser compatible

---

## 👏 Credits

**Created by Hydration**

Built with ❤️ to solve the Discord vs Google Chat problem - giving you the best of both worlds.

---

## 📄 License

FriendSpaces is released under the **MIT License**.

This means you can:
- ✅ Use it for free (personal or commercial)
- ✅ Modify and customize it
- ✅ Distribute it
- ✅ Include it in other projects

Just keep the copyright notice. See [LICENSE](LICENSE) for full details.

---

**FriendSpaces Alpha - Version 1.1 - Chat for friends, built by friends** 💬
