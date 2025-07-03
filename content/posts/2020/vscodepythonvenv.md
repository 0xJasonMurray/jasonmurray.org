---
title: "Creating and using a Python virtual environment in Visual Studio Code"
date: 2020-05-18T13:15:16-05:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - visualstudiocode
  - virtualenv
---

Python [virtual environments](https://docs.python.org/3/library/venv.html) allow developers to separate projects so that libraries do not conflict and projects can maintain separation with each other. Visual Studio Code makes it easy to create and switch between these environments. 

First, create a new workspace (directory) for each unique Python virtual environment:

![vscode settings](/images/vspyopenworkspace.png)

Create a new folder (directory):

![vscode settings](/images/vspynewfolder.png)

Create a new python file: 

![vscode settings](/images/vspynewfile.png)

Save the file with a `.py` extension:

![vscode settings](/images/vspysavenewpy.png)

Create a new terminal window:

![vscode settings](/images/vspynewterm.png)

Create a new Python virtual environment by running `/usr/local/bin/python3 -m venv .venv` within the VSCode terminal, note how VSCode automatically detects the virtual environment by asking if it should be enabled:

![vscode settings](/images/vspycreatevenv.png)

Install lint:

![vscode settings](/images/vspyinstalllint.png)


Validate the `venv` is enabled by clicking the Python version in the botton left corner of the screen:

![vscode settings](/images/vspyvalidatevenv.png)


Create a simple hello world script:

```
#!/usr/bin/env python

print(“hello world!”)
```


Run the code by clicking the play button, note the `.venv` and `source “/Users/jemurray/Google Drive/scripts/personalPython/helloworld/.venv/bin/activate”` in the terminal shows the script is activated and running in the virtual environment:

![vscode settings](/images/vspyruninvenva.png)