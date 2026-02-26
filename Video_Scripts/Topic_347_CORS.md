# What is CORS? (Cross-Origin Resource Sharing)

## Video Length: ~4-5 minutes | Level: Beginner

---

## The Hook (20-30 seconds)

Your frontend runs on `localhost:3000`. Your API runs on `localhost:8080`. Same machine. Same developer. You click "Login." The browser sends a request to `localhost:8080`. The browser blocks it. Red error in the console: "CORS policy." You're confused. It's *your* server. Why won't the browser let you? Because the browser doesn't trust that. It sees two different **origins**. And by default, it says no. That's **CORS**—Cross-Origin Resource Sharing. Let me show you why it exists and how to fix it.

---

## The Story

**Same-origin policy** is a browser security rule. Two URLs are the "same origin" only if they match on **protocol** (http vs https), **domain** (example.com vs api.example.com), and **port** (3000 vs 8080). Change any one—different origin. `http://localhost:3000` and `http://localhost:8080`? Different ports. Different origins. The browser restricts what a page from one origin can do with data from another. Why? So a malicious site can't silently read your bank account from another tab. Without this, any website could make requests to your bank and steal your data. The policy protects you.

**CORS** is the mechanism that *relaxes* this—when the server says it's okay. The server sends headers: `Access-Control-Allow-Origin: http://localhost:3000`. "I allow requests from this origin." The browser checks. "The page is from localhost:3000. The server allows localhost:3000. Go ahead." Request succeeds. Without that header, the browser blocks the response. The server might have sent data—but the browser hides it from your JavaScript. You see a CORS error. The fix is on the server: add the right headers.

For some requests (like POST with custom headers), the browser sends a **preflight** first. An **OPTIONS** request. "Can I send a POST with Content-Type: application/json from localhost:3000?" The server responds with `Access-Control-Allow-Origin`, `Access-Control-Allow-Methods`, `Access-Control-Allow-Headers`. If the server says yes, the browser sends the real request. If not, it blocks. Preflight = extra safety check for "non-simple" requests.

---

## Another Way to See It

Imagine a nightclub. The bouncer (the browser) has a rule: "Only people from the guest list get in." The guest list = same origin. You're on a page from site A. Site A's page wants to fetch data from site B's API. The bouncer says: "You're from site A. Site B's data? I need Site B to put you on the guest list." Site B's server sends the header: "Access-Control-Allow-Origin: site A." Now you're on the list. The bouncer lets the data through. CORS is the server saying "I allow this origin." The bouncer enforces it.

---

## Connecting to Software

- **Origin** = protocol + domain + port. `https://api.example.com:443` and `https://app.example.com:443` = different origins (different subdomains).
- **Same-origin policy** = browser blocks cross-origin requests by default. Protects users from malicious sites.
- **CORS** = server sends `Access-Control-Allow-Origin` (and related headers) to allow specific origins.
- **Preflight (OPTIONS)** = browser sends OPTIONS before POST/PUT/DELETE with custom headers. Server must respond with CORS headers.
- **Common mistake:** Forgetting CORS when frontend and backend are on different origins (localhost:3000 vs localhost:8080, or app.example.com vs api.example.com).
- **Real tools:** Express.js (`cors` middleware), Spring Boot (`@CrossOrigin`), Django (`django-cors-headers`), Nginx (add headers in config).

---

## Let's Walk Through the Diagram

```
    BROWSER (localhost:3000)              API SERVER (localhost:8080)
    =========================             ==========================

    1. JS: fetch('http://localhost:8080/api/users')
           │
    2. Browser: "Different origin! Sending preflight OPTIONS..."
           │
    3. OPTIONS /api/users  ──────────────────►  Server receives OPTIONS
           │                                              │
    4. Server responds:                                   │
       Access-Control-Allow-Origin: http://localhost:3000  │
       Access-Control-Allow-Methods: GET, POST             │
           │                                              │
    5. Browser: "OK, server allows me. Sending real GET."
           │
    6. GET /api/users  ──────────────────────►  Server returns data
           │                                              │
    7. Browser: "CORS headers present. Passing data to JS."
           │
    8. Your code receives the response ✓

    Without CORS headers: Browser blocks at step 7. You see CORS error.
```

---

## Real-World Examples (2-3)

**1. Every SPA + API setup.** React app on `app.mycompany.com`. API on `api.mycompany.com`. Different subdomains = different origins. Without CORS headers on the API, the React app can't fetch data. Every modern web app faces this. The fix: add `Access-Control-Allow-Origin: https://app.mycompany.com` (or use a proxy in development).

**2. Local development.** Frontend: `npm run dev` → localhost:3000. Backend: `python app.py` → localhost:8080. First API call. CORS error. You add `Access-Control-Allow-Origin: http://localhost:3000` to your backend. Problem solved. This is the most common "why won't it work" moment for beginners.

**3. Third-party APIs.** You build a dashboard that fetches from Stripe's API. Stripe's API has CORS configured for allowed origins. If your domain isn't on their list, the browser blocks it. Some APIs don't support browser CORS at all—they're meant for server-to-server calls. You need a backend proxy.

---

## Let's Think Together

**Question:** Can you fix CORS by changing something in your frontend code?

**Pause. Think about it...**

**Answer:** No. CORS is enforced by the browser. The server must send the right headers. Your frontend can't add `Access-Control-Allow-Origin`—that header is set by the *server* in the *response*. The only frontend "fix" is a workaround: proxy your API through your own backend (same origin as frontend), and have the backend call the real API. The backend isn't subject to CORS—only browser JavaScript is. So: fix it on the server, or proxy.

---

## What Could Go Wrong? (Mini Disaster Story)

A company launched a new frontend. They used `Access-Control-Allow-Origin: *`—allow all origins. "We're a public API. Anyone can use us." A phishing site built a page that looked exactly like the company's login. It made requests to the real API. Because of `*`, the browser allowed the responses. The phishing site could read the API data. User credentials. The company didn't intend that. `*` means "any website can call us from the browser." For public read-only APIs, sometimes that's fine. For authenticated APIs with sensitive data? Dangerous. They switched to a whitelist of allowed origins. Lesson: `*` is convenient. It's also a blanket permission. Use it only when you truly mean "anyone."

---

## Surprising Truth / Fun Fact

**CORS** only applies to *browser* requests. When your backend server calls another API (server-to-server), there's no CORS. No browser. No same-origin policy. CORS is purely a browser security feature. That's why server-side API clients (curl, Postman, your Node.js backend) never hit CORS. It's a client-side thing.

---

## Quick Recap (5 bullets)

- **Origin** = protocol + domain + port. Different = cross-origin.
- **Same-origin policy** = browser blocks cross-origin requests by default. Security.
- **CORS** = server sends `Access-Control-Allow-Origin` to allow specific origins.
- **Preflight (OPTIONS)** = browser sends it for non-simple requests. Server must respond with CORS headers.
- **Fix on the server** = add CORS headers. Frontend can't fix it. Or proxy through your backend.

---

## One-Liner to Remember

> **CORS is the server saying "I allow this origin." The browser enforces it. Fix it on the server.**

---

## Next Video

You write code on your laptop. It works. You push to GitHub. But how does it get to a server that millions of users can access? Not by FTP. Not by hand. By a **pipeline**—code to tests to deployment, automatically. That's **CI/CD**. The bridge from your commit to production. That's next.
