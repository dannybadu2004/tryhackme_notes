# CyberChef - Hoperation Save McSkidy

## Overview
In this room, we explored the art of data manipulation using **CyberChef** and browser **Developer Tools**. The goal was to break through five security "locks" by identifying different encoding schemes, hidden headers, and cryptographic hashes to rescue McSkidy.

We learned the crucial difference between **Encoding** (transforming data for usability) and **Encryption** (transforming data for confidentiality).

---

## Key Concepts

### Encoding vs. Decoding
* **Encoding**: Changing data into a new format to ensure compatibility between different systems (e.g., sending binary images over text-based email using Base64). It provides **no security**.
* **Decoding**: Reversing the process to get the original data back.

| Feature | Encoding | Encryption |
| :--- | :--- | :--- |
| **Purpose** | Usability & Compatibility | Security & Confidentiality |
| **Key Required?** | No | Yes |
| **Example** | Base64, URL Encoding, Hex | AES, RSA, TLS |

### The Toolkit
1.  **CyberChef**: Known as the "Cyber Swiss Army Knife," it allows you to chain operations (Recipes) to manipulate data easily (e.g., `From Base64` -> `XOR` -> `To Hex`).
2.  **Browser Developer Tools**:
    * **Inspector**: View the HTML/CSS source code.
    * **Network Tab**: Analyze HTTP headers and requests.
    * **Debugger/Sources**: View JavaScript logic to understand how authentication works.
    * **Console**: Execute JavaScript and view errors.

---

## Technical Investigation (Breaking the Locks)

### Lock 1: The Outer Gate (Base64)
* **Recon**: We inspected the **Network** tab to find a "magic question" in the HTTP headers.
* **Action**: We encoded the guard's name and the magic question to Base64 in CyberChef.
* **Decoding**: The guard replied with a Base64 string. We used the `From Base64` operation to reveal the plaintext password.

### Lock 2: The Outer Wall (Nested Encoding)
* **Logic**: The **Debugger** tab revealed that the password was encoded *twice*.
* **Solution**: In CyberChef, we chained the decoding operation:
    `From Base64` -> `From Base64`.

### Lock 3: The Guard House (XOR)
* **Concept**: **XOR** is a bitwise operation. A unique property of XOR is that if you apply it twice with the same key, you get the original data back (`A XOR Key = B` -> `B XOR Key = A`).
* **Logic**: The script showed the password was XOR'd with a specific key and then Base64 encoded.
* **Solution**: We built a reverse recipe:
    `From Base64` -> `XOR` (using the extracted key).

### Lock 4: The Inner Castle (Hashing)
* **Concept**: **MD5** is a hashing algorithm. Unlike encoding or XOR, hashing is **one-way** (you cannot "decrypt" a hash).
* **Logic**: The guard provided an MD5 hash.
* **Solution**: Since we couldn't reverse it, we used **CrackStation** (a database of known hashes) to lookup the hash and find the original password.

### Lock 5: The Prison Tower (Dynamic Logic)
* **Challenge**: The decoding logic changed based on a `recipe_id` found in the HTTP Headers.
* **Scenario**: We found the recipe ID in the Network tab and matched it to the provided cheat sheet.
* **Example Recipe (ID 1)**: `From Base64` -> `Reverse` -> `ROT13`.

---

## Answers Summary

| Lock Level | Password | Flag / Note |
| :--- | :--- | :--- |
| **Lock 1** | `Iamsofluffy` | Simple Base64 |
| **Lock 2** | `Itoldyoutochangeit!` | Double Base64 |
| **Lock 3** | `BugsBunny` | Base64 + XOR |
| **Lock 4** | `passw0rd1` | MD5 Hash Crack |
| **Lock 5** | `51rBr34chBl0ck3r` | Dynamic Recipe |
| **Final Flag** | `THM{M3D13V4L_D3C0D3R_4D3P7}` | - |

---

## Lessons Learned
* **Always Check Headers**: Critical information (like tokens or magic questions) is often hidden in HTTP headers, invisible on the main page.
* **Client-Side Logic is Visible**: If security logic (like decoding instructions) is written in JavaScript and runs in the browser, the attacker can see it and reverse it.
* **Encoding is Not Encryption**: Using Base64 or Hex does not protect a password; it merely hides it from plain sight.
* **CyberChef is Essential**: The ability to quickly drag-and-drop decoding modules saves massive amounts of time during CTFs and investigations.

---
