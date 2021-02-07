---
title: "Installing Ansible on a fresh install of Big Sur"
date: 2021-01-02T12:28:00-06:00
toc: false
images:
categories:
  - tech
tags: 
  - ansible
  - pip
  - virtualenv
  - venv
---

# Overview

While attempting to install Ansible in a virtual environment on a clean install of Big Sur, I received the following error: 

```
ERROR: Could not build wheels for cryptography which use PEP 517 and cannot be installed directly
```

The problem resolved itself after upgrading `pip`:

```
pip install --upgrade pip
```

After the upgrade:

```
(.venv) jemurray@jasons-mbp:~/Documents/src/ansible $ pip install ansible
Collecting ansible
  Using cached ansible-2.10.4.tar.gz (28.6 MB)

...

Successfully installed MarkupSafe-1.1.1 PyYAML-5.3.1 ansible-2.10.4 ansible-base-2.10.4 cffi-1.14.4 cryptography-3.3.1 jinja2-2.11.2 packaging-20.8 pycparser-2.20 pyparsing-2.4.7 six-1.15.0
```




# Details

First attempt installing Ansible (failed):

```
jemurray@jasons-mbp:~/Documents/src/ansible $ python3 -m venv .venv
jemurray@jasons-mbp:~/Documents/src/ansible $ source .venv/bin/activate
(.venv) jemurray@jasons-mbp:~/Documents/src/ansible $ pip install ansible
Collecting ansible
  Downloading https://files.pythonhosted.org/packages/01/ff/0bdc7b0698f7d4e02e7da6e96d7d856a42667419c5c48bbfc3f8dda9a80e/ansible-2.10.4.tar.gz (28.6MB)
     |████████████████████████████████| 28.6MB 1.3MB/s
Collecting ansible-base<2.11,>=2.10.3 (from ansible)
  Downloading https://files.pythonhosted.org/packages/2d/e0/3eabf1be1211f653c73bc7827084c301aaefc6d8fc73e33572172cf0306b/ansible-base-2.10.4.tar.gz (5.7MB)
     |████████████████████████████████| 5.7MB 1.3MB/s
Collecting jinja2 (from ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/30/9e/f663a2aa66a09d838042ae1a2c5659828bb9b41ea3a6efa20a20fd92b121/Jinja2-2.11.2-py2.py3-none-any.whl (125kB)
     |████████████████████████████████| 133kB 276kB/s
Collecting PyYAML (from ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/64/c2/b80047c7ac2478f9501676c988a5411ed5572f35d1beff9cae07d321512c/PyYAML-5.3.1.tar.gz (269kB)
     |████████████████████████████████| 276kB 286kB/s
Collecting cryptography (from ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/b7/82/f7a4ddc1af185936c1e4fa000942ffa8fb2d98cff26b75afa7b3c63391c4/cryptography-3.3.1.tar.gz (539kB)
     |████████████████████████████████| 542kB 2.4MB/s
  Installing build dependencies ... done
  Getting requirements to build wheel ... done
    Preparing wheel metadata ... done
Collecting packaging (from ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/b1/a7/588bfa063e7763247ab6f7e1d994e331b85e0e7d09f853c59a6eb9696974/packaging-20.8-py2.py3-none-any.whl
Collecting MarkupSafe>=0.23 (from jinja2->ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/b9/2e/64db92e53b86efccfaea71321f597fa2e1b2bd3853d8ce658568f7a13094/MarkupSafe-1.1.1.tar.gz
Collecting cffi>=1.12 (from cryptography->ansible-base<2.11,>=2.10.3->ansible)
  Using cached https://files.pythonhosted.org/packages/66/6a/98e023b3d11537a5521902ac6b50db470c826c682be6a8c661549cb7717a/cffi-1.14.4.tar.gz
Collecting six>=1.4.1 (from cryptography->ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/ee/ff/48bde5c0f013094d729fe4b0316ba2a24774b3ff1c52d924a8a4cb04078a/six-1.15.0-py2.py3-none-any.whl
Collecting pyparsing>=2.0.2 (from packaging->ansible-base<2.11,>=2.10.3->ansible)
  Downloading https://files.pythonhosted.org/packages/8a/bb/488841f56197b13700afd5658fc279a2025a39e22449b7cf29864669b15d/pyparsing-2.4.7-py2.py3-none-any.whl (67kB)
     |████████████████████████████████| 71kB 2.5MB/s
Collecting pycparser (from cffi>=1.12->cryptography->ansible-base<2.11,>=2.10.3->ansible)
  Using cached https://files.pythonhosted.org/packages/ae/e7/d9c3a176ca4b02024debf82342dab36efadfc5776f9c8db077e8f6e71821/pycparser-2.20-py2.py3-none-any.whl
Building wheels for collected packages: cryptography
  Building wheel for cryptography (PEP 517) ... error
  ERROR: Command errored out with exit status 1:
   command: /Users/jemurray/Documents/src/ansible/.venv/bin/python3 /Users/jemurray/Documents/src/ansible/.venv/lib/python3.8/site-packages/pip/_vendor/pep517/_in_process.py build_wheel /var/folders/w2/3npd26fn3j7_0dskk29jygpw0000gn/T/tmp4c3old_u
       cwd: /private/var/folders/w2/3npd26fn3j7_0dskk29jygpw0000gn/T/pip-install-btjjhm_x/cryptography
  Complete output (147 lines):
  running bdist_wheel
  running build
  running build_py
  creating build
  creating build/lib.macosx-10.14.6-x86_64-3.8
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography
  copying src/cryptography/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography
  copying src/cryptography/utils.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography
  copying src/cryptography/__about__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography
  copying src/cryptography/exceptions.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography
  copying src/cryptography/fernet.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat
  copying src/cryptography/hazmat/_der.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat
  copying src/cryptography/hazmat/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat
  copying src/cryptography/hazmat/_oid.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/oid.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/ocsp.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/general_name.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/extensions.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/name.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/base.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  copying src/cryptography/x509/certificate_transparency.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/x509
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends
  copying src/cryptography/hazmat/backends/interfaces.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends
  copying src/cryptography/hazmat/backends/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/cmac.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/poly1305.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/constant_time.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/keywrap.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/hmac.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/hashes.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  copying src/cryptography/hazmat/primitives/padding.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/bindings
  copying src/cryptography/hazmat/bindings/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/bindings
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/x448.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/backend.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/ec.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/ciphers.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/ocsp.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/x509.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/aead.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/encode_asn1.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/rsa.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/dh.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/cmac.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/utils.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/poly1305.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/ed25519.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/dsa.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/decode_asn1.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/hmac.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/ed448.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/x25519.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  copying src/cryptography/hazmat/backends/openssl/hashes.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/backends/openssl
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/scrypt.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/pbkdf2.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/hkdf.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/x963kdf.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/kbkdf.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  copying src/cryptography/hazmat/primitives/kdf/concatkdf.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/kdf
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/twofactor
  copying src/cryptography/hazmat/primitives/twofactor/totp.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/twofactor
  copying src/cryptography/hazmat/primitives/twofactor/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/twofactor
  copying src/cryptography/hazmat/primitives/twofactor/hotp.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/twofactor
  copying src/cryptography/hazmat/primitives/twofactor/utils.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/twofactor
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/serialization
  copying src/cryptography/hazmat/primitives/serialization/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/serialization
  copying src/cryptography/hazmat/primitives/serialization/pkcs12.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/serialization
  copying src/cryptography/hazmat/primitives/serialization/pkcs7.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/serialization
  copying src/cryptography/hazmat/primitives/serialization/ssh.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/serialization
  copying src/cryptography/hazmat/primitives/serialization/base.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/serialization
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/ciphers
  copying src/cryptography/hazmat/primitives/ciphers/algorithms.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/ciphers
  copying src/cryptography/hazmat/primitives/ciphers/aead.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/ciphers
  copying src/cryptography/hazmat/primitives/ciphers/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/ciphers
  copying src/cryptography/hazmat/primitives/ciphers/modes.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/ciphers
  copying src/cryptography/hazmat/primitives/ciphers/base.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/ciphers
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/x448.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/ec.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/rsa.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/dh.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/utils.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/ed25519.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/dsa.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/ed448.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/x25519.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  copying src/cryptography/hazmat/primitives/asymmetric/padding.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/primitives/asymmetric
  creating build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/bindings/openssl
  copying src/cryptography/hazmat/bindings/openssl/__init__.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/bindings/openssl
  copying src/cryptography/hazmat/bindings/openssl/_conditional.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/bindings/openssl
  copying src/cryptography/hazmat/bindings/openssl/binding.py -> build/lib.macosx-10.14.6-x86_64-3.8/cryptography/hazmat/bindings/openssl
  running egg_info
  writing src/cryptography.egg-info/PKG-INFO
  writing dependency_links to src/cryptography.egg-info/dependency_links.txt
  writing requirements to src/cryptography.egg-info/requires.txt
  writing top-level names to src/cryptography.egg-info/top_level.txt
  reading manifest file 'src/cryptography.egg-info/SOURCES.txt'
  reading manifest template 'MANIFEST.in'
  no previously-included directories found matching 'docs/_build'
  warning: no previously-included files found matching 'vectors'
  warning: no previously-included files matching '*' found under directory 'vectors'
  warning: no previously-included files matching '*' found under directory '.github'
  warning: no previously-included files found matching 'release.py'
  warning: no previously-included files found matching '.coveragerc'
  warning: no previously-included files found matching 'codecov.yml'
  warning: no previously-included files found matching '.readthedocs.yml'
  warning: no previously-included files found matching 'dev-requirements.txt'
  warning: no previously-included files found matching 'rtd-requirements.txt'
  warning: no previously-included files found matching 'tox.ini'
  warning: no previously-included files matching '*' found under directory '.zuul.d'
  warning: no previously-included files matching '*' found under directory '.zuul.playbooks'
  writing manifest file 'src/cryptography.egg-info/SOURCES.txt'
  running build_ext
  generating cffi module 'build/temp.macosx-10.14.6-x86_64-3.8/_padding.c'
  creating build/temp.macosx-10.14.6-x86_64-3.8
  generating cffi module 'build/temp.macosx-10.14.6-x86_64-3.8/_openssl.c'
  building '_openssl' extension
  creating build/temp.macosx-10.14.6-x86_64-3.8/build
  creating build/temp.macosx-10.14.6-x86_64-3.8/build/temp.macosx-10.14.6-x86_64-3.8
  clang -Wno-unused-result -Wsign-compare -Wunreachable-code -fno-common -dynamic -DNDEBUG -g -fwrapv -O3 -Wall -iwithsysroot/System/Library/Frameworks/System.framework/PrivateHeaders -iwithsysroot/Applications/Xcode.app/Contents/Developer/Library/Frameworks/Python3.framework/Versions/3.8/Headers -arch arm64 -arch x86_64 -I/Users/jemurray/Documents/src/ansible/.venv/include -I/Library/Developer/CommandLineTools/Library/Frameworks/Python3.framework/Versions/3.8/include/python3.8 -c build/temp.macosx-10.14.6-x86_64-3.8/_openssl.c -o build/temp.macosx-10.14.6-x86_64-3.8/build/temp.macosx-10.14.6-x86_64-3.8/_openssl.o -Wconversion -Wno-error=sign-conversion
  build/temp.macosx-10.14.6-x86_64-3.8/_openssl.c:575:10: fatal error: 'openssl/opensslv.h' file not found
  #include <openssl/opensslv.h>
           ^~~~~~~~~~~~~~~~~~~~
  1 error generated.

      =============================DEBUG ASSISTANCE=============================
      If you are seeing a compilation error please try the following steps to
      successfully install cryptography:
      1) Upgrade to the latest pip and try again. This will fix errors for most
         users. See: https://pip.pypa.io/en/stable/installing/#upgrading-pip
      2) Read https://cryptography.io/en/latest/installation.html for specific
         instructions for your platform.
      3) Check our frequently asked questions for more information:
         https://cryptography.io/en/latest/faq.html
      =============================DEBUG ASSISTANCE=============================

  error: command 'clang' failed with exit status 1
  ----------------------------------------
  ERROR: Failed building wheel for cryptography
  Running setup.py clean for cryptography
Failed to build cryptography
ERROR: Could not build wheels for cryptography which use PEP 517 and cannot be installed directly
WARNING: You are using pip version 19.2.3, however version 20.3.3 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
(.venv) jemurray@jasons-mbp:~/Documents/src/ansible $ brew search ssl
==> Formulae
cfssl                    libressl                 osslsigncode             sslh                     sslmate                  sslsplit                 testssl
glib-openssl             openssl@1.1              ssldump                  ssllabs-scan             sslscan                  sslyze                   wolfssl
==> Casks
homebrew/cask/watchguard-mobile-vpn-with-ssl
```

