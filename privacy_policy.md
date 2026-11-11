# Privacy Policy for LinkGuard

**Effective Date:** November 11, 2026  
**Last Updated:** November 11, 2026  
**Application Identity:** `com.namanpandey.linkguard`  

LinkGuard ("we", "us", or "our") is designed from the ground up as a privacy-preserving security tool. Our mission is to protect users from malicious links, phishing scams, and fraudulent websites while maintaining strict data minimization and respecting user privacy.

This Privacy Policy describes what information LinkGuard processes, how it is handled, what remains strictly on your device, and what optional interactions occur with external cloud services.

---

## 1. Core Principles: On-Device Processing by Default

LinkGuard performs security analysis locally on your Android device whenever possible:

1. **Local Heuristic URL Inspection:**  
   Our pattern-detection algorithms, character-entropy calculations, deceptive domain heuristics, and top-level domain risk evaluations execute entirely within the app's local code. Scanned URLs are not sent to any remote server for heuristic analysis.

2. **Curated Official Domain Recommendations:**  
   LinkGuard compares scanned links against a locally bundled registry of verified official domains (`official_domains.json`). This comparison occurs entirely on-device without issuing search-engine queries or querying external registries.

3. **QR Code Scanning:**  
   When you use the QR scanner, camera access is used exclusively in real time to locate and decode QR code symbols. Camera frames are processed in volatile memory on your device. Video or photos are never recorded, saved to storage, or transmitted off your device.

4. **Screenshot and Image OCR:**  
   When you analyze a screenshot or gallery image, text recognition and QR extraction are performed locally using on-device machine learning models (Google ML Kit). The image bytes remain on your device and are never uploaded to any remote server.

5. **Local Scan History & Storage:**  
   Your scan history is stored locally within Android's private application storage using SharedPreferences (serialized as JSON). When scans are recorded, embedded secrets, query values, and fragments are redacted via our privacy sanitizer (`UrlPrivacyRedactor`) before persistent storage. Records are protected by Android's operating-system application sandbox and are never synchronized to cloud servers, shared with analytics providers, or monetized. You may clear individual scan records or wipe the entire history at any time from within the app.

---

## 2. Optional Cloud Features and External Threat Intelligence

LinkGuard provides optional cloud-assisted threat intelligence features that are disabled by default or require explicit user initiation:

### 2.1 External Threat Intelligence
- **Disabled by Default:** Cloud threat lookups are optional and turned **OFF** by default upon installation.
- **Explicit Consent Required:** Cloud lookups are activated only if you explicitly enable "Online threat checking" in the app's Settings screen. You may revoke this consent at any time.
- **URL Sanitization & Privacy Minimization:** When enabled, eligible public URLs are processed by our privacy-filtering engine before dispatch:
  - User authentication credentials (such as `user:password@`) and URL fragment identifiers (`#...`) are stripped.
  - Sensitive authentication query parameters (such as `token`, `access_token`, `auth`, `password`, `secret`, `jwt`, `api_key`) are deleted, and values from all other query parameters are emptied.
  - The URL path (such as `/login` or `/verify`) remains in the evaluated URL. Because URL paths may still contain sensitive context, external lookups remain strictly opt-in.
- **Private and Internal Network Blocking:** URLs pointing to local or internal resources (including `localhost`, IPv4 loopback `127.0.0.1`, IPv6 loopback `::1`, RFC 1918 private subnets such as `10.0.0.0/8`, `172.16.0.0/12`, `192.168.0.0/16`, link-local addresses, and `.local`/`.internal` domains) are blocked client-side and at the Edge Function gateway from ever triggering external network requests.
- **Provider Architecture & Current Status:** LinkGuard is architected to check public URLs against Google Web Risk via a dedicated Supabase Edge Function gateway. In the current release, external provider API credentials are intentionally unconfigured to maintain a fail-closed operation: when enabled, lookup requests receive a provider-not-configured response and immediately fall back to LinkGuard's local on-device heuristics without querying third-party servers. If an external threat intelligence provider is configured in future updates, eligible URLs will be evaluated in accordance with the provider's privacy policy.

### 2.2 Community Threat Reporting
- **User-Initiated Submission:** LinkGuard includes an optional community reporting feature. No report is ever submitted automatically. A report is sent only when you explicitly tap "Report link" and confirm the submission.
- **Pseudonymous Identifiers & Minimal Profile:** Community reports use Supabase Anonymous Authentication. We do not require, collect, or store your name, email address, phone number, password, or any personal identity details. When an anonymous session is established, Supabase creates a pseudonymous unique identifier (UUID). A minimal database record is automatically created in `public.user_profiles` containing this pseudonymous UUID, an anonymous account flag (`is_anonymous: true`), your role (`'user'`), and activity timestamps. This record is used by backend functions to correlate reports from the same session for abuse prevention, rate limiting (maximum 10 reports per hour, 100 per month), and duplicate suppression. While this pseudonymous identifier does not reveal your real-world identity, activity within that session is correlated to that UUID for security and operational integrity.
- **Reported Data:** When you submit a report, the application transmits:
  - The sanitized URL (query parameters, credentials, and fragments removed).
  - The threat category you selected (e.g., Phishing, Scam, Malware).
  - The timestamp of submission.
  - Your pseudonymous reporter UUID.
