# Fixed view of ArrayBuffer

## Status

Champion(s): *[Jack Works](https://github.com/Jack-Works)*

Author(s): *[CrimsonCodes0](https://github.com/CrimsonCodes0), Jack Works*

Stage: 0

## Motivation

Provide a fixed view to an `ArrayBuffer` so the application won't be able to access the data they're not supposed to access.

## Use cases

> Consider an application using JavaScript and WebAssembly, communicating through the Wasm heap.
>
> If the JS wants to pass around a read-only slice of the Wasm heap (a JS ArrayBuffer object), the JS must either pass around a TypedArray and always make sure to use it's .byteLength and .byteOffset properties to ensure correct usage, or copy the memory in the slice into a new ArrayBuffer.
>
> One wants the slice to be read-only in order to prevent writing to the memory, and one doesn't want to move around the entire ArrayBuffer object, as that would allow reading into the memory at practically arbitrary locations.
>
> Without being able to do both, view a section of an ArrayBuffer, and mark it as read-only, developers will have to find a workaround, which will likely come to creating ad-hoc wrapper classes or copying.

-- By CrimsonCodes0 https://github.com/Jack-Works/proposal-arraybuffer-fixed-view/issues/1

## Possible API design

### Possible API 1:

Add a new option to the `TypedArray` API and `DataView` API.

```js
const x = new Uint8Array(buffer, { byteOffset: 4, length: 12, fixed: true })
x.buffer // undefined
```

By limiting the `.buffer` property to get the original buffer, we can prevent the holder of `x` to construct full access to the underlying ArrayBuffer.

### Possible API 2:

Add a new `ArrayBufferSlice`, this is a new kind of intermediate "view" to the `ArrayBuffer` which limits the view size.

```js
const slice = new ArrayBufferSlice(buffer, 4, 12)
const x = new Uint8Array(slice)
x.buffer === slice
x.byteOffset === 0
x.byteLength === 12
```

From the perspective of the holder of `x`, it looks like they're accessing the full ArrayBuffer thus there is no way to expand the view anymore.

<!-- ## Implementations

### Polyfill/transpiler implementations

*A JavaScript implementation of the proposal, ideally packaged in a way that enables easy, realistic experimentation. See [implement.md](https://github.com/tc39/how-we-work/blob/master/implement.md) for details on creating useful prototype implementations.*

You can try out the implementation of this proposal in the npm package [frobnicate](https://www.npmjs.com/package/frobnicate). Note, this package has semver major version 0 and is subject to change.

### Native implementations

*For Stage 3+ proposals, and occasionally earlier, it is helpful to link to the implementation status of full, end-to-end JavaScript engines. Filing these issues before Stage 3 is somewhat unnecessary, though, as it's not very actionable.*

- [V8]() (*Links to tracking issues in each JS engine*)
- [JSC]()
- [SpiderMonkey]()
- ... -->

<!-- ## Q&A
**Q**: Why is the proposal this way?
**A**: Because reasons! -->
