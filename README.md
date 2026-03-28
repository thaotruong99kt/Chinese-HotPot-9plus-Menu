# 🍲 Paper-to-QR Ordering System

> Transforming manual paper ordering into a real-time QR code ordering experience for a Chinese hotpot restaurant in Warsaw — at zero infrastructure cost.

---

## 📌 Background

This project is built around a real operational problem at a small Chinese hotpot restaurant. The existing paper-based ordering workflow was slow, error-prone, and created a language barrier between staff and customers. The goal was to modernize the ordering process without introducing recurring software costs — a key constraint for SMEs.

| | Details |
|---|---|
| **Company size** | SME, under 30 employees |
| **Weekday footfall** | ~20 customers/day |
| **Weekend footfall** | ~50 customers/day |
| **Front-of-house staff** | 2 |
| **Menu types** | Buffet, Standard |
| **Languages** | Chinese, English, Polish |

---

## 🔴 Pain Points

**Old Ordering Process**

<img width="1615" height="724" alt="image" src="https://github.com/user-attachments/assets/ab3dc2e3-bd8c-4771-b7c8-537eca1ed956" />

### 1. Operational bottleneck
With only 2 front-of-house staff, taking paper orders manually during peak hours left no capacity to attend to other guests. The process was a constant bottleneck on busy weekend shifts.

### 2. Language barrier
The restaurant serves a multilingual customer base (Chinese, Polish, English). Each paper order required staff to verbally explain:
- Soup base options
- Spicy level preference
- Portion sizes
- Pricing rules

This explanation had to be repeated for nearly every table, consuming disproportionate staff time.

### 3. Cost sensitivity
As a small business, the restaurant could not justify subscription fees for commercial POS or ordering platforms. Existing habits and workflows were deeply embedded, making adoption of expensive tools unlikely.

---

## ✅ Solution

**New Ordering Process**

<img width="1123" height="864" alt="image" src="https://github.com/user-attachments/assets/44779349-f6b6-4cf1-87e5-2aa05eae4016" />

A lightweight, serverless QR ordering system built entirely on free-tier services.

### System Architecture

<img width="1778" height="1550" alt="system architect" src="https://github.com/user-attachments/assets/37cc9d57-2862-423f-a524-03a3bfe5e575" />

### How It Works

1. Each table has a unique QR code linking to `/?table=TableN`
2. Customer scans → selects menu type (Buffet or Standard)
3. Built-in rules enforce required choices before submission:
   - Soup base selection (including dual-soup logic)
   - Spicy level gating (only shown when spicy soup is selected)
   - Portion limits (e.g. max 5 shrimp per person)
4. Order is written to Firebase Realtime Database
5. Dashboard auto-updates in real-time → staff reviews, edits if needed, then prints to thermal printer
6. Bill is printed in **80mm thermal format** with item code, Chinese name, Polish/English name, and notes inline

### Key Design Decisions

| Decision | Rationale |
|---|---|
| GitHub Pages | Free static hosting, zero DevOps |
| Firebase Realtime DB | Free tier sufficient for SME volume; instant sync |
| No backend server | Eliminates hosting cost and maintenance overhead |
| Shared cart per table | Multiple customers at same table see one live cart |
| Separate cart per menu type | Buffet and Standard orders never mix |
| Browser print → popup window | Reliable thermal print without A4 bleed |

---

## 🌐 Multilingual Support

All customer-facing UI and printed bills are displayed in **3 languages**:

- 🇨🇳 Chinese
- 🇵🇱 Polish
- 🇬🇧 English

---

## 🖥️ UI Overview

### Customer — Menu App (Mobile)

> Accessible via QR code scan. No app install required — runs entirely in the mobile browser.

<img width="1699" height="669" alt="image" src="https://github.com/user-attachments/assets/0973cc0e-cb51-4c7a-9445-74c5c6f64d0e" />

- Menu grouped by category with item code, Chinese name, and Polish/English description on one line
- Buffet and Standard menu types selectable from landing screen
- Required choices (soup base, spicy level) enforced before cart submission
- Shared real-time cart — all guests at the same table see the same basket

### Staff — Dashboard App (Desktop)

> Opened in Chrome on the counter PC. No installation required.

<img width="904" height="709" alt="image" src="https://github.com/user-attachments/assets/55291668-9df6-47e2-a714-757a9b3e93e8" />


- New orders appear instantly with sound notification and on-screen alert
- Staff can edit quantity, remove items, or add notes before printing
- One-click print to 80mm thermal printer
- Orders labeled **A** (Buffet) or **B** (Standard) for quick identification
- Auto-clears all orders and sessions daily at 22:00

---

## 📊 Results
| Metric | Before | After |
|---|---|---|
| Time to take one table's order | ~5–8 min | ~1 min (self-serve) |
| Staff verbal explanation per table | Required every time | Eliminated |
| Front-of-house workload | High during peak hours | Reduced by ~80% |

---

## 🛠️ Tech Stack

| Layer | Technology |
|---|---|
| Frontend hosting | GitHub Pages |
| Database & realtime sync | Firebase Realtime Database |
| UI | Vanilla HTML / CSS / JavaScript (no framework) |
| QR generation | qrcode.js |

---

## 📁 Project Structure

```
/
├── index.html          # Customer-facing menu app (QR target)
├── dashboard.html      # Staff order management screen
└── qr-tables.html      # QR code print sheet for all tables
```

---

## 🚀 Setup

### 1. Firebase
- Create a Firebase project and enable **Realtime Database**
- Import `menu_buffet.json` into `menus/buffet`
- Import `menu_standard.json` into `menus/standard/standard`
- Set database rules to restrict write access by domain

### 2. GitHub Pages
- Fork or upload files to a public GitHub repo
- Enable GitHub Pages from the `main` branch root
- QR links follow the format:
  ```
  https://<username>.github.io/<repo>?table=Table1
  ```

### 3. Thermal Printer
- Connect printer via Wi-Fi or LAN
- Open `dashboard.html` in Chrome on the counter PC or mobile
- Click **Print** on any order card — browser prints directly to the thermal printer in 80mm format

---

## 🔒 Security Notes

Firebase API keys in web apps are **intended to be public** — they identify the project, not authenticate it. Access is controlled via:
- **HTTP referrer restrictions** on the API key (Google Cloud Console)
- **Firebase Security Rules** limiting read/write by path
