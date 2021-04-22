---
title: "pip search fails with the error: xmlrpc.client.Fault"
date: 2021-04-22T08:50:48-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - pip
description: The PyPi maintainers disabled the `pip search` API due to overwhelming the servers with search queries. Use pip_search as an alternative.
---

According to this [GitHub issues thread](https://github.com/pypa/pip/issues/5216) the PyPi maintainers disabled the `pip search` API due to overwhelming the servers with search queries.

This behavior is replicated on a RedHat 8 server using the `pip3 search` command:

```
[jemurray@unix-host ~]$ pip3 search rdiff-backup
Exception:
Traceback (most recent call last):
  File "/usr/lib/python3.6/site-packages/pip/basecommand.py", line 215, in main
    status = self.run(options, args)
  File "/usr/lib/python3.6/site-packages/pip/commands/search.py", line 45, in run
    pypi_hits = self.search(query, options)
  File "/usr/lib/python3.6/site-packages/pip/commands/search.py", line 62, in search
    hits = pypi.search({'name': query, 'summary': query}, 'or')
  File "/usr/lib64/python3.6/xmlrpc/client.py", line 1112, in __call__
    return self.__send(self.__name, args)
  File "/usr/lib64/python3.6/xmlrpc/client.py", line 1452, in __request
    verbose=self.__verbose
  File "/usr/lib/python3.6/site-packages/pip/download.py", line 779, in request
    return self.parse_response(response.raw)
  File "/usr/lib64/python3.6/xmlrpc/client.py", line 1342, in parse_response
    return u.close()
  File "/usr/lib64/python3.6/xmlrpc/client.py", line 656, in close
    raise Fault(**self._stack[0])
xmlrpc.client.Fault: <Fault -32500: "RuntimeError: PyPI's XMLRPC API is currently disabled due to unmanageable load and will be deprecated in the near future. See https://status.python.org/ for more information.">
```

Use the `pip-search` command as an alternative.

Install `pip-search`:

```text
pip3 install pip-search
```

Perform a search:

```
[jemurray@unix-host ~]$ pip_search rdiff-backup
------------------  --------------------------------------------------------------------------------------------------------------------
Name                Description

rdiff-backup        Backup and Restore utility, easy to use, efficient, locally and remotely usable
...
------------------  --------------------------------------------------------------------------------------------------------------------
```