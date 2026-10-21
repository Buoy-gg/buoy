---
title: Buoy Web
seoTitle: "Buoy Web — Ask Buoy in Chrome, and a web MCP for your AI coding tool"
id: buoy-web
description: "Buoy Web lets an AI use the web for you, with real clicks. Use Ask Buoy in Chrome's side panel, or let Claude Code, Cursor, Codex or VS Code use Buoy's own tabs. It never types your passwords."
---

Buoy Web lets an AI use the web for you. It clicks and types like a person. It only works in the tabs you give it. Your other tabs are left alone.

<!-- ::buoy-web-race -->

## Two ways to use it

| | Ask Buoy in Chrome | Your AI coding tool |
|---|---|---|
| **What it is** | A chat in Chrome's side panel | Claude Code, Cursor, Codex or VS Code use Chrome for you |
| **What you need** | The Buoy Web extension and a Buoy sign-in | The Buoy MCP. The extension is best, but not a must. |
| **Cost** | Uses AI credits. They come with paid plans. | Free. No account. |
| **Works on** | Every system | Mac for now |

## Let your AI set it up

Copy this prompt into Claude Code, Cursor or Codex. Your AI runs the setup and tells you the steps it can't do for you.

<!-- ::agent-install platform="buoy-web" where="buoy-web-docs" -->

## Get the extension

Get Buoy Web from the [Chrome Web Store](https://chromewebstore.google.com/detail/buoy-web/cfolfiajhlenfifknbomdogooagnlamf). Click **Add to Chrome**.

## Ask Buoy in Chrome

1. Add the Buoy Web extension to Chrome.
2. Go to the tab you want help with. Click the Buoy icon. The Ask Buoy panel opens on the side of that tab. Other tabs don't show it.
3. Click **Sign in with Buoy**. Then approve the code on buoy.gg.

Now ask it to do something, like "Find the weather in Provo this weekend." It works in that tab and tells you what it found. Its tabs sit in a green "Buoy" tab group. The page glows green while it works.

The first time it clicks or types on a site in a chat, it asks you. Pick **Always allow** for sites you trust. It also asks before it clicks a button with a word like Buy, Pay, Send, Post or Delete. It finds those buttons by their words, so it can miss some. Watch what it does on sites where it can spend money.

### Your rules

Open the panel's menu and pick **Settings**.

- **Just look:** Ask Buoy can open and read pages. It never clicks or types.
- **Stop asking:** it skips the site question. It still asks before it clicks a Buy, Send or Delete button. It never types passwords.
- **Always allowed:** the sites it acts on without asking. Remove one any time.

## Your AI coding tool

The prompt above does this for you. To do it by hand:

1. Run this once in a terminal:

   ```bash
   npx -y @buoy-gg/mcp@latest init --web
   ```

   It adds Buoy Web to Claude Code, Cursor, Codex and VS Code, if you have them. It works in every project.

2. Add the Buoy Web extension to Chrome.
3. Restart your editor.

Then ask your AI to do something on the web. When it's connected, the Ask Buoy panel's footer says "Editor bridge on".

No extension? Buoy Web still works with your AI tool. It opens its own Chrome window. Sign in to sites there once. It keeps those sign-ins in `~/.buoy/web-profile`.

## What it can do

Each step is a real mouse click or key press. The reply says what changed on the page. So the AI rarely needs to read the page again. That's what makes it fast.

The tools are `open`, `read`, `click`, `type`, `key`, `wait`, `scroll`, `drag`, `hover`, `select`, `tabs` and `dialog`. Your AI coding tool also gets `upload` and `screenshot`.

## Safety

- Ask Buoy only uses the tab you opened it on, plus tabs that tab opens. Your AI coding tool only uses tabs it opened. Neither one can see, switch to or close your other tabs.
- Buoy's tabs use your Chrome profile, so they have your sign-ins. The AI can act on sites you're signed in to.
- It never types into password boxes. It also skips code and card boxes when the site marks them. It won't send a form while one of those is empty. The AI asks you to type it on the page.
- It treats page text as words to read, never as orders.
- The extension only allows the Chrome calls Buoy needs. It refuses cookies, site storage and other tabs.
- `upload` only sends files inside your project folder. It never sends secret files like `.env` or keys.
- With your AI coding tool, your AI never waits on a step forever. After 60 seconds it stops waiting and says why.

Chrome shows a "started debugging this browser" bar while Buoy works. That's Chrome's rule for this kind of extension. If you click Cancel, Buoy stops. The AI asks before it tries again.

## Where your data goes

- With Ask Buoy, your messages and the pages it reads go to Buoy's AI service, so it can answer you.
- With your own AI tool, they go only to that tool on your computer. Your tool may send them to its AI provider.

See the [privacy policy](https://buoy.gg/privacy).

## If something goes wrong

- **"Chrome is closed":** open Chrome. Buoy connects again by itself.
- **The panel says "Editor bridge off":** run the `init --web` command again. Then wait a few seconds.
- **Two AI chats at once:** that's fine. Each one gets its own tabs.

## Usage count

Once a day, the Buoy MCP sends a random id, its version, your system type and a tag that says it's Buoy Web. It never sends web addresses, page text or what your AI did. Turn it off with `BUOY_TELEMETRY=0` or `DO_NOT_TRACK=1`. See [Telemetry](./telemetry).

## Works on

Google Chrome. Other browsers built on Chrome, like Brave and Edge, may work. We haven't tested them yet.

We measured our speed numbers in Claude Code. Claude Code loads Buoy Web's tools up front. Other editors may load them more slowly.
