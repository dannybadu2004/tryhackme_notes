# Race Conditions - Toy to The World

## Overview
In this room, we explored a critical vulnerability known as a **Race Condition**. This occurs when a web application attempts to handle multiple requests simultaneously but fails to synchronize them correctly.

We exploited a "Time-of-Check to Time-of-Use" (TOCTOU) flaw in the TBFC shopping application. By sending multiple purchase requests in parallel using **Burp Suite**, we were able to purchase more items than were actually in stock, driving the inventory into negative numbers.

---

## Key Concepts

### What is a Race Condition?
A situation where the system's output depends on the sequence or timing of other uncontrollable events.
* **Analogy**: Two people grab the last carton of milk from the shelf at the exact same second. The system checks stock (sees 1), allows Person A to buy, but before it updates the stock to 0, it allows Person B to buy as well.

### Types of Race Conditions
1.  **TOCTOU (Time-of-Check to Time-of-Use):** The system checks if a resource is available (Check) but the state changes before the system uses it (Use).
2.  **Atomicity Violation:** A process that should be indivisible (Atomic) is interrupted or run partially, leading to inconsistent states.

### The Toolkit: Burp Suite Repeater
We used **Burp Suite Professional/Community** to intercept and manipulate web traffic.
* **Repeater:** A tool that allows you to manually modify and resend individual HTTP requests.
* **Parallel Requesting:** A feature in Repeater that sends a group of requests at the exact same millisecond to stress-test the application's synchronization logic.

---

## Technical Investigation

### Step 1: Environment Setup
We configured Firefox to route traffic through **Burp Suite** (using FoxyProxy) and logged into the target application.

### Step 2: Capturing the Request
We performed a legitimate purchase of the "SleighToy". In Burp Suite, we located the `POST /process_checkout` request in the HTTP History.
* **Action:** Right-click -> `Send to Repeater`.

### Step 3: Preparing the Attack
In the **Repeater** tab:
1.  We created a **Tab Group** for the checkout request.
2.  We duplicated the request tab 15 times (creating 15 identical purchase orders).
3.  We set the send mode to **Send group in parallel (last-byte sync)**. This ensures all requests hit the server simultaneously.

### Step 4: Exploitation (The Race)
We clicked **Send group (parallel)**.
* **Result:** The server processed multiple requests before updating the stock count.
* **Outcome:** We bought more toys than existed, and the stock counter dropped to a negative value (e.g., -5), revealing the flag.

---

## Answers Summary

| Question | Answer |
| :--- | :--- |
| What is the flag value once the stocks are negative for SleighToy Limited Edition? | `THM{WINNER_OF_R@CE007}` |
| What is the flag value once the stocks are negative for Bunny Plush (Blue)? | `THM{WINNER_OF_Bunny_R@ce}` |

---

## Lessons Learned
* **Concurrency is Hard:** Web applications are multi-threaded. If developers don't lock the database row while updating stock, race conditions occur.
* **Atomic Transactions:** Critical operations (deduct stock -> process payment) must happen as a single, unbreakable unit.
* **Testing:** Standard functional testing often misses race conditions. You need specialized tools (like Burp Suite) to simulate high-concurrency attacks.

---
> **Key Takeaway**: In high-speed systems, checking availability is not enough; you must "lock" the item until the transaction is fully complete.
