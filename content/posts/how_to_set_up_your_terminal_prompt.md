---
title: "How to config your mac terminal prompt"
date: 2021-02-20T14:41:40-08:00
draft: false
---

I recently bought a new Mac M1. While setting up this new mac, I realize it might be useful to record some tutorials | steps how to do certain useful things, cause you never know when next time you'd need it. Without recording it, likely we'll need to google little things here and there again, "waste" on repatitve tasks and Hopefully this one helps you save some time as well!

Alright, right into our topic. If you haven't customized your terminal prompt before, your prompt will likely to be similar to this 
<img src="./imgs/original_prompt.png" width="200">
The composition of it is username + @ + pc_host_name + current_dir_name. I personally feel the pc_host_name info is something I rarely use, thus wanting to remove it. While git branch info is important to me, since my daily work is highly involved with git repository. Adding it to the prompt can significantly reduce the times I typed `git branch v`.

There's a value specificlly designed for terminal prompt called PS1(prompt string one). If we open ~/.bash_profile and type `export PS1=''` and then `source ~/.bash_profile`. Your terminal will be prompt string will be empty, since you overwrite this value to be a empty string, as simple as that. What we need to do, is to find the right representation string of things we want and assing to this value.

The m1 by default uses zsh terminal, and you can find documentation [here](http://zsh.sourceforge.net/Doc/Release/Prompt-Expansion.html#Prompt-Expansion). If your need is similar to mine, keep the username and current dir, then we can type `export PS1="[%n:%1~]"` 
`%n` for username while `%~` for current dir, and 1 in between means we only want the last element, otherwise it could be fairly long string.
After this, your prompt should look like <img src="./imgs/prompt_after_removing_host.png" width="200" height="23">

The next step is to figure out how to retrieve git branch and attach this value into our PS1 variable.
```
parse_git_branch() {
    git branch 2> /dev/null | sed -e '/^[^*]/d' -e 's/* \(.*\)/ (\1)/'
}
```
If you don't have interest about how this works you can simply put above func into your bash profile and then change your PS1 value to be 
`export PS1="[%n:%1~]$(parse_git_branch) $ "` and everything will just work.

However, it's also not too hard to understand above cmd, and it might help you in other cases as well! Let's decomposite this cmd to several parts
`git branch` --> print out all branches, the current branch is leading with *

`2> /dev/null` --> you can think of /dev/null as a black hole, things that fed in will be discard | disappear, in here if there's any error, we discard it.

`sed -e '/^[^*]/d'` --> `^[^*]` is a regex means find all lines that starts with non-star character, d flag means delete it, thus the only one left is current branch

`sed -e 's/* \(.*\)/ (\1)/'` --> s means substitute, substitude `* xxx` to `(xxx)` note the first parenthesis is for regex group. \1 refers to that.

For more detailed sed tutorial, you can have a look [here](https://www.tutorialspoint.com/unix/unix-regular-expressions.htm)