Upgrading PIP:

```
(.venv) jemurray@jasons-mbp:~/Documents/src/ansible $ pip install --upgrade pip
Collecting pip
  Downloading https://files.pythonhosted.org/packages/54/eb/4a3642e971f404d69d4f6fa3885559d67562801b99d7592487f1ecc4e017/pip-20.3.3-py2.py3-none-any.whl (1.5MB)
     |████████████████████████████████| 1.5MB 7.0MB/s
Installing collected packages: pip
  Found existing installation: pip 19.2.3
    Uninstalling pip-19.2.3:
      Successfully uninstalled pip-19.2.3
Successfully installed pip-20.3.3
```

Trying again to install Ansible after the `pip` upgrade (success):

```
(.venv) jemurray@jasons-mbp:~/Documents/src/ansible $ pip install ansible
Collecting ansible
  Using cached ansible-2.10.4.tar.gz (28.6 MB)
Collecting ansible-base<2.11,>=2.10.3
  Using cached ansible-base-2.10.4.tar.gz (5.7 MB)
Collecting cryptography
  Downloading cryptography-3.3.1-cp36-abi3-macosx_10_10_x86_64.whl (1.8 MB)
     |████████████████████████████████| 1.8 MB 3.3 MB/s
Collecting cffi>=1.12
  Downloading cffi-1.14.4-cp38-cp38-macosx_10_9_x86_64.whl (176 kB)
     |████████████████████████████████| 176 kB 10.5 MB/s
Collecting six>=1.4.1
  Using cached six-1.15.0-py2.py3-none-any.whl (10 kB)
Collecting jinja2
  Using cached Jinja2-2.11.2-py2.py3-none-any.whl (125 kB)
Collecting MarkupSafe>=0.23
  Downloading MarkupSafe-1.1.1-cp38-cp38-macosx_10_9_x86_64.whl (16 kB)
Collecting packaging
  Using cached packaging-20.8-py2.py3-none-any.whl (39 kB)
Collecting pyparsing>=2.0.2
  Using cached pyparsing-2.4.7-py2.py3-none-any.whl (67 kB)
Collecting pycparser
  Using cached pycparser-2.20-py2.py3-none-any.whl (112 kB)
Collecting PyYAML
  Using cached PyYAML-5.3.1.tar.gz (269 kB)
Using legacy 'setup.py install' for ansible, since package 'wheel' is not installed.
Using legacy 'setup.py install' for ansible-base, since package 'wheel' is not installed.
Using legacy 'setup.py install' for PyYAML, since package 'wheel' is not installed.
Installing collected packages: pycparser, six, pyparsing, MarkupSafe, cffi, PyYAML, packaging, jinja2, cryptography, ansible-base, ansible
    Running setup.py install for PyYAML ... done
    Running setup.py install for ansible-base ... done
    Running setup.py install for ansible ... done
Successfully installed MarkupSafe-1.1.1 PyYAML-5.3.1 ansible-2.10.4 ansible-base-2.10.4 cffi-1.14.4 cryptography-3.3.1 jinja2-2.11.2 packaging-20.8 pycparser-2.20 pyparsing-2.4.7 six-1.15.0
```