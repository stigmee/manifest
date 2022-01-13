# Repo manifest for creating Stigmee workspace

## Prerequisites

**The following steps have to be done once.**

You have to install a tool that allows to keep up-to-date several git repositories within a workspace thanks to a file, called manifest, holding the
list of GitHub repositories, their branches or tags or SHA1 and the path inside the workspace where they have to be installed.

You have to install one of the follow tool:
- [tsrc](https://github.com/dmerejkowsky/tsrc)
- [git-repo](https://source.android.com/setup/build/downloading#installing-repo)

They do the same job depict in the previous paragraph. The `tsrc` is more recommanded because because `git-repo` have major drawbacks that `tsrc` does not have :
- No symlink is managed with Windows.
- To use symlinks, you will have to install >= Window 10 and set adminstration rights.
- The ultimate point, is that `tsrc` does not detached your branches and therefore you will have less risk to loose your work.

Anyway this repo contains manifests for each of these tools. Only the syntax of the manifest is different (yaml for tsrc versus xml for git-repo)
and both tools have similar command lines.

### Install the tsrc tool

Make sure to have Python 3.7 or later installed. The recommended way to install tsrc is to use `pipx`:
```
pipx install tsrc
```

You can also install with pip:
```
python3.9 -m pip install tsrc
```

### Install the git-repo tool

You will need to install [git-repo](https://gerrit.googlesource.com/git-repo/).

- Linux: The faster way to install is to type:

```bash
sudo curl https://storage.googleapis.com/git-repo-downloads/repo > /usr/bin/repo
sudo chmod a+x /usr/bin/repo/repo
```

- MacOS X: The faster way to install is to use [homebrew](https://formulae.brew.sh/formula/repo):

```bash
brew install repo
```

- Window: good luck and see https://gerrit.googlesource.com/git-repo/+/HEAD/docs/windows.md

### Be sure to have a SSH connection to GitHub

You shall install and configure SSH for accessing to GitHub.
Follow steps in this [inlik](https://docs.github.com/en/authentication/connecting-to-github-with-ssh) to generate your SSH keys.
Once done, you will fetch the whole Stigmee's code source using SSH protocol. Do not use HTTPS protocol since the manifest is
configured to use SSH.

### Set Stigmee's environment variables

Save this environment variable in your `~/.bashrc` file (or any equivalent file), the environement variable
`$WORKSPACE_STIGMEE` refering to the workspace folder for compiling Stigmee. It is used by our internal scripts:

```bash
export WORKSPACE_STIGMEE=/your/desired/path/for/workspace_stigmee
```

## Download Stigmee code source

**Clone Stigmee with tsrc:**

```bash
cd $WORKSPACE_STIGMEE
tsrc init git@github.com:stigmee/manifest.git
tsrc sync
```

The manifest for tsrc is [manifest.yml](manifest.yml).

**Clone Stigmee with git-repo:**

```bash
cd $WORKSPACE_STIGMEE
repo init -u git@github.com:stigmee/manifest.git
repo sync
```

The manifest for git-repo is [default.xml](default.xml).

**WARNING:**

With git-repo, do not forget it makes your git repos with HEAD detached and add a remote branch named `m/master` pointing to the branch/tag/sha1
indicated by the repo manifest. To avoid loosing your modifications (by typing `repo sync`) you should type:

```bash
git checkout <name-of-the-branch>
```

inside your git repo before starting to work. Please read this [document](https://github.com/stigmee/doc/blob/main/doc/tuto_git_fr.md#travailler-sur-plusieurs-repo-git) for more information.

**Stigmee workspace:**

If everything works well you will have the following workspace for Stigmee:
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
 ┣ 📂beebots             ➡️ AI to "bookmark" tabs
 ┣ 📜README.md           ➡️ Installation guide
 ┗ 📜build.sh            ➡️ A copy of stigmee/build.sh
```

To install Stigmee, please refer to this [documentation](https://github.com/stigmee/stigmee).

### Get Stigmee worksape up-to-date

**The following steps have to done for updating your code source from your team mates latest changes.**

1. Check if you have uncommited changes:

```bash
cd $WORKSPACE_STIGMEE
tsrc status
```

Or:
```bash
cd $WORKSPACE_STIGMEE
repo status
```

2. Synchronize your code source from your team mate latest changes:

```bash
cd $WORKSPACE_STIGMEE
tsrc sync
```
Or:
```bash
cd $WORKSPACE_STIGMEE
repo sync
```

**WARNING:**

If you have uncommited changes on a branch, `repo sync` will failed. You have to commit your changes first (and eventually commit them on GitHub) or simply delete or stash them. The repo command will not delete your local commits **except if your are working on detached branch**.

### How to update a tsrc manifest safely ?

It is not advised to edit the file in .tsrc/manifest/manifest.yml directly, because tsrc sync will silently undo any local changes made to this file.
The good solution is described [here](https://dmerejkowsky.github.io/tsrc/guide/manifest/#using_the_apply-manifest_command_to_avoid_breaking_developers_workflow) and commands summarized here, for our Stigmee project, by the following Unix shell commands:

```bash
cd /tmp
git clone git@github.com:stigmee/manifest.git stigmee-manifest
# Modify /tmp/stigmee-manifest/manifest.yml

# Check that the changes are OK
cd $WORKSPACE_STIGMEE
tsrc apply-manifest /tmp/stigmee-manifest/manifest.yml

# If so, commit and push manifest changes:
cd /tmp/stigmee-manifest
git commit -a -m "..."
git push

# Now you know that everyone can safely run `tsrc sync`
```
