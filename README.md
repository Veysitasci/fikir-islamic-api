# 🌙 FİKİR Islamic API - Comprehensive Developer Hub & Integration Manual

<p align="center">
  <img src="https://api.fikirogrencitoplulugu.org.tr/assets/icons/android/icon-192x192.png" alt="FİKİR Logo" width="120" style="border-radius: 24px; box-shadow: 0 8px 24px rgba(0,0,0,0.2);"/>
</p>

<h3 align="center">FİKİR Islamic API v2.2.0</h3>
<p align="center">A High-Performance, Zero-Cost, and Secure Islamic Data Engine for Modern Software Applications.</p>

<p align="center">
  <a href="https://api.fikirogrencitoplulugu.org.tr/status"><img src="https://img.shields.io/badge/API-Active-brightgreen.svg?style=for-the-badge&logo=statuspage" alt="API Status"/></a>
  <a href="https://api.fikirogrencitoplulugu.org.tr/doc"><img src="https://img.shields.io/badge/Version-v2.2.0-blue.svg?style=for-the-badge&logo=openapi" alt="Version"/></a>
  <a href="https://opensource.org/licenses/MIT"><img src="https://img.shields.io/badge/License-MIT-yellow.svg?style=for-the-badge&logo=opensourceinitiative" alt="License"/></a>
  <a href="https://api.fikirogrencitoplulugu.org.tr/support"><img src="https://img.shields.io/badge/Community-Support-orange.svg?style=for-the-badge&logo=discord" alt="Community"/></a>
</p>

---

## 🌍 Language Switcher
- 🇺🇸 **English (Current)**
- 🇹🇷 **[Türkçe Dökümantasyon (Turkish Version)](README.tr.md)**

---

## 🏗️ System & Integration Architecture

To ensure enterprise-grade stability while maintaining open source boundaries, FİKİR API utilizes an Edge-routed gateway architecture. 

```mermaid
sequenceDiagram
    autonumber
    actor Client as Developer Application
    participant Edge as Edge CDN & WAF (Cloudflare)
    participant Auth as Auth & Rate Limiter
    participant API as Python API Engine (Flask)
    participant Cache as Redis Cache Layer

    Client->>Edge: HTTPS GET /api/fikir/quran/v2/tr/diyanet/surah/1
    activate Edge
    Edge->>Edge: Sanitize request (XSS/SQLi Checks)
    Edge->>Auth: Validate Client IP & Rate Limits
    activate Auth
    Auth-->>Edge: OK (Rate limits check passed)
    deactivate Auth
    
    Edge->>Cache: Query cached response
    activate Cache
    alt Cache Hit
        Cache-->>Edge: Cached JSON Payload
        Edge-->>Client: HTTP 200 OK (Served in < 5ms)
    else Cache Miss
        Cache-->>Edge: Miss
        deactivate Cache
        Edge->>API: Route Request to API Engine
        activate API
        API->>API: Process query & generate payload
        API-->>Edge: JSON response + Cache-Control Headers
        deactivate API
        Edge->>Cache: Set cache key
        Edge-->>Client: HTTP 200 OK (Served in < 50ms)
    end
    deactivate Edge
```

---

## 🔑 LLM Context Configuration (For AI Coding Assistants)
If you are an AI assistant (Cursor, Copilot, Gemini, ChatGPT) writing code wrappers, strictly adhere to these specifications:
- **API Base URL**: `https://api.fikirogrencitoplulugu.org.tr/api`
- **Request Protocol**: `HTTPS / TLS 1.3`
- **Response Format**: `application/json` (UTF-8)
- **Primary Headers**: `Accept: application/json`
- **Secondary Headers (Optional)**: `X-App-Client: <YOUR-APP-NAME>` (helps bypass rate limiting for verified apps)

---

## 📁 Core Modules & Dynamic Endpoints

### 1. Quran & Multilingual Recitations (v2)

This module provides verse-by-verse and chapter-by-chapter representations across 48+ languages.

#### 📡 Endpoints Table
| Method | Endpoint | Parameters | Returns |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/fikir/quran/v2/languages` | None | Array of supported languages. |
| `GET` | `/api/fikir/quran/v2/editions` | None | Complete catalog of translation editions. |
| `GET` | `/api/fikir/quran/v2/editions/{lang}` | `lang` (ISO-2 code) | Available editions for specified language. |
| `GET` | `/api/fikir/quran/v2/{lang}/{edition}/surahs` | `lang`, `edition` | Metadata for all 114 Surahs. |
| `GET` | `/api/fikir/quran/v2/{lang}/{edition}/surah/{no}` | `lang`, `edition`, `no` (1-114) | Full text and translation of a Surah. |
| `GET` | `/api/fikir/quran/v2/{lang}/{edition}/surah/{surah}/{ayah}` | `lang`, `edition`, `surah`, `ayah` | Single ayah translation. |
| `GET` | `/api/fikir/quran/v2/{lang}/{edition}/juz/{no}` | `lang`, `edition`, `no` (1-30) | Full texts for a specific Juz. |
| `GET` | `/api/fikir/quran/v2/{lang}/{edition}/page/{no}` | `lang`, `edition`, `no` (1-604) | Full texts for a specific Mushaf page. |

---

### 2. Hadith Library Module (v1)

Access Sahih collections with verified text representations.

#### 📡 Endpoints Table
| Method | Endpoint | Parameters | Returns |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/hadith/collections/{lang}` | `lang` (ISO-2 code) | Supported Hadith collections list. |
| `GET` | `/api/hadith/{lang}/{collection}/{no}` | `lang`, `collection`, `no` | Full content of specific Hadith entry. |

