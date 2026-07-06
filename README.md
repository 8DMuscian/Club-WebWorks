<p align="center">
  <img width="384" height="178" alt="bgCSILogo-a8e0ce5f" src="https://github.com/user-attachments/assets/492a0ab4-6c8a-40a3-8a36-7ef85e445bf0" />

</p>

<h1 align="center">PICT CSI Official Website</h1>

<p align="center">
  <strong>Computer Society of India — PICT Student Chapter</strong>
  <br/>
  Full-stack club management platform with student portal & admin dashboard
</p>

<p align="center">
  <img src="https://img.shields.io/badge/React-19-61DAFB?logo=react" />
  <img src="https://img.shields.io/badge/Vite-7-646CFF?logo=vite" />
  <img src="https://img.shields.io/badge/Tailwind-4-06B6D4?logo=tailwindcss" />
  <img src="https://img.shields.io/badge/Express-4.16-000000?logo=express" />
  <img src="https://img.shields.io/badge/MongoDB-Mongoose_8-47A248?logo=mongodb" />
  <img src="https://img.shields.io/badge/JWT-Auth-000000?logo=jsonwebtoken" />
  <img src="https://img.shields.io/badge/pdf--lib-PDF_Generation-FF0000" />
  <img src="https://img.shields.io/badge/Cloudinary-Image_Upload-3448C5?logo=cloudinary" />
  <img src="https://img.shields.io/badge/TanStack_Query-5-FF4154?logo=reactquery" />
  <img src="https://img.shields.io/badge/Zustand-5-443E38" />
  <img src="https://img.shields.io/badge/Zod-4-3E67B1" />
  <img src="https://img.shields.io/badge/GSAP-3-88CE02?logo=greensock" />
</p>

> **Note:** The actual project repository is private and cannot be shared publicly.
---

## 📋 Table of Contents

