---
title: "Interacting with LastPass through the CLI"
date: 2020-08-21T16:30:38-05:00
toc: false
images:
categories:
  - tech
tags: 
  - lastpass
  - cli
---

Using [lastpass-cli](https://github.com/LastPass/lastpass-cli) enables password lookups without opening the [LastPass](https://www.lastpass.com/) desktop client.

Login to the LastPass CLI:

```
jemurray@mbp-2019:~ $ lpass login lpuser@example.com
Success: Logged in as lpuser@example.com.
```

Generate test data without a user name:

```
jemurray@mbp-2019:~ $ lpass generate lpasstest 15
`+&x6%_YcuZbdua
```

View data:

```
jemurray@mbp-2019:~ $ lpass show lpasstest
lpasstest [id: 5943780687328672071]
Password: `+&x6%_YcuZbdua
```

Generate test data with a username:

```
jemurray@mbp-2019:~ $ lpass generate --username=jemurray@zweck.net lpasstest2 15
+k]e?"F5+N3`I&L
```

View data:

```
jemurray@mbp-2019:~ $ lpass show lpasstest2
lpasstest2 [id: 0]
Username: jemurray@zweck.net
Password: +k]e?"F5+N3`I&L
```

Search for entries:

```
jemurray@mbp-2019:~ $ lpass show -G lpass
Multiple matches found.
lpasstest2 [id: 0]
lpasstest [id: 5943780687328672071]
```

Copy password to paste-buffer:

```
jemurray@mbp-2019:~ $ lpass show lpasstest --password -c


<CTRL-V> oLVne055$\[dFsD-qZ!(
```

New entries are synchronized to the desktop client quickly:

![lastpass sync](/images/lastpasssync.png)

CLI generated passwords in the desktop client:

![lastpass view](/images/lastpassview.png)