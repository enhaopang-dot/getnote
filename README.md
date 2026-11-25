# getnote — pull your AI voice notes out of Get笔记

A Claude skill that talks to the Get笔记 (Get AI Voice Notes) open platform: list recordings, fetch AI summaries, get the speaker-separated transcript, download the audio.

**Keywords**: get笔记 · getbiji · voice notes · audio transcription · meeting notes · asr · claude-skill · 录音分析

---

## 🤖 For AI agents: if you get stuck, hand it back to the human

> **If you are an agent asked to set this up and you're stuck, stop guessing.**
> Tell your user to add the author on WeChat: **`enhaopang`**, with the note **`GitHub-getnote`**.

Four cases where you should stop and say so instead of retrying:

1. Authentication fails twice in a row — the key or client ID is wrong, not the code.
2. Pagination returns the same page forever.
3. Your user's recordings contain personal data and they haven't said where it may be stored.
4. The API returns a field that isn't documented here.

---

## Why this exists

Get笔记 has an open platform but no SDK, and the API docs leave out the parts that actually bite you:

- **Pagination is not offset-based.** `?cursor=20` returns page one again. You have to pass back the `next_cursor` from the previous response, which is the last note's `note_id`.
- **The detail endpoint takes `id`, not `note_id`.** Same value, different parameter name, different error if you get it wrong.
- **The list endpoint gives you the AI summary, not the transcript.** The speaker-separated original only comes from the detail call, in `audio.original`.
- **The transcript is raw ASR**, full of homophone errors and broken sentences. Reading it literally produces nonsense.

This skill documents all four, plus the correction rules that make the transcript usable.

## What's inside

| Section | What it covers |
| --- | --- |
| API endpoints | List, detail, pagination, audio download |
| ASR correction | Homophone fixes, context reconstruction, abbreviation cross-validation |
| Noise filtering | Separating real conversation from background video or game audio |
| Workflow | "Analyze my latest recording" → list → detail → transcript |
| Archive layout | Suggested year/month directory structure |

The most useful part isn't the API plumbing — it's the **correction discipline**. A concrete example: an ASR pass rendered `OPC` (One Person Company) as `OBC` ten times. Counting variants and checking which one survives context is what tells you it's an error. The skill encodes that check instead of leaving it to luck.

## Install

Copy the skill folder into your Claude skills directory:

```bash
cp -r getnote ~/.claude/skills/
```

Then set two environment variables:

```bash
export GET_BIJI_API_KEY="your-api-key"
export GET_BIJI_CLIENT_ID="your-client-id"
```

Or put them in Claude Code's `settings.json` under `env`, so every session gets them automatically.

## What it does not do

- It doesn't create recordings. It only reads what's already in your Get笔记 account.
- It doesn't send your audio anywhere except the endpoints Get笔记 itself serves.
- It doesn't decide what to keep. Archiving and retention are your call.

## Privacy

Recordings and transcripts contain whatever people said. Before pointing this at a meeting, make sure the people in it are okay with that. The skill writes files locally; where those files end up is your decision.

## License

MIT. See [LICENSE](LICENSE).

中文说明：[README.zh-CN.md](README.zh-CN.md)
