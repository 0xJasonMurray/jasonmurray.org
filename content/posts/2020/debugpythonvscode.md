---
title: "Debugging Python code with vscode"
date: 2020-11-27T10:30:32-06:00
toc: false
images:
categories:
  - tech
tags: 
  - python
  - vscode
  - debug
---

# Overview

Replace the traditional `print("DEBUG: " + str(variable))` with `vscode` breakpoints and variable inspection.

# Details

## Basic Debugging

Start with a Python script by running it in the built in [terminal](/posts/2020/vscodeplay/) (click the play button in the upper right corner):

![](/images/2020-11-27-10-37-35.png)


To enable debugging, click the Run and Debug button in the left side bar: 

![](/images/2020-11-27-10-43-13.png)

Click `Run and Debug`:

![](/images/2020-11-27-10-45-35.png)

Select `Python file`:

![](/images/2020-11-27-10-46-14.png)

Set a break point by clicking the space directly to the left of the line number in the Python code.  A small red dot is added at every break point:

![](/images/2020-11-27-10-49-30.png)

Click the `step` button to start.  Each press of the step button will execute the code until the breakpoint is reached:

![](/images/2020-11-27-10-48-35.png)

Expand the `Variables` section in the left hand debugging menu to examine individual variables:

![](/images/2020-11-27-10-47-52.png)

In larger code bases it may be easier to view variable values by hovering the mouse over a variable name:

![](/images/2020-11-27-10-52-32.png)


## Django Debugging

Enabling debug mode in a Django environment will automatically start the debug server.

Start by selecting the `Run and Debug`:

![](/images/2020-11-27-11-27-25.png)

Select the location of the Django `manage.py` file:

![](/images/2020-11-27-11-28-10.png)

The debug server is automatically started:

![](/images/2020-11-27-11-28-34.png)
