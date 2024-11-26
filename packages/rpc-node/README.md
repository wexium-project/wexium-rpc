# @wexium/rpc-node-solution

Type-safe communication between message ports from Node.js worker_threads module.

## Installation

```bash
$ npm install --save @wexium/rpc-node @wexium/rpc-core
```

## Quick start

Here's an example of communication from the main thread to a `worker_thread`:

- Create a file that is imported in both the `worker_thread` and the main thread, for example `ping-pong.ts`:

```ts
import { WorkerChannel } from '@wexium/rpc-node';

export interface PingPongService {
  ping(): string;
}

export const pingPongChannel = new WorkerChannel<PingPongService>('ping-pong');
```

- Code for your `worker_thread`:

```ts
import { RpcWorkerHandler } from '@wexium/rpc-node';
import { PingPongService, pingPongChannel } from './ping-pong';

class PingPongHandler implements RpcWorkerHandler<PingPongService> {
  // Equivalent of |parentPort.on('message')|
  ping(): string {
    return 'pong';
  }
}

// |parentPort| is the default value for the |messagePort| parameter in |getReceiver|.
pingPongChannel.getReceiver().handler = new PingPongHandler();
```

- Code for the main thread:

```ts
import { resolve } from 'path';
import { Worker } from 'worker_threads';

import { pingPongChannel } from './ping-pong';

const worker = new Worker('path/to/worker.js');
const pingPongService = pingPongChannel.getInvoker(worker);

(async () => {
  console.log(await pingPongService.ping()); // Prints `pong`.
})();
```

### More examples

- [Main to Worker](examples/main-to-worker)

## Documentation

WIP
