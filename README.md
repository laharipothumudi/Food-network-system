# Food Network

> **Share More. Waste Less. Feed More.**
> A real-time web platform connecting food donors with receivers to reduce food wastage and feed those in need.

---

##  Table of Contents

- [About the Project](#about-the-project)
- [Features](#features)
- [Tech Stack](#tech-stack)
- [System Architecture](#system-architecture)
- [Database Structure](#database-structure)
- [Getting Started](#getting-started)
- [Firebase Setup](#firebase-setup)
- [Project Structure](#project-structure)
- [How It Works](#how-it-works)
- [Security Rules](#security-rules)
- [Screenshots](#screenshots)
- [Contributing](#contributing)
- [License](#license)

---

## About the Project

**Food Network** is a web-based platform built to bridge the gap between food surplus and food need. Every day, restaurants, hotels, and individuals throw away large quantities of perfectly good food while thousands of people go hungry nearby.

Food Network solves this by providing a simple, real-time digital marketplace where:
- **Donors** (restaurants, hotels, individuals) can post surplus food listings in under a minute
- **Receivers** (NGOs, shelters, community kitchens, individuals) can browse and request available food instantly
- All data syncs in **real time** via Firebase — no page refresh needed

### Problem It Solves

| Problem | Solution |
|--------|----------|
| Food surplus goes to waste | Donors post listings in real time |
| Receivers don't know where food is available | Live feed with location & pickup time |
| No trust between strangers | Donor must confirm each request |
| Delayed information | Firebase real-time sync across all devices |

---

## Features

          **User Authentication** — Register and login with email & password via Firebase Auth
             **Role-based Access** — Separate dashboards for donors and receivers
            **Post Food Listings** — Donors add food name, quantity, location, and pickup time
           **Real-time Feed** — Receivers see new listings instantly without refreshing
           **Request System** — Receivers send pickup requests; donors confirm or reject
          **Live Notifications** — Instant status updates for both parties
            **Firebase Backend** — Realtime Database stores and syncs all data
           **Responsive Design** — Works on desktop and mobile browsers

---

##  Tech Stack

| Layer | Technology |
|-------|-----------|
| Frontend | HTML5, CSS3, JavaScript (ES6+) |
| Authentication | Firebase Authentication |
| Database | Firebase Realtime Database |
| Hosting | Firebase Hosting |
| Package Manager | npm |
| Firebase SDK | Firebase v9 (Modular) |

---

## System Architecture

```
┌─────────────────────────────────────────────────────┐
│                   Web Browser                       │
│  ┌──────────────┐          ┌──────────────────────┐ │
│  │  Donor UI    │          │     Receiver UI      │ │
│  │  - Post food │          │  - Browse feed       │ │
│  │  - Confirm   │          │  - Send request      │ │
│  └──────┬───────┘          └────────────┬─────────┘ │
└─────────┼──────────────────────────────┼────────────┘
          │                              │
          ▼                              ▼
┌─────────────────────────────────────────────────────┐
│              Firebase SDK (v9 Modular)               │
│   getAuth()  |  getDatabase()  |  onValue()         │
└─────────────────────────┬───────────────────────────┘
                          │
          ┌───────────────┴───────────────┐
          ▼                               ▼
┌──────────────────┐           ┌─────────────────────┐
│  Firebase Auth   │           │  Firebase Realtime  │
│  - Register      │           │  Database           │
│  - Login         │           │  - users/           │
│  - Session mgmt  │           │  - food_posts/      │
└──────────────────┘           │  - requests/        │
                               └─────────────────────┘
```

---

##  Database Structure

```json
{
  "users": {
    "uid_abc123": {
      "name": "Saravana Bhavan",
      "email": "sb@example.com",
      "role": "donor",
      "city": "Chennai",
      "createdAt": 1700000000000
    }
  },

  "food_posts": {
    "postId_xyz": {
      "donorId": "uid_abc123",
      "foodName": "Vegetable Biriyani",
      "quantity": "60 portions",
      "location": "T. Nagar, Chennai",
      "pickupTime": "Before 6:00 PM",
      "status": "open",
      "createdAt": 1700000000000
    }
  },

  "requests": {
    "reqId_def": {
      "postId": "postId_xyz",
      "receiverId": "uid_receiver456",
      "portionsNeeded": 30,
      "message": "We serve 30 people daily at our shelter",
      "status": "pending",
      "createdAt": 1700000001000
    }
  }
}
```

### Status States

**food_posts.status:**
- `open` — Available for requests
- `claimed` — Confirmed by donor, no longer available

**requests.status:**
- `pending` — Waiting for donor response
- `confirmed` — Donor approved, pickup arranged
- `rejected` — Donor declined

---

## Getting Started

### Prerequisites

- [Node.js](https://nodejs.org/) v16 or higher
- A [Firebase account](https://firebase.google.com/) (free tier works)
- A modern web browser

### Installation

```bash
# 1. Clone the repository
git clone https://github.com/your-username/food-network.git

# 2. Navigate into the project
cd food-network

# 3. Install dependencies (if using npm)
npm install

# 4. Add your Firebase config (see Firebase Setup below)

# 5. Open in browser
open index.html
# or serve with a local server:
npx serve .
```

---

##  Firebase Setup

### Step 1 — Create a Firebase Project

1. Go to [console.firebase.google.com](https://console.firebase.google.com)
2. Click **Add project** → name it `food-network`
3. Disable Google Analytics (optional) → **Create project**

### Step 2 — Enable Authentication

1. In the Firebase Console, go to **Build → Authentication**
2. Click **Get started**
3. Under **Sign-in method**, enable **Email/Password**

### Step 3 — Create a Realtime Database

1. Go to **Build → Realtime Database**
2. Click **Create Database**
3. Choose your region (e.g., `asia-south1` for India)
4. Start in **Test mode** (we'll add rules later)

### Step 4 — Get Your Config Keys

1. Go to **Project Settings** (gear icon) → **General**
2. Under **Your apps**, click **Web** (`</>`)
3. Register the app and copy the config object

### Step 5 — Add Config to Project

Create a file `firebase-config.js` in your project root:

```javascript
// firebase-config.js
import { initializeApp } from "firebase/app";
import { getAuth } from "firebase/auth";
import { getDatabase } from "firebase/database";

const firebaseConfig = {
  apiKey: "YOUR_API_KEY",
  authDomain: "your-app.firebaseapp.com",
  databaseURL: "https://your-app-default-rtdb.firebaseio.com",
  projectId: "your-app",
  storageBucket: "your-app.appspot.com",
  messagingSenderId: "YOUR_SENDER_ID",
  appId: "YOUR_APP_ID"
};

const app = initializeApp(firebaseConfig);
export const auth = getAuth(app);
export const db = getDatabase(app);
```

> **Never commit API keys to GitHub.** Use environment variables or a `.env` file and add `firebase-config.js` to `.gitignore`.

---

## Project Structure

```
food-network/
├── index.html              # Landing page & food feed
├── register.html           # User registration page
├── login.html              # Login page
├── donor-dashboard.html    # Donor: post food, view requests
├── receiver-dashboard.html # Receiver: browse feed, send requests
│
├── css/
│   └── styles.css          # Global styles
│
├── js/
│   ├── firebase-config.js  # Firebase initialization
│   ├── auth.js             # Register, login, logout
│   ├── foodPosts.js        # Post, update, delete listings
│   ├── requests.js         # Send and confirm requests
│   └── feed.js             # Real-time listener for food feed
│
└── README.md
```

---

##  How It Works

### 1. Register & Login

```javascript
// Register a new user
import { createUserWithEmailAndPassword } from "firebase/auth";
import { ref, set } from "firebase/database";

async function registerUser(name, email, password, role, city) {
  const { user } = await createUserWithEmailAndPassword(auth, email, password);
  await set(ref(db, `users/${user.uid}`), { name, email, role, city });
}
```

### 2. Post a Food Listing (Donor)

```javascript
import { ref, push, set } from "firebase/database";

async function postFood(foodName, quantity, location, pickupTime) {
  const donorId = auth.currentUser.uid;
  const newRef = push(ref(db, "food_posts"));
  await set(newRef, {
    donorId, foodName, quantity, location, pickupTime,
    status: "open",
    createdAt: Date.now()
  });
}
```

### 3. Real-time Feed Listener (Receiver)

```javascript
import { ref, onValue } from "firebase/database";

function listenToFoodPosts(callback) {
  return onValue(ref(db, "food_posts"), (snapshot) => {
    const data = snapshot.val() || {};
    const posts = Object.entries(data)
      .map(([id, post]) => ({ id, ...post }))
      .filter(p => p.status === "open")
      .sort((a, b) => b.createdAt - a.createdAt);
    callback(posts);
  });
}
```

### 4. Send Request (Receiver)

```javascript
async function sendRequest(postId, portionsNeeded, message) {
  const receiverId = auth.currentUser.uid;
  const newRef = push(ref(db, "requests"));
  await set(newRef, {
    postId, receiverId, portionsNeeded, message,
    status: "pending",
    createdAt: Date.now()
  });
}
```

### 5. Confirm Request (Donor)

```javascript
import { ref, update } from "firebase/database";

async function confirmRequest(requestId, postId) {
  await update(ref(db), {
    [`requests/${requestId}/status`]: "confirmed",
    [`food_posts/${postId}/status`]: "claimed"
  });
}
```

---

##  Security Rules

Paste these into **Firebase Console → Realtime Database → Rules**:

```json
{
  "rules": {
    "users": {
      "$uid": {
        ".read": "$uid === auth.uid",
        ".write": "$uid === auth.uid"
      }
    },
    "food_posts": {
      ".read": "auth != null",
      "$postId": {
        ".write": "auth != null && (!data.exists() || data.child('donorId').val() === auth.uid)"
      }
    },
    "requests": {
      ".read": "auth != null",
      "$requestId": {
        ".write": "auth != null"
      }
    }
  }
}
```

**What these rules do:**
- ✅ Any logged-in user can read food posts and requests
- ✅ Only the original donor can edit or delete their own post
- ✅ Only the owner can read their own profile
- ❌ Unauthenticated users cannot read or write anything

---


##  Contributing

Contributions are welcome! To contribute:

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/your-feature`
3. Commit your changes: `git commit -m "Add your feature"`
4. Push to the branch: `git push origin feature/your-feature`
5. Open a Pull Request

### Ideas for Contribution
- Add push notifications when a request is confirmed
- Add a map view showing nearby food listings
- Add image upload for food posts
- Add chat between donor and receiver
- Add email notifications via Firebase Functions

---

##  License

This project is licensed under the [MIT License](LICENSE).

---

##  Acknowledgements

- [Firebase](https://firebase.google.com/) for the real-time backend
- Built with a mission to reduce food waste and fight hunger

---

<p align="center">
  Made with ❤️ to reduce food waste · Food Network © 2024
</p>
