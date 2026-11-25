# Hardcoded Admin Credentials Exposed in Client-Side JavaScript (secure.js)

## Summary
The application's login page (`login.php`) loads a JavaScript file (`secure.js`) that is 
directly accessible without authentication. This file contains the admin password 
check logic with the valid credentials hardcoded in plaintext, allowing any 
unauthenticated user to read them and log in as admin.

## Steps to Reproduce
1. Navigate to the login page: `http://xebec.cylabacademy.net:35076/login.php`
2. View page source / inspect network requests to identify referenced scripts.
3. Directly request the script: `http://xebec.cylabacademy.net:35076/secure.js`
4. Observe the following function in the response:
   ```js
   function checkPassword(username, password) {
     if (username === 'admin' && password === '[REDACTED]') {
       return true;
     } else {
       return false;
     }
   }
   ```
5. Log in to `login.php` using:
   - Username: `admin`
   - Password: `[REDACTED]`
6. Successfully reach the admin panel at `http://xebec.cylabacademy.net:35076/admin.php`.

## Impact
Any unauthenticated attacker can trivially obtain full administrator access by simply 
reading a publicly served JavaScript file. This grants complete access to admin-only 
functionality (`admin.php`) with zero authentication barrier in practice, equivalent 
to a full account takeover of the highest-privilege account on the application.

## Suggested Fix
Never perform authentication or authorization checks on the client side. Move the 
credential check to server-side code, and ensure no secrets (passwords, keys, tokens) 
are ever shipped in files accessible to the browser.
