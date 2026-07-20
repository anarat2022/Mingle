# Mingle

**Voice-based small talk practice — for the awkward pause after "how's it going?"**

Mingle drops you into a realistic small-talk scenario — a coffee shop line, a work hallway, a party — and lets you practice out loud with an AI conversation partner that behaves like a real person, not an infinitely patient chatbot. Give a dead-end reply and the conversation goes a little flat, the way it actually would. Give a good one and it opens up. After each exchange, a quiet coaching note explains why, and a debrief at the end pulls out real patterns from the conversation.

---

## Why this exists

A friend described a specific, recognizable moment: someone asks "how's it going?", she answers, and then it just stalls — she doesn't know how to keep it going. That's a narrow, well-defined skill gap (extending a reply, not making stiff conversation from scratch), and it's the kind of thing that's genuinely hard to practice — you can't rehearse small talk by reading about it, you need to actually do it and see what happens next.

## What it does

- **Realistic scenarios** — a handful of everyday categories (coffee shop, work, party, waiting room) or a random one, each with a freshly generated setting and opening line
- **A conversation partner with real reactions** — closed-off replies get a natural, slightly flatter response; engaged replies keep the exchange going, the way an actual person would react, not an assistant trying to be endlessly agreeable
- **In-the-moment coaching** — a short, specific, encouraging note after each reply: what worked, or one concrete way to extend it next time
- **A debrief, not just a transcript** — after the scene wraps (4–6 exchanges, like real small talk), a summary pulls out actual patterns from that specific conversation
- **Session history** — past scenes and their key takeaways are saved locally, so patterns across sessions become visible over time
- **Fully voice** — speak your replies, hear the character respond, no typing

## Architecture

![Mingle architecture diagram](./mingle-architecture.svg)

Three focused Claude calls handle the whole loop: one to open a scene, one per reply (which does double duty — stay in character *and* generate a coaching note), and one to write the debrief once the scene ends. Voice input runs on the browser's own speech recognition rather than a hosted transcription API, which means there's no external quota or extra service to manage on that side at all.

### Why the character reacts realistically instead of always being warm

An AI partner that's endlessly encouraging regardless of what you say isn't actually useful practice — it doesn't teach you anything, because there's no real feedback loop. The character is instructed to react the way a real person would: genuine engagement gets warmth back, a one-word closed-off answer gets a natural, slightly flatter beat. The stakes are real without ever tipping into rudeness — the goal is honest practice, not punishment.

### Why this reuses existing infrastructure

Rather than standing up a new backend, this points at a Cloudflare Worker originally built for a different project. The Worker's Claude proxy is fully generic — it just forwards whatever system prompt and conversation it's given — so a second, unrelated app could reuse it with zero backend changes. One Worker, multiple independent apps, no duplicated infrastructure.

## Tech stack

| Layer | Choice |
|---|---|
| Frontend | Vanilla HTML/CSS/JS, single file, no build step |
| Backend | Cloudflare Worker (serverless, shared with another project) |
| Conversation, coaching, debrief | Anthropic Claude API |
| Voice input | Web Speech API (browser-native recognition) |
| Voice output | Web Speech API (browser-native synthesis) |
| Persistence | `localStorage` — session history only |

## Running your own copy

1. **Get an Anthropic API key** — [console.anthropic.com](https://console.anthropic.com)
2. **Deploy a Worker** with a generic Claude-proxy route that forwards `system` + `messages` to the Anthropic API, holding the key server-side as a secret
3. **Point the app at it** — set `WORKER_URL` near the top of the script
4. **Host it** — any static file host works; no server-side code lives in the frontend itself

## What I'd build next

- A short warm-up mode with easier openers before harder scenarios
- Difficulty/persona variety — a more reserved character vs. a chattier one
- A lightweight trend view across sessions ("closed-off replies are down 40% this month")

---

Built for one person, to solve one specific, real problem.