- [Overview](#-overview)
- [System Architecture](#-system-architecture)
- [Student Portal Features](#-student-portal-features)
- [Admin Dashboard Features](#-admin-dashboard-features)
- [Detailed Tech Stack](#-detailed-tech-stack)
- [Project Structure](#-project-structure)
- [How It Works — Key Flows](#-how-it-works--key-flows)
- [Screenshots](#-screenshots)

---

## 🚀 Overview

**PICT CSI Official Website** is a comprehensive club management platform built for the Computer Society of India — PICT Student Chapter. It consists of two integrated applications:

| Portal              | Users             | Purpose                                                                                        |
| ------------------- | ----------------- | ---------------------------------------------------------------------------------------------- |
| **Student Portal**  | Club members      | Register, view profile, track attendance, browse events & gallery, download attendance reports |
| **Admin Dashboard** | Club coordinators | Manage memberships, create events, record attendance via QR scanning, view analytics           |

The platform handles the complete lifecycle of club operations — from student registration and membership approval, to event creation, QR-based attendance tracking, automated absence marking, and PDF report generation.

---

## 🏗 System Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                    CLIENT SIDE (React 19 + Vite 7)              │
│                                                                 │
│  ┌──────────────────────────┐  ┌──────────────────────────────┐ │
│  │     Student Portal        │  │      Admin Dashboard         │ │
│  │  (localhost:5173)         │  │   (localhost:5174)           │ │
│  │                           │  │                              │ │
│  │  • TanStack Query v5      │  │  • Same stack as client     │ │
│  │  • Zustand v5 (auth)      │  │  • QR scanner integration   │ │
│  │  • pdf-lib (PDF gen)      │  │  • HTML5 QR code reader     │ │
│  │  • GSAP animations        │  │  • Lottie animations        │ │
│  │  • Tailwind CSS v4        │  │  • Confetti effects         │ │
│  └──────────┬───────────────┘  └──────────┬───────────────────┘ │
│             │                              │                      │
│      Axios / JWT                     Axios / JWT                 │
└─────────────┼──────────────────────────────┼────────────────────┘
              │                              │
┌─────────────┼──────────────────────────────┼────────────────────┐
│  ┌──────────▼───────────────┐  ┌──────────▼───────────────────┐ │
│  │   Student API (Express)   │  │    Admin API (Express)       │ │
│  │   (localhost:1010)        │  │   (localhost:5000)           │ │
│  │                           │  │                              │ │
│  │  • JWT auth (7d expiry)   │  │  • JWT auth (1h expiry)     │ │
│  │  • Attendance computation  │  │  • Admin-only endpoints     │ │
│  │  • Profile CRUD           │  │  • Event & attendance mgmt  │ │
│  │  • Gallery (Cloudinary)   │  │  • Cron job (every 5 min)   │ │
│  │  • Rate limiting          │  │  • Rate limiting             │ │
│  └──────────┬───────────────┘  └──────────┬───────────────────┘ │
│             │                              │                      │
│             └──────────┬───────────────────┘                      │
│                        │                                          │
│              ┌─────────▼──────────┐                                │
│              │     MongoDB        │                                │
│              │  (Shared DB)       │                                │
│              │                    │                                │
│              │  • Users           │                                │
│              │  • Events          │                                │
│              │  • Attendance      │                                │
│              └────────────────────┘                                │
│                                                                     │
│  ┌────────────────────────────────────────────────────────────────┐ │
│  │                    Cloudinary                                  │ │
│  │  • Event posters   • Profile photos   • Gallery images        │ │
│  │  • Screenshots     • Cover images (tagged)                    │ │
│  └────────────────────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────────────────────┘
```

**Key Architectural Decisions:**

- **Shared MongoDB database** — both APIs connect to the same database, ensuring consistency. The Admin API performs writes (event creation, attendance marking), while the Student API reads and serves data to members.
- **Separate auth domains** — student JWT (7 days) and admin JWT (1 hour) are stored in separate Zustand stores with different localStorage keys.
- **All models are duplicated** — both backends have identical Mongoose models (`User.js`, `Events.js`, `Attendance.js`) under their respective `models/` directories.

---

## 🎓 Student Portal Features

### 1. 🔐 Authentication

| Feature          | Detail                                                               |
| ---------------- | -------------------------------------------------------------------- |
| Registration     | Multi-step form: personal info → payment details → screenshot upload |
| Login            | Phone + password with Zod validation                                 |
| JWT Sessions     | 7-day token stored in localStorage                                   |
| Route Protection | `ProtectedRoute` component redirects unauthenticated users           |
| Rate Limiting    | 3 req/min for register, 5 req/min for login                          |

### 2. 👤 Profile Dashboard

Three-tab interface with:

**Overview Tab:**

- Personal info (name, year, division, branch, email)
- **CSI Member QR Code** — dynamically generated from enrollment number
- **Co-curricular Hours** progress ring (goal-based tracking)
- **Attendance by Category** (Workshop, Hackathon, Seminar, Volunteering) with percentage bars
- **Overall Attendance** percentage
- **Recent Attendance** (last 5 records)
- **Skills & Technologies** — add/remove skills with persistence

**Attendance Tab:**

- Full attendance history table (Event, Day, Date, Time, Status, Duration)
- **PDF Export** — generates semester-wise attendance PDFs using `pdf-lib`
  - Filters `Present` records only
  - Splits by semester (Sem 1 / Sem 2)
  - Fills a pre-designed AcroForm template (30 rows max)
  - Downloaded as `attendance-sem1.pdf` and/or `attendance-sem2.pdf`

**Settings Tab:**

- Profile photo upload (Cloudinary, old photo auto-deleted)
- Edit personal info (name, email, phone, year, division, branch, roll number)
- Change password (current + new password validation)
- Delete account

### 3. 🗓 Events & Gallery

**Events Page:**

- Lists all club events as card layouts
- Each card shows: poster image, event name, category badge, description

**Gallery Page:**

- Index view showing all gallery folders with cover images
- Detail view with masonry grid layout + full-screen image lightbox
- Images served from Cloudinary sub-folders, cached with `node-cache`

### 4. ℹ️ Public Pages

- **Home** — Hero section, About CSI, Why Join, Upcoming Events, Team Preview, Gallery, Testimonials, Contact Form
- **About** — Vision/Mission/Values, statistics counter animation
- **Team** — Mentors, Student Council, Web Dev Team, Design/Social Media Team

### 5. 📱 UI/UX Highlights

- **Mobile-responsive** — hamburger menu, slide-in navigation drawer
- **GSAP Animations** — fade-in effects, interactive 3D cube background
- **Lottie Animations** — engaging micro-interactions
- **Confetti** — celebratory effect on successful registration
- **Dark Theme** — consistent dark UI with blue accent accent

---

## 🛠 Admin Dashboard Features

### 1. 👥 Student Management

| Feature          | Detail                                    |
| ---------------- | ----------------------------------------- |
| Pending Requests | Lists students with `isMember: "pending"` |
| Approve          | Sets `isMember: "approved"`               |
| Reject           | Removes user from database                |
| Info Display     | Name, CSI ID, email per student card      |

### 2. 📅 Event Management

**Create Event Form:**

- Event name, description, category (workshop/hackathon/seminar/volunteering)
- **Semester selection** (1 or 2) — drives PDF template routing
- Poster upload with preview (Cloudinary)
- Dynamic sessions — add/remove multiple date + time fields
- Creates both `Events` document and corresponding `Attendance` document with empty attendee arrays

### 3. 📸 QR Attendance Marking

**3-Step Workflow:**

```
Step 1: Select Event → Step 2: Select Session → Step 3: Scan QR
  (card grid)        (shows date/time/count)    (live camera)
```

| Feature         | Detail                                          |
| --------------- | ----------------------------------------------- |
| QR Scanning     | Uses `html5-qrcode` library, loaded dynamically |
| Manual Entry    | Fallback prompt for QR read failures            |
| Duplicate Check | 409 error if already marked present             |
| Live Feedback   | Success notification with student name          |
| Session Panel   | Shows current present students sorted by name   |

### 4. 📊 Attendance Analytics

- Lists all approved students with attendance count and rate
- Color-coded rates: Green (≥80%), Yellow (≥60%), Red (<60%)
- Client-side search by name or CSI ID
- Event filter dropdown

### 5. ⏰ Automated Cron Jobs

Runs every **5 minutes** via `node-cron`:

- Scans all attendance documents for sessions past their start time
- Marks all `"Upcoming"` attendees as `"Absent"`
- Runs immediately on server startup

---

## 📦 Detailed Tech Stack

### Frontend (Student + Admin)

| Technology                   | Version | Purpose                 |
| ---------------------------- | ------- | ----------------------- |
| React                        | 19.1.1  | UI framework            |
| Vite                         | 7.1.2   | Build tool & dev server |
| Tailwind CSS                 | 4.1.12  | Utility-first styling   |
| TanStack React Query         | 5.101.1 | Server state & caching  |
| Zustand                      | 5.0.14  | Client state (auth)     |
| React Router                 | 7.8.2   | Client-side routing     |
| Axios                        | 1.11.0  | HTTP client             |
| Zod                          | 4.4.3   | Schema validation       |
| pdf-lib                      | 1.17.1  | PDF generation          |
| GSAP                         | 3.13.0  | Animations              |
| Lucide React                 | 0.546.0 | Icons                   |
| QRCode.react                 | 4.2.0   | QR code display         |
| html5-qrcode                 | 2.3.8   | QR scanning (admin)     |
| canvas-confetti              | 1.9.3   | Celebration effects     |
| @lottiefiles/dotlottie-react | 0.17.5  | Lottie animations       |

### Backend (Student + Admin)

| Technology         | Version | Purpose                |
| ------------------ | ------- | ---------------------- |
| Node.js            | —       | Runtime                |
| Express            | 4.16    | Web framework          |
| Mongoose           | 8.x     | MongoDB ODM            |
| jsonwebtoken       | —       | JWT auth               |
| bcryptjs           | —       | Password hashing       |
| Cloudinary SDK     | —       | Image storage & CDN    |
| multer             | —       | File upload handling   |
| helmet             | —       | Security headers       |
| express-rate-limit | —       | Rate limiting          |
| cors               | —       | Cross-origin requests  |
| Zod                | 4.4.3   | Server-side validation |
| node-cron          | —       | Scheduled tasks        |
| node-cache         | —       | In-memory caching      |
| QRCode (npm)       | 1.5.4   | QR code generation     |

### Database

| Technology | Purpose                      |
| ---------- | ---------------------------- |
| MongoDB    | Document database            |
| Mongoose   | Schema modeling & validation |

---

## 📁 Project Structure

```
csi-official-site/
│
├── Client/                          # Student-facing application
│   ├── CSI_NEW/                     # React + Vite frontend (port 5173)
│   │   └── src/
│   │       ├── pages/               # 10 page components
│   │       │   ├── Home.jsx         # Landing page
│   │       │   ├── Login.jsx        # Student login
│   │       │   ├── Register.jsx     # Multi-step registration
│   │       │   ├── Profile.jsx      # 3-tab dashboard
│   │       │   ├── Attendance.jsx   # Records table + PDF export
│   │       │   ├── Settings.jsx     # Account management
│   │       │   ├── Events.jsx       # Event listings
│   │       │   ├── GalleryPage.jsx  # Gallery index
│   │       │   ├── GalleryDetail.jsx# Single gallery
│   │       │   ├── About.jsx        # About CSI
│   │       │   └── TeamPage.jsx     # Team listing
│   │       ├── components/          # 14 shared components
│   │       │   ├── Navbar.jsx
│   │       │   ├── Footer.jsx
│   │       │   ├── ProtectedRoute.jsx
│   │       │   ├── Loader.jsx
│   │       │   ├── MemberOnly.jsx
│   │       │   ├── SkillsCard.jsx
│   │       │   ├── MobileMenu.jsx
│   │       │   ├── Hamburger.jsx
│   │       │   ├── Cubes.jsx        # 3D animated background
│   │       │   ├── EventInfo.jsx
│   │       │   ├── GalleryCard.jsx
│   │       │   ├── MasonaryGrid.jsx
│   │       │   └── ImageModal.jsx
│   │       ├── lib/
│   │       │   ├── api.js           # Axios instance + JWT interceptor
│   │       │   ├── query.js         # QueryClient config
│   │       │   └── generatePDF.js   # pdf-lib attendance PDF generator
│   │       ├── store/
│   │       │   └── auth.js          # Zustand auth store
│   │       ├── shared/
│   │       │   └── validation.js    # Zod schemas
│   │       └── public/
│   │           └── templates/       # PDF templates (sem1.pdf, sem2.pdf)
│   │
│   └── backend/                     # Express API (port 1010)
│       ├── models/
│       │   ├── User.js
│       │   ├── Events.js
│       │   └── Attendance.js
│       ├── routes/
│       │   ├── auth.js              # Login / Register
│       │   ├── profile.js           # Profile CRUD + stats
│       │   ├── records.js           # Attendance records
│       │   ├── settings.js          # Account settings
│       │   ├── events.js            # Public events
│       │   └── gallery.js           # Cloudinary gallery
│       ├── middleware/
│       │   └── auth.js              # JWT verification
│       └── config/
│           └── cloudinary.js        # Cloudinary SDK + cache
│
├── Admin/                           # Admin-facing application
│   ├── Frontend/                    # React + Vite frontend (port 5174)
│   │   └── src/
│   │       ├── pages/
│   │       │   ├── Login.jsx        # Admin login
│   │       │   └── adminDash.jsx    # 4-section dashboard
│   │       ├── components/
│   │       │   ├── Navbar.jsx
│   │       │   ├── Footer.jsx
│   │       │   ├── ProtectedRoute.jsx
│   │       │   └── Loader.jsx
│   │       ├── lib/
│   │       │   ├── api.js           # Axios + admin JWT interceptor
│   │       │   └── query.js         # QueryClient
│   │       ├── store/
│   │       │   └── auth.js          # Admin Zustand store
│   │       └── shared/
│   │           └── validation.js    # Zod schemas
│   │
│   └── backend/                     # Express API (port 5000)
│       ├── models/
│       │   ├── User.js
│       │   ├── Events.js
│       │   └── Attendance.js
│       ├── routes/
│       │   ├── adminRoutes.js       # All admin endpoints
│       │   └── cronJobs.js          # Attendance auto-update
│       ├── middleware/
│       │   └── verifyAdminToken.js  # Admin JWT verification
│       └── config/
│           └── cloudinary.js        # Cloudinary SDK
│
└── project.md                       # This file
```

---

## 🔄 How It Works — Key Flows

### 1. Authentication Flow

```
  Student                     Server                       Database
    │                          │                             │
    │  POST /auth/login        │                             │
    │  { phone, password }     │                             │
    │ ──────────────────────→  │                             │
    │                          │  Find user by phone         │
    │                          │ ──────────────────────────→ │
    │                          │ ←────────────────────────── │
    │                          │                             │
    │                          │  bcrypt.compare(password)   │
    │                          │  jwt.sign({ id }, 7d)       │
    │                          │                             │
    │  { token, user }         │                             │
    │ ←──────────────────────  │                             │
    │                          │                             │
    │  Store token + user      │                             │
    │  in Zustand + localStorage│                            │
```

### 2. Attendance Tracking Flow

```
  Admin creates event → MongoDB stores Event + Attendance docs
         │
         ▼
  Admin selects event + session → QR scanner opens
         │
         ▼
  Student scans QR code → POST /admin/record-attendance
         │                   └─ Find attendance doc
         │                   └─ Find session by index
         │                   └─ Push { userId, status: "Present" }
         │                   └─ 409 if duplicate
         ▼
  MongoDB updated → Student sees record in Profile
         │
         ▼
  Student clicks "Download PDF" → Present records filtered
                             └─ Split by semester (1/2)
                             └─ Load sem1.pdf or sem2.pdf
                             └─ Fill form fields via pdf-lib
                             └─ Flatten & download
```

### 3. Cron Job — Auto Absent Marking

```
  Every 5 minutes:
    For each Attendance document:
      For each session:
        If session.startTime < now:
          For each attendee with status "Upcoming":
            Set status → "Absent"
            Save to database
```

### 4. PDF Generation Flow

```
  generateAttendancePDF({ records, studentName, semester })
         │
  fetch(`/templates/sem${semester}.pdf`)
         │
  PDFDocument.load(arrayBuffer)
         │
  form.getTextField("name").setText(studentName)
         │
  For each Present record (max 30):
    Fill 5 fields per row (tab order):
      [ day, formattedDate, startTime, duration, event ]
         │
  form.flatten() → pdfDoc.save() → Uint8Array
         │
  Blob → URL.createObjectURL → <a download> → click
```

---

## 📸 Screenshots
<img width="1917" height="933" alt="Screenshot 2026-06-29 133026" src="https://github.com/user-attachments/assets/0b77210b-0b0f-484a-a97c-916231767459" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 123006" src="https://github.com/user-attachments/assets/09e3a509-62e6-4c49-a6d0-ab5b94c7696b" /><img width="1901" height="932" alt="Screenshot 2026-06-29 133643" src="https://github.com/user-attachments/assets/edcfca83-4ff1-41cd-aa10-cae895cc75fe" />
<img width="1913" height="933" alt="Screenshot 2026-06-29 133651" src="https://github.com/user-attachments/assets/9bd25345-96e4-4ebc-9b49-c3373aac6c9e" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 131224" src="https://github.com/user-attachments/assets/46029005-fa22-45c9-8d66-d2a665d22e53" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 131045" src="https://github.com/user-attachments/assets/86eefcd5-bdb2-49be-a469-18dab7147338" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122939" src="https://github.com/user-attachments/assets/617b3e32-8149-4456-a5f4-9799357a6931" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122820" src="https://github.com/user-attachments/assets/0c8e41a5-668b-494f-b96f-e7e00e27f2d6" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122626" src="https://github.com/user-attachments/assets/eaa96297-2151-4fb6-9d15-0c8bbe9f6fcf" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122608" src="https://github.com/user-attachments/assets/f1933ab9-e20c-42ba-afeb-d53cd6351dad" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122533" src="https://github.com/user-attachments/assets/184da036-1504-4804-8999-19642248d9ac" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122249" src="https://github.com/user-attachments/assets/552d6628-ae67-4c9c-84e4-37bde457abd3" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 122053" src="https://github.com/user-attachments/assets/4cb4d798-1d5e-47b8-a127-69a03f387b4a" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 120633" src="https://github.com/user-attachments/assets/f57e22a6-0965-48a9-8e30-4157a1cdb990" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 120607" src="https://github.com/user-attachments/assets/71a520e4-e525-4a56-8c20-98f267013cf3" />
<img width="1920" height="1080" alt="Screenshot 2026-06-29 120530" src="https://github.com/user-attachments/assets/5410ceb4-ee39-436d-8cdd-892f90f23eb5" />




<p align="center">
  Built with ❤️ for PICT CSI Student Chapter
  <br/>
  <sub>Computer Society of India — PICT Pune</sub>
</p>
