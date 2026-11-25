# Fix: Banglar Shiksha login "Server error" in WebView (works in Chrome)

Changes in MainActivity.kt:
1. Removed the `X-Requested-With: com.bspsms.android` header (WebSettingsCompat.setRequestedWithHeaderOriginAllowList).
2. User-Agent is now Chrome-style (no `wv`, no `Version/4.0`), built from the device's real WebView Chrome version.
3. NO_CACHE_HEADERS (Cache-Control/Pragma/Expires) are no longer sent on page loads.
Applied to all 3 portal WebViews (main, popup, background marksheet).

## v2 (captcha/session mismatch)
Screenshot showed correct captcha (4+9=13) rejected with "Incorrect captcha", then "Server error".
-> likely session cookie mismatch. Added test flags in MainActivity.kt companion:
- PORTAL_USE_PROFILE_API = false  (default WebView cookie store instead of per-user Profile)
- PORTAL_CHROME_STYLE_UA = false  (keep WebView's own UA; fake Chrome UA without sec-ch-ua hints can look suspicious)
X-Requested-With removal and no custom no-cache headers are kept.

## v3 (diagnostic)
Screenshots: captcha correct and rotating, but login shows "Server error" -> the login POST itself fails.
- Added read-only XHR/fetch observer (MainActivity.onPageStarted): when a POST fails, the banner shows
  "Portal Request Failed (diagnostic)" with URL, HTTP status and the first 300 chars of the response.
- Enabled WebView.setWebContentsDebuggingEnabled(true) so chrome://inspect works over USB.

## v4 (Result Ready tool)
Problem: wrong marks, and one extra subject column added per scanned student.
Cause: any non-marks row (remarks/name/etc.) in the Summative table could become a "subject" with a student-specific key;
the exporter then unioned subjects by key, adding a column per student. Body rowspans also shifted mark columns.
Fixes:
- PortalHrpcDomScripts.extractSummativeMarksJs: rowspan-aware body grid; rows with no marks are never subjects; stricter "art" match.
- MarksheetAutomationController: drop subjects without any marks; recompute full marks.
- ResultXlsxExporter.resolveSubjectSchema: only subjects that have marks for at least one student.
