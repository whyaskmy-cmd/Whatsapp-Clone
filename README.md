# Whatsapp-Clone
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>WhatsApp-Style Chat UI (Static)</title>
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
  <style>
    :root {
      --bg: #0b141a;                 /* app background */
      --panel: #111b21;              /* panel dark */
      --panel-2: #202c33;            /* headers / input */
      --border: #2a3942;             /* borders */
      --text: #e9edef;               /* primary text */
      --muted: #8696a0;              /* muted text */
      --accent: #00a884;             /* WhatsApp-like green */
      --bubble-in: #005c4b;          /* incoming bubble */
      --bubble-out: #1f2c33;         /* outgoing bubble */
      --unread: #25d366;             /* unread badge */
    }

    * { box-sizing: border-box; }
    html, body { height: 100%; }
    body {
      margin: 0;
      font-family: "Inter", system-ui, -apple-system, Segoe UI, Roboto, Arial, sans-serif;
      background: var(--bg);
      color: var(--text);
    }

    /* App layout */
    .app {
      height: 100dvh;
      display: grid;
      grid-template-columns: 380px 1fr;
      gap: 0;
      background:
        /* chat page paper texture */
        linear-gradient(0deg, rgba(11,20,26,0.96), rgba(11,20,26,0.96)),
        url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="320" height="320" viewBox="0 0 320 320"><g fill="%232a3942" fill-opacity="0.5"><circle cx="20" cy="20" r="2"/><circle cx="160" cy="100" r="2"/><circle cx="300" cy="60" r="2"/><circle cx="60" cy="300" r="2"/><circle cx="280" cy="220" r="2"/></g></svg>');
      background-size: cover, 440px 440px;
    }

    /* Sidebar */
    .sidebar {
      display: flex;
      flex-direction: column;
      border-right: 1px solid var(--border);
      background: var(--panel);
    }

    .side-header {
      display: flex;
      align-items: center;
      justify-content: space-between;
      gap: 12px;
      padding: 14px 16px;
      background: var(--panel-2);
      border-bottom: 1px solid var(--border);
    }

    .me {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .avatar {
      width: 36px; height: 36px;
      border-radius: 50%;
      background: linear-gradient(135deg, #3b82f6, #06b6d4);
      display: grid; place-items: center;
      font-weight: 700; color: white; font-size: 14px;
    }

    .side-actions {
      display: flex; gap: 10px;
    }

    .icon-btn {
      width: 36px; height: 36px;
      display: grid; place-items: center;
      background: transparent; border: none; color: var(--text);
      border-radius: 999px; cursor: pointer;
    }
    .icon-btn:hover { background: #2a394233; }

    .search {
      padding: 8px 12px;
      border-bottom: 1px solid var(--border);
      background: var(--panel);
    }

    .search input {
      width: 100%;
      padding: 10px 12px;
      border-radius: 10px;
      border: 1px solid var(--border);
      background: #0b141a;
      color: var(--text);
      outline: none;
    }
    .search input::placeholder { color: var(--muted); }

    .chat-list { overflow: auto; }

    .chat-item {
      display: grid;
      grid-template-columns: 56px 1fr auto;
      gap: 12px; align-items: center;
      padding: 10px 14px;
      cursor: pointer;
      border-bottom: 1px solid var(--border);
    }

    .chat-item:hover { background: #111b21; }

    .chat-item.active { background: #1a242b; }

    .chat-item .avatar { width: 44px; height: 44px; }

    .chat-meta { display: grid; gap: 4px; }
    .chat-meta .name { font-weight: 600; }
    .chat-meta .preview { color: var(--muted); font-size: 13px; overflow: hidden; white-space: nowrap; text-overflow: ellipsis; }

    .chat-time { text-align: right; display: grid; gap: 8px; }
    .time { font-size: 12px; color: var(--muted); }
    .badge { place-self: end; min-width: 20px; height: 20px; background: var(--unread); color: #0b141a; border-radius: 999px; display: grid; place-items: center; font-size: 12px; font-weight: 700; padding: 0 6px; }

    /* Chat area */
    .chat {
      display: grid;
      grid-template-rows: auto 1fr auto;
      background:
        linear-gradient(0deg, rgba(32,44,51,0.25), rgba(32,44,51,0.25)),
        url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="400" height="400" viewBox="0 0 400 400"><defs><pattern id="p" width="80" height="80" patternUnits="userSpaceOnUse"><path d="M0 40h80M40 0v80" stroke="%232a3942" stroke-width="1" opacity="0.35"/></pattern></defs><rect width="100%" height="100%" fill="url(%23p)"/></svg>');
      background-size: cover, 500px 500px;
    }

    .chat-header {
      display: flex; align-items: center; justify-content: space-between;
      gap: 12px; padding: 10px 16px; background: var(--panel-2);
      border-bottom: 1px solid var(--border);
    }

    .peer { display: flex; align-items: center; gap: 12px; }
    .peer .avatar { width: 36px; height: 36px; }
    .peer .name { font-weight: 700; }
    .peer .status { font-size: 12px; color: var(--muted); }

    .chat-actions { display: flex; gap: 10px; }

    .messages { padding: 16px; overflow: auto; display: grid; gap: 12px; align-content: start; }

    .bubble {
      max-width: min(72ch, 75%);
      padding: 8px 10px 6px 10px;
      line-height: 1.35;
      border-radius: 12px;
      position: relative;
      word-wrap: break-word;
      font-size: 15px;
      box-shadow: 0 1px 0 rgba(0,0,0,0.15);
    }

    .in { justify-self: start; background: var(--bubble-in); border-top-left-radius: 4px; }
    .out { justify-self: end; background: var(--bubble-out); border-top-right-radius: 4px; }

    .meta {
      display: inline-flex; align-items: center; gap: 6px;
      font-size: 11px; color: var(--muted);
      margin-left: 8px;
    }

    .ticks { display: inline-flex; gap: 2px; vertical-align: middle; }
    .ticks svg { width: 14px; height: 14px; }

    .day-sep { justify-self: center; background: #1f2c33; color: var(--muted); padding: 6px 10px; border-radius: 999px; font-size: 12px; margin: 8px 0 2px; }

    .chat-input {
      padding: 10px; background: var(--panel-2); border-top: 1px solid var(--border);
      display: grid; grid-template-columns: auto 1fr auto; gap: 10px; align-items: center;
    }

    .chip { background: var(--panel); border: 1px solid var(--border); color: var(--muted); font-size: 13px; padding: 6px 10px; border-radius: 999px; }

    .field-wrap { position: relative; }
    .field {
      width: 100%;
      resize: none; border: 1px solid var(--border); outline: none;
      background: var(--panel); color: var(--text);
      padding: 12px 48px 12px 12px; border-radius: 12px; min-height: 44px; max-height: 120px;
    }
    .field::placeholder { color: var(--muted); }

    .send-btn {
      display: grid; place-items: center;
      width: 44px; height: 44px; border-radius: 12px;
      border: 1px solid var(--border); background: var(--accent); color: #052419;
      font-weight: 700; cursor: not-allowed; opacity: 0.9;
    }

    .right-inside-icon {
      position: absolute; right: 10px; top: 50%; transform: translateY(-50%);
      width: 24px; height: 24px; display: grid; place-items: center; pointer-events: none; opacity: 0.8;
    }

    /* Mobile */
    @media (max-width: 900px) {
      .app { grid-template-columns: 1fr; }
      .sidebar { display: none; }
      .chat-header .back { display: inline-flex; }
    }

    @media (min-width: 901px) {
      .chat-header .back { display: none; }
    }

    /* Utilities */
    .sr-only { position: absolute; width: 1px; height: 1px; padding: 0; margin: -1px; overflow: hidden; clip: rect(0,0,0,0); white-space: nowrap; border: 0; }
    .mono { font-variant-numeric: tabular-nums; }
    a.clean { color: inherit; text-decoration: none; }
  </style>
</head>
<body>
  <div class="app" role="application" aria-label="WhatsApp-style chat interface (static demo)">
    <!-- Sidebar -->
    <aside class="sidebar" aria-label="Chat list">
      <header class="side-header">
        <div class="me">
          <div class="avatar" aria-hidden="true">AS</div>
          <div>
            <div style="font-weight:700">Ali Shahid</div>
            <div style="font-size:12px;color:var(--muted)">Online</div>
          </div>
        </div>
        <div class="side-actions" aria-label="Actions">
          <button class="icon-btn" title="New chat" aria-label="New chat">➕</button>
          <button class="icon-btn" title="Menu" aria-label="Menu">⋮</button>
        </div>
      </header>

      <div class="search">
        <label class="sr-only" for="q">Search chats</label>
        <input id="q" type="search" placeholder="Search or start a new chat" />
      </div>

      <nav class="chat-list" aria-label="Chats">
        <div class="chat-item active">
          <div class="avatar" aria-hidden="true">JD</div>
          <div class="chat-meta">
            <div class="name">Jordan</div>
            <div class="preview">You: Let’s meet at 5? I’ll bring the docs.</div>
          </div>
          <div class="chat-time">
            <div class="time mono">09:43</div>
            <div class="badge" aria-label="2 unread messages">2</div>
          </div>
        </div>

        <div class="chat-item">
          <div class="avatar" aria-hidden="true">DZ</div>
          <div class="chat-meta">
            <div class="name">Design Team</div>
            <div class="preview">Aisha: Final screens uploaded to Drive ✅</div>
          </div>
          <div class="chat-time">
            <div class="time mono">08:10</div>
          </div>
        </div>

        <div class="chat-item">
          <div class="avatar" aria-hidden="true">UM</div>
          <div class="chat-meta">
            <div class="name">Umer</div>
            <div class="preview">Voice call ended • 03:12</div>
          </div>
          <div class="chat-time">
            <div class="time mono">Yesterday</div>
          </div>
        </div>

        <div class="chat-item">
          <div class="avatar" aria-hidden="true">FM</div>
          <div class="chat-meta">
            <div class="name">Family</div>
            <div class="preview">Mom: Beta, dinner at 8 🙂</div>
          </div>
          <div class="chat-time">
            <div class="time mono">Sun</div>
            <div class="badge">5</div>
          </div>
        </div>

        <div class="chat-item">
          <div class="avatar" aria-hidden="true">PT</div>
          <div class="chat-meta">
            <div class="name">Project - Titan</div>
            <div class="preview">Reminder: standup moved to 11:30</div>
          </div>
          <div class="chat-time">
            <div class="time mono">Sat</div>
          </div>
        </div>
      </nav>
    </aside>

    <!-- Chat Area -->
    <section class="chat" aria-label="Conversation">
      <header class="chat-header">
        <button class="icon-btn back" aria-label="Back">←</button>
        <div class="peer">
          <div class="avatar" aria-hidden="true">JD</div>
          <div>
            <div class="name">Jordan</div>
            <div class="status">online • typing…</div>
          </div>
        </div>
        <div class="chat-actions">
          <button class="icon-btn" title="Search in chat" aria-label="Search in chat">🔎</button>
          <button class="icon-btn" title="Voice call" aria-label="Voice call">📞</button>
          <button class="icon-btn" title="Video call" aria-label="Video call">🎥</button>
          <button class="icon-btn" title="Menu" aria-label="Menu">⋮</button>
        </div>
      </header>

      <div class="messages">
        <div class="day-sep">Today</div>

        <div class="bubble in">
          Hey Ali! Did you get a chance to review the mockups?
          <span class="meta"><span class="mono">09:41</span></span>
        </div>

        <div class="bubble out">
          Yep, looks solid. I’ve added a few comments on spacing and color.
          <span class="meta"><span class="mono">09:42</span>
            <span class="ticks" aria-label="Delivered, read">
              <!-- double tick icon -->
              <svg viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M1 14l2-2 5 5-2 2zM7 14l2-2 5 5-2 2z"/></svg>
              <svg viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M7 14l2-2 5 5-2 2z"/></svg>
            </span>
          </span>
        </div>

        <div class="bubble in">
          Nice. Could you also prepare a static demo of the chat UI for stakeholders?
          <span class="meta"><span class="mono">09:43</span></span>
        </div>

        <div class="bubble out">
          Already on it. Here’s a non-functional clone using only HTML & CSS.
          <span class="meta"><span class="mono">09:43</span>
            <span class="ticks" aria-label="Delivered, read">
              <svg viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M1 14l2-2 5 5-2 2zM7 14l2-2 5 5-2 2z"/></svg>
              <svg viewBox="0 0 24 24" aria-hidden="true"><path fill="currentColor" d="M7 14l2-2 5 5-2 2z"/></svg>
            </span>
          </span>
        </div>

        <div class="bubble in">
          Perfect. Send over the file when ready.
          <span class="meta"><span class="mono">09:44</span></span>
        </div>

      </div>

      <footer class="chat-input" aria-label="Message input (disabled demo)">
        <span class="chip" aria-hidden="true">😊</span>
        <div class="field-wrap">
          <label for="msg" class="sr-only">Type a message</label>
          <textarea id="msg" class="field" placeholder="Type a message" disabled></textarea>
          <span class="right-inside-icon" aria-hidden="true">📎</span>
        </div>
        <button class="send-btn" title="Send (disabled)" aria-disabled="true">➤</button>
      </footer>
    </section>
  </div>
</body>
</html>
