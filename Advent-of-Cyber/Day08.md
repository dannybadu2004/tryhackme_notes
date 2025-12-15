# Prompt Injection - Sched-yule conflict

## Overview
In this room, we explored how a **Large Language Model (LLM)** can become a security risk when it is
integrated with external functions and system permissions.
Although the AI was not a fully autonomous agent, the way it was implemented caused it to behave
like an **agent-like AI**, making it vulnerable to exploitation.

The goal was to restore the SOC-mas calendar by abusing weaknesses in how the AI exposed its internal reasoning
and tool-calling capabilities.

---

## LLM vs Agentic AI (Important Distinction)

### Large Language Model (LLM)
- Generates text-based responses
- Uses internal reasoning (Chain-of-Thought)
- Cannot act on systems by itself
- Has no real-world access

### Agent-Like AI (LLM + Tools)
- LLM connected to functions / APIs
- Can execute actions (e.g. reset data, read logs)
- Maintains state
- Acts based on user prompts

The system in this room was an **LLM with tool-calling**, not a fully autonomous agent,
but it behaved like one due to poor security design.

---

## Chain-of-Thought (CoT) Exposure

The application exposed the AI’s **Chain-of-Thought (Thinking section)**.
This is a serious security issue because it reveals:
- Internal reasoning
- Available functions
- Access control logic
- Required parameters (e.g. tokens)

Exposing CoT is equivalent to leaking internal debug logs in a production system.

---

## Discovery Phase

By interacting with the chatbot and observing the Thinking section:
- Internal functions were revealed
- Tool names became visible
- Authorization requirements were exposed

Discovered functions included:
- `reset_holiday`
- `booking_a_calendar`
- `get_logs`

This was a clear case of **information disclosure**.

---

## Access Control Weakness

Attempting to execute `reset_holiday` failed due to a missing token.
This confirmed that:
- Access control existed
- But it relied on a token that could potentially be leaked

---

## Prompt Injection via Tool Use

The `get_logs` function was used as an attack vector.
By carefully crafting prompts that influenced the model’s reasoning,
the AI was manipulated into revealing sensitive internal data.

A prompt requesting execution of `get_logs` while prioritizing token output
caused the model to leak the hidden access token:

```
TOKEN_SOCMAS
```

This demonstrates **prompt injection against an LLM-based system**.

---

## Exploitation & Goal Achievement

With the leaked token, the restricted function was executed successfully:
- `reset_holiday` was called with a valid token
- The calendar was restored to Christmas (December 25)

This completed the objective and restored SOC-mas.

---

## Flag

```
THM{XMAS_IS_COMING__BACK}
```

---

## Security Lessons Learned

- LLMs are not inherently dangerous; **integration flaws are**
- Chain-of-Thought must never be exposed to users
- Tool-calling requires strict server-side validation
- Tokens and secrets must never be returned in model output
- LLMs should not decide what internal data is safe to display
- Agent-like behaviour introduces insider-level risk

---

## Key Takeaway

> The vulnerability was not in the AI model itself, but in how an LLM was integrated with tools and exposed internal reasoning, effectively turning it into an exploitable agent-like system.
