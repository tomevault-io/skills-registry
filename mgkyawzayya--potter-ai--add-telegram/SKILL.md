---
name: add-telegram
description: Add Telegram integration to NanoClaw. Can be configured as a replacement for WhatsApp, an additional channel running alongside WhatsApp, a control channel for triggering actions, or action-only for outbound notifications. Guides through BotFather setup and implements the integration. Use when this capability is needed.
metadata:
  author: mgkyawzayya
---

# Add Telegram Integration

This skill adds Telegram capabilities to NanoClaw. It can be configured in four modes:

1. **Replace WhatsApp** - Telegram becomes the sole communication channel
2. **Additional Channel** - Both platforms operate simultaneously
3. **Control Channel** - Telegram triggers actions while WhatsApp remains primary
4. **Action-Only** - Telegram restricted to outbound notifications only

## Initial Questions

Ask the user:

> How do you want to use Telegram with NanoClaw?
>
> **Option 1: Replace WhatsApp**
> - Telegram becomes your only communication channel
> - WhatsApp connection will be disabled
> - All triggers and responses go through Telegram
>
> **Option 2: Additional Channel**
> - Both Telegram and WhatsApp work simultaneously
> - Messages from either platform trigger the agent
> - Responses go back to the same platform
>
> **Option 3: Control Channel**
> - Telegram can trigger actions and send commands
> - WhatsApp remains the primary channel
> - Useful for admin commands from Telegram
>
> **Option 4: Action-Only**
> - Telegram only for outbound notifications
> - Agent cannot be triggered from Telegram
> - Useful for alerts, reminders, scheduled task outputs

Store their choice and proceed to the appropriate section.

---

## Prerequisites (All Modes)

### 1. Create Telegram Bot

**USER ACTION REQUIRED**

Tell the user:

> I need you to create a Telegram bot. I'll walk you through it:
>
> 1. Open Telegram and search for `@BotFather`
> 2. Start a chat and send `/newbot`
> 3. Follow the prompts:
>    - Enter a name for your bot (e.g., "NanoClaw Assistant")
>    - Enter a username ending in `bot` (e.g., "nanoclaw_assistant_bot")
> 4. BotFather will give you an API token like `123456789:ABCdefGHIjklMNOpqrsTUVwxyz`
>
> Copy that token and paste it here.

Wait for user to provide the token, then save it:

```bash
# Add to .env file
echo "TELEGRAM_BOT_TOKEN=<token_from_user>" >> .env
```

Verify the token works:

```bash
TOKEN=$(grep TELEGRAM_BOT_TOKEN .env | cut -d= -f2)
curl -s "https://api.telegram.org/bot${TOKEN}/getMe" | head -20
```

If successful, you'll see bot info with `"ok": true`.

### 2. Get Your Chat ID

**USER ACTION REQUIRED**

Tell the user:

> Now I need your Telegram chat ID to register it with the bot.
>
> 1. Open Telegram and search for your new bot by its username
> 2. Start a chat with it and send any message (e.g., "hello")
> 3. Tell me when you've sent the message

After user confirms, get the chat ID:

```bash
TOKEN=$(grep TELEGRAM_BOT_TOKEN .env | cut -d= -f2)
curl -s "https://api.telegram.org/bot${TOKEN}/getUpdates" | jq '.result[-1].message.chat'
```

This will show:
- `id`: The chat ID (positive for DMs, negative for groups)
- `first_name`/`title`: Name of the user or group

Save the chat ID:

```bash
echo "TELEGRAM_CHAT_ID=<chat_id_from_output>" >> .env
```

### 3. Install Dependencies

```bash
npm install telegraf dotenv
```

---

## Mode 1: Replace WhatsApp

This mode makes Telegram the sole communication channel.

### Step 1: Create Telegram Channel Module

Create `src/telegram-channel.ts`:

