# Quickstart (Node.js)

Use this guide when you want to run Whisp from a **trusted server environment** — a backend API, a background worker, a CLI tool, an integration test, or a bot. In these environments it’s safe to hold the API key directly, so you can skip the backend-proxy dance from the [Browser Quickstart](/sdks/js/quickstart-browser) and call `registerUser()` / `signIn()` directly.

## What you’ll build

By the end of this guide you’ll have a Node.js script that:

1. Registers a new user
2. Signs them in and receives tokens
3. Creates a chat
4. Connects to realtime
5. Sends a message and listens for it coming back

---

## Step 1 — Install the SDK (+ `ws`)

```bash
npm install whisp-sdk ws
```

Why `ws`? Node.js historically has no built-in WebSocket client, and the SDK intentionally doesn’t bundle one (so browser users don’t pay for it). `ws` is the de-facto standard. See [Installation](/sdks/js/installation) for the full explanation.

---

## Step 2 — Construct the client

```ts
import { WhispClient } from "whisp-sdk";
import WebSocket from "ws";

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY!, // from client.whispchat.com
  webSocketImpl: WebSocket,            // required for realtime in Node.js
});
```

:::scalar-callout{type="info"}
If this script only makes REST calls (e.g. a webhook handler that just looks up chats), you can **omit `webSocketImpl`**. It’s only consulted when you call `whisp.realtime.connect()`.
:::

---

## Step 3 — Register a user

:::scalar-callout{type="warning"}
`registerUser()` and `signIn()` require the `x-api-key` header. Calling them will throw a clear `WhispError` if you forgot to pass `apiKey` in the constructor. Never call them from browser code.
:::

```ts
await whisp.registerUser({
  username: "johndoe",
  email: "john.doe@example.com",
  password: "SecureP@ss123",
  firstName: "John",    // optional
  surName: "Doe",       // optional
});
```

---

## Step 4 — Sign in

`signIn()` returns the user’s profile and **automatically stores** the JWT, refresh token, and user ID inside the SDK. You don’t need to call `setAuth()` yourself.

```ts
const user = await whisp.signIn({
  username: "johndoe",
  password: "SecureP@ss123",
});

console.log(`Signed in as ${user.username} (${user.id})`);
console.log("Authenticated?", whisp.isAuthenticated); // true
```

---

## Step 5 — Create a chat

```ts
const chat = await whisp.createChat("Bot testing", ["johndoe", "janedoe"]);
console.log(`Created chat ${chat.chatId}`);
```

---

## Step 6 — Connect realtime

```ts
await whisp.realtime.connect();

whisp.realtime.on("connected", () => console.log("✓ realtime connected"));
whisp.realtime.on("disconnected", ({ reason, willReconnect }) => {
  console.log(`✗ realtime disconnected: ${reason}`, willReconnect ? "(retrying)" : "");
});

whisp.realtime.on("message", (evt) => {
  console.log(`[${evt.chatId}] ${evt.senderId}: ${evt.message}`);
});
```

---

## Step 7 — Send a message

```ts
whisp.realtime.sendMessage(chat.chatId, "Hello from Node.js!");
```

Because Whisp echoes every message back to the sender, the `message` listener above will fire for your own send — so you can use that as a “persisted in the backend” signal.

---

## Full script

Here’s a complete, self-contained example you can drop into a single file (e.g. `demo.ts`) and run with `tsx demo.ts`. It registers two fresh users (`alice` and `bob`), signs in as `alice`, creates a chat that includes both of them, connects realtime, sends a message, waits for the echo, and cleans up.

```ts
import { WhispClient, WhispError } from "whisp-sdk";
import WebSocket from "ws";

const PASSWORD = "SecureP@ss123";

// Use a timestamp suffix so the script is idempotent —
// you can run it repeatedly without username collisions.
const suffix = Date.now();
const ALICE = `alice-${suffix}`;
const BOB   = `bob-${suffix}`;

const whisp = new WhispClient({
  baseUrl: "https://demo.api.whispchat.com",
  apiKey: process.env.WHISP_API_KEY!,
  webSocketImpl: WebSocket,
});

async function registerIfMissing(username: string, email: string) {
  try {
    await whisp.registerUser({ username, email, password: PASSWORD });
    console.log(`✓ registered ${username}`);
  } catch (err) {
    // 409 = username/email already taken — fine for re-runs
    if (err instanceof WhispError && err.status === 409) {
      console.log(`• ${username} already exists, continuing`);
      return;
    }
    throw err;
  }
}

async function main() {
  // 1. make sure both users exist
  await registerIfMissing(ALICE, `${ALICE}@example.com`);
  await registerIfMissing(BOB,   `${BOB}@example.com`);

  // 2. sign in as alice — SDK stores the JWT automatically
  const me = await whisp.signIn({ username: ALICE, password: PASSWORD });
  console.log(`✓ signed in as ${me.username} (${me.id})`);

  // 3. connect realtime and attach listeners BEFORE we send anything
  await whisp.realtime.connect();
  console.log("✓ realtime connected");

  // Use a Promise so main() can wait for the echo before exiting.
  const echoReceived = new Promise<void>((resolve) => {
    const off = whisp.realtime.on("message", (evt) => {
      console.log(`← [${evt.chatId}] ${evt.senderId}: ${evt.message}`);
      off();       // unsubscribe after the first echo
      resolve();
    });
  });

  // 4. create a brand new chat with alice and bob
  const chat = await whisp.createChat(
    `demo-${suffix}`,
    [ALICE, BOB],
  );
  console.log(`✓ created chat ${chat.chatId}`);

  // 5. send a message — Whisp echoes it back over realtime
  whisp.realtime.sendMessage(chat.chatId, "Hello from Node.js!");
  console.log("→ sent 'Hello from Node.js!'");

  // 6. wait for the server echo (with a 5s safety timeout)
  await Promise.race([
    echoReceived,
    new Promise((_, reject) =>
      setTimeout(() => reject(new Error("no echo within 5s")), 5_000),
    ),
  ]);

  // 7. clean up
  whisp.realtime.disconnect();
  await whisp.logout();
  console.log("✓ done");
}

main().catch((err) => {
  console.error(err);
  process.exit(1);
});
```

**What this script demonstrates**
- Idempotent user registration (safe to run multiple times).
- Automatic JWT storage via `signIn()`.
- Attaching realtime listeners *before* sending, so you never miss the echo.
- Using the realtime echo as a "persisted on the backend" confirmation.
- Clean shutdown with `disconnect()` + `logout()`.

---

## Common Node.js use cases

- **Integration tests** — spin up a throwaway user, exercise flows, tear down.
- **Bots & automations** — moderation bots, notification bots, LLM-powered assistants.
- **Admin tooling** — batch user creation, migration scripts, support tools.
- **Webhook handlers** — REST-only (no `webSocketImpl` needed), great for serverless.

## Next

- [Authentication](/sdks/js/authentication) — every auth method in detail
- [Realtime](/sdks/js/realtime) — the full list of events
- [Realtime Actions](/sdks/js/realtime-actions) — `sendMessage`, `editMessage`, `addReaction`, …
- [Errors & Retries](/sdks/js/errors)
