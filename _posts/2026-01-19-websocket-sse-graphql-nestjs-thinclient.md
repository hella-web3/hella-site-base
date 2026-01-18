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

Blockchain data is by nature, streaming event-based data. It's a perfect fit for Reactive Streams with RxJS, and event
streaming
technologies like Websockets and Server Sent Events.

Let's walk through how to build superfast dApps and offload the heavy lifting to a Nest.js backend. This Nest.js
backend, allows the
client to be much thinner and allows the backend business logic to serve multiple clients such as web, mobile, native
and various
dev tools and analytics.

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

You can follow along with the following [Github Repo](https://github.com/anataliocs/nestjs-typeorm-full-example).

{: .tip }
Open this repo in a [Github Codespace Devcontainer](https://codespaces.new/anataliocs/nestjs-typeorm-full-example) to
follow along!

The `nestjs-typeorm-full-example` is also a template repo so you can make a copy by clicking the template button.
Otherwise, follow the [NestJS Quickstart](https://docs.nestjs.com/first-steps).

### Integrating Ethers.js

Ethers.js is the current best-practice library for interacting with the Ethereum ecosystem.

Check out the [Ethers.js Docs](https://docs.ethers.org/v5/getting-started/).

**Install ethers.js:**

```shell
pnpm add ethers
```

Next we are going to create a [NestJS module](https://docs.nestjs.com/modules) to encapsulate the library and handle
it's lifecycle & configuration.

#### Ethers Sdk Config

Full
example:[ethersSdkConfig.ts](https://github.com/anataliocs/nestjs-typeorm-full-example/blob/main/src/etherssdk/ethersSdkConfig.ts)

The config file provides a strongly-typed interface for configuring the module.

**ethersSdkConfig.ts**

```typescript

export interface EthersSdkConfig {
    rpcServerUrl: string;
    network: string;
    apiKey?: string;
}

```

#### Ethers Sdk Module Definition

Full
example: [ethersSdk.module.ts](https://github.com/anataliocs/nestjs-typeorm-full-example/blob/main/src/etherssdk/ethersSdk.module.ts)

The module file provides the structure for configuring the module using the `registerAsync()` function.

{: .tip }
This is a very common pattern for [NestJS modules](https://docs.nestjs.com/fundamentals/dynamic-modules).

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

Full example: [ethers.sdk.service.ts](https://github.com/anataliocs/nestjs-typeorm-full-example/blob/main/src/etherssdk/ethers.sdk.service.ts)

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

Various functions are exposed to allow other classes to access Ethers.js functionality.

**ethers.sdk.service.ts function example**

```typescript
export class EthersSdkService extends SdkServiceBase<EthersProvider> implements SdkService {
    // ...

    /**
     * Get a block by its number.
     * @see https://docs.ethers.org/v6/api/providers/#Provider-getBlock
     *
     * @param blockHashOrBlockTag
     * @returns `Promise<BlockOrNull>`
     */
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

The function `registerAsync()` configures the dynamic `EthersSdkModule` asynchronously at runtime.

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

The EthersSdk module is a reusable component that can be used anywhere in your service.

#### Ethers Service Constructor

The `EthersSdkService` is injected into the  `EthersService` as a dependency.

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

The `EthersService` defines a function that invokes `ethersSdkService.getFinalizedBlock()` and transforms the response
into
a `FinalizedBlock`.

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

The function `finalizedBlocksStreamForWebsocket()` passes the `_getFinalizedBlocksJson()` arrow function to
`buildWsResponse()` with the
generic type `FinalizedBlock` that returns a `WsResponse<FinalizedBlock>`.

This is then wrapped into an Observable stream of data using `from`.

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

The `onEvent()` function is decorated with the `@SubscribeMessage('ethers-subscribe-blocks')` annotation that

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

The function `finalizedBlocksStream()`

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

The thin client consists of a single `.html` file

#### Front-end Client - HTML

The function

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

#### Front-end Client - HTML

The function

**ws-finalized-blocks.html excerpt**

```javascript
const websocket = new WsConnect(
    'ws://localhost:81',
    'ethers-subscribe-blocks',
    createLinkEth,
);
```

----

##### Example:

```css

```

----

### Conclusion

TODO

{: .note }
TODO

Thanks for reading!  Check out more [CodePen here](https://codepen.io/anataliocs)!

----

_What are your thoughts? Let me know in the comments below!_

