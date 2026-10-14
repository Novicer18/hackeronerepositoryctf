# Hacker101 CTF - Postbook Writeup

This repository contains the complete walkthrough and step-by-step solutions for capturing all **7 flags** in the **Postbook** challenge on Hacker101 CTF.

---

## 🚩 Flag 0: Weak Authentication (Brute Force)
* **Vulnerability Class:** Broken Authentication
* **Walkthrough:**
  1. Navigate to the **Sign In** page of the application.
  2. Enter `user` as the username.
  3. Enter `password` as the password (a common default/weak credential).
  4. Submit the form. Upon successful authentication, the first flag will be displayed on your user dashboard.

---

## 🚩 Flag 1: IDOR (Viewing Private Posts)
* **Vulnerability Class:** Insecure Direct Object Reference (IDOR)
* **Walkthrough:**
  1. Log into your account and view any public post on the timeline.
  2. Examine the browser's address bar. The URL will look similar to: `.../post?id=2`.
  3. Manually change the `id` parameter value in the URL to a different integer (e.g., change `2` to `1` or `3`).
  4. Press **Enter**. You will bypass authorization checks and view a private post belonging to another user containing the flag.

---

## 🚩 Flag 2: Hidden Form Parameter Tampering
* **Vulnerability Class:** Mass Assignment / Parameter Tampering
* **Walkthrough:**
  1. Click on the button to **Create a new post**.
  2. Right-click inside the title or body field and select **Inspect Element** (or press `F12` to open Developer Tools).
  3. Look inside the HTML `<form>` tags for a hidden input field: `<input type="hidden" name="user_id" value="2">`.
  4. Double-click the `value` attribute and change it from your user ID (`2`) to the administrator's ID (`1`).
  5. Fill in dummy content for the title and body, then click submit. The platform will publish the post on behalf of the administrator and grant you the flag.

---

## 🚩 Flag 3: Guessing Deobfuscated Post IDs
* **Vulnerability Class:** Security through Obscurity / Information Disclosure
* **Walkthrough:**
  1. Solve the equation provided in the challenge hint: `189 * 5 = 945`.
  2. Navigate to the post-viewing page in your browser.
  3. Manually edit the URL query parameter to target the computed ID: `.../post?id=945`.
  4. Press **Enter** to access the hidden page index and view the flag.

---

## 🚩 Flag 4: IDOR (Unauthorized Post Editing)
* **Vulnerability Class:** Insecure Direct Object Reference (Bypassing Write Protections)
* **Walkthrough:**
  1. Find a post that you own and click the **Edit** button.
  2. Observe the URL format in the address bar: `.../edit?id=2`.
  3. Change the `id` parameter in the URL bar to an ID of a post you do not own (e.g., `.../edit?id=1`).
  4. Press **Enter**. The application will load the editing interface for the foreign post and expose the flag inside the text inputs.

---

## 🚩 Flag 5: Cookie Tampering & Session Hijacking
* **Vulnerability Class:** Broken Session Management / Weak Cryptographic Cookie
* **Walkthrough:**
  1. Open your browser's Developer Tools (`F12`) and navigate to the **Application** or **Storage** tab, then expand the **Cookies** section.
  2. Inspect the session cookie value. It will look like a 32-character hexadecimal string (e.g., `c81e728d9d4c2f636f067f89cc14862c`).
  3. Recognize that this string is an **MD5 hash** of your user ID (`2`).
  4. Generate a new MD5 hash for User ID `1` (The MD5 of `1` is `c4ca4238a0b923820dcc509a6f75849b`).
  5. Replace your active session cookie value with the new admin hash, refresh the page, and you will be authenticated as the admin with the flag visible.

---

## 🚩 Flag 6: Hashed Parameter Bypass (Delete Action)
* **Vulnerability Class:** Insecure Direct Object Reference (Obfuscated Identifiers)
* **Walkthrough:**
  1. Attempt to delete a post that you own while observing your browser's network traffic or inspecting the delete button link.
  2. Notice that the application identifies deletion targets using an MD5 hash of the post ID rather than a plain integer.
  3. Identify the post ID you want to target (e.g., Post ID `1`), and generate its MD5 hash (`c4ca4238a0b923820dcc509a6f75849b`).
  4. Force a manual delete action by structuring the URL or request parameters with the target post's hash string to execute unauthorized deletion and capture the final flag.
