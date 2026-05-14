---
layout: post
title: Open-Source Web3 Ethereum Projects at Hella Labs
description: Supporting the Web3 Ethereum ecosystem with open source projects, tools and test automation.
date: 2026-01-16 9:29:35 +0300
image: '/images/blog/github-open-source.png'
tags: [ Ethereum, Javascript, Typescript, Testcontainers, Wiremock, Foundry ]
tags_color: '#4643ec'
featured: true
sitemap: true
---

## Open Source Web3 Ethereum Projects

[Hella Labs](https://github.com/hella-web3) supports several Ethereum open-source projects.  These projects specialize in test automation.

**Projects:**

- [Foundry Anvil Testcontainers Nodejs](https://www.npmjs.com/package/@hellaweb3/foundryanvil-testcontainers-nodejs)
- [Wiremock Testcontainers Nodejs](https://www.npmjs.com/package/@hellaweb3/wiremock-testcontainers-nodejs)

----

## Foundry Anvil Testcontainers Nodejs

This repository provides a [Testcontainers](https://testcontainers.com/) module for Node.js to run a customized Anvil node in your E2E tests.

This Typescript module provides a Fluent API style method of configuring and starting the Anvil node. And during your test execution, 
the module provides a [viem test client](https://viem.sh/docs/clients/test) and streamlined helper methods to interact with the node.

- [https://github.com/hella-web3/foundryanvil-testcontainers-nodejs](https://github.com/hella-web3/foundryanvil-testcontainers-nodejs)
- [https://www.npmjs.com/package/@hellaweb3/foundryanvil-testcontainers-nodejs](https://www.npmjs.com/package/@hellaweb3/foundryanvil-testcontainers-nodejs)

<br/>
_Get started with Foundry Anvil Testcontainers Nodejs by following the steps below._

#### Install:
```
pnpm add -D @hellaweb3/foundryanvil-testcontainers-nodejs
```

#### Setup:
```typescript
describe("AnvilContainer", () => {
  let container: StartedAnvilContainer;

  beforeAll(async () => {
    const options = new AnvilOptions()
      .logs.verboseLogs(LogVerbosity.Five)
      .logs.jsonLogFormat()
      .account.withRandomMnemonic()
      .evm.autoImpersonate();

    container = await new AnvilContainer(options).start();
  }, 60000);

  afterAll(async () => {
    if (container) await container.stop();
  });
});
```

Check out the [docs](https://www.npmjs.com/package/@hellaweb3/foundryanvil-testcontainers-nodejs) for more info:

----

## Wiremock Testcontainers Nodejs

wiremock-testcontainers-nodejs is a Typescript nodejs library that spins up a WireMock instance with [Testcontainers](https://testcontainers.com/). 
It provides a fluent API to configure and start WireMock containers, including support for loading mappings and files from recorded [Wiremock](https://wiremock.org/) 
sessions.

- [https://github.com/hella-web3/wiremock-testcontainers-nodejs](https://github.com/hella-web3/wiremock-testcontainers-nodejs)
- [https://www.npmjs.com/package/@hellaweb3/wiremock-testcontainers-nodejs](https://www.npmjs.com/package/@hellaweb3/wiremock-testcontainers-nodejs)

<br/>
_Get started with Wiremock Testcontainers Nodejs by following the steps below._

#### Install:
```
pnpm add -D @hellaweb3/wiremock-testcontainers-nodejs testcontainers
```

#### Setup:
```typescript
import { WiremockContainer } from "@hellaweb3/wiremock-testcontainers-nodejs";

describe("E2E Test", () => {
  let container;

  beforeAll(async () => {
    container = await new WiremockContainer()
      .withMappings("./test/__mocks__/wiremock")
      .start();
  }, 60000);

  afterAll(async () => {
    await container.stop();
  });

  it("should interact with WireMock", async () => {
    const url = container.rpcUrl;
    // Your test logic here
  });
});
```

Check out the [docs](https://www.npmjs.com/package/@hellaweb3/wiremock-testcontainers-nodejs) for more info:



_Thanks for reading, supporting open source and be sure to post your comments below!_

