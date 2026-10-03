# Day 1 — HTTP

## 1. What is HTTP?

HTTP (HyperText Transfer Protocol) is the communication protocol used between a client, such as a web browser, and a web server.

It allows the client to send requests to the server and allows the server to send responses back to the client.

---

## 2. What is the difference between a request and a response?

A **request** flows from the client (browser) to the server. The client uses a request to ask the server for a resource or to perform an action.

A **response** flows from the server back to the client. The server processes the request and sends back the result, along with information such as a status code, headers, and sometimes a response body.

In simple terms:

- **Request:** "Server, please do this or give me this."
- **Response:** "Here is the result of your request."

---

## 3. What is the difference between a header and a body?

### Header

A header contains additional information (metadata) about an HTTP request or response.

Examples include:

- `User-Agent` — information about the client/browser.
- `Cookie` — cookies sent by the browser.
- `Content-Type` — tells what type of data is being sent.
- `Cache-Control` — gives caching instructions.
- `Referer` — can indicate the page from which the request originated.

### Body

The body contains the actual data being sent.

For example, in a POST request, the body may contain login information or other form data. In a response, the body may contain HTML, JSON, an image, or other requested content.

In simple terms:

- **Header:** Information about the message.
- **Body:** The actual data in the message.

---

## 4. What does 200 mean?

`200 OK` means that the server successfully processed the request.

---

## 5. What does 401 mean?

`401 Unauthorized` generally means that the request does not contain valid authentication credentials.

For example, a user may need to log in before accessing a protected resource, or the authentication credentials provided may be missing or invalid.

A useful way to remember it is:

> **401 = "Who are you?"**

---

## 6. What does 403 mean?

`403 Forbidden` means that the server understood the request but is refusing to allow access to the requested resource.

For example, a normal user may try to access `/admin`, but the server may return `403 Forbidden` because that user does not have permission to access the admin area.

A useful way to remember it is:

> **403 = "I understand what you want, but you are not allowed."**

---

## 7. What does 404 mean?

`404 Not Found` means that the server could not find the requested resource at the requested location.

For example, if a user requests `/this-page-does-not-exist` and that resource is not available, the server may return `404 Not Found`.

A useful way to remember it is:

> **404 = "I can't find it."**

---

## 8. Why can a session cookie be extremely sensitive after login?

After a successful login, the server usually does not ask the user to enter their password again for every request.

Instead, the server can create a session and give the browser a session identifier, which is commonly stored in a cookie.

For example:

```http
Set-Cookie: session=abc123
```

The browser stores the cookie and may send it with later requests:

```http
Cookie: session=abc123
```

The server can use this session identifier to recognize the authenticated session.

Therefore, an authentication session cookie is highly sensitive. If an attacker obtains a valid session token, the application may treat requests containing that token as belonging to the authenticated session.

**Important:** A session cookie should be treated like a temporary digital identity for an authenticated session.

---

## 9. What is a parameter?

A parameter is a value supplied as part of a request that can provide additional information to the server.

For example:

```text
/profile?id=42
```

Here, `id=42` is a query parameter.

Other examples include:

```text
/search?q=shoes
/product?id=15
```

Parameters can also appear in request bodies, such as:

```text
username=harshal&role=user
```

From a WAPT perspective, parameters are important because they may contain user-controlled input. A security tester can check whether the application properly validates and authorizes changes to those values.

For example, if a request contains:

```text
/profile?id=42
```

a tester may check whether changing the value to another authorized test account's ID changes the returned resource. Testing access to resources must only be performed where the tester has permission.

---

## 10. Pick one request from DevTools.

### Method:
`GET`

### Path:
`/`

### Parameters:
There were no query parameters in the request I tested.

### Important headers:
Some important headers I observed included:

- `User-Agent`
- `Cookie`
- `Cache-Control`
- `Referer`
- `Priority`
- `Accept`

There were also several other headers.

### Status code:
`304 Not Modified`

### What does this request do?

This request was used while opening the Indian Cyber Club Academy website's home page.

The `304 Not Modified` response indicates that the requested resource has not changed since the version the browser already has cached, so the browser can reuse its cached copy instead of downloading the resource again.

---

## 11. What would you investigate if you saw:

```http
GET /profile?id=42
```

I would investigate whether the `id` parameter controls which user's profile is returned.

For example, in an authorized testing environment, I could change:

```text
id=42
```

to another permitted test account ID and observe whether the application properly checks authorization.

The important question is:

> **Does the server verify that the currently authenticated user is authorized to access the profile identified by the `id` parameter?**

If the server allows one user to access another user's private profile simply by changing the ID, that could indicate an **IDOR/BOLA-style authorization vulnerability**.

The important point is that changing the parameter alone does not prove a vulnerability. The vulnerability exists when the application fails to enforce proper authorization and exposes a resource that the user should not be allowed to access.

---

# Key Takeaways

```text
HTTP
├── Request  → Client → Server
└── Response → Server → Client

Header → Metadata/information about the message
Body   → Actual data being sent

200 → Request succeeded
401 → Authentication is missing/invalid
403 → Access is forbidden
404 → Resource not found
304 → Resource has not changed; cached version can be reused

Cookie → Can maintain an authenticated session
Parameter → User-supplied/request value that can influence server behavior

WAPT mindset:
"What does the client send?"
"What does the server trust?"
"Does the server properly validate and authorize it?"
```
