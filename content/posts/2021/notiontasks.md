---
title: "Embedding a task list board in a Notion page"
date: 2021-01-30T06:16:37-06:00
toc: false
images:
categories:
  - tech
tags: 
  - notion
  - journal
  - todo
  - database
---

# Overview

My day starts and ends by reviewing outstanding tasks tracked in [Notion](https://notion.io/). Each day is unique, requiring shuffling items between the `active` and `later` buckets. In addition, I keep a high level journal of the day’s events. Being mindful of the day helps me to prioritize the import tasks and use my time wisely.

Each of these items, tasks and journal, are tracked on their own separate page.  However, I discovered a option within Notion to insert a linked database from one page into another.  This post will explain how to create a linked database within Notion.    


# Details

## Managing Tasks and Creating the Journal

`The Daily ToDo` within Notion is setup as a full page `Board - Inline`, as seen in the image below.  First thing in the morning and at the end of the day I review this list and move items as needed:

![Image of Notion inline board task management system](/images/2021-01-30-15-17-05.png)

After task management is complete, I create a page called `Daily Journal: @today` using the journal template:  

![](/images/2021-01-30-15-20-21.png)


Within the daily journal, I keep a running commentary of how the day is going.  Since my journal file is open most of the day and to keep from switching back and forth between journal and the todo list, I embedded the todo list directly in the page:

![Image of Notion daily journal page with embedded todo list](/images/2021-01-30-15-25-54.png)

## Embedding The Task Database

To create a page with a linked database, move the cursor to a blank line, click the `+`, then select `Create Linked Database`:

![Image of menu within Notion to create a linked database](/images/2021-01-30-15-33-09.png)

Select the database:

![Image of menu within Notion to select a database to link](/images/2021-01-30-15-35-09.png)

By default all entries are displayed:

![Image of database embedded within a Notion page](/images/2021-01-30-15-36-56.png)

For the daily journal, I only care about the tasks I selected as important for the current day.   To filter everything else, we create a filter by clicking the `...` in the top row:

![Image of filter selection menu within Notion](/images/2021-01-30-15-38-15.png)

Add a filter:

![Image of filter selection within Notion](/images/2021-01-30-15-39-15.png)

In my example, I filter everything where `Status = Today`:

![Image of database filter options within Notion](/images/2021-01-30-15-40-26.png)

The page now contains the important tasks and daily journal entires:

![Image of Notion daily journal page with embedded todo list](/images/2021-01-30-15-25-54.png)