---

### 3. Prayer Times & Calendar Engine (v1)

Provides highly accurate prayer schedule calculations with timezone handling.

#### 📡 Endpoints Table
| Method | Endpoint | Parameters | Returns |
| :--- | :--- | :--- | :--- |
| `GET` | `/api/fikir/prayer/turkey` | `city` (query param) | Today's calculated prayer times. |
| `GET` | `/api/fikir/prayer/methods` | None | Calculation methods used globally. |
| `GET` | `/api/fikir/calendar/today` | None | Live Hijri date and holy nights. |

---

## 🎨 Client Integration Guidelines (i18n, Themes, Toasts & Modals)

When integrating the FİKİR Islamic API into client-side interfaces (Web, Mobile, Desktop), adhere to these best practices:

### 1. 🌍 Internationalization (i18n) Structure
- **Dynamically Bind Locales**: Always map the client application's active language context (e.g., `tr`, `en`, `ar`) directly to the API's `{lang}` parameters.
- **Bi-directional Formatting**: Keep translations aligned in your client context. Ensure numbers (like Ayah numbers) are converted appropriately (e.g., to Arabic numerals `٠-٩` for Arabic text representation).

### 2. 🌗 Theme-Aware Display
- **Typography & Legibility**: Quranic Arabic requires highly legible fonts and scale controls. Provide settings in the client to toggle font size (Small, Medium, Large) for readability.
- **Curated Color Palettes**: Switch interface themes seamlessly (e.g., Midnight blue, Sunset amber, Forest green, Royal purple) in both Dark and Light modes. Keep contrast levels high for reading.

### 3. 🍞 Toast Notifications for API Events
- **Operation Alerts**: Trigger lightweight, ephemeral Toast notifications for transient success actions (e.g., copy-to-clipboard, saving config settings).
- **API Error Display**: If the API returns a validation or connection error (such as `429 Rate Limit Exceeded`), display a clear Toast notification to prevent user confusion.

### 4. 🗖 Modals for Crucial Decisions
- **Destructive Confirmations**: Wrap actions like deleting accounts or revoking API credentials in confirm Modals. Do not perform state mutation without explicit user approval.
- **Multi-step Forms**: Nest credential creation, CAPTCHA verification, and 2FA configuration in overlay modals to maintain UX focus.

---

## 🚫 Robust Error Handling Scheme

FİKİR API returns standardized error envelopes for easier application troubleshooting.

```json
{
  "status": "error",
  "error": {
    "code": 429,
    "message": "Too Many Requests - Rate limit exceeded. Limit is 100 requests per minute.",
    "timestamp": "2026-06-02T10:07:00Z"
  }
}
```

| HTTP Code | Cause | Solution |
| :--- | :--- | :--- |
| `400 Bad Request` | Invalid/Missing URL parameters. | Validate input parameters (e.g. Surah ranges 1-114). |
| `401 Unauthorized` | Attempting to access private endpoints. | Use public namespace pathways. |
| `404 Not Found` | Route or requested ID doesn't exist. | Check Surah/Hadith/Edition code spelling. |
| `429 Too Many Requests` | Exceeding the rate limits. | Cache responses locally or contact support for higher limits. |
| `500 Server Error` | Unexpected backend failure. | Report to [Sistem Durumu](https://api.fikirogrencitoplulugu.org.tr/status). |

---

## ❓ Frequently Asked Questions (FAQ)

### 1. Is there an API key required for public projects?
No. Standard API endpoints are completely open. You can query them instantly using standard HTTP client packages.

### 2. Can I use this API in commercial mobile apps?
Yes. The FİKİR Islamic API is distributed under the MIT license, meaning you can integrate it into free, ad-supported, or commercial applications without licensing fees. We only request that you credit the API link in your credits or settings pages.

### 3. How do I request a rate-limit exemption?
If you are building an app with a large user-base, you should contact the FİKİR team via `destek@fikirogrencitoplulugu.org.tr` to register your `X-App-Client` headers.
