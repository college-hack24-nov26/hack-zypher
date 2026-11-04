# NagarNiti (नगरनीति) - Satara Civic Platform

A city-focused civic issue reporting and resolution tracking web application built specifically for **Satara city**, adhering strictly to the *Satara Civic Platform Surface-Level Product Specification (v2.0)*.

---

## Architecture & Technology Stack

- **Frontend**: React 18, Vite, Tailwind CSS, React Router v6, Lucide Icons, Leaflet, Leaflet MarkerCluster.
- **Backend**: Node.js, Express, Mongoose, JWT (JSON Web Tokens), bcryptjs, Helmet, Morgan.
- **Database**: MongoDB (Atlas) with spatial `2dsphere` indexes.
- **Geographic Boundary Engine**: Turf.js (`@turf/boolean-point-in-polygon`) loaded dynamically from `server/data/satara-boundary.geojson`.
- **Media Uploads**: Cloudinary streaming integration (JPG/PNG, up to 5 photos per report, 5 MB each).
- **Authentication & Security**: Multi-method citizen registration (Email, Phone, Google stub) with OTP verification (`DEV_MODE` console logging), separate authority portal, and strict RBAC barriers.

---

## Key Features & Product Rules

1. **City-Only Service Area**:
   - Every report pin is verified server-side against the Satara municipal boundary polygon (`OSM ID 10354583`). Pins in Koregaon, Wai, Karad, or outside Satara are rejected with HTTP 400.
2. **Citizen Feed & Support**:
   - Social-style mobile-first feed with all statuses visible (`New`, `Active`, `Resolved`, `Rejected`, `Duplicate`).
   - One-vote-per-citizen Support toggle. Authors cannot support their own reports.
   - Fixed civic categories: Roads and potholes, Streetlights, Garbage and sanitation, Drainage and sewage, Water supply, Footpaths and public spaces, Traffic infrastructure, Other civic issue.
3. **Interactive Maps**:
   - **Per-Report Map**: Displays exact pinned location.
   - **City-Wide Map (`/map`)**: Clustered pins showing aggregate counts, status-coded pins, Satara municipal boundary overlay, and compact report previews that open full details.
4. **Authority Dashboard (`/authority/dashboard`)**:
   - Dedicated restricted console with live queue tabs: `Overview`, `New / Unreviewed`, `Active`, and `Completed / History`.
   - Compact stacked cards with square photo preview on the right.
   - Anonymous "Start Review" action with `Reviewing` indicator.
   - Flexible status updates, internal rejection reason logging (hidden from citizens), and duplicate linking.
   - Priority engine combining category severity, community support, and waiting time with manual override.
5. **Account Deletion Cascade**:
   - Deleting a citizen account removes their profile, published reports, support votes, and **cascades to delete any duplicate reports that were linked to their reports** everywhere.

---

## Quickstart & Setup Guide

### 1. Prerequisites
- Node.js (v18+ or v20+)
- MongoDB Atlas or local MongoDB instance

### 2. Configure Environment
1. Copy the environment file template:
   ```bash
   cp server/.env.example server/.env
   ```
2. Configure credentials in `server/.env`:
   - `MONGODB_URI`: MongoDB Atlas connection string.
   - `JWT_SECRET`: Random secure string.
   - `DEV_MODE=true`: Logs generated OTPs directly to terminal.
   - `CLOUDINARY_CLOUD_NAME`, `CLOUDINARY_API_KEY`, `CLOUDINARY_API_SECRET`: Cloudinary API keys.

### 3. Install Dependencies
```bash
npm install
cd server && npm install
cd ../client && npm install
cd ..
```

### 4. Seed the Database
Populate the database with the official authority account, citizen accounts, and 33 realistic Satara civic reports:
```bash
cd server
node seedData.js
cd ..
```

### 5. Run the Application
Run both servers from the project root:
```bash
# Terminal 1 - Backend Server (Port 5000)
npm run server:dev

# Terminal 2 - Frontend Client (Port 5173)
npm run client:dev
```

- **Citizen Web App**: [http://localhost:5173](http://localhost:5173)
- **Authority Portal**: [http://localhost:5173/authority/login](http://localhost:5173/authority/login)
- **API Health Check**: [http://localhost:5000/api/health](http://localhost:5000/api/health)

---

## Demo Credentials

### Authority Official Accounts (Demo Only)
- **Authority 1 (Roads & Infrastructure)**:
  - Username: `authority1`
  - Password: `Authority1#Satara2026`
  - Role: `authority`
- **Authority 2 (Sanitation & Water)**:
  - Username: `authority2`
  - Password: `Authority2#Satara2026`
  - Role: `authority`
- **Authority 3 (Electrical & Safety)**:
  - Username: `authority3`
  - Password: `Authority3#Satara2026`
  - Role: `authority`

*(Restricted access at `/authority/login`. No public self-registration).*

---

## Running Automated Verification
Execute the test suite to verify all product rules, boundary enforcement, and deletion cascades:
```bash
cd server
node testFinalSuite.js
```
