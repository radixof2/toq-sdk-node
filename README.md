<p align="center">
  <strong>toq SDK for Node</strong>
</p>

<p align="center">
  Node/TypeScript client for <a href="https://github.com/toqprotocol/toq">toq protocol</a>. Async. Zero runtime dependencies.
</p>

<p align="center">
  <a href="https://github.com/toqprotocol/toq-sdk-node/actions"><img src="https://github.com/toqprotocol/toq-sdk-node/actions/workflows/ci.yml/badge.svg" alt="CI"></a>
  <a href="https://www.npmjs.com/package/@toqprotocol/toq"><img src="https://img.shields.io/npm/v/@toqprotocol/toq.svg" alt="npm"></a>
  <a href="https://github.com/toqprotocol/toq-sdk-node/blob/main/LICENSE"><img src="https://img.shields.io/badge/license-Apache%202.0-blue.svg" alt="License"></a>
</p>

---

## Install

```bash
npm install @toqprotocol/toq
```

Requires Node 18+. Zero runtime dependencies (uses native `fetch`).

## Prerequisites

1. Install the [toq binary](https://github.com/toqprotocol/toq)
2. Run `toq setup`
3. Run `toq up`

## Quick Start

```typescript
import { connect } from '@toqprotocol/toq';

const client = connect();

// Send a message
const resp = await client.send('toq://192.168.1.50/bob', 'Hey, are you available?');
console.log(resp.thread_id);

// Check status
const status = await client.status();
console.log(status.address);  // toq://192.168.1.50/alice

// List peers
const peers = await client.peers();
for (const peer of peers) {
  console.log(`${peer.address} - ${peer.status}`);
}

// Stream incoming messages
for await (const msg of client.messages()) {
  console.log(`From ${msg.from}: ${msg.body}`);
  await msg.reply('Got it');
}
```

### Streaming Delivery

```typescript
const stream = await client.streamStart('toq://192.168.1.50/bob');
for (const word of 'Hello from a streaming message'.split(' ')) {
  await client.streamChunk(stream.stream_id, word + ' ');
}
await client.streamEnd(stream.stream_id);
```

## URL Resolution

`connect()` resolves the daemon URL in this order:

1. Explicit URL: `connect("http://127.0.0.1:9009")`
2. `TOQ_URL` environment variable
3. `.toq/state.json` in the current directory
4. `~/.toq/state.json`
5. Default: `http://127.0.0.1:9009`

## API

| Method | Description |
|--------|-------------|
| `send(to, text)` | Send a message |
| `messages()` | Stream incoming messages (async generator) |
| `streamStart(to)` | Open a streaming connection |
| `streamChunk(id, text)` | Send a stream chunk |
| `streamEnd(id)` | End a stream |
| `getThread(threadId)` | Get messages in a thread |
| `peers()` | List known peers |
| `block(opts)` / `unblock(opts)` | Block/unblock by key or address |
| `approvals()` | List pending approvals |
| `approve(id)` / `deny(id)` | Resolve an approval |
| `revoke(id)` | Revoke an approved rule |
| `permissions()` | List all permission rules |
| `ping(address)` | Ping a remote agent |
| `history(opts)` | Query message history |
| `discover(host)` / `discoverLocal()` | DNS/mDNS discovery |
| `connections()` | List active connections |
| `status()` / `health()` | Daemon status |
| `shutdown()` | Stop the daemon |
| `logs()` / `clearLogs()` | Read/clear logs |
| `diagnostics()` / `checkUpgrade()` | Diagnostics |
| `rotateKeys()` | Rotate identity keys |
| `exportBackup(passphrase)` | Create encrypted backup |
| `importBackup(passphrase, data)` | Restore from backup |
| `config()` / `updateConfig(updates)` | Read/update config |
| `card()` | Get agent card |
| `handlers()` | List message handlers |
| `addHandler(name, command)` | Register a handler |
| `removeHandler(name)` | Remove a handler |
| `stopHandler(name)` | Stop handler processes |

## Framework Plugins

For LangChain or CrewAI integration, see [toq-plugins](https://github.com/toqprotocol/toq-plugins).

## License

Apache 2.0
