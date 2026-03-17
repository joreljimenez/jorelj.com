# Personal Website — Developer Notes

## Project Overview
Static portfolio/CV site for Jorel Jimenez. Access is restricted by a client-side password system using SHA-256 hashes and a session cookie.

---

## Authentication System

Passwords are stored as SHA-256 hex hashes in a `PASSWORD_HASHES` array. When a user logs in, the browser hashes their input using the Web Crypto API and checks it against the array. A `jj_auth` cookie is set on success (7-day expiry).

**The hash array exists in TWO places — both must be kept in sync:**

| File | Line |
|------|------|
| `index.html` | Line 8–11 (auth guard, runs before page load) |
| `login.html` | Line 156–159 (login form handler) |

---

## How to Add a New Password

### Step 1 — Generate the SHA-256 hash

Open any browser console (F12 → Console) and run:

```js
crypto.subtle.digest('SHA-256', new TextEncoder().encode('your-password-here'))
  .then(buf => console.log([...new Uint8Array(buf)].map(b => b.toString(16).padStart(2,'0')).join('')))

  echo -n 'NEW_PASSWORD' | sha256sum

```

Copy the 64-character hex string that appears.

### Step 2 — Add the hash to both files

In `index.html` (line 9), add a new line inside the array:
```js
var PASSWORD_HASHES = [
  '8529ab6899015263573df683e57a9de269d60d38f9a5493152e1f53e8c5c2730', // mitchell
  'YOUR_NEW_HASH_HERE', // label (person/use-case, NOT the password itself)
  // add more hashes here
];
```

Repeat the same change in `login.html` (line 157).

### Step 3 — Commit
```
git add index.html login.html
git commit -m "Add password for <person/use-case>"
```

**Never commit plaintext passwords. Labels in comments are fine (name/use-case only).**

---

## Current Passwords

| Label | Notes |
|-------|-------|
| mitchell | Original password |

---

## Removing a Password

Delete the corresponding hash line from both files and commit. The cookie for that password will immediately stop working.
