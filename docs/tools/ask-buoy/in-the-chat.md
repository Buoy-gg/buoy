---
title: "Ask Buoy: in the chat"
seoTitle: "Ask Buoy chat — approvals, undo, checks, saved chats and Desktop"
id: tools-ask-buoy-in-the-chat
description: "How the Ask Buoy chat works day to day: approval cards, checks after each change, read-only mode, Stop and the message queue, agent thinking, saved chats and Buoy Desktop."
---

Start with the [Ask Buoy](../ask-buoy) page. This page explains what you see in the chat.

## Approvals

Big changes, like wiping storage, show a card first. The card says what will happen, like "Clear all saved app data". You tap to say yes or no.

- **Add a note.** Type *"just the second line"* before you tap **Not now**. The AI gets your words as you typed them.
- **Allow for this chat.** Says yes and stops asking about that action until the chat ends. Ten storage writes take one tap, not ten. Rules like `readOnly`, `deny` and release-build refusals still apply.
- **Settings → Permissions** lists what you allowed. Tap **Ask again** to undo one. A new chat forgets all of them.
- Long card text hides behind **Details**, so the buttons are always in reach.
- A card you didn't answer comes back after a reload. **Allow** reads the value again first. It refuses if the value changed since.

## It checks its own work

"The tool said ok" doesn't mean the app shows it. So after a change, Ask Buoy reads the app again. Each change row then says one of these:

- **Verified.** The change is really there.
- **Done · not yet visible.** The change went in, but the app hasn't shown it yet. Say it faked a web call that the app hasn't made yet. The AI is told what would show it, like a refresh, before it can say it's done.
- **Done · check failed.** The app doesn't have what was written. The AI may try once more with a different change. It never repeats the same one or says it worked.

Tap a row to see why. Today this works for storage, store and cache edits, screen changes and fake web calls.

## Undo

The changes bar counts what Ask Buoy changed. Storage and query cache edits can be undone, because it reads the old value first. If the app has since loaded fresh cache data, Undo leaves it and says so. A state change with no old value is marked **permanent**. Screen changes aren't counted. Typing *"undo that"* uses the same list as the bar.

## Two switches in Settings

- **Read only** (Settings → Permissions). Refuses every change until you turn it off. The header says **Read only** while it's on. Use it on a shared or support phone. It only adds limits to your `policy`, never removes them. It starts on the very next step. Undo still works.
- **Skip approval prompts** (Settings → Permissions). Every action runs right away, wipes too. Use it on your own dev phone when you want to move fast. `readOnly`, `deny` and release-build refusals still apply. The changes bar still tracks everything. It stays on after restarts, and the row stays amber so you notice.

## While it works

- **Keep typing.** Messages you send while it works wait in a list above the box. Tap one to edit it, or ✕ to drop it. They send one at a time as each answer ends. The list also waits after an error, after Stop, and while a question or approval is open. A waiting message is a new task, not an answer.
- **Stop** stops before the next step. A step already running finishes and keeps its result. Skipped steps show "Not run — stopped". **Try again** keeps the stopped try above the new one.
- **Questions are buttons.** When it needs you to pick, you get buttons to tap. If it asks in plain text anyway, Buoy turns that into a card. You can still type instead.
- **Peek.** When it taps or changes screens, the sheet shrinks for a moment so you see the app. Shrink the sheet and it keeps going. If it then needs a tap, its chip in the dock gets a **!**. Closing the sheet stops the answer.

## Reading an answer

- Reads in a row fold into one line, like *Looked at Network and Storage · 3 reads*. Each change, failure, refusal and "no" gets its own line.
- A small line under each answer shows how long it took and how many steps ran.
- Long tables say when they're cut and offer **Show all**.
- **Big results.** The AI only sees the first 24,000 characters of a result. The whole result is kept for the chat, and the AI can read any part of it again. So *"what did the server send for the third item?"* still works. It's kept in memory only, never on disk.

## Copy, tickets and thinking

- **Copy conversation** is under each answer and in the header. It copies the whole chat, cards too, as plain text.
- **Summarize for a ticket.** The document button in the header makes one card with steps, expected, what happened, build, the proof it saw and what's still changed. Guesses get their own row. Tap **Copy finding** to copy it. Nothing runs or changes to make it.
- **Show agent thinking** (Settings → Chat). Each answer gets a closed strip, like `2 steps · 1 thought · 3.2s · 6210 tokens`. Open it to see the AI's thinking and each step in order. Tap a step to see what it **sent** and what it **returned**. This is how you spot a step that says `ok` but returned `{"ok": false, "error": "no such key"}`. It's off by default. Not every model shares its thinking. Then the strip shows steps only, and says so.

With thinking on, **Copy conversation** includes the steps too:

```text
You: show my cart
  [thinking]
    I should read the bag store first.
  [1] zustand.getStoreState — Done
      {"storeName":"Poké Mart bag"}
Ask Buoy: Here are the items in your cart.
```

## Saved chats

The chat comes back after a reload, a restart or a crash. The AI still remembers it, so *"undo that"* still works.

- An answer that was cut off by a crash comes back marked as stopped.
- Messages that were waiting come back as unsent.
- An approval you didn't answer comes back as a card that says it's from before the restart.
- In a very long chat, the AI forgets the oldest parts first. A small line marks where. Parts with changes still in place go last, so *"turn that off"* keeps working.

**New conversation** deletes it. `persistTranscript: false` turns saving off. See [what stays on the phone](../ask-buoy#what-it-sends-and-what-it-saves).

## Watch from Buoy Desktop

If the phone is also connected to [Buoy Desktop](../../desktop), the chat shows up there too. You see it stream, what it changed, whether each change can be undone and how many tokens it used. You can follow a tester from your own desk.

Desktop has two buttons. **Undo everything reversible** puts back what it can. **Reset** undoes first, and it won't clear if an undo fails. That way you keep the record of what's still changed.

You can't start a chat from Desktop. Start it on the phone. Only use the broker on a dev network you trust. Signing in to it doesn't stop one person from changing another person's phone.

## Troubleshooting

- **A strange error on the first message.** Your `endpoint` and `protocol` don't match. Ask Buoy warns you when it can tell.
- **"Endpoint busy — retrying in …".** The AI company is busy or hit a limit. Ask Buoy waits and tries again by itself. It does this only when nothing came back yet, so no step ever runs twice.
- **"Couldn't reach your AI endpoint".** The connection dropped. Tap **Retry**. If it keeps happening, a work proxy may be holding back the stream.
- **"This conversation is too large for the AI endpoint".** The AI's memory is full and there was nothing old to trim. Start a new chat, or ask a shorter question. Ask Buoy trims old parts by itself first, so this is rare.
- **The answer shows up all at once.** React Native's own `fetch` can't stream. Buoy switches to a different way after the first answer. On Expo, you can also use `expo/fetch` as the global `fetch`.
- **"does not work in this build".** That action needs a dev build. It's not a bug.
