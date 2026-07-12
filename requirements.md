# Requirements Specification: Personal Clipboard & Note Manager

## 1. Overview
A lightweight, fast, offline desktop utility designed as a persistent personal clipboard. It runs locally as a server-rendered web application (`localhost:8080`) targeting Windows and macOS workflows. The app prioritizes speed, functional UI, and strict keyboard/one-click utility over aesthetic styling.

## 2. Core Constraints & Tech Stack
*   **Deployment:** Local desktop environment running on `localhost:8080`.
*   **Backend:** Java with Spring Boot (Latest stable), Spring Data JPA.
*   **Database:** H2 Database Engine, configured for local file persistence (`~/clipboard_db/`).
*   **Frontend Architecture:** Server-driven UI using Thymeleaf templates enhanced with HTMX for low-overhead, partial-page interactions. No heavy JavaScript frameworks (React, Vue, etc.).
*   **Operating Mode:** Strictly offline; no external authentication or cloud syncing required.

---

## 3. Functional Requirements

### 3.1 Note & Scratchpad Management
*   **Persistent Scratchpad:** A centralized text area to dump random scribbles, formatted in Markdown.
*   **Bi-Modal State:** 
    *   **Edit Mode:** Standard plain text editor layout.
    *   **Read Mode:** The server parses raw Markdown into clean, readable HTML.
    *   **Interaction:** Toggling between states must be instant, handled via HTMX swap without requiring a full browser page refresh.

### 3.2 Link Identification & Organization
*   **Automated Parsing:** On saving or updating a note, the backend must parse the text using Regular Expressions to identify all valid HTTP/HTTPS URLs.
*   **Data Extraction:** Extracted URLs must be decoupled and indexed in a separate database relation linked to the parent note.
*   **Visual Organization:** Extracted URLs must be displayed cleanly in an independent UI component (e.g., side-dock or dedicated list view) for rapid reference.

### 3.3 Quick Actions & One-Click Clipboard Utilities
*   **One-Click Copying:** 
    *   Every entry in the extracted link list must feature a dedicated icon button.
    *   Clicking this button copies the raw target URL directly to the OS clipboard via the browser's native Clipboard API.
*   **Hover Enhancements:** Actionable items (like links or code blocks) should display lightweight, contextual hover icons to trigger secondary activities (e.g., copy URL, open link in a new browser tab).

---

## 4. Non-Functional Requirements
*   **Performance:** UI interactions (mode swapping, link refreshing) must have sub-100ms latency via HTMX.
*   **UI Aesthetic:** Minimalist, clean, and clean-cut. Standard utility design over highly-styled, padding-heavy alternative templates.
*   **Data Reliability:** Notes must safely survive application lifecycle restarts via H2 file-backed storage.

---

## 5. Proposed Domain Model

### 5.1 Note Entity
*   `id` (Long, Primary Key)
*   `rawContent` (Clob/Text, Stores the raw Markdown)
*   `createdAt` (Timestamp)
*   `updatedAt` (Timestamp)

### 5.2 ExtractedLink Entity
*   `id` (Long, Primary Key)
*   `noteId` (Foreign Key referencing Note)
*   `rawUrl` (String/Varchar)
*   `displayTitle` (String, Optional/Fall-back to truncated URL)
