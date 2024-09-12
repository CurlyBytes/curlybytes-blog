<!--
author: Francisco Abayon
head: https://www.gravatar.com/avatar/dd90d96a247f981286ae0092abc026ba.jpg
date: 2024-09-12    
title: SSH On Multiple Git Accounts
tags: ssh, git, security, github, devops, terminal, bash, cli, mac, windows, linux, dx, workflow, automation
images: blog/img/multiple-git-accounts.png
category: Developer Experience
status: publish
summary: How do juggle multiple git accounts to different git repository in a secure way on the same machine
-->
## Scenario in a Nutshell
I have a lot of git accounts with different git repository both work and personal and the same time im handling Windows Desktop PC, A personal laptops and company own mac book. With that i spend my days on desktop when working home, or remote working by bringing my personal laptop on a remote island and vendor/stakeholder meeting ranging from coffee shop to corporate board room meeting with my macbook.

## Problem Context
And the dilema is now handling the authentication on each corresponding repository per account with per device. And it annoys each time on asking credentials when manually configure and relogin git accounts on the credential manager built on those devices and it also clash to github that it is not allow to have the same ssh key being reuse to another account due to security issues. Thats why its quite hassle to work in git. I just doing that my personal git repository is using cli(git bash, zsh) while the corporate account is using the gui version(github desktop) just to temporary resolve the complete.

## Pathfinding Solution
Of course im not the only who have this problem, i'm pretty sure some of you are overemploy or doing side hustle whether extra income or hobby projects and you manage to make it work it to. What solution im looking for as follows:

* It should have not an impact on cloning whether cli or gui, the urls on cloning should be consistent
* Need to configure it Once on the git repository per device
* It should be well organize
* It should be centralize to support multiple OS 
* The structure should be follow on the docs provided

So first we should have **gitconfig** file, this will be auto created once we define few global key/pair value configurations with *name* and *email* which can be found at **/Users/curlybytes/gitconfig** (Mac OS) or **C:/users/curlybytes/gitconfig** (Windows OS)


```git
git config --global user.name "<<replace your name here>>"
git config --global user.email "<<replace your email here>>"
```
Next generate your key, currently i have 4 git repository needed which means i to generate four times(1 personal github, 1 personal azure devops, 1 company github, 1 company azure devops)

For Azure Devops
``` powershell
ssh-keygen -t rsa-sha2-512 -C "johndoe@testing.com"
```

For Github
``` powershell
ssh-keygen -t ed25519 -C "johndoe@testing.com"
```
* P.S. On windows os server, by default the openssh is disable, you need to enable thisone to make it work*


Each ssh key should be have standard name and pattern to easily distinguish it, upload this ssh keys to its corresponding repository account. Add it to your personal GitHub account using this(https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account) and for Add it to your work Azure account using this(https://learn.microsoft.com/en-us/azure/devops/repos/git/use-ssh-keys-to-authenticate?view=azure-devops#step-2--add-the-public-key-to-azure-devops-servicestfs).

Once done you may proceed to configure the pointing of hostname with alias on each corresponding ssh key and git account by creating a **config** file inside in *.ssh* folder and it will look like this
``` powershell
# Github Personal
Host githubpersonal
    Hostname github.com 
    User git 
    IdentitiesOnly yes
    IdentityFile ~/.ssh/git_github_personal

# Github ibsdev
Host githubibsdev
    Hostname github.com 
    User git 
    IdentitiesOnly yes
    IdentityFile ~/.ssh/git_github_ibsdev

#Azure DevOps RAFI MFI
Host azdorafimfi
    Hostname ssh.dev.azure.com
    User git 
    IdentitiesOnly yes
    IdentityFile ~/.ssh/git_azdo_rafimfi

#Azure Devops Personal
Host azdopersonal
    Hostname ssh.dev.azure.com
    User git 
    IdentitiesOnly yes
    IdentityFile ~/.ssh/git_azdo_personal
```
By explicitly defining the **Host* , you can use that alias for identifier on each key, the **Hostname** is the url whenever performing the cloning,pull/push or any git command to the repository, **User** helps to identify in which account to name and email address is going to reflect on it, by adding **IdentitiesOnly** makes the configuration to follow explicity ssh key instead of standard *id_rsa* file and lastly the **Identityfile** to map the key of that specific alias

Next modify the the **gitconfig** because the dynamic pointing per organization per key is done here and the auto mapping of user, remeber the **User* in the **config** file, this will identify the code and its corresponding author
``` powershell
[filter "lfs"]
	process = git-lfs filter-process
	required = true

[includeif "gitdir:/Users/curlybytes/Developer/repository/githubpersonal/"]
	path = ./.gitconfig-github-personal

[includeif "gitdir:/Users/curlybytes/Developer/repository/githubibsdev/"]
	path = ./.gitconfig-github-ibsdev

[includeif "gitdir:/Users/curlybytes/Developer/repository/azdorafimfi/"]
	path = ./.gitconfig-azdo-rafimfi

[includeif "gitdir:/Users/curlybytes/Developer/repository/azdopersonal/"]
	path = ./.gitconfig-azdo-personal

[user]
	name = John Doe
	email = johndoe@testing.com
[color]
	ui = auto

```
we are adding the config each organization has its own specific folder and git authors, so that whenever we clone a specific project on specific organzation, we just arrange and clone it accordingly base on **gitdir** we define it. Let's use period, forwardslash and period, this is going to work on different OS, i did try the tilde, but is not functioning as expected. **.gitconfig-github-personal** is save side by side with **.gitconfig** in outside *.ssh* folder.
```.gitconfig-github-personal
[user]
	name = Curlybytes
	email = johndoe@testing.com
[core]
	sshCommand = "ssh -i ~/.ssh/git_github_personal"

```
With this we can identity that the author is authenticating via ssh with that *sshCommand* in the git config. Once done you need to add those identities
``` powershell
ssh-add ./git_github_personal
```

and validate those identities with command 
``` powershell
ssh-add -l
#or
ssh-add -L
```

Once the identities are added in the device, validate the connection by calling the alias.

``` powershell
ssh -T azdopersonal
ssh -T azdorafimfi
ssh -T githubpersonal
ssh -T githubibsdev
```

The output of azure devops looks like this
``` powershell
remote: Shell access is not supported.
shell request failed on channel 0
```
While  output of Github looks like this
``` powershell
Hi YOURUSERNAME! You've successfully authenticated, but GitHub does not provide shell access.

```

###Referrence
* https://www.youtube.com/watch?v=6lA0oPoFCAE
* https://blog.serialexperiments.co.uk/posts/git-ssh-azure-repo/
* https://psychowhiz.medium.com/configuring-multiple-ssh-keys-for-git-on-the-same-device-41c29320e5fe
* https://superuser.com/questions/1717601/how-to-fix-server-shell-request-failed-on-channel-0-on-windows-11-openssh-defa