- **Deduplication:** Reports are deduplicated within a 24-hour rolling window to minimize redundant records.

---

## 3. Data Infrastructure & Location

Cloud services supporting optional threat lookups and community reports are hosted on Supabase infrastructure located in the **Mumbai, India (`ap-south-1`)** region:
- **Data in Transit:** All network communication is encrypted in transit using HTTPS / Transport Layer Security (TLS).
- **Data at Rest:** Stored community reports are protected with database-level Row Level Security (RLS) policies and host-level storage encryption.
- **No Third-Party Analytics or Advertising Trackers:** LinkGuard contains zero third-party advertising SDKs, zero user tracking libraries, and zero behavioral telemetry frameworks.

---

## 4. Android Device Permissions

LinkGuard requests only permissions that are directly necessary for its security functions:

| Android Permission | Purpose | When Active |
|---|---|---|
| `android.permission.CAMERA` | Allows the device camera to scan and decode QR codes. | Only while the QR scanner screen is actively displayed. |
| `android.permission.INTERNET` | Allows optional external threat intelligence lookups, community reporting, and store update checks. | On demand when network features are invoked. |
| `android.permission.ACCESS_NETWORK_STATE` | Checks network availability before attempting optional cloud requests. | Read-only state check. |
| `android.permission.SYSTEM_ALERT_WINDOW` | Allows displaying the optional floating edge handle over other applications for quick access to link scanning. | Only when enabled by the user in settings. |
| `android.permission.FOREGROUND_SERVICE` | Maintains background components for the user-enabled floating handle and ephemeral screen capture. | User-initiated only; accompanied by a persistent notification. |
| `android.permission.FOREGROUND_SERVICE_SPECIAL_USE` | Powers the floating edge handle (`EdgeHandleService`) so it remains responsive across apps. | User-initiated; stopped at any time via notification action. |
| `android.permission.FOREGROUND_SERVICE_MEDIA_PROJECTION` | Enables the single-frame on-demand screen capture (`ScreenCaptureService`) for OCR link detection. | Strictly ephemeral; runs only after system consent and terminates immediately after one frame. |
| `android.permission.POST_NOTIFICATIONS` | Displays the required ongoing notification for active foreground services (Android 13+). | Only while foreground services are running. |

LinkGuard's Google Play distribution flavor (`play`) strictly excludes the `REQUEST_INSTALL_PACKAGES` permission and contains no direct package installer components.

---

## 5. Data Retention, Control, and Deletion

1. **Local Data Deletion:**  
   You have complete control over data stored on your device. You can delete individual scan history records or clear the entire history at any time via the "Previous scans" screen. Uninstalling LinkGuard permanently removes all private application storage, settings, and cache files from your device.

2. **Cloud Data Retention & Deletion:**  
   Anonymous reporter profiles and submitted community threat reports are retained indefinitely for ongoing threat detection, community moderation, duplicate suppression, and abuse prevention. Disabling "Online threat checking" in the app's Settings turns off future cloud lookups but does not delete previously submitted community threat reports from the database. Because reports are associated only with a pseudonymous session UUID and lack personal accounts, individual submissions cannot be attributed back to your personal identity or selectively purged on demand.

---

## 6. Children's Privacy

LinkGuard does not target and does not knowingly collect any personal information from children under the age of 13 (or the applicable age in your jurisdiction). If you believe a child has provided us with personal information, please contact us so we can take appropriate steps.

---

## 7. Changes to This Privacy Policy

We may update this Privacy Policy from time to time to reflect enhancements to LinkGuard or changes in legal requirements. Any updates will be published with a revised "Last Updated" date. We encourage you to review this policy periodically.

---

## 8. Contact Us

If you have questions, feedback, or concerns regarding this Privacy Policy or LinkGuard's privacy practices, please contact us:

- **Project Maintainer:** Naman Pandey  
- **Email:** [pandeynaman68@gmail.com](mailto:pandeynaman68@gmail.com)  
- **Application Repository:** `https://github.com/namanpandey9393/linkguard`  
- **Privacy Policy Repository:** `https://github.com/namanpandey9393/linkguard-privacy`  
