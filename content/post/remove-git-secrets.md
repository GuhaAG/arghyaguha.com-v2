+++
author = "Arghya Guha"
title = "Remove sensitive data from your git repository"
date = "2020-08-10"
description = "Remove sensitive data from your git repository"
tags = [
    "git",
    "github",
    "security",
    "privacy"
]
categories = [
    "security",
    "privacy"
]
aliases = ["remove-git-secrets"]
+++

Remove accidentally pushed sensitive data from your git repository without leaving a trace.

This article was first published on my dev.to page, check it here - https://dev.to/guha/remove-sensitive-data-from-your-git-repository-1ia2.
<!--more-->
---

### Why

We've all done it at some point or the other - committed and pushed a password or an API key into a remote repository. 
Rookie mistake and yet there are [thousands](https://www.ndss-symposium.org/wp-content/uploads/2019/02/ndss2019_04B-3_Meli_paper.pdf) of secrets pushed into public github repositories every single day. 

So what do you do when that happens ? Well, the first thing you *MUST* do is invalidate the key/password immediately. 

>Invalidating the secret is absolutely *a must* and especially if you are using a third party API key make sure to contact the API provider to ensure the key is invalidated.

###What next though ? 

Here in this article i will go over two ways you can clean up your repository after the erroneous push. 

>Why not just delete the file/data and commit and push again. That should fix it right ? 
Nope. The beauty of Git is that it stores the history of every file. So even if your latest commit did remove the data, it still exists in the repository - on your local and on the remote. 

## The `git rebase` approach

This approach is great if you're only one or two commits ahead of your mistake but keep in mind you will have to fix every commit that contains the bad data.

### How

  - Fix your local branch (delete the files/remove the keys etc).
  - Stage and Commit your changes.
  - `git rebase -i HEAD~n`

> Here `n` is the number of commits you want to rebase

Let's look at an example screen of what this will do for you

![rebase editor example](https://dev-to-uploads.s3.amazonaws.com/i/b0qwu8ltjp3gfoun3pe8.png)

  - Change `pick` to `drop` to delete each commit that contains 
     the data you want to get rid of.

  - Fix conflicts if any.
  - Stage the changes.
  - `git rebase --continue`

![Alt Text](https://dev-to-uploads.s3.amazonaws.com/i/snxu8yv9mmbo0igp5tie.png)

  - Commit and force push (`git push -uf...`) changes to remote.

And that's it ! Check your repo history, the bad data should be gone.

>While removing sensitive data is probably the most common use-case, another common scenario is to get rid of temp files that you committed by mistake. For instance it is common to have large temp files in a machine learning project (datasets etc..) and pushing them into your repository by mistake might slow it down to a crawl.

## The `git filter-branch` approach

This command will remove the file(s) with the bad data from your repository history.
For this reason it is important to know that _you will have to do this for every `path` the file has resided on. What that means is that for instance if you changed the directory name you will have to do this for the old name/path and the new one as well because git keeps both records in its history._

### How

- Run the following on your local
```
   git filter-branch --force --index-filter \
    'git rm --cached --ignore-unmatch FILE/PATH/HERE' \
        --prune-empty --tag-name-filter cat -- --all
  ```

>If you have any stashed changes you will lose them.

- Force push your changes to remote.

### Food for thought

So that's some information on two common ways of repository cleanup, but always make sure you understand what you are doing before you push changes into an important remote repository.
 
Moreover messing with git history and overwriting a remote repository is not for the faint-hearted. 

Especially if you have collaborators on your project make sure to communicate with them before taking such drastic steps as they will have to `rebase` your changes and not `merge` them as they normally would. 

Ultimately the best solution is to not create problems that require such drastic solutions. 

The only way to do so is through `workflow` and `tooling`. 

Consider using tools like [`git-secrets`](https://github.com/awslabs/git-secrets), remember to start every project with a comprehensive `.gitignore` and explore better ways of secret handling like [vault-gatekeeper](https://github.com/nemosupremo/vault-gatekeeper).
