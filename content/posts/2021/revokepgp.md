---
title: "Lost Password: Revoking PGP keys with a revocation certificate"
date: 2021-11-09T10:12:51-06:00
toc: false
images:
categories:
  - tech
tags: 
  - pgp
  - gnupg
  - revocation
description: How to revoke a PGP key with a revocation certificate when the password is lost.
---
It happened, I can't remember the passphrase to my PGP key:

```
gpg: signing failed: Bad passphrase
```

Many years ago, I signed every email and validate every PGP signature. As time passed, `pgp` faded into the background as modern email clients lacked support and newer and easier to use tools replaced encrypted email.

Luckily, I had the foresight to create a [revocation certificate](https://www.gnupg.org/gph/en/manual/c14.html) to revoke my key without the password.

In this guide, we will be revoking the following key:

```
jemurray@phalanges:~/.gnupg $ gpg --list-keys f2f9138c
pub   rsa4096 2012-06-08 [SC] [expires: 2022-06-15]
      DDCB9849B102C5F1CE1926856E8A4FCDF2F9138C
uid           [  full  ] Jason E. Murray <jemurray@wustl.edu>
uid           [  full  ] Jason E. Murray (zweck.net) <jemurray@zweck.net>
sub   rsa4096 2012-06-08 [E] [expires: 2022-06-15]
```

It's worth nothing this key was created with an expiration date. This is important for instances where the key is corrupted, lost, or the password was forgotten AND a revocation certificate was never created. An expired key reduces the impact by automatically disabling itself from the public keyring at a specific date.

We start by locating the revocation certificate. I had both a hard-copy and electronic-copy in a secure location.  The certificate looks something like this:

```
jemurray@phalanges:~/.gnupg $ cat revoke-f2f9138c.asc
-----BEGIN PGP PUBLIC KEY BLOCK-----
Version: GnuPG/MacGPG2 v2.0.17 (Darwin)
Comment: A revocation certificate should follow

iQIfBCABAgAJBQJP3jHgAh0CAAoJEG6KT83y+ROMRnMP/Ao3OkvVYNVLNFnvDaNI
Tbh+KP5sBYgDXyKJesb3uLH2lBwsV76uGzs0SYUJlyeighT3MyGOIjjPhrHZLdx3
8k+MalIiiWblDoZqeBZpPCx+ebhSIOnJb2sGTnyfMGB+tyaBpjq21LWE0gOa/XF+
t/LBed43pWc0+NGd1CyXfbtoqxqMkfAAPDAQyd1sbIlpz1REEtZSrAsJ4O83t8kI
ubxWkMZnrnny4aXbkSMg5zRNSC/MkZqxlRTX0jN8jR3ma296fC3qZx+m6PpLSgXv
twQaSN1oY/Vtrj+kiMIvLP1KHFMurQwdWrjaR37Uxlp0jU/wBHZb9b5T3RtOvNyg
nWSRZT4N1mRkAn9FpiwD+D5G1sgE3WYmPeKFVuwGcPEkesluqpbspo0J+o+m8x29
/Slb0vtZPAN9QC4VrqT6hx5hoCXCM5uvaxB737+KtLEmmYERQa5VzwhVJivJb75J
aPcH+AAdmugKe/cXQSji4TBSCC7AchUyvSWcuKfBR/X3H0uwo4GCvBmRrfQBbCco
n5rtSn0w9dt+nUJgYNdrk5vYad+nOwLK9l1gPhY6/GEaUQUb6XwP2C7S7U+m+KkR
6GyhN+j7DY5uBdPvn1aC/poxFx2ZUciZpge0FJr2Iu6KjsVWtXNm6ukvrpvy6lxG
1UD+nsJlKaQqknzihYjx612f
=mp+i
-----END PGP PUBLIC KEY BLOCK-----
```

Once your sure permanently disabling the key is the only path forward.  Revoke the key by adding the certificate to the key ring with the following command:

```text
gpg --import revoke-f2f9138c.asc
```

Example output:

```
jemurray@phalanges:~/.gnupg $ gpg --import revoke-f2f9138c.asc
gpg: key 6E8A4FCDF2F9138C: "Jason E. Murray <jemurray@wustl.edu>" revocation certificate imported
gpg: Total number processed: 1
gpg:    new key revocations: 1
gpg: marginals needed: 3  completes needed: 1  trust model: classic
gpg: depth: 0  valid:   3  signed:   9  trust: 0-, 0q, 0n, 0m, 0f, 3u
gpg: depth: 1  valid:   9  signed:  11  trust: 8-, 0q, 0n, 0m, 1f, 0u
gpg: next trustdb check due at 2022-06-10
```

Validate the key is revoked:

```
jemurray@phalanges:~/.gnupg $ gpg --list-keys f2f9138c
pub   rsa4096 2012-06-08 [SC] [revoked: 2012-06-17]
      DDCB9849B102C5F1CE1926856E8A4FCDF2F9138C
uid           [ revoked] Jason E. Murray <jemurray@wustl.edu>
uid           [ revoked] Jason E. Murray (zweck.net) <jemurray@zweck.net>
```

Notice the `[ revoked]` comment in the key.

Finally, (try to) update the public key rings with the revoked status. There are not many functioning public key servers anymore:

```
jemurray@phalanges:~/.gnupg $ gpg --keyserver keys.openpgp.org --send-keys DDCB9849B102C5F1CE1926856E8A4FCDF2F9138C
gpg: sending key 6E8A4FCDF2F9138C to hkp://keys.openpgp.org
```