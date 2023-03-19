---
title: "Multiple Git Account SSH Keys Management"
datePublished: Tue Oct 04 2022 18:22:34 GMT+0000 (Coordinated Universal Time)
cuid: clffq7l0p00060alagkdz0iz3
slug: multiple-git-account-ssh-keys-management
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1679249329991/7f610d70-75c0-4265-8bfe-953fc31febfd.png
tags: github, technology, git, ssh-keys

---

As a software developer, I have to work with git a lot. While I work for my organization, I have to use the official GitHub account given to me by the organization and when I am working on my projects I have to use my GitHub account. I will walk through how I set up my SSH keys and configuration to work with 2 different GitHub accounts.

## Generate SSH Keys

I need to create 2 SSH keys for the respective accounts.

```bash
ssh-keygen -t ecdsa -b 521 -C "vishnukyatannawar@gmail.com"
```

```bash
ssh-keygen -t ecdsa -b 521 -C "vishnukyatannawar@officialemail.com"
```

## Add your SSH Keys to ssh-agent

Since I want to add both SSH keys to the `ssh-agent`, I can run the below command.

```bash
ssh-add
```

If you want to add each SSH key to the `ssh-agent` manually then you can use the below command.

```bash
ssh-add -K ~/.ssh/id_personal
```

Here `id_personal` is the filename I used to save the SSH key while generating.

To confirm that your SSH keys are added to `ssh-agent` successfully you can run the below command.

```bash
ssh-add -l
```

This will list the fingerprint of each SSH key that is added to the `ssh-agent`.

## Add SSH Key to GitHub Account

You have to log in to your respective GitHub account and add the newly generated SSH public Keys.

You can find details on how to add SSH Keys to GitHub Account [here](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account).

## Create Config File

Go to the SSH folder.

```bash
cd ~/.ssh
```

Create an empty config file.

```bash
touch config
```

## Add Config File Content

Paste the below content in that config file.

```bash
nano config
```

```bash
# Personal GitHub account
Host github.com
HostName github.com
User git
AddKeysToAgent yes
UseKeychain yes
IdentityFile ~/.ssh/id_personal

# Work GitHub account
Host github.com-work
HostName github.com
User git
AddKeysToAgent yes
UseKeychain yes
IdentityFile ~/.ssh/id_office
```

If you haven’t added any SSH passphrases while creating the SSH keys then remove `UseKeychain yes` lines from the config file.

Please note that the `Host` for the work account is `github.com-work`. You can give any hostname here, but you need to remember this.

## How To Clone Repo?

You don’t have to do any changes to the git URL when you clone the repo for your personal GitHub account.

But, when you are going to clone a repo for your work GitHub account then you need to make the following changes.

**From**

```bash
git clone git@github.com:<repo path>.git
```

**To**

```bash
git clone git@github.com-work:<repo path>.git
```

This will ensure that for host `github.com-work`, `~/.ssh/id_office` SSH key will be used as an identifier.

## Configure [user.name](http://user.name) and [user.email](http://user.email)

While you are inside your repo folder run the below command.

```bash
git config user.name "<work username>"
git config user.email "<work email address>"
```

## Handle Other Accounts

If you have a GitLab or Github Gist account then you have to update the config and add respective hosts.

```bash
# Personal GitLab Account
Host gitlab.com
HostName gitlab.com
User git
AddKeysToAgent yes
IdentityFile ~/.ssh/id_personal

# Personal Gists Account
Host gist.github.com
HostName gist.github.com
User git
AddKeysToAgent yes
IdentityFile ~/.ssh/id_personal

# Work Gists Account
Host gist.github.com-work
HostName gist.github.com
User git
AddKeysToAgent yes
IdentityFile ~/.ssh/id_office
```

Please feel free to leave comments, if you have any issues with the setup.