```typescript
import { Telegraf, Context } from 'telegraf';
import { message } from 'telegraf/filters';
import pino from 'pino';
import dotenv from 'dotenv';
import path from 'path';
import fs from 'fs';
import { ASSISTANT_NAME, GROUPS_DIR, DATA_DIR } from './config.js';
import { runContainerAgent, ContainerInput, writeTasksSnapshot, writeGroupsSnapshot } from './container-runner.js';
import { RegisteredGroup } from './types.js';
import { storeMessage, getNewMessages, initDatabase, getActiveTasks } from './db.js';

dotenv.config();

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: { target: 'pino-pretty', options: { colorize: true } }
});

const bot = new Telegraf(process.env.TELEGRAM_BOT_TOKEN!);

// Registered chats (like WhatsApp groups)
let registeredChats: Record<string, RegisteredGroup> = {};
let sessions: Record<string, string> = {};

function loadState(): void {
  registeredChats = loadJson(path.join(DATA_DIR, 'registered_telegram.json'), {});
  sessions = loadJson(path.join(DATA_DIR, 'telegram_sessions.json'), {});
  logger.info({ chatCount: Object.keys(registeredChats).length }, 'Telegram state loaded');
}

function loadJson<T>(filePath: string, defaultValue: T): T {
  try {
    if (fs.existsSync(filePath)) {
      return JSON.parse(fs.readFileSync(filePath, 'utf-8'));
    }
  } catch (err) {
    logger.error({ err, filePath }, 'Failed to load JSON');
  }
  return defaultValue;
}

function saveJson(filePath: string, data: unknown): void {
  fs.mkdirSync(path.dirname(filePath), { recursive: true });
  fs.writeFileSync(filePath, JSON.stringify(data, null, 2));
}

const TRIGGER_PATTERN = new RegExp(`^@${ASSISTANT_NAME}\\b`, 'i');

async function handleMessage(ctx: Context): Promise<void> {
  if (!ctx.message || !('text' in ctx.message)) return;

  const chatId = ctx.message.chat.id.toString();
  const text = ctx.message.text;
  const fromUser = ctx.message.from?.first_name || 'Unknown';

  // Check if chat is registered
  const group = registeredChats[chatId];
  if (!group) {
    logger.debug({ chatId }, 'Ignoring message from unregistered chat');
    return;
  }

  // Check trigger pattern
  if (!TRIGGER_PATTERN.test(text)) {
    logger.debug({ chatId, text: text.slice(0, 50) }, 'Message does not match trigger');
    return;
  }

  // Remove trigger from message
  const prompt = text.replace(TRIGGER_PATTERN, '').trim();
  if (!prompt) return;

  logger.info({ chatId, from: fromUser, prompt: prompt.slice(0, 100) }, 'Processing Telegram message');

  // Show typing indicator
  await ctx.sendChatAction('typing');

  const isMain = group.folder === 'main';

  // Write context for container
  const tasks = getActiveTasks();
  writeTasksSnapshot(group.folder, isMain, tasks);

  const input: ContainerInput = {
    prompt,
    sessionId: sessions[group.folder],
    groupFolder: group.folder,
    chatJid: `telegram:${chatId}`,
    isMain
  };

  try {
    const output = await runContainerAgent(group, input);

    if (output.newSessionId) {
      sessions[group.folder] = output.newSessionId;
      saveJson(path.join(DATA_DIR, 'telegram_sessions.json'), sessions);
    }

    if (output.status === 'success' && output.result) {
      await ctx.reply(output.result, { parse_mode: 'Markdown' });
    } else if (output.error) {
      await ctx.reply(`Error: ${output.error}`);
    }
  } catch (err) {
    logger.error({ err, chatId }, 'Failed to process message');
    await ctx.reply('Sorry, something went wrong processing your message.');
  }
}

export async function startTelegramBot(): Promise<void> {
  loadState();
  initDatabase();

  bot.on(message('text'), handleMessage);

  bot.catch((err) => {
    logger.error({ err }, 'Telegram bot error');
  });

  await bot.launch();
  logger.info(`Telegram bot running (trigger: @${ASSISTANT_NAME})`);

  // Graceful shutdown
  process.once('SIGINT', () => bot.stop('SIGINT'));
  process.once('SIGTERM', () => bot.stop('SIGTERM'));
}

// Export bot for sending messages from other modules
export { bot };
```

