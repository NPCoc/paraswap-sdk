[**@paraswap/sdk**](../../../../README.md) • **Docs**

***

[@paraswap/sdk](../../../../globals.md) / [\<internal\>](../../../README.md) / [internal](../README.md) / addAbortSignal

# Function: addAbortSignal()

> **addAbortSignal**\<`T`\>(`signal`, `stream`): `T`

A stream to attach a signal to.

Attaches an AbortSignal to a readable or writeable stream. This lets code
control stream destruction using an `AbortController`.

Calling `abort` on the `AbortController` corresponding to the passed `AbortSignal` will behave the same way as calling `.destroy(new AbortError())` on the
stream, and `controller.error(new AbortError())` for webstreams.

```js
import fs from 'node:fs';

const controller = new AbortController();
const read = addAbortSignal(
  controller.signal,
  fs.createReadStream(('object.json')),
);
// Later, abort the operation closing the stream
controller.abort();
```

Or using an `AbortSignal` with a readable stream as an async iterable:

```js
const controller = new AbortController();
setTimeout(() => controller.abort(), 10_000); // set a timeout
const stream = addAbortSignal(
  controller.signal,
  fs.createReadStream(('object.json')),
);
(async () => {
  try {
    for await (const chunk of stream) {
      await process(chunk);
    }
  } catch (e) {
    if (e.name === 'AbortError') {
      // The operation was cancelled
    } else {
      throw e;
    }
  }
})();
```

Or using an `AbortSignal` with a ReadableStream:

```js
const controller = new AbortController();
const rs = new ReadableStream({
  start(controller) {
    controller.enqueue('hello');
    controller.enqueue('world');
    controller.close();
  },
});

addAbortSignal(controller.signal, rs);

finished(rs, (err) => {
  if (err) {
    if (err.name === 'AbortError') {
      // The operation was cancelled
    }
  }
});

const reader = rs.getReader();

reader.read().then(({ value, done }) => {
  console.log(value); // hello
  console.log(done); // false
  controller.abort();
});
```

## Type Parameters

• **T** *extends* [`Stream`](../../../classes/Stream.md)

## Parameters

• **signal**: `AbortSignal`

A signal representing possible cancellation

• **stream**: `T`

A stream to attach a signal to.

## Returns

`T`

## Since

v15.4.0

## Defined in

node\_modules/@types/node/stream.d.ts:1395