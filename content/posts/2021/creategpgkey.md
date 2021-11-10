---
title: "Secure Communications: Generating a New GnuPG Key"
date: 2021-11-10T07:49:47-06:00
toc: false
images:
categories:
  - tech
tags: 
  - gnupg
description:
---
A few days ago, I realized [I didn't remember the password to my PGP key](/posts/2021/revokepgp/) and had to revoke it.


While PGP is no longer the most popular way to securely communicate, it is still widely used in the technical community. In this post we will provide  the basic steps required to generate a secure PGP key pair on a macOS or Linux machine (anything with a command line).

Install [GnuPG](https://www.gnupg.org/download/index.html) using your favorite package manager.

To create a new `gnupg` key run the following command from a terminal session:

```text
gpg --full-generate-key
```

A few important things to note:

- Make sure [gpg](https://gnupg.org/download/index.html) is running the latest version.
- Unless you know what your doing, select the defaults.
- Technology changes and keys or passwords get lost. Set an expiration date on the key that is not too far out. Three years or less is probably a safe choice.
- Copy, print, and store the revocation certificate created in the `~/.gnupg/openpgp-revocs.d/` directory to a safe location.

Example output:

```
jemurray@phalanges:~/.gnupg $ gpg --full-generate-key
gpg (GnuPG) 2.3.3; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Please select what kind of key you want:
   (1) RSA and RSA
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
   (9) ECC (sign and encrypt) *default*
  (10) ECC (sign only)
  (14) Existing key from card
Your selection?
Please select which elliptic curve you want:
   (1) Curve 25519 *default*
   (4) NIST P-384
   (6) Brainpool P-256
Your selection?
Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 3y
Key expires at Fri Nov  8 14:53:39 2024 CST
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.

Real name: Jason E. Murray
Email address: jemurray@zweck.net
Comment: https://jasonmurray.org/
You selected this USER-ID:
    "Jason E. Murray (https://jasonmurray.org/) <jemurray@zweck.net>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
We need to generate a lot of random bytes. It is a good idea to perform
some other action (type on the keyboard, move the mouse, utilize the
disks) during the prime generation; this gives the random number
generator a better chance to gain enough entropy.
gpg: key 77AF85EEC586B19C marked as ultimately trusted
gpg: directory '/Users/jemurray/.gnupg/openpgp-revocs.d' created
gpg: revocation certificate stored as '/Users/jemurray/.gnupg/openpgp-revocs.d/1E97D7C3F99E5DB6E8B4099677AF85EEC586B19C.rev'
public and secret key created and signed.

pub   ed25519 2021-11-09 [SC] [expires: 2024-11-08]
      1E97D7C3F99E5DB6E8B4099677AF85EEC586B19C
uid                      Jason E. Murray (https://jasonmurray.org/) <jemurray@zweck.net>
sub   cv25519 2021-11-09 [E] [expires: 2024-11-08]
```

Optionally, you can edit and add additional identities to the key with the following command:

```text
gpg --edit-key 1E97D7C3F99E5DB6E8B4099677AF85EEC586B19C
```

Example output:

```
jemurray@phalanges:~/.gnupg $ gpg --edit-key 1E97D7C3F99E5DB6E8B4099677AF85EEC586B19C
gpg (GnuPG) 2.3.3; Copyright (C) 2021 Free Software Foundation, Inc.
This is free software: you are free to change and redistribute it.
There is NO WARRANTY, to the extent permitted by law.

Secret key is available.

gpg: checking the trustdb
gpg: marginals needed: 3  completes needed: 1  trust model: classic
gpg: depth: 0  valid:   4  signed:   9  trust: 0-, 0q, 0n, 0m, 0f, 4u
gpg: depth: 1  valid:   9  signed:  11  trust: 8-, 0q, 0n, 0m, 1f, 0u
gpg: next trustdb check due at 2022-06-10
sec  ed25519/77AF85EEC586B19C
     created: 2021-11-09  expires: 2024-11-08  usage: SC
     trust: ultimate      validity: ultimate
ssb  cv25519/D9BE57B1D7E68869
     created: 2021-11-09  expires: 2024-11-08  usage: E
[ultimate] (1). Jason E. Murray (https://jasonmurray.org/) <jemurray@zweck.net>

gpg> adduid
Real name: Jason E. Murray
Email address: jemurray@wustl.edu
Comment: https://jasonmurray.org/
You selected this USER-ID:
    "Jason E. Murray (https://jasonmurray.org/) <jemurray@wustl.edu>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? o

sec  ed25519/77AF85EEC586B19C
     created: 2021-11-09  expires: 2024-11-08  usage: SC
     trust: ultimate      validity: ultimate
ssb  cv25519/D9BE57B1D7E68869
     created: 2021-11-09  expires: 2024-11-08  usage: E
[ultimate] (1)  Jason E. Murray (https://jasonmurray.org/) <jemurray@zweck.net>
[ unknown] (2). Jason E. Murray (https://jasonmurray.org/) <jemurray@wustl.edu>

gpg> quit
Save changes? (y/N) y
```


Export the public key and publish it on the web or key server, with the following command:

```text
gpg --armor --export 1E97D7C3F99E5DB6E8B4099677AF85EEC586B19C
```

Example output:

```
jemurray@phalanges:~/.gnupg $ gpg --armor --export 1E97D7C3F99E5DB6E8B4099677AF85EEC586B19C
-----BEGIN PGP PUBLIC KEY BLOCK-----
Comment: Verify keys at https://jasonmurray.org/

mDMEYYrf5BYJKwYBBAHaRw8BAQdAFdfJsPWFMKWa916NAHLmKAmDHZ6cxZNh9oMw
vt8cO6a0P0phc29uIEUuIE11cnJheSAoaHR0cHM6Ly9qYXNvbm11cnJheS5vcmcv
KSA8amVtdXJyYXlAendlY2submV0PoiaBBMWCgBCFiEEHpfXw/meXbbotAmWd6+F
7sWGsZwFAmGK3+QCGwMFCQWjmoAFCwkIBwIDIgIBBhUKCQgLAgQWAgMBAh4HAheA
AAoJEHevhe7FhrGc6DIBAPuN2va5c46QSWUK/ytyhijJhhtrIqLYiSW1SC21o0Br
AQDmD+5hwJSj1j3zgQ0hNURfq2sg5imLyYYqV/uwnA/uB7Q/SmFzb24gRS4gTXVy
cmF5IChodHRwczovL2phc29ubXVycmF5Lm9yZy8pIDxqZW11cnJheUB3dXN0bC5l
ZHU+iJoEExYKAEIWIQQel9fD+Z5dtui0CZZ3r4XuxYaxnAUCYYrlJAIbAwUJBaOa
gAULCQgHAgMiAgEGFQoJCAsCBBYCAwECHgcCF4AACgkQd6+F7sWGsZzXWQEAsVCQ
eYg9M9Lk0YQRs4BffsNnPzcmU+G0i3bWbc49zqQA/2amb/+9DI6Y1j8l51GmvRZc
p04tmHQMLqNWgqlt/u4LuDgEYYrf5BIKKwYBBAGXVQEFAQEHQBDL3/NWLAavUU/Y
gu0aY8TdSD9piwxerT8WYAlUH/tbAwEIB4h+BBgWCgAmFiEEHpfXw/meXbbotAmW
d6+F7sWGsZwFAmGK3+QCGwwFCQWjmoAACgkQd6+F7sWGsZyJqwEAvkREXNrDqbdB
UTFweeRSFb03hzsviEdYjsxa6kgOMtUA/iJe+G1tyacBwNr+mOCGvYr3YtzugAzm
1ruWeog4qFwJ
=/TCe
-----END PGP PUBLIC KEY BLOCK-----
```