### Step 2: Create Telegram Entry Point

Create `src/telegram-index.ts`:

```typescript
import { startTelegramBot } from './telegram-channel.js';
import { ensureContainerSystemRunning } from './index.js';

async function main(): Promise<void> {
  ensureContainerSystemRunning();
  await startTelegramBot();
}

main().catch(err => {
  console.error('Failed to start Telegram bot:', err);
  process.exit(1);
});
```

### Step 3: Update package.json

Add these scripts to `package.json`:

```json
{
  "scripts": {
    "telegram": "tsx src/telegram-index.ts",
    "telegram:build": "node dist/telegram-index.js"
  }
}
```

### Step 4: Register Your Chat

Create `data/registered_telegram.json`:

```json
{
  "<your_chat_id>": {
    "name": "main",
    "folder": "main",
    "trigger": "@<assistant_name>",
    "added_at": "<current_iso_timestamp>"
  }
}
```

### Step 5: Update systemd Service

Update the systemd service to run Telegram instead of WhatsApp:

```bash
# Edit the service file
sed -i 's/dist\/index.js/dist\/telegram-index.js/' ~/.config/systemd/user/nanoclaw.service
systemctl --user daemon-reload
```

### Step 6: Build and Start

```bash
npm run build
systemctl --user restart nanoclaw
```

Verify it's running:

```bash
journalctl --user -u nanoclaw -f
```

Tell the user:

> Telegram bot is now your main channel! Send `@<assistant_name> hello` in your registered Telegram chat to test.

---

## Mode 2: Additional Channel (Dual Mode)

This mode runs both WhatsApp and Telegram simultaneously.

### Step 1: Add Telegram Dependencies

```bash
npm install telegraf dotenv
```

### Step 2: Create Telegram Handler Module

Create `src/telegram-handler.ts`:

