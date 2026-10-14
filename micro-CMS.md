# Micro-CMS v1 Walkthrough - Hacker101 CTF

Congratulations on completing the **Micro-CMS v1** web security challenge! This repository contains a structured, easy-to-understand writeup detailing how to capture all 4 flags by exploiting critical web vulnerabilities.

## 🛠️ Overview of Vulnerabilities Covered
*   **Insecure Direct Object Reference (IDOR)**
*   **Stored Cross-Site Scripting (XSS)**
*   **SQL Injection (SQLi)**
*   **Markdown Sanitization Bypass (XSS)**

---

## 🚩 Flag 0: Insecure Direct Object Reference (IDOR)
* **Vulnerability Concept:** The application manages page navigation using sequential, guessing-friendly IDs in the URL parameter without proper authorization checks.

### 📝 Step-by-Step Solution:
1. Navigate to the main home page of the challenge instance.
2. Click on the first default page (**Testing**). Notice the URL structure ends in `/page/1`.
3. Go back to the homepage and click on the second page (**Markdown Test**). The URL reads `/page/2`.
4. Click **Create a new page**, fill out the fields, and save it. Notice that the new page skips logically to a much higher index parameter (e.g., `/page/7` or `/page/10`).
5. This gap indicates that hidden pages exist (e.g., page IDs 3, 4, 5, etc.). 
6. Attempting to directly visit a hidden page by modifying the URL path to `/page/3` returns a **403 Forbidden** error response.
7. To bypass this authorization control, navigate to the editing path of a valid page (e.g., `/page/edit/1`).
8. Replace the `1` in the URL with the forbidden page ID (e.g., `/page/edit/3` or `/page/edit/4`). 
9. **Result:** The authorization check is missing on the edit endpoint, revealing the page metadata alongside **Flag 0**.

---

## 🚩 Flag 1: Stored Cross-Site Scripting (XSS)
* **Vulnerability Concept:** The platform blindly reflects user-supplied content within the page title header on the main landing index without sanitization.

### 📝 Step-by-Step Solution:
1. On the main application workspace, click **Create a new page** (or choose to **Edit** an existing one).
2. Locate the **Title** input entry text box.
3. Inject a standard HTML `<script>` payload into the Title field instead of generic plain text:
   ```html
   <script>alert(1)</script>
   ```
4. Place any arbitrary text in the page content body section and click **Save**.
5. Return directly to the main homepage index path (`/`).
6. **Result:** The application fails to sanitize the title text before rendering it on the home page. The malicious JavaScript instantly fires an executable window popup, displaying **Flag 1** directly inside or behind the alert panel.

---

## 🚩 Flag 2: SQL Injection (SQLi)
* **Vulnerability Concept:** Database queries are directly concatenated with user URL parameters, allowing unescaped database manipulation.

### 📝 Step-by-Step Solution:
1. Click to **Edit** any existing webpage so your browser points to a backend location parameter (e.g., `/page/edit/1`).
2. Select the browser's top address search bar.
3. Escape the native query parameters by attaching a single quote (`'`) character to the end of the identifier sequence:
   ```text
   .../page/edit/1'
   ```
4. Hit **Enter** to pass the dirty string value directly into the application query engine.
5. **Result:** Because input parsing formatting constraints are completely absent, the underlying relational database throws a syntax execution exception, leaking **Flag 2** raw in the debug error message.

---

## 🚩 Flag 3: Stored XSS via Markdown / Event Handler
* **Vulnerability Concept:** The system attempts to block standard script tag strings inside markdown body regions but overlooks standard HTML attribute listeners.

### 📝 Step-by-Step Solution:
1. Go to the interface dashboard and select **Create a new page**.
2. Avoid standard `<script>` text identifiers since input validation logic blacklists or strips them inside the content block text area.
3. Instead, bypass the filtration logic by adding a native interactive HTML markup tag equipped with an autonomous event listener property:
   ```html
   <button onclick="alert(1)">Click Me</button>
   ```
4. Click **Save** and select the viewing reference pathway link for the new post.
5. Trigger the event loop cycle execution by manually clicking on your custom **"Click Me"** web button component.
6. If the pop-up box does not display the flag immediately, right-click the workspace layout and choose **Inspect Element** (or press `Ctrl` + `U` to **View Page Source**).
7. Look through the modified application Document Object Model (DOM) to capture **Flag 3** embedded within the processed layout tree.

---

*Disclaimer: This writeup is intended for educational and authorized CTF practice contexts only.*
