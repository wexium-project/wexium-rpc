# @wexium/rpc-electron-solution

Type-safe communication between Electron processes.
No more remembering IPC channel names, parameters order and their types.

## Installation

```bash
$ npm install --save @wexium/rpc-electron @wexium/rpc-core
```

## Quick start

Here's an example of communication from the renderer process to the main process:

- Create a file that is imported in both main and renderer processes, for example `ping-pong.ts`:

```ts
import { RendererToMainChannel } from '@wexium/rpc-electron';

export interface PingPongService {
  ping(): string;
}

export const pingPongChannel = new RendererToMainChannel<PingPongService>(
  'ping-pong',
);
```

- Code for the renderer process:

```ts
import { ipcRenderer } from 'electron';
import { pingPongChannel } from './ping-pong';

const pingPongService = pingPongChannel.getInvoker();

(async () => {
  // Equivalent of |ipcRenderer.invoke|
  console.log(await pingPongService.ping()); // Prints `pong`.
})();
```

- Code for the main process:

```ts
import { RpcMainHandler } from '@wexium/rpc-electron';
import { PingPongService, pingPongChannel } from './ping-pong';

// Equivalent of |ipcMain.handle|
class PingPongHandler implements RpcMainHandler<PingPongService> {
  ping(): string {
    return 'pong';
  }
}

pingPongChannel.getReceiver().handler = new PingPongHandler();
```

### More examples

- [Renderer to Main](examples/renderer-to-main)

## Documentation

WIP