```typescript
import { Telegraf, Context } from 'telegraf';
import { message } from 'telegraf/filters';
import pino from 'pino';
import dotenv from 'dotenv';
import path from 'path';
import fs from 'fs';
import { ASSISTANT_NAME, GROUPS_DIR, DATA_DIR } from './config.js';
import { runContainerAgent, ContainerInput, writeTasksSnapshot } from './container-runner.js';
import { RegisteredGroup } from './types.js';
import { getActiveTasks } from './db.js';

dotenv.config();

const logger = pino({
  level: process.env.LOG_LEVEL || 'info',
  transport: { target: 'pino-pretty', options: { colorize: true } }
});

let bot: Telegraf | null = null;
let registeredTelegramChats: Record<string, RegisteredGroup> = {};
let telegramSessions: Record<string, string> = {};

function loadJson<T>(filePath: string, defaultValue: T): T {
  try {
    if (fs.existsSync(filePath)) {
      return JSON.parse(fs.readFileSync(filePath, 'utf-8'));
    }
  } catch (err) {
    logger.error({ err, filePath }, 'Failed to load JSON');
  }
  return defaultValue;
}

function saveJson(filePath: string, data: unknown): void {
  fs.mkdirSync(path.dirname(filePath), { recursive: true });
  fs.writeFileSync(filePath, JSON.stringify(data, null, 2));
}

const TRIGGER_PATTERN = new RegExp(`^@${ASSISTANT_NAME}\\b`, 'i');

async function handleTelegramMessage(ctx: Context): Promise<void> {
  if (!ctx.message || !('text' in ctx.message)) return;

  const chatId = ctx.message.chat.id.toString();
  const text = ctx.message.text;
  const fromUser = ctx.message.from?.first_name || 'Unknown';

  const group = registeredTelegramChats[chatId];
  if (!group) {
    logger.debug({ chatId }, 'Ignoring Telegram message from unregistered chat');
    return;
  }

  if (!TRIGGER_PATTERN.test(text)) return;

  const prompt = text.replace(TRIGGER_PATTERN, '').trim();
  if (!prompt) return;

  logger.info({ chatId, from: fromUser }, 'Processing Telegram message');

  await ctx.sendChatAction('typing');

  const isMain = group.folder === 'main';
  const tasks = getActiveTasks();
  writeTasksSnapshot(group.folder, isMain, tasks);

  const input: ContainerInput = {
    prompt,
    sessionId: telegramSessions[group.folder],
    groupFolder: group.folder,
    chatJid: `telegram:${chatId}`,
    isMain
  };

  try {
    const output = await runContainerAgent(group, input);

    if (output.newSessionId) {
      telegramSessions[group.folder] = output.newSessionId;
      saveJson(path.join(DATA_DIR, 'telegram_sessions.json'), telegramSessions);
    }

    if (output.status === 'success' && output.result) {
      await ctx.reply(output.result, { parse_mode: 'Markdown' });
    } else if (output.error) {
      await ctx.reply(`Error: ${output.error}`);
    }
  } catch (err) {
    logger.error({ err, chatId }, 'Telegram message processing failed');
    await ctx.reply('Sorry, something went wrong.');
  }
}

export async function startTelegramHandler(): Promise<void> {
  const token = process.env.TELEGRAM_BOT_TOKEN;
  if (!token) {
    logger.info('TELEGRAM_BOT_TOKEN not set, Telegram channel disabled');
    return;
  }

  registeredTelegramChats = loadJson(path.join(DATA_DIR, 'registered_telegram.json'), {});
  telegramSessions = loadJson(path.join(DATA_DIR, 'telegram_sessions.json'), {});

  if (Object.keys(registeredTelegramChats).length === 0) {
    logger.warn('No Telegram chats registered, bot will start but ignore all messages');
  }

  bot = new Telegraf(token);
  bot.on(message('text'), handleTelegramMessage);

  bot.catch((err) => {
    logger.error({ err }, 'Telegram bot error');
  });

  await bot.launch();
  logger.info({ chatCount: Object.keys(registeredTelegramChats).length }, 'Telegram bot started');
}

export function getTelegramBot(): Telegraf | null {
  return bot;
}

export async function sendTelegramMessage(chatId: string, message: string): Promise<void> {
  if (!bot) {
    throw new Error('Telegram bot not initialized');
  }
  await bot.telegram.sendMessage(chatId, message, { parse_mode: 'Markdown' });
}

export function stopTelegramBot(): void {
  if (bot) {
    bot.stop('SHUTDOWN');
  }
}
```

### Step 3: Integrate into Main Index

Read `src/index.ts` and add at the top with other imports:

```typescript
import { startTelegramHandler, stopTelegramBot } from './telegram-handler.js';
```

Find the `main()` function and add after `connectWhatsApp()`:

```typescript
// Start Telegram bot if configured
startTelegramHandler().catch(err => {
  logger.error({ err }, 'Failed to start Telegram handler');
});
```

Add graceful shutdown handling:

```typescript
process.on('SIGINT', () => {
  stopTelegramBot();
  process.exit(0);
});
```

### Step 4: Register Telegram Chat

Create `data/registered_telegram.json`:

```json
{
  "<your_chat_id>": {
    "name": "telegram-main",
    "folder": "telegram-main",
    "trigger": "@<assistant_name>",
    "added_at": "<current_iso_timestamp>"
  }
}
```

Create the group folder:

```bash
mkdir -p groups/telegram-main
```

Create `groups/telegram-main/CLAUDE.md`:

```markdown
# Telegram Channel

You are responding via Telegram. Format messages appropriately:
- Telegram supports Markdown formatting
- Keep messages concise (Telegram has 4096 char limit)
- Use code blocks for code snippets
```

### Step 5: Build and Restart

```bash
npm run build
systemctl --user restart nanoclaw
```

Check logs for both channels:

```bash
journalctl --user -u nanoclaw -f | grep -E "(WhatsApp|Telegram)"
```

---

## Mode 3: Control Channel

Telegram as a control interface while WhatsApp remains primary.

