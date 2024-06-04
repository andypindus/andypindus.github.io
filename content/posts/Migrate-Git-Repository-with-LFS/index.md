---
title: "Migrate Git Repository with LFS"
date: 2024-05-14
draft: false
tags: ["Git", "SDLC"]
categories: ["DevOps"]
---

## Problem

When it comes to migrating a Git repository containing Large Files (LFS), it's essential to address a fundamental aspect: Git repositories are generally not optimized for handling large files, typically exceeding 10MB. However, in practical development scenarios, it's not uncommon for developers to include such files alongside their projects.

Imagine encountering the need to migrate such a repository, perhaps from GitLab to GitHub. Unfortunately, the standard Git migration tools provided by these services often stumble when dealing with repositories containing LFS files. It's not uncommon to encounter errors during the migration process. In some fortunate cases, while the migration may complete, crucial metadata might be missing, leading to a fragmented or incomplete repository on the destination platform.

Sure, you can say that there are some tools that could help with this, but personally, I found them overcomplicated for the task, and almost all of them require rewriting the commit history. In my case, I want my history, tags, and metadata to remain intact. That's why I opted for a more straightforward approach using Bash commands.

So, what's the solution?

## Solution

After extensive investigation and experimentation, I've developed a set of Bash commands that streamline the migration process. These commands are consolidated into a script for ease of use. Here's how you can utilize it:

```bash
#!/bin/bash

# Check if number of arguments is correct
if [ "$#" -ne 2 ]; then
    echo "Usage: $0 <origin> <destination>"
    exit 1
fi

# Assign input parameters to variables
origin=$1
destination=$2

# Clone bare repository
git clone --mirror "$origin"

# Access the repository
repo_name=$(basename "$origin" .git)
cd "${repo_name}.git/"

# Fetch LFS files
git lfs fetch --all

# Add a new remote
git remote add github "$destination"

# Push LFS files to new remote
git lfs push github --all

# Remove the old remote
git remote remove origin

# Push the bare repo to the new remote
git push --mirror "$destination"

```

### Usage: 
```bash
./script "origin_repo" "destination_repo"
```

Feel free to further refine or customize the script to suit your specific migration needs.