# Guiding Pillar - System Architecture
*Version 1.0 | Last Updated: 16 January 2026

## 1. Core Vision & Principles
Guiding Pillar is a community-powered platform to provide accurate, location-based mosque prayer times and Islamic connectivity. It is built on three core principles:
*   **Utility First:** The app must work reliably everywhere, even without mosque data (via calculated fallback).
*   **Trust Through Community:** Data accuracy is built via a verified network, not centralized control.
*   **Offline-First Design:** Core features must function without an internet connection.

## 2. High-Level System Overview
The system consists of a **mobile client app** (Flutter) and a **cloud backend & database** (Firebase). Mosque data flows from verified admins to the cloud database, which is then delivered to users' apps.

```mermaid
graph TD
    
    A[Mosque Admin] -->|Updates via| B[Admin Web Portal]
    B -->|Writes to| C[(Firestore DB)]
    D[User Mobile App] -->|Reads from| C
    D -->|Stores cache in| E[Local SQLite]
    D -->|Gets fallback from| F[Aladhan API]
    D -->|Displays map via| G[Maps Service]
3. Key Components & Technical Decisions
3.1. Data: The Hybrid Prayer Time Engine
Primary Source: Firestore Database holding mosque profiles and verified prayer timetables submitted by admins.

Fallback Source: Aladhan API provides calculated times when verified data is unavailable.

Client Logic: The app uses a decision function (see getPrayerTimes() in planning) to choose the best available source.

Offline Cache: Recent data is persisted locally using SQLite to ensure functionality without internet.

3.2. Users & Permissions: The Trust Model
The system defines three main user roles with distinct permissions:

Standard User: Can view prayer times, mosque info, and submit time suggestions as a "Guardian."

Mosque Admin (Verified): Can claim and manage a mosque profile, set official timetables, and view basic analytics. Verified via a "Trusted Trustee" (e.g., national body) or direct contact proof.

Super Admin/Trusted Trustee: (e.g., Muslim Association of Malawi) Can verify multiple mosque admins within their network.

3.3. Backend: Firebase Services
Authentication (Firebase Auth): Manages user logins for admins and guardians.

Database (Cloud Firestore): NoSQL database storing all structured data (Mosques, Users, Schedules).

Hosting (Firebase Hosting): Will host the Admin Web Portal.

3.4. Admin Web Portal
A responsive web application (built with Flutter Web or a simple framework) where mosque admins log in to update their mosque's data, including the crucial "Share to WhatsApp" image generator.

4. Data Models (Initial Schema)
The core data structures in Firestore will be:

Collection: mosques

javascript
{
  mosqueId: "auto-generated-id",
  name: "Masjid al-Noor",
  location: { geoPoint, address },
  contact: { phone, email },
  // TIMETABLES: The most critical data
  verifiedTimetable: { fajr: "04:30", dhuhr: "13:15", ... },
  lastUpdated: "timestamp",
  verifiedBy: "adminUserId | trusteeOrgName",
  // Status flags for the app's badge system
  dataSource: "verified | community | calculated",
  guardianSuggestions: [ {time: "...", userId: "...", votes: 3}, ... ]
}
Collection: users

javascript
{
  userId: "from-firebase-auth",
  email: "user@example.com",
  role: "user | guardian | admin | trustee",
  associatedMosqueId: "reference-to-mosque" // for admins/guardians
}
5. Implementation Roadmap (Phases)
Phase 0 - Foundation (Current): Repository, legal docs, architecture. ✅

Phase 1 - Core App: Flutter app with Hybrid Engine, offline cache, basic map. (Next)

Phase 2 - Admin System: Firebase setup, Auth, Admin Portal for time updates.

Phase 3 - Community Layer: Guardian system, WhatsApp image generator, featured partners.

6. Open Decisions & Considerations
Maps Provider: Final choice between Google Maps Platform and Mapbox (based on cost and regional coverage).

SMS Verification: Need for low-tech phone number verification in areas with low email usage.

Monetization Integration: Technical design for the "Featured Partner" badges and listings.