Follow Mode 2 steps, but register the Telegram chat with `"folder": "main"` to share context with WhatsApp main channel:

```json
{
  "<your_chat_id>": {
    "name": "telegram-control",
    "folder": "main",
    "trigger": "@<assistant_name>",
    "added_at": "<current_iso_timestamp>"
  }
}
```

This way commands sent via Telegram affect the same context as WhatsApp.

---

## Mode 4: Action-Only (Notifications)

For outbound-only notifications without incoming message handling.

### Step 1: Create Notification Module

Create `src/telegram-notify.ts`:

```typescript
import { Telegraf } from 'telegraf';
import dotenv from 'dotenv';

dotenv.config();

let bot: Telegraf | null = null;

export function initTelegramNotify(): void {
  const token = process.env.TELEGRAM_BOT_TOKEN;
  if (!token) return;

  bot = new Telegraf(token);
}

export async function sendTelegramNotification(chatId: string, message: string): Promise<void> {
  if (!bot) {
    throw new Error('Telegram notification not initialized');
  }
  await bot.telegram.sendMessage(chatId, message, { parse_mode: 'Markdown' });
}

export function getDefaultChatId(): string | undefined {
  return process.env.TELEGRAM_CHAT_ID;
}
```

### Step 2: Add to IPC MCP

Read `container/agent-runner/src/ipc-mcp.ts` and add a Telegram notification tool:

```typescript
{
  name: 'send_telegram',
  description: 'Send a notification to Telegram',
  inputSchema: {
    type: 'object',
    properties: {
      message: { type: 'string', description: 'Message to send' },
      chatId: { type: 'string', description: 'Telegram chat ID (optional, uses default if not provided)' }
    },
    required: ['message']
  }
}
```

Then handle it in the IPC processing in `src/index.ts`.

### Step 3: Use in Scheduled Tasks

Example scheduled task that sends a Telegram notification:

```
Every morning at 9am, send me a Telegram message with the weather forecast.
```

The agent can use `mcp__nanoclaw__send_telegram` to send notifications.

---

## Rate Limits

Telegram has these rate limits:
- **30 messages/second** for broadcast to different chats
- **1 message/second** sustained to the same chat
- **20 messages/minute** to the same group

The bot will be temporarily blocked if limits are exceeded.

---

## Group Privacy Mode

By default, Telegram bots only see messages that:
- Mention the bot directly (`@botname`)
- Are replies to the bot's messages
- Start with `/` (commands)

To see all messages in a group:
1. Open BotFather
2. Send `/mybots`
3. Select your bot
4. Go to **Bot Settings → Group Privacy**
5. Turn it **OFF**

---

## Troubleshooting

### Bot not responding

```bash
# Check if token is valid
TOKEN=$(grep TELEGRAM_BOT_TOKEN .env | cut -d= -f2)
curl -s "https://api.telegram.org/bot${TOKEN}/getMe"
```

### Can't get chat ID

```bash
# Get recent messages
TOKEN=$(grep TELEGRAM_BOT_TOKEN .env | cut -d= -f2)
curl -s "https://api.telegram.org/bot${TOKEN}/getUpdates" | jq '.result[] | {chat: .message.chat, text: .message.text}'
```

### Bot sees no messages in group

- Check group privacy mode (see above)
- Make sure the trigger pattern matches (must start with `@<assistant_name>`)

### Service not starting

```bash
journalctl --user -u nanoclaw -f
```

Check for missing environment variables or syntax errors.

---

## Removing Telegram Integration

1. Remove from `.env`:
   - `TELEGRAM_BOT_TOKEN`
   - `TELEGRAM_CHAT_ID`

2. Remove Telegram imports and handlers from `src/index.ts`

3. Delete Telegram-specific files:
   - `src/telegram-handler.ts`
   - `src/telegram-notify.ts`
   - `data/registered_telegram.json`
   - `data/telegram_sessions.json`

4. Rebuild and restart:
   ```bash
   npm run build
   systemctl --user restart nanoclaw
   ```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mgkyawzayya) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
