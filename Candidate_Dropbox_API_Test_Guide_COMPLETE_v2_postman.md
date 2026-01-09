# Candidate Dropbox API  
**External Test & Integration Guide (Backend Email Only)**

This API accepts a **public resume PDF**, generates a structured candidate review, and **emails the review directly from the backend** to the specified recipient.

This guide is written so **any tester (technical or non-technical)** can successfully test the API.

---

## 1) Base URL

```
https://<YOUR_BACKEND_BASE_URL>
```

Example:
```
https://candidate-dropbox-backend.onrender.com
```

---

## 2) Authentication (Bearer Token)

Every request must include a **Bearer token**.

```
Authorization: Bearer <API_KEY>
```

### How tokens work
- Each tester is issued a **separate API key**
- All keys are centrally managed by the backend owner
- Tokens are validated by the backend (testers do not manage infrastructure)

If the token is missing or invalid, the API will return:
```
401 Unauthorized
```

---

## 3) Email Delivery (Important)

### One simple rule

> **The review is always sent to `delivery_email`.**

There are:
- No fallbacks
- No hidden defaults
- No use of candidate email for delivery

If `delivery_email` is missing, the request will fail.

---

## 4) Endpoint: Create Candidate Review

### Method
```
POST /intake
```

### Required Headers

| Header | Value |
|------|------|
| Authorization | `Bearer <API_KEY>` |
| Content-Type | `application/json` |

---

## 5) Supported Input (Current Scope)

### Public Resume PDF (Required)

You must provide a **publicly downloadable PDF**.

```
resume_url
```

Example:
```
https://example.com/sample-resume.pdf
```

> Public profile links (LinkedIn, GitHub, portfolios, etc.) are **not supported yet**.

---

## 6) Required Request Body

```json
{
  "source": "api-test",
  "resume_url": "https://example.com/sample-resume.pdf",
  "delivery_email": "receive.review@email.com"
}
```

### Optional candidate metadata
```json
"candidate": {
  "name": "Candidate Name",
  "email": "candidate@email.com"
}
```

- Candidate email is stored as **metadata only**
- It does **not** affect email delivery

---

## 7) cURL Example

```bash
curl -X POST "https://candidate-dropbox-backend.onrender.com/intake" \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "source": "curl-test",
    "resume_url": "https://example.com/sample-resume.pdf",
    "delivery_email": "receive.review@email.com",
    "candidate": {
      "name": "Curl Tester"
    }
  }'
```

---

## 8) Postman Setup

### Recommended Environment Variables

Create a Postman Environment with:

- `BASE_URL`
- `API_KEY`

Example values:
- `BASE_URL` → `https://candidate-dropbox-backend.onrender.com`
- `API_KEY` → `<YOUR_API_KEY>`

Header configuration:
```
Authorization: Bearer {{API_KEY}}
```

Request URL:
```
{{BASE_URL}}/intake
```

---

## 9) Using Different API Keys (Multiple Testers)

Each tester should use a **different API key**.

### Example

| Tester Type | API Key |
|-----------|--------|
| Reviewer / Hiring | `TESTER_REVIEW_API_KEY` |
| Candidate Self-Test | `TESTER_CANDIDATE_API_KEY` |
| QA / Automation | `TESTER_QA_API_KEY` |

This enables:
- Independent rate limits
- Clear audit trails
- Easy revocation of individual access

---

## 10) Successful Response

```json
{
  "ok": true,
  "candidate_id": "CND-abc123",
  "status": "reviewed"
}
```

### What happens after success
- 📧 Review email is sent to `delivery_email`
- 💬 Slack notification may be sent (if enabled)
- 🧾 Submission is logged internally
- 🔁 Duplicate submissions are automatically detected

---

## 11) Duplicate Handling

Submitting the same resume again returns:

```json
{
  "ok": true,
  "candidate_id": "CND-abc123",
  "status": "reviewed",
  "deduped": true
}
```

- No re-processing
- No duplicate email

---

## 12) Rate Limits (Clarified)

To keep testing smooth and avoid inbox spam, limits apply on **different dimensions**:

**Per API key (tester identity)**
- ~10 requests per minute
- Protects the system from accidental loops or misuse
- Does NOT limit how many different candidates you can test

**Per delivery email (recipient protection)**
- ~3–5 review emails per hour per `delivery_email`
- Prevents inbox flooding
- Applies only to the same recipient email
- Different emails are unaffected

**Duplicate resumes**
- Automatically ignored

> These limits protect recipients and system stability —  
> they do **not** restrict legitimate testing by different users.

For higher limits or automation testing, request access.

---

## 13) Common Errors

### Missing or invalid token
```
401 Unauthorized
```

### Missing resume URL
```
400 resume_url is required
```

### Missing delivery email
```
400 delivery_email is required to receive the review email
```

### Resume not accessible
```
400 Could not download resume_url. Make sure it is publicly accessible.
```

### Review timeout
```
504 Review timed out. Please try again.
```

---

## 14) How to Review & Report Issues

After testing, please confirm:

- API returned `200 OK`
- You received the review email
- Email content is readable and complete

### If something doesn’t work, report the issue with:

1. Timestamp (your local time)
2. API endpoint used
3. HTTP status code + response body
4. `candidate_id` (from response, if present)
5. Resume URL domain (example.com, drive.google.com, etc.)
6. Whether you received the email (yes/no)

### Where to report issues
Issues and feedback are tracked in a **public issues-only repository**:

```
<GITHUB_ISSUES_REPO_URL>/issues
```

⚠️ Please do NOT include:
- API keys
- Authorization headers
- Private resume content

---

## 15) Tester Summary

> Send a public resume PDF + a delivery email.  
> You’ll receive the review by email.  
> Use your assigned API key.

---

This API provides **review and insights only**.  
It does not make hiring or rejection decisions.

---

## Postman Collection (Recommended)

If you are using Postman, you can import the provided collection file:

- **Collection file:** `Candidate_Dropbox_API_Postman_Collection.json`

### A) Import the collection
1. Open Postman
2. Click **Import**
3. Choose the file `Candidate_Dropbox_API_Postman_Collection.json`
4. The collection will appear as: **“Candidate Dropbox API - Tester Collection”**

### B) Create an environment (so you don’t edit URLs/tokens repeatedly)
1. In Postman, click **Environments**
2. Create a new environment (example: **Candidate Dropbox - Tester**)
3. Add these variables:

| Variable | Example value |
|---|---|
| `BASE_URL` | `https://candidate-dropbox-backend.onrender.com` |
| `API_KEY` | `<YOUR_API_KEY>` |

4. Click **Save**
5. Select this environment from the top-right environment dropdown

### C) Run the test request
1. Open the imported collection
2. First run **Health Check** (should return `{ "ok": true }`)
3. Then run **Create Review (Resume PDF)**  
4. Update the request body values:
   - `resume_url` → your public resume PDF link
   - `delivery_email` → your email address (where you want the review)
   - optional: `candidate.name`

### D) What to expect
- You should get a **200 OK** response with `candidate_id`
- You should receive the **review email** at `delivery_email`

> Important: Do not paste your API key into screenshots when reporting issues.
