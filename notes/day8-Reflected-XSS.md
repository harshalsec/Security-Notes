# Day 8 — Reflected XSS

**Track:** WAPT  
**Topic:** Cross-Site Scripting (XSS) — Reflected XSS  
**Difficulty:** Beginner

## 1. What is Reflected XSS?

Reflected XSS is a web vulnerability where user-controlled input is reflected by the server into the HTTP response without proper context-appropriate encoding or handling. If the browser interprets that reflected input as executable JavaScript, the script can execute in the browser.

**Memory:** Request → Reflection → Browser → Execution

## 2. Why is it called “Reflected”?

The input comes from the current request and is immediately returned, or “reflected,” in the server’s response instead of being stored first.

**Memory:** Reflected = Request → Response

## 3. Reflected vs Stored XSS

**Reflected:** Request → Response → Browser

**Stored:** Input → Storage → Later Response → Browser

## 4. Why “My input appeared on the page” is not enough

Seeing input on the page only proves a possible reflection point. It does not prove XSS. To confirm XSS, the browser must actually interpret the input as executable code in the affected context.

**Reflection identifies a place to investigate; execution confirms XSS.**

## 5. Three Questions for XSS Testing

1. Is my input reflected?
2. Where exactly is my input reflected?
3. Can the browser interpret it as executable code and actually execute it?

**Memory:** Reflection → Context → Execution

## 6. Why Context Matters

Input can appear in HTML text, an HTML attribute, or a JavaScript context. These contexts are parsed differently, so I must first determine where my input landed.

**Key lesson:** Don’t start by asking which payload to use. Start by asking which context I am in.

## 7. Why a Script Tag May Not Execute Inside an Attribute

If input lands in:

```html
<input value="INPUT_HERE">
```

a basic script-tag payload may not execute because the input is inside an HTML attribute context rather than being parsed as a standalone script element.

**Memory:** Same input + different context = potentially different behavior.

## 8. Why Use a Harmless Marker First?

A marker such as `xss_test_123` helps confirm whether input is reflected and where it appears before testing for actual execution. This is safer and more controlled than immediately using an execution payload.

**Flow:** Harmless marker → Reflection → Context → Execution testing

## 9. Burp Repeater Workflow

1. Capture the request in **Proxy → HTTP history**.
2. Send it to **Repeater**.
3. Modify the relevant parameter.
4. Send the request.
5. Inspect the response.
6. Determine whether the input was reflected and where it appeared.

**Memory:** Repeater = Modify → Resend → Inspect

## 10. Why Use a Browser?

Burp Repeater lets me inspect HTTP requests and responses. The browser parses and renders the response and executes JavaScript, so I need a browser to confirm actual execution.

## 11. Main Remediation

The primary defense is **context-appropriate output encoding** of user-controlled input before inserting it into the page.

A **Content Security Policy (CSP)** can provide an additional layer of protection, but it is a secondary defense rather than a replacement for proper output encoding.

**Memory:** Primary = context-appropriate encoding; Secondary = CSP

# Mistakes I Made and What I Learned

### Mistake 1 — Saying the server executes the JavaScript

The server returns the response. The **browser parses the response and may execute JavaScript** contained in it.

```text
Server → Response
Browser → Parse → Execute
```

### Mistake 2 — Focusing only on “different context”

The important question is not simply whether the input is returned in a different context. I need to determine **what context the application places the input into and how the browser interprets it**.

### Mistake 3 — Thinking JavaScript only executes in a JavaScript context

The important lesson is that browser parsing behavior depends on context. I should identify the exact context before deciding how input could become executable.

### Mistake 4 — Using vague terminology for encoding

A better description is **HTML character encoding / context-appropriate output encoding**. For example:

```text
< → &lt;
> → &gt;
```

### Mistake 5 — Treating reflection as confirmed XSS

Reflection is not enough. The key distinction is:

```text
Input appears → Reflection point
Input executes → Confirmed XSS
```

# Corrected Answers

## Q1. What is Reflected XSS?

Reflected XSS is a type of web vulnerability where user-controlled input is reflected by the server into the HTTP response without proper context-appropriate encoding or handling. If the browser interprets that reflected input as executable JavaScript, the script can execute in the browser.

**Simple version:** I send input → the server reflects it into the response → the browser interprets it as code → JavaScript executes.

## Q2. Why is it called “Reflected”?

It is called Reflected XSS because input sent in the current request is immediately returned, or “reflected,” in the server’s response instead of being stored first.

## Q3. What is the difference between Reflected and Stored XSS?

Reflected XSS occurs when input from the current request is reflected into the response and can execute in the browser. Stored XSS occurs when input is stored somewhere, such as a database, and is delivered later when a user views the affected content.

## Q4. Why is “My input appeared on the page” not enough?

Seeing my input on the page only proves that the application may have a reflection point. It does not prove that JavaScript executed. I need to confirm that the browser actually interpreted the input as executable code.

## Q5. What three questions should I ask?

1. Is my input reflected?
2. Where exactly is my input reflected?
3. Can the browser interpret my input as executable code and actually execute it?

## Q6. Why might `<script>alert(1)</script>` not execute inside `<input value="INPUT_HERE">`?

Because the input is inside an HTML attribute context rather than being parsed as a standalone script element. The important lesson is to identify the context where the input lands before deciding how the browser may interpret it.

## Q7. Why use `xss_test_123` first?

A harmless marker helps me safely confirm whether my input is reflected and determine exactly where it appears in the response or page before testing for actual execution.

## Q8. What is Burp Repeater used for?

Burp Repeater is used to modify and resend HTTP requests. It lets me change parameters, resend the request, and inspect the response to understand whether my input is reflected and where it appears.

## Q9. Why do I need the browser to confirm execution?

Burp Repeater lets me inspect HTTP requests and responses, but the browser is responsible for parsing and rendering the HTML and executing JavaScript. Therefore, the browser is needed to confirm actual execution.

## Q10. What is the main remediation?

The primary remediation is **context-appropriate output encoding** of user-controlled input before it is inserted into the page. CSP can provide an additional layer of protection, but it should not replace proper output encoding.

# Day 8 Memory Map

```text
REFLECTED XSS

Input
  ↓
Request
  ↓
Server
  ↓
Reflection
  ↓
Context
  ↓
Browser Parsing
  ↓
Execution
  ↓
Impact
```

### Golden Rule

> **Don’t hunt for payloads first. Hunt for reflection, identify the context, then determine whether the browser can interpret the input as code.**

## Day 8 Status

**Understanding:** ✅  
**Conceptual questions:** ✅  
**Main corrections:** Server vs browser execution, context terminology, reflection vs confirmed XSS  
**Next practical step:** Complete at least one authorized Reflected XSS lab and document what happened in your own words.

**Week 2 — Day 8: Reflected XSS**
