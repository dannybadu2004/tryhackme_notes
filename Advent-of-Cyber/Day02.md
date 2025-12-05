# Phishing – Merry Clickmass

### Definition
- Social engineering = manipulating humans to make mistakes
- Phishing = stealing information using fake messages and websites

---

### Psychological Triggers
- Urgency
- Authority
- Curiosity
- Fear
- Reward

---

### Phishing Types
- Email phishing
- Smishing (SMS)
- Vishing (Calls)
- Quishing (QR codes)
- Social DMs

---

### Anti-Phishing – S.T.O.P Method

#### Detection
- Suspicious message?
- Telling me to click something?
- Offering me an amazing deal?
- Pushing me to do something now?

#### Protection
- Slow down
- Type the address yourself. Don’t use the message’s link.
- Open nothing unexpected. Verify first.
- Prove the sender. Check the real From address/number, not just the display name.

---

### Fake Login Page
```bash
./server.py
```

Runs on:
```
http://0.0.0.0:8000
```

---

### Email Delivery (SET Toolkit)

Start the tool:
```bash
setoolkit
```

Menu path:
```
1 → Social-Engineering Attacks  
5 → Mass Mailer Attack  
1 → Single Email Target
```

---

### Example Phishing Setup

| Field | Value |
|--------|-------|
| To | factory@wareville.thm |
| From | updates@deershipping.thm |
| Subject | Shipping Schedule Changes |
| Link | http://ATTACKER_IP:8000 |

---

### Credential Harvesting
Credentials are captured and displayed directly in the terminal running `server.py`.

Password captured:
```
unranked-wisdom-anthem
```

---

### Lessons Learned
- Humans are the weakest security link
- Phishing bypasses many security systems
- Password reuse enables account takeover
- Users must be trained continuously

