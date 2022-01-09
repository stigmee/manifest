# Repo manifest for creating Stigmee workspace

## Prerequisites

**The following steps have to be done once.**

### Install the repo command

You will need to install [Google repo command](https://source.android.com/setup/build/downloading#installing-repo). This tool allows
to keep up-to-date several git repositories within a workspace thanks to a XML file the called [manifest](default.xml) holding the
list of GitHub repositories.

- Linux: The faster way to install is to type:

```bash
sudo curl https://storage.googleapis.com/git-repo-downloads/repo > /usr/bin/repo
sudo chmod a+x /usr/bin/repo/repo
```

- MacOS X: The faster way to install is to use [homebrew](https://formulae.brew.sh/formula/repo):

```bash
brew install repo
```

- Window: ???

### Be sure to have a SSH connection to GitHub

You need to install and configure SSH for accessing to GitHub.
Follow steps in this [inlik](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) to generate your SSH keys.
Once done, you will fetch the whole Stigmee's code source using SSH protocol. Do not use HTTPS protocol since the manifest is
configured to use SSH.

## Download Stigmee code source

### Set Stigmee's environment variables

Save this environment variable in your `~/.bashrc` file (or any equivalent file), the environement variable
`$WORKSPACE_STIGMEE` refering to the workspace folder for compiling Stigmee. It is used by our internal scripts:

```bash
export WORKSPACE_STIGMEE=/your/desired/path/for/workspace_stigmee
```

### Clone Stigmee

```bash
cd $WORKSPACE_STIGMEE
repo init -u git@github.com:stigmee/manifest.git
repo sync
```

If everything works well you will have the following workspace:
```
📦workspace_stigmee
 ┣ 📂doc
 ┃ ┣ 📂API               ➡️ Public documentation
 ┃ ┗ 📂internal          ➡️ Stigmee documention
 ┣ 📂stigmee             ➡️ Main Stigmee project
 ┃ ┣ 📂stigmee
 ┃ ┃ ┗ 📂build           ➡️ (Generated) Hold Stigmee's binaries
 ┃ ┗ 📜build.sh          ➡️ Main build script to compile Stigmee
 ┣ 📂godot
 ┃ ┣ 📂3.4.2
 ┃ ┃ ┣ 📂editor          ➡️ To compile the Godot editor
 ┃ ┃ ┗ 📂cpp             ➡️ Godot C++ API
 ┃ ┗ 📂gdnative          ➡️ Stigmee modules as Godot native modules
 ┃   ┣ 📂stigmark
 ┃   ┗ 📂browser
 ┣ 📂stigmark            ➡️ Browser extensions to "bookmark" tabs on private server 
 ┗ 📂beebots             ➡️ AI to "bookmark" tabs
```

To install Stigmee, please refer to this [documentation](https://github.com/stigmee/stigmee).

**WARNING:**
Do not forget that the repo command makes your git repos detached and add a remote branch named `m/master` pointing to the branch/tag/sha1
indicated by the repo manifest (the [default.xml](default.xml) file). To avoid loosing your modifications (by typing `repo sync`) you should type:

```bash
git checkout <name-of-the-branch>
```

inside your git repo before starting to work. Please read this [document](https://github.com/stigmee/doc/blob/main/doc/tuto_git_fr.md#travailler-sur-plusieurs-repo-git) for more information.

### Get Stigmee worksape up-to-date

**The following steps have to done for updating your code source from your team mates latest changes.**

1. Check if you have uncommited changes:

```bash
cd $WORKSPACE_STIGMEE
repo status
```

2. Synchronize your code source from your team mate latest changes:

```bash
cd $WORKSPACE_STIGMEE
repo sync
```

If you have uncommited changes on a branch, `repo sync` will failed. You have to commit your changes first (and eventually commit them on GitHub) or simply delete or stash them. The repo command will not delete your local commits **except if your are working on detached branch**.
