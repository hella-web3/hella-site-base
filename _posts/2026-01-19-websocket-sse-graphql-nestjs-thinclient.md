---
layout: post
title: On-Chain Ethereum Data via WebSockets with Ethers.js, RxJS and Nest.js
description: Build more responsive, snappy, performant dApps with reactive streams with RxJS Observables, ethers.js and the power of a Nest.js backend
date: 2026-01-16 9:29:35 +0300
image: '/images/blog/coding-sunset.jpeg'
tags: [ Nestjs, Ethereum, EthersJS, RxJS, WebSockets ]
tags_color: '#4643ec'
featured: true
sitemap: true
---

### On-Chain Ethereum Data via WebSockets with Ethers.js, RxJS and Nest.js

Blockchain data is by nature, streaming event-based data. It's a perfect fit
for [Reactive Streams](https://www.reactive-streams.org/) with [RxJS](https://rxjs.dev/), and data
streaming technologies like [Websockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)
and [Server Sent Events](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events).

Let's walk through how to build superfast dApps and offload the heavy lifting to a [Nest.js](https://nestjs.com/)
backend. This Nest.js
backend allows the client to be much thinner and allows the backend business logic to serve multiple clients such as
web, mobile, native and various dev tools and analytics.

Using NestJS will allow us to re-use our components with NestJS built-in functionality to create a
structured, modular way of
exposing [Websockets](https://docs.nestjs.com/websockets/gateways), [REST API](https://docs.nestjs.com/controllers),
[Server Sent Events](https://docs.nestjs.com/techniques/server-sent-events)
and [GraphQL](https://docs.nestjs.com/graphql/quick-start)
for multiple chains such as Ethereum, Solana and Bitcoin in a single service.

The approach detailed here is modular and re-usable, and we will be re-using the same components in future articles to
demonstrate
this technique with [Solana](https://www.solanakit.com/), [Bitcoin](https://github.com/bitcoinjs/bitcoinjs-lib), and
other chains.

**Thin clients have:**

- Much faster initial load times
- Drastically lower JS bundle download sizes
- Faster & more responsive UX
- Usability across even low-powered devices
- Better SEO b/c load time is a factor
- Less exposed attack surface exposed locally

<br/>

_⭐️ Some notes on ultra-thin clients vs full front-end frameworks:_

{: .note }
While the ultra-thin clients techniques demonstrated in this tutorial may not be ideal for some production dApps, you
should spend time considering if stuffing a bunch of business logic into an already bloated ReactJS app is really the
right choice for your users.

ReactJS is a great full-featured framework for complex UIs with teams working on them
but may not be the right choice for a prototype, hackathon project or early-stage project.

You can read more about the clean separation of the presentation/UI layer and business
logic [Martinfowler.com](https://martinfowler.com/articles/micro-frontends.html)
and [here](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html).

Every application has its own needs and tradeoffs, and we hope you learn some new techniques to apply to your dApp in
this article.

#### Why Websockets

WebSockets reduce overhead up to ~98.8% compared to HTTP, by eliminating repeated handshakes,
large headers payloads and by leveraging smaller message frames.

{: .tip }
**Vs HTTP Polling:** WebSockets can handle up to ~390–400x more requests per second. Latency is reduced by up to ~1/3.
Payload size can be ~500x-1000x smaller.

Websockets can be a huge advantage when it comes to time-sensitive, event-driven applications.

> The haves paid for nanoseconds; the have-nots had no idea that a nanosecond had value.
>
> <cite>- Michael Lewis(Author of Flash Boys)</cite>

---

### Getting Started with Nest.js

NestJS is a comprehensive Node.js framework for building reliable, and super scalable server backends.

Learn more about [Nest.js](https://docs.nestjs.com/).

You can follow along with the following [GitHub Repo](https://github.com/anataliocs/nestjs-typeorm-full-example).

{: .tip }
Open this repo in a [GitHub Codespace Devcontainer](https://codespaces.new/anataliocs/nestjs-typeorm-full-example) to
follow along!

The [nestjs-typeorm-full-example](https://github.com/anataliocs/nestjs-typeorm-full-example) is also a template repo so
you can make a copy by clicking the **Use this template** button.
Otherwise, follow the [NestJS Quickstart](https://docs.nestjs.com/first-steps) in the project README.

### Integrating Ethers.js

Ethers.js is the current most widely used library for interacting with the Ethereum ecosystem.

Check out the [Ethers.js Docs](https://docs.ethers.org/v5/getting-started/).

First we will review the [NestJS module](https://docs.nestjs.com/modules) to encapsulate the library and handle
its lifecycle and configuration.

#### Ethers Sdk Config

**Full example:**
[ethersSdkConfig.ts](https://github.com/anataliocs/nestjs-typeorm-full-example/blob/main/src/etherssdk/ethersSdkConfig.ts)

The config file provides a strongly typed interface for configuring the module.

**ethersSdkConfig.ts**

```typescript

export interface EthersSdkConfig {
    rpcServerUrl: string;
    network: string;
    apiKey?: string;
}

```

#### Ethers Sdk Module Definition

**Full example:**
[ethersSdk.module.ts](https://github.com/anataliocs/nestjs-typeorm-full-example/blob/main/src/etherssdk/ethersSdk.module.ts)

The module file provides the structure for configuring the module using the `registerAsync()` function.

{: .tip }
This is a widespread pattern for [NestJS modules](https://docs.nestjs.com/fundamentals/dynamic-modules).

**ethersSdk.module.ts**

```typescript

@Module({imports: [ConfigModule]})
export class EthersSdkModule {
    static registerAsync(options: EthersSdkModuleAsyncOptions): DynamicModule {
        return {
            module: EthersSdkModule,
            imports: [ConfigModule],
            providers: [
                {
                    provide: CONFIG_OPTIONS,
                    useFactory: options.useFactory,
                    inject: [ConfigService],
                },
                EthersSdkService,
            ],
            exports: [EthersSdkService],
        };
    }
}
```

#### Ethers Sdk Service

**Full example:**
[ethers.sdk.service.ts](https://github.com/anataliocs/nestjs-typeorm-full-example/blob/main/src/etherssdk/ethers.sdk.service.ts)

The service file provides an interface to a reusable singleton instance of a `Provider` for other classes to use.

The constructor initializes the EthersJS `Provider`.

**ethers.sdk.service.ts constructor**

```typescript

export class EthersSdkService extends SdkServiceBase<EthersProvider> implements SdkService {
    // ...
    private readonly _rpcServerUrl: string;
    private network: string | ethers.Network;

    constructor(
        private _configService: ConfigService,
        @Inject(CONFIG_OPTIONS) private options: ethersSdkConfig.EthersSdkConfig,
    ) {
        super(`Ethers SDK Rpc Server is not initialized.`);
        this._rpcServerUrl = options.rpcServerUrl;
        this.network = options.network;
        this._rpcServer = new ethers.JsonRpcProvider(
            this._rpcServerUrl + options.apiKey,
        );
    }

    // ...
}

```

Various functions are exposed to allow other classes to access **Ethers.js** functionality.

**ethers.sdk.service.ts function example**

```typescript
export class EthersSdkService extends SdkServiceBase<EthersProvider> implements SdkService {
    // ...

    getBlock(blockHashOrBlockTag: ethers.BlockTag): Promise<BlockOrNull> {
        return this._rpcServer.getBlock(blockHashOrBlockTag);
    }

    // ...
}
```

Lifecycle hooks manage various lifecycle events in managing the server.

**ethers.sdk.service.ts lifecycle hook example**

```typescript
export class EthersSdkService extends SdkServiceBase<EthersProvider> implements SdkService {
    // ...
    async onModuleInit() {
        this.network = await this._rpcServer.getNetwork();
        this.connected();
        this.logger.log(
            `Ethers SDK Rpc Server Status: ${this._rpcServerStatus} - RPC Server URL: ${this._rpcServerUrl}`,
        );
    }

    // ...
}
```

#### Ethers Sdk Service Configuration

The function `registerAsync()` configures the dynamic **EthersSdkModule** asynchronously at runtime.

**app.module.ts excerpt**

```typescript
EthersSdkModule.registerAsync({
    imports: [ConfigModule],
    useFactory: (configService: ConfigService) =>
        ({
            network: 'Mainnet',
            rpcServerUrl: configService.get<string>('ETHERS_RPC_SERVER_URL'),
            apiKey: configService.get<string>('ETHERS_RPC_API_KEY'),
        }) as EthersSdkConfig,
    inject: [ConfigService],
})
```

----

### Using the EthersSdk Module

The **EthersSdk** module is a reusable component that can be used anywhere in your service.

#### Ethers Service Constructor

The **EthersSdkService** is injected into the  **EthersService** as a dependency.

**ethers.service.ts excerpt**

```typescript

@Injectable()
export class EthersService {
    // ...
    constructor(private readonly ethersSdkService: EthersSdkService) {
    }

    // ...
}
```

#### Ethers Service getFinalizedBlocksJson Function

The **EthersService** defines a function that invokes `getFinalizedBlock()` and transforms the response
into a **FinalizedBlock**.

**ethers.service.ts excerpt**

```typescript
export class EthersService {
    // ...
    readonly _getFinalizedBlocksJson: () => Promise<FinalizedBlock> =
        async (): Promise<FinalizedBlock> => {
            const blockOrNull = await this.ethersSdkService.getFinalizedBlock();
            if (blockOrNull === null) {
                return {} as FinalizedBlock;
            }

            return {
                blockNumber: blockOrNull.number,
                date: blockOrNull.date ?? '',
                hash: blockOrNull.hash ?? '',
            } as FinalizedBlock;
        };
    // ...
}
```

#### Ethers Service getFinalizedBlocksJson Function

This function **finalizedBlocksStreamForWebsocket** passes the `_getFinalizedBlocksJson()` arrow function to
`buildWsResponse()` with the generic type **FinalizedBlock**.

The returned `WsResponse<FinalizedBlock>` is then wrapped into an **Observable** stream of data using `from()`.

**ethers.service.ts excerpt**

```typescript
export class EthersService {
    // ...
    finalizedBlocksStreamForWebsocket(): (
        n: number,
    ) => Observable<WsResponse<FinalizedBlock>> {
        return (n: number): Observable<WsResponse<FinalizedBlock>> =>
            from(buildWsResponse<FinalizedBlock>(n, this._getFinalizedBlocksJson));
    }

    // ...
}
```

#### Message Utilities buildWsResponse Function

The function `buildWsResponse()` creates a `WsResponse` object and sets the data field to the results of the passed
in function `_getFinalizedBlocksJson()`

**message-utils.ts excerpt**

```typescript
export async function buildWsResponse<DataType>(
    n: number,
    sdkFunction: () => Promise<DataType>,
): Promise<WsResponse<DataType>> {
    return {
        type: 'events',
        data: await sdkFunction(),
    } as unknown as WsResponse<DataType>;
}
```

----

#### Websocket Gateway Event Listener

The `onEvent()` function is decorated with the `@SubscribeMessage('ethers-subscribe-blocks')` annotation that listens
for incoming websocket messages that have `event: "ethers-subscribe-blocks"` in the payload.

**It performs the following:**

- Validates that the **data** is the correct JSON shape matching **WebSocketSubscribeDto**
- Then returns a new stream of data based on the **topic** included in the DTO payload
- A stream of **WsResponse** JSON objects are emitted with the data depending on the topic

<br/>

**ethers.gateway.ts excerpt**

```typescript

@WebSocketGateway(81, {cors: {origin: '*'}})
export class EthersGateway {
    // ...
    @SubscribeMessage('ethers-subscribe-blocks')
    onEvent(
        @MessageBody(new ValidationPipe()) payload: WebSocketSubscribeDto,
    ): Observable<WsResponse<BlockNumber | FinalizedBlock | WsError>> {
        switch (payload.topic) {
            case 'block-number':
                return this.newBlocksStream();
            case 'finalized-blocks':
                return this.finalizedBlocksStream();
            default:
                return errorMsgWsResponse(payload);
        }
    }

    // ...
}
```

#### Websocket Gateway finalizedBlocksStream

The function `finalizedBlocksStream()` uses the RxJS function `interval(5000)` to create an **Observable**
stream of data that emits every 5 seconds.

On each emission, the `finalizedBlocksStreamForWebsocket()` service function is called
that emits a stream of finalized block data which is merged into the interval stream.

Then the stream is de-duplicated
using the `distinct` function, which emits data with a unique block number.

**ethers.gateway.ts excerpt**

```typescript
export class EthersGateway {
    // ...
    private finalizedBlocksStream() {
        return interval(5000)
            .pipe(mergeMap(this.ethersService.finalizedBlocksStreamForWebsocket()))
            .pipe(distinct(({data}) => data.blockNumber));
    }

    // ...
}
```

----

### Front-end Client

The thin client consists of a single `.html` file and a `.js` file.

#### Front-end Client - HTML

The `ws-finalized-blocks.html` file allows a user to subscribe to a websocket stream of finalized blocks.

**The dynamic elements are:**

- The `article` element with the id `messages` is appended with websocket data
- The `send-message-button` subscribes to websocket messages
- The `close-connection-button` closes the websocket stream

<br/>

**ws-finalized-blocks.html excerpt**

```html

<div>
    <article>
        <strong>Emitted Events - Finalized Blocks</strong>
        <p>
            <ins id="connection-status"></ins>
        </p>
        <progress id="progress-bar"></progress>
        <article id="messages"></article>
    </article>
    <div role="group">
        <button id="send-message-button"
                title="Send websocket message to subscribe to Finalized Blocks">
            Subscribe to Finalized Blocks
        </button>
        <button class="secondary" id="close-connection-button"
                title="Close websocket connection">
            Close Connection
        </button>
    </div>
</div>
```

----

#### Front-end Client - Javascript

The `wsFinalizedBlock.js` defines the basic logic to:

- Establish a websocket connection
- Send and receive websocket messages
- Close the connection

<br/>

A `WsConnect` object is instantiated passing in:

- The websocket endpoint
- The name of the event on sent websocket messages
- The function used to generate block explorer links, `createLinkEth`

<br/>

**wsFinalizedBlock.js excerpt**

```javascript
const websocket = new WsConnect(
    'ws://localhost:81',
    'ethers-subscribe-blocks',
    createLinkEth,
);
```

A **click** event listener is attached to the button with the id, `#send-message-button`. When the
button is clicked, the `sendMessage()` function on the **WsConnect** object is invoked subscribing to the
`'finalized-blocks'` stream of data via websocket.

**wsFinalizedBlock.js excerpt**

```javascript
document.querySelector('#send-message-button').addEventListener('click', () => {
    console.log('Sending message to subscribe to finalized blocks');
    websocket.sendMessage('finalized-blocks');
});
```

A **click** event listener is attached to the button with the id, `#close-connection-button`. When the
button is clicked, the `closeConnection()` function is called.

**wsFinalizedBlock.js excerpt**

```javascript
document
    .querySelector('#close-connection-button')
    .addEventListener('click', () => {
        console.log(
            'Closing finalized blocks websocket connection: User Requested',
        );
        websocket.closeConnection('User Requested');
    });
```

----

#### Front-end Client - WsConnect Class

The **WsConnect** abstracts away common functions used in all websocket clients.

The constructor initializes the object and creates a `new WebSocket(url)` using the
standard [The WebSocket API](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API)

**wsConnect.js excerpt**

```javascript
export class WsConnect {
    // ...
    constructor(url, eventName, linkFunction) {
        this.url = url;
        this.socket = new WebSocket(url);
        this.eventName = eventName;
        this.linkFunction = linkFunction;
        this.setEventHandlers();
    }

    // ...
}
```

The `setEventHandlers()` parses the returned websocket messages from the backend service and formats
them for display. The raw JSON data is displayed as well as a link to etherscan built using the passed in
`linkFunction()`.

```javascript
export class WsConnect {
    // ...
    setEventHandlers() {
        this.socket.onmessage = (msg) => {
            const data = JSON.parse(msg.data).data;
            const message = document.createElement('div');
            message.appendChild(document.createTextNode(msg.data));
            message.appendChild(document.createElement('br'));
            const link = this.linkFunction(data);
            message.appendChild(link);
            message.appendChild(document.createElement('hr'));
            document.querySelector('#messages').appendChild(message);
        };
    }

    // ...
}
```

The `sendMessage()` function sends a websocket message to subscribe to the desired topic.

```javascript
export class WsConnect {
    // ...
    sendMessage(topic) {
        this.socket.send(
            JSON.stringify({
                event: this.eventName,
                data: {
                    client: 'vanilla.js',
                    topic: topic,
                },
            }),
        );
    }

    // ...
}
```

The `closeConnection()` function closes the websocket connection.

```javascript
export class WsConnect {
    // ...
    closeConnection(reason) {
        this.socket.close(1000, reason);
        document.querySelector('#connection-status').innerHTML = '';
        document.querySelector('#progress-bar').value = 0;
    }

    // ...
}
```

----

### Conclusion

Thanks for reading!

We walked through a performant, reactive dApp with WebSockets, Ethers.js, RxJS, and NestJS.
This approach has a clean separation the client and a robust backend service capable of handling real-time blockchain
data streams.

The backend service can then be re-used for other front-end clients, such as desktop, terminal, mobile apps, analytics,
and dev tooling.

#### Some Key Takeaways to Consider

While there is no one right answer for every application, we hope you learned some new techniques in this article.

**Benefits:**

- **Thin Client**: Reduced bundle sizes, faster load times, and better performance across devices
- **Reactive Streams**: Perfect fit for blockchain's event-driven nature using RxJS Observables
- **WebSocket Efficiency**: Up to 98.8% overhead reduction and 390-400x more requests per second compared to HTTP
  polling
- **Modular Design**: Reusable NestJS modules that can easily extend to multiple blockchain networks

<br/>

**Concepts:**

- **Clean Separation**: Business logic resides in the backend, keeping frontend focused on presentation
- **Real-time Data**: Efficient streaming of blockchain events with automatic deduplication
- **Scalability**: Single backend service can serve multiple client types (web, mobile, analytics tools)

<br/>

The modular, reactive approach demonstrated here provides a clear example of a unique way of writing dApps that can
scale efficiently with a snappy, responsive UX.

_Thanks again for reading and be sure to post your comments below!_

