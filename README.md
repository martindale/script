script
======

JavaScript implementation of Script, Bitcoin's scripting language.

Live editor is inspired by (and heavily borrows from) Joel Burget's [react-live-editor](https://github.com/joelburget/react-live-editor).

## How It Works

Script programs are compiled to JavaScript using [Jison](http://zaach.github.io/jison/), with behavior following the specification outlined on the [Bitcoin Wiki](https://en.bitcoin.it/wiki/Script). BigInteger and elliptical curve operations are off-loaded to the [big-integer](https://www.npmjs.org/package/big-integer) and [ecdsa](https://www.npmjs.org/package/ecdsa) packages, respectively.

The live editor is based off [Joel Burget's](http://joelburget.com/) [react-live-editor](https://github.com/joelburget/react-live-editor/).

### Core Behavior

Script is primarily a stack-based programming language. That is, it operates by maintaining a stack, onto which it pushes and pops as necessary. It is relatively simple, containing if-statements, but no looping. The standard library includes functions for performing basic arithmetic and cryptographic operations, all of which can be found on the [Wiki](https://en.bitcoin.it/wiki/Script).

When the compiler runs over a Script program, it returns an object of the following form:

```js
{
    value: [value],
    code: [code]
}
```

This allows for inspection into the compiled code, as well as the return value (`true` or `false`) of the Script.

(Note that the function exported by `index.js` abstracts this object away, simply returning the result of executing the script, i.e., the `value` field.) The compiler throws if the Script does not terminate with a proper terminating operation, e.g., `OP_RETURN`, `OP_VERIFY`, etc.

### Deviations From the Spec

Note that as this is an educational tool, the goal is _not_ to create a full-fidelity re-implementation of Script, but rather, to focus on re-creating Script's core behavior. With that in mind, the implementation here differs from that described on the Wiki in a few ways. For example:

- Unlike in the official implementation, OP_CHECKMULTISIG does _not_ pop an extra, arbitrary value off the stack (as this is considered a bug and would only serve to confuse new users).
- Signatures aren't generated by hashing transaction inputs and outputs (as the snippets only exist in isolation); instead, the protocol expects users to sign a simple nonce (in this case, the string 'Secure').

Operations labeled as _Disabled_ are not implemented.

## Usage

This repository can be roughly split into two parts:

1. The core Script-to-JavaScript compiler.
2. The Script live editor.

### Compiler

In its simplest form, usage of (1) might look like the following:

```js
var exec = require('./index.js');
var script = 'OP_0 OP_1ADD OP_VERIFY';
exec(script);
```

In this case, `exec` would return `true`. Additional examples can be found in `__tests__/test-stack.js` and `__tests__/test-crypt.js`.

If you edit the Jison script, you can re-build the compiler with `npm run compile`. The compiler can also be bundled for usage in the browser with `npm run bundle`, which produces a bundle at `bundle.js`.

### Live Editor

To build the live editor, run `make` from the `live-editor` directory, followed by `python -m SimpleHTTPServer`. Once running, you can head to `localhost:8000/script-compiler.html`.

(You will need to run `npm run compile` before you can run the live editor, as the compiler must be generated from the Jison file, but it's unnecessary to bundle it, as the live editor takes care of that.)

### Testing

Unit tests can be run with `npm run test`, which will execute the [Jest](https://facebook.github.io/jest/) test runner.

## License

MIT.
