# AI in Security - old sAInt nick

## Overview
In this room, we explored how **Artificial Intelligence (AI)** is being integrated into cyber security.
We looked at how AI supports defensive and offensive security, software development, and the risks
that must be considered when deploying AI-driven solutions.

---

## AI in Cyber Security

Artificial Intelligence is increasingly used to:
- Speed up investigations
- Process large volumes of data
- Assist humans, not replace them

AI is now embedded into daily workflows rather than being used only for quick questions or searches.

---

## Why AI is Useful in Cyber Security

| AI Feature | Cyber Security Benefit |
|-----------|------------------------|
| Large-scale data processing | Analysing massive log, network, and endpoint data |
| Behaviour analysis | Detecting abnormal activity compared to normal behaviour |
| Generative AI | Summarising incidents and providing investigation context |

---

## Defensive Security (Blue Team)

AI is widely used in blue team operations to:
- Detect threats faster
- Analyse alerts with added context
- Assist incident response

Examples:
- AI-assisted firewalls
- Intrusion Detection Systems (IDS)
- Automatic isolation of infected machines
- Blocking suspicious emails in real time
- Flagging unusual login behaviour

AI acts as an **automated assistant**, not a decision-maker.

---

## Offensive Security (Red Team)

AI helps penetration testers by:
- Automating reconnaissance and OSINT
- Analysing noisy scan results
- Mapping attack surfaces faster

This allows human testers to focus on:
- Exploitation logic
- Creative attack paths
- Manual verification

AI increases speed but does not replace human judgement.

---

## AI in Software Development

AI-assisted development can:
- Help brainstorm code logic
- Act as a virtual coding assistant
- Scan code for vulnerabilities

Common uses:
- SAST (Static Application Security Testing)
- DAST (Dynamic Application Security Testing)

Important:
AI can detect vulnerabilities well,  
but it is **not reliable at writing secure code by itself**.

---

## Considerations When Using AI

AI is **not a silver bullet**. Key risks include:
- AI outputs are not guaranteed to be correct
- Poor training data leads to poor results
- AI decisions may lack transparency
- Offensive AI can accidentally cause outages
- Privacy and data protection concerns
- AI models themselves can be attacked

All AI output must be **verified by humans**.

---

## Practical AI Showcase

The room included three practical AI use cases:

### Red Team
- Generate and execute an exploit
- SQL injection exploit used against a vulnerable web app

### Blue Team
- Analyse web logs of an attack
- Identify attacker behaviour

### Software
- Analyse source code for vulnerabilities

---

## Flags Collected

| Task | Flag |
|----|----|
| Complete AI showcase | **THM{AI_MANIA}** |
| Execute SQLi exploit | **THM{SQLI_EXPLOIT}** |

---

## Lessons Learned
- AI enhances speed, not trust
- Humans remain responsible for decisions
- AI is powerful in detection and analysis
- Verification is mandatory
- Poor AI usage can cause real damage
- Secure AI deployment requires caution and oversight
