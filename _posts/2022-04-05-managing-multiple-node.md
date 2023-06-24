---
layout: post
title: Managing node using nvm
date: 2022-04-05
categories: web-dev
subtitle: Effectively manage multiple node versions in a large team
share-img: /assets/images/managing-multiple-node/shareImg.jpg
cover-img: /assets/images/managing-multiple-node/heroImage.jpg
thumbnail-img: ""
css: /assets/css/style.css
---

I work on a project where we have microservices and some of them use a different version of node.

This creates a lot of problems because few developers install only one version of node and use it everywhere.  
Some install multiple versions but forget to switch to the right version for that microservice.

Over the years, I've found nvm to be very helpful in managing multiple node versions. 
It also automates switching to the right node version in the terminal.

#### Installing nvm
Open the terminal and run the command below.
```zsh
$ curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
```
You can read the latest installation instructions [here](https://github.com/nvm-sh/nvm).

#### Using nvm

Installing specific node versions
```zsh
$ nvm install 16

$ nvm install 12.22.11
```

Switching node version
```zsh
$ nvm use 16
```
List installed node versions
```zsh
$ nvm ls
```
At this point, you can manually change to whichever version of node you want.  
Whichever version you pick, that version of node will be used across all the terminals you open or whichever repo you are in.

#### Automating `nvm use` command
I use the terminal for almost all the npm scripts that I need to run.
And I'm too lazy to check which version of node does a repo use.
Neither do I want to run nvm use command every time I change the repo I'm working in.

To automate this we will create a `.nvmrc` file at the root of the repository.
You can use any one of the below commands. Remember to change the command to the right version you use.
```zsh
$ echo "16.14" > .nvmrc

$ echo "16" > .nvmrc

$ echo "lts/*" > .nvmrc # to default to the latest LTS version

$ echo "node" > .nvmrc # to default to the latest version
```
Now that the `.nvmrc` file is present, we don't need to remember which node to use.  
We can simply run the command `nvm use`.  
nvm will search for the version in `.nvmrc` and based on that file it will switch to the right node.

The next step is to automatically run the `nvm use` command when I enter a repo.  
To make this work there are multiple ways, but the easiest is to copy-paste the following in `.zshrc`

```zsh
# place this after nvm initialization!
autoload -U add-zsh-hook
load-nvmrc() {
  local node_version="$(nvm version)"
  local nvmrc_path="$(nvm_find_nvmrc)"

  if [ -n "$nvmrc_path" ]; then
    local nvmrc_node_version=$(nvm version "$(cat "${nvmrc_path}")")

    if [ "$nvmrc_node_version" = "N/A" ]; then
      nvm install
    elif [ "$nvmrc_node_version" != "$node_version" ]; then
      nvm use
    fi
  elif [ "$node_version" != "$(nvm version default)" ]; then
    echo "Reverting to nvm default version"
    nvm use default
  fi
}
add-zsh-hook chpwd load-nvmrc
load-nvmrc
```
For bash, follow this [link](https://github.com/nvm-sh/nvm#bash) to find the code snippet to paste in `.bashrc`

Now, commit and push the `.nvmrc` files into repositories.

If everyone in your team uses nvm with the bash/zsh integration then you shouldn't have issues 
like `package-lock.json` being modified without making any changes to `package.json`

That's all you need to do. Happy Coding!

#### Photo Credits
Cover image - [Computer programmer photo created by rawpixel.com - www.freepik.com](https://www.freepik.com/photos/computer-programmer)
