This is the corrected and refined version of your Guiding Pillar System Architecture document. I have fixed the Mermaid syntax, cleaned up the data models, and organized the roadmap into actionable steps.

Guiding Pillar - System Architecture
Tagline: Locate. Connect. Belong.

Version: 1.0 | Updated: 16 January 2026

1. Core Vision & Principles
Guiding Pillar is a community-powered platform providing accurate, location-based mosque prayer times and Islamic connectivity.

Utility First: Reliable functionality with or without verified mosque data.

Trust Through Community: Accuracy via a verified network and "Guardian" contributions.

Offline-First Design: Core prayer times must function without an active data connection.

2. High-Level System Overview
The system utilizes a Flutter mobile client and a Firebase backend. Data flows from verified admins to the cloud, then synchronizes to the end-user's local cache.

Code snippet

graph TD
    A[Mosque Admin] -->|Updates via| B[Admin Web Portal]
    B -->|Writes to| C[(Firestore DB)]
    D[User Mobile App] -->|Reads from| C
    D -->|Stores cache in| E[Local SQLite]
    D -->|Gets fallback from| F[Aladhan API]
    D -->|Displays map via| G[Maps Service]
3. Key Components & Technical Decisions
3.1. The Hybrid Prayer Time Engine
Primary: Firestore DB (Verified mosque-specific times).

Secondary: Community "Guardian" suggestions (unverified but recent).

Fallback: Aladhan API (Mathematical calculations based on GPS coordinates).

Local Storage: SQLite (Persists the last known good data for offline use).

3.2. User Roles & Permissions
Standard User: View times, info, and submit "Guardian" time suggestions.

Mosque Admin (Verified): Manage mosque profile, set official timetables, and generate "Share to WhatsApp" images.

Trusted Trustee: (e.g., Muslim Association of Malawi) High-level access to verify mosque admins within their jurisdiction.

4. Data Models (Firestore Schema)
Collection: mosques
JSON

{
  "mosqueId": "id_123",
  "name": "Masjid al-Noor",
  "location": {
    "lat": -14.28,
    "lng": 35.26,
    "address": "Namwera, Mangochi"
  },
  "verifiedTimetable": {
    "fajr": "04:30",
    "dhuhr": "13:15",
    "asr": "16:45",
    "maghrib": "18:20",
    "isha": "19:45"
  },
  "lastUpdated": "2026-01-16T09:00:00Z",
  "dataSource": "verified", 
  "guardianSuggestions": [
    { "time": "04:35", "userId": "user_456", "votes": 3 }
  ]
}
Collection: users
JSON

{
  "userId": "auth_id_99",
  "email": "shadreckyasidu16@gmail.com",
  "role": "admin",
  "associatedMosqueId": "id_123"
}
5. Implementation Roadmap
Step 1: Environment Setup
Initialize Flutter project with flutter_bloc or provider for state management.

Configure Firebase Project (Auth, Firestore, Hosting).

Install dependencies: sqflite, Maps_flutter, dio.

Step 2: The Offline-First Engine
Create a Repository Pattern that checks: SQLite -> Firestore -> API.

Implement background sync to update SQLite whenever the user has internet.

Step 3: Admin Portal & Branding
Build the Flutter Web dashboard.

Implement the "Share to WhatsApp" feature using the screenshot and share_plus packages to generate dynamic timetable image