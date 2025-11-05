---
description: Document codebase as-is with thoughts directory for historical context
---

# Create Issue

You are tasked with creating a github issue to reflect a feature, bug fix, code refactor through an interactive, iterative process. You should be skeptical, thorough, and work collaboratively with the user to produce a high quality summary of what will be accomplished.

## Initial Response

When this command is invoked, respond with:
```
I'll help you create a detailed issue. Let me start by understanding what we're building.

Please provide:
1. what you wish to accomplish
2. any related issue numbers in github
3. any historical plans related to this issue
4. the nature of the ticket itself (bug, feature, refactor, etc)

I'll analyze this information and work with you to create a comprehensive issue.
```

Then wait for the user's input.

## Process Steps

### Step 1: Identify any related issue numbers passed in

If a past github issue is identified as related, you can use the following tool to get its information

<mcp>
<use_mcp_tool>
<server_name>github</server_name>
<tool_name>get_issue</tool_name>
<arguments>
{
  "owner": "ponix-dev",
  "repo": "ponix",
  "issue_number": 3
}
</arguments>
</use_mcp_tool>
</mcp>

Note that the owner, issue_number and repo variables should be replaced with relevant values.  If you are unsure what those values should be, please clarify with the user before callign the tool.

### Step 2: Extend and summarize

Once we have the context for any past issues passed in, you should summarize what you understand so far.  Be curious.  If something needs to be clarified, or if the user has not given enough information, you need to call it out.  Once you feel you have enough information, you will need to create a summarized description using the following format.

```
# [Issue Title]

## Ticket Type
[ticket type goes here]

## Description
[description goes here]
```

This should then be presented to the user to confirm.  Continue this step until both you and the user feel comfortable creating the issue.

### Step 3: Create the issue

Once you have confirmed with the user that issue is ready to be created, you should use the following mcp tool to create the issue. 

<mcp>
<use_mcp_tool>
<server_name>github</server_name>
<tool_name>create_issue</tool_name>
<arguments>
{
  "owner": "ponix-dev",
  "repo": "ponix",
  "title": "{{TITLE}}",
  "body": "{{BODY}}",
  "labels": [{{LABELS}}]
}
</arguments>
</use_mcp_tool>
</mcp>

Note that the owner and repo variables should be replaced with the proper owner and repo name.  If you are unsure what those values should be, please clarify with the user before callign the tool.  You also should use labels.  The folowing labels are available to use:

- bug
- enhancement
- documentation
- refactor

You most likely will only use one of these labels depending upon the goal of what the user wants to accomplish with the issue

### Step 4: Validate that the issue is created

Once you've created the issue, you can validate it exists using the following tool

<mcp>
<use_mcp_tool>
<server_name>github</server_name>
<tool_name>get_issue</tool_name>
<arguments>
{
  "owner": "ponix-dev",
  "repo": "ponix",
  "issue_number": 3
}
</arguments>
</use_mcp_tool>
</mcp>

Note that the owner, issue_number and repo variables should be replaced with relevant values.  If you are unsure what those values should be, please clarify with the user before callign the tool.

### Step 5: Add issue to project

After we've confirmed the issue was created, we will need to add it to our project. We can assume that the fields are accurate except for the item id.  That will need to be the item_id of the issue.

<mcp>
<use_mcp_tool>
<server_name>github</server_name>
<tool_name>add_project_item</tool_name>
<arguments>
{
  "owner_type": "org",
  "owner": "ponix-dev",
  "project_number": 1,
  "item_type": "issue",
  "item_id": 123456789
}
</arguments>
</use_mcp_tool>
</mcp>

### Step 6: Prompt user for next 

Once you have verified the issue is created, you can prompt the user to start implementing the issue.  Please use the following output:

```
Now that the issue is created, do you wish to start working on it? If so, please use `/create_plan [owner] [repo] [issue_number]` to get started!
```

Note in the above output, `[owner]`, `[repo]`, and `[issue_number]` should be from the issue that was just created.