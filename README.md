# x25519-dalek  [![](https://img.shields.io/crates/v/x25519-dalek.svg)](https://crates.io/crates/x25519-dalek) [![](https://docs.rs/x25519-dalek/badge.svg)](https://docs.rs/x25519-dalek) [![](https://travis-ci.org/dalek-cryptography/x25519-dalek.svg?branch=master)](https://travis-ci.org/dalek-cryptography/x25519-dalek)

A pure-Rust implementation of x25519 elliptic curve Diffie-Hellman key exchange,
with curve operations provided by 
[curve25519-dalek](https://github.com/dalek-cryptography/curve25519-dalek).

This crate provides two levels of API: a bare byte-oriented `x25519`
function which matches the function specified in [RFC7748][rfc7748], as
well as a higher-level Rust API for static and ephemeral Diffie-Hellman.

## Examples

<a href="https://shop.bubblesort.io">
<img 
  style="float: right; width: auto; height: 300px;"
  src="https://raw.githubusercontent.com/dalek-cryptography/x25519-dalek/master/res/bubblesort-zines-secret-messages-cover.jpeg"/>
</a>

Alice and Bob are two adorable kittens who have lost their mittens, and they
wish to be able to send secret messages to each other to coordinate finding
them, otherwise—if their caretaker cat finds out—they will surely be called
naughty kittens and be given no pie!

But the two kittens are quite clever.  Even though their paws are still too big
and the rest of them is 90% fuzziness, these clever kittens have been studying
up on modern public key cryptography and have learned a nifty trick called
*elliptic curve Diffie-Hellman key exchange*.  With the right incantations, the
kittens will be able to secretly organise to find their mittens, and then spend
the rest of the afternoon nomming some yummy pie!

First, Alice uses `EphemeralSecret::new()` and then
`PublicKey::from()` to produce her secret and public keys:

```rust
extern crate rand_os;
use rand_os::OsRng;

extern crate x25519_dalek;
use x25519_dalek::EphemeralSecret;
use x25519_dalek::PublicKey;

# fn main() {
let mut alice_csprng = OsRng::new().unwrap();
let     alice_secret = EphemeralSecret::new(&mut alice_csprng);
let     alice_public = PublicKey::from(&alice_secret);
# }
```

Bob does the same:

```rust
# extern crate rand_os;
# use rand_os::OsRng;
# 
# extern crate x25519_dalek;
# use x25519_dalek::EphemeralSecret;
# use x25519_dalek::PublicKey;
# fn main() {
let mut bob_csprng = OsRng::new().unwrap();
let     bob_secret = EphemeralSecret::new(&mut bob_csprng);
let     bob_public = PublicKey::from(&bob_secret);
# }
```

Alice meows across the room, telling `alice_public` to Bob, and Bob
loudly meows `bob_public` back to Alice.  Alice now computes her
shared secret with Bob by doing:

```rust
# extern crate rand_os;
# use rand_os::OsRng;
# 
# extern crate x25519_dalek;
# use x25519_dalek::EphemeralSecret;
# use x25519_dalek::PublicKey;
# 
# fn main() {
# let mut csprng = OsRng::new().unwrap();
# let alice_secret = EphemeralSecret::new(&mut csprng);
# let alice_public = PublicKey::from(&alice_secret);
# let bob_secret = EphemeralSecret::new(&mut csprng);
# let bob_public = PublicKey::from(&bob_secret);
let alice_shared_secret = alice_secret.diffie_hellman(&bob_public);
# }
```

Similarly, Bob computes a shared secret by doing:

```rust
# extern crate rand_os;
# use rand_os::OsRng;
# 
# extern crate x25519_dalek;
# use x25519_dalek::EphemeralSecret;
# use x25519_dalek::PublicKey;
# 
# fn main() {
# let mut csprng = OsRng::new().unwrap();
# let alice_secret = EphemeralSecret::new(&mut csprng);
# let alice_public = PublicKey::from(&alice_secret);
# let bob_secret = EphemeralSecret::new(&mut csprng);
# let bob_public = PublicKey::from(&bob_secret);
let bob_shared_secret = bob_secret.diffie_hellman(&alice_public);
# }
```

These secrets are the same:

```rust
# extern crate rand_os;
# use rand_os::OsRng;
# 
# extern crate x25519_dalek;
# use x25519_dalek::EphemeralSecret;
# use x25519_dalek::PublicKey;
# 
# fn main() {
# let mut csprng = OsRng::new().unwrap();
# let alice_secret = EphemeralSecret::new(&mut csprng);
# let alice_public = PublicKey::from(&alice_secret);
# let bob_secret = EphemeralSecret::new(&mut csprng);
# let bob_public = PublicKey::from(&bob_secret);
# let alice_shared_secret = alice_secret.diffie_hellman(&bob_public);
# let bob_shared_secret = bob_secret.diffie_hellman(&alice_public);
assert_eq!(alice_shared_secret.as_bytes(), bob_shared_secret.as_bytes());
# }
```

Voilá!  Alice and Bob can now use their shared secret to encrypt their
meows, for example, by using it to generate a key and nonce for an
authenticated-encryption cipher.

This example used the ephemeral DH API, which ensures that secret keys
cannot be reused; Alice and Bob could instead use the static DH API
and load a long-term secret key.

# Installation

To install, add the following to your project's `Cargo.toml`:

```toml
[dependencies.x25519-dalek]
version = "^0.4"
```

# Documentation

Documentation is available [here](https://docs.rs/x25519-dalek).

# Note

This code matches the [RFC7748][rfc7748] test vectors.
The elliptic curve
operations are provided by `curve25519-dalek`, which makes a best-effort
attempt to prevent software side-channels.

"Secret Messages" cover image and [zine](https://shop.bubblesort.io/products/secret-messages-zine)
copyright © Amy Wibowo ([@sailorhg](https://twitter.com/sailorhg))

[rfc7748]: https://tools.ietf.org/html/rfc7748
