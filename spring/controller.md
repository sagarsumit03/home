| Feature / Scenario | `@Controller` | `@RestController` |
|--------------------|--------------|-------------------|
| **Primary Use**    | Return **webpages** (HTML, JSP, Thymeleaf templates) | Return **JSON/XML** data |
| **Default Behavior** | Interprets return value as a **view name** (e.g., `return "hello"` → `hello.html`) | Writes return value directly to HTTP **response body** |
| **Returning Data** | Needs `@ResponseBody` to return raw data instead of a view | `@ResponseBody` is **implicit** on all methods |


---
| Feature / Aspect | `@ResponseBody` | `ResponseEntity<T>` |
|------------------|-----------------|---------------------|
| **What it is**   | Annotation | Class |
| **Purpose**      | Tells Spring: “Don’t try to find a view, just write the return value directly to the HTTP response body.” | Represents the full HTTP response, allowing control over body, headers, and status code. |
| **Controls Status Code** | ❌ (default 200, unless `@ResponseStatus` is used) | ✅ |

---

| Feature / Aspect | `@RequestBody` | `RequestEntity<T>` |
|------------------|----------------|--------------------|
| **What it is**   | Annotation | Class |
| **Purpose**      | Get only the **request payload** and convert it to a Java object. | Access the **entire HTTP request** — headers, method, URL, and body. |
| **Gives You**    | Body only (mapped to object) | Body + headers + HTTP method + URL |


---
```plaintext
┌──────────────────────────────────┐
│       Client (Browser/App)       │
└──────────────────────────────────┘
                  │ HTTP Request
                  ▼
┌──────────────────────────────────┐
│       Filter(s) [Servlet]        │ ← Auth, Logging, CORS, Compression
└──────────────────────────────────┘
                  │ Pass request
                  ▼
┌──────────────────────────────────┐
│        DispatcherServlet         │ ← Spring MVC Front Controller
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│     Interceptor(s): preHandle    │ ← Before Controller method
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│       Controller (Handler)       │ ← Handles request
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│    Interceptor(s): postHandle    │ ← After Controller, before View
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│    View Rendering / Response     │
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│ Interceptor: afterCompletion     │ ← After complete processing
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│       Filter(s) [Servlet]        │ ← Modify outgoing response
└──────────────────────────────────┘
                  │
                  ▼
┌──────────────────────────────────┐
│     Client Receives Response     │
└──────────────────────────────────┘
```
