---
title: Backup full git repository as single bundle file
date: 2019-09-27
tags: [bash, git]
---

# Backup full git repository as single bundle file

Git is capable of "bundling" its data into a single file. The [bundle command](https://git-scm.com/docs/git-bundle) 
will package up everything that would normally be pushed over the wire with a git push command 
into a binary file that you can email to someone or put on a flash drive,
then unbundle into another repository.

Following bash function will clone repository and create one 
signle bundle file with nice name:

```shell script
#!/bin/bash

function git_backup() {	
	target=$(echo ${1#*:} | tr / _)		
	git clone --mirror $1 ${target} && cd ${target}
	git bundle create ${2-../}/${target%%.git}.bundle --all
	cd - && rm -rf ${target}
}
```

Usage:

```
git_backup git@github.com:OzzyCzech/dotfiles.git ~/Downloads/
```

PS: Note that **git bundle** only copies commits that lead to some reference 
(branch or tag) in the repository. So *tangling commits* are not stored 
to the bundle.

You can also create nice alias in `.gitconfig` file:

```ini
[alias]
  backup="!gb() { target=$(echo ${1#*:} | tr / _); git clone --mirror $1 ${target} && cd ${target}; git bundle create ${2-../}/${target%%.git}.bundle --all; cd - && rm -rf ${target}; }; gb"
```

For more informtion view https://github.com/OzzyCzech/dotfiles


## Backup whole GitHub account

You can use [GitHub API](https://developer.github.com/v3/repos/) to get list of all [user repos](https://api.github.com/users/OzzyCzech/repos).
Then you have to apply all your bash magic power to getting right names from that.

```shell script
curl -s https://api.github.com/users/OzzyCzech/repos | json_pp | grep full_name | cut -d\" -f4
```

Or there are a number of tools specifically designed for the purpose of manipulating JSON from the command line. 
One of the best seems to me [jq](https://stedolan.github.io/jq/)

```shell script
for repo in $(curl -s https://api.github.com/users/OzzyCzech/repos | jq -r ".[].ssh_url")
do  
  git backup $repo /Volumes/Backup/git
done;
```

## Restore

You can difectly clone repository from bundle file:

```shell script
git clone my-super-file.bundle directory
```
