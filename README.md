# Repo manifest for creating Stigmee workspace

## Prerequisites

**The following steps have to be done once.**

Firstly, you have to install a tool that will help you to keep up-to-date the
numerous git repositories stored inside a main folder, named workspace. The
list of git repositories is stored inside a text file named manifest. It also
includes information such as branches or tags or SHA1 of the repositories and
where they have to be installed inside the workspace.

You have to install one of the follow tool:
- [tsrc](https://github.com/dmerejkowsky/tsrc) (recommended).
- [git-repo](https://source.android.com/setup/build/downloading#installing-repo) (less recommended).

Both tools do exactly the same job depicted in the previous paragraph. They have
closed command lines. The `tsrc` is more recommended because because `git-repo`
have major drawbacks that `tsrc` had fixed :
- git-repo on Windows is unofficially maintained and uses internally symlink that is not managed
  by all Windows versions (>= 10) and need administration rights.
- The ultimate point, is that `tsrc` does not detached your branches and
  therefore you will have less risk to loose your work.

Anyway this current git repository contains the manifest for each of these
tools. Only the format of the file is different (yaml for tsrc versus xml for
git-repo).

### Operating systems

Stigmee can be compiled for Linux, Windows 10, and MacOS X. Stigmee is not yet working for MacOS X.

### Mandatory tools to be installed

Install the following tools: `python` (greater or equal to 3.7), `git`, `git-gui`.

- For Linux, depending on your distribution you can use `sudo apt-get install`
- For MacOS X you can install [homebrew](https://brew.sh/index_fr).
- For Windows user you will have to install:
  - Python: https://www.python.org/downloads/windows
  - git and git-gui: and https://git-scm.com/download/win (allows ssh and use conversion CRLF to CR)
  - Ideally have adminstrator rights.

### Install the tsrc tool (recommended way)

Make sure to have Python 3.7 or later installed. The recommended way to install tsrc is to use `pipx`:
```
pipx install tsrc
```

You can also install with pip:
```
python3.9 -m pip install tsrc
```

Note: for MacOS X if you have the following error: `tsrc not found` you can export this line:
```
export PATH="${PATH}:$(python3 -c 'import site; print(site.USER_BASE)')/bin"
```

### Or install the git-repo tool (less recommended)

You will need to install [git-repo](https://gerrit.googlesource.com/git-repo/).

- Linux: The faster way to install is to type:

```bash
mkdir -p ~/.bin
PATH="${HOME}/.bin:${PATH}"
curl https://storage.googleapis.com/git-repo-downloads/repo > ~/.bin/repo
chmod a+rx ~/.bin/repo
```

- MacOS X: The faster way to install is to use [homebrew](https://formulae.brew.sh/formula/repo):

```bash
brew install repo
```

- Window: good luck and see https://gerrit.googlesource.com/git-repo/+/HEAD/docs/windows.md

### Be sure to have a SSH connection to GitHub

You shall install and configure SSH for accessing to GitHub. The simplest way is to
make generate your SSH key with `git-gui` as shown in the following figure

![gitgui](https://i.stack.imgur.com/UdlWS.png)

Else, you can follow steps in this
[inlik](https://docs.github.com/en/authentication/connecting-to-github-with-ssh)
to generate your SSH keys. Once done, you will fetch the whole Stigmee's code
source using SSH protocol. Do not use HTTPS protocol since both manifests are
configured to use SSH.

Avoid to use a passphrase to avoid typing it for each git repos. On Windows the
SSH keys are in `C:\users\<your name>\.ssh` and in Linux in `/home/<your name>/.ssh`.

### Set Stigmee's environment variables

- For Linux and MacOS X, you have this environment variable in your `~/.bashrc` file
(or any equivalent file), the environment variable `$WORKSPACE_STIGMEE` referring to
the workspace folder for compiling Stigmee. It is used by our internal scripts:

```bash
export WORKSPACE_STIGMEE=/your/desired/path/for/workspace_stigmee
```

- For Windows, you can save this variable inside the "System Properties" as
"Environnement Variables".

### Linux Workaround

For the moment Godot does not find correctly the libcef.so while indicated in the
gdnlib file. So for the moment:

```bash
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$WORKSPACE_STIGMEE/stigmee/build
```

## Download Stigmee code source

### Clone Stigmee using tsrc (recommended way)

```bash
cd $WORKSPACE_STIGMEE
tsrc init git@github.com:stigmee/manifest.git
tsrc sync
```

On Linux `tsrc init` will only create the `.tsrc` folder and `tsrc sync`
will clone repositories. On Windows it seems that `tsrc init` also calls `tsrc sync`.

The manifest for tsrc is [manifest.yml](manifest.yml). Note: for some Window
users the color is broken. You can add the option `--color never` and for people
with some low connection, they can the option `--verbose` to see if tsrc is not
frozen. For example:
```
tsrc --color=never --verbose init git@github.com:stigmee/manifest.git
```

### Alternative, clone Stigmee with git-repo (less recommended)

```bash
cd $WORKSPACE_STIGMEE
repo init -u git@github.com:stigmee/manifest.git
repo sync
```

The manifest for git-repo is [default.xml](default.xml). On Linux `repo init` will only create the `.repo` folder and `repo sync` will clone repositories.

**WARNING:**

With git-repo, do not forget it makes your git repos with HEAD detached and add
a remote branch named `m/master` pointing to the branch/tag/sha1 indicated by
the repo manifest. To avoid loosing your modifications (by typing `repo sync`)
you should type:

```bash
git checkout <name-of-the-branch>
```

inside your git repo before starting to work. Please read this
[document](https://github.com/stigmee/doc/blob/main/doc/tuto_git_fr.md#travailler-sur-plusieurs-repo-git)
for more information.

### Stigmee workspace

If everything is working well, you will have the following workspace for
Stigmee (may change):

```
📦workspace_stigmee
 ┣ 📂stigmee             ➡️ Main Stigmee project
 ┃ ┗ 📂build             ➡️ (Generated) Stigmee binaries
 ┃   ┗ 📦stigmee         ➡️ (Generated) Stigmee application
 ┣ 📂doc
 ┃ ┣ 📂API               ➡️ Public documentation
 ┃ ┗ 📂internal          ➡️ Stigmee documention
 ┣ 📂godot
 ┃ ┣ 📂3.4.2
 ┃ ┃ ┣ 📂editor          ➡️ To compile the Godot editor
 ┃ ┃ ┗ 📂cpp             ➡️ Godot C++ API
 ┃ ┗ 📂gdnative          ➡️ Stigmee modules as Godot native modules
 ┃   ┣ 📂stigmark        ➡️ Client for workspace_stigmee/stigmark
 ┃   ┗ 📂browser         ➡️ Chromium Embedded Framework
 ┣ 📂packages
 ┃ ┣ 📂install           ➡️ Scripts for building and continous integration
 ┃ ┃ ┗ 📜build.py        ➡️ Main build script for compiling Stigmee
 ┃ ┣ 📂manifest          ➡️ Manifest knowing all Stigmee git repositories
 ┃ ┣ 📂beebots           ➡️ AI to "bookmark" tabs
 ┃ ┗ 📂stigmark          ➡️ Browser extensions to "bookmark" tabs on private server
 ┣ 📜README.md           ➡️ Link to the installation guide
 ┗ 📜build.py            ➡️ Link to packages/install/build.sh for compiling Stigmee
```

### Compile Stigmee

Please refer to this [documentation](https://github.com/stigmee/stigmee/blob/dev-helloworld-cef/README.md) for more information since this current document is mainly talking about the manifest but as quick summary:

- For Linux and MacOS X:

``` bash
cd $WORKSPACE_STIGMEE
./build.sh release
# Alternative:
# ./build.py debug
```

- For Windows. Call the Visual Studio 2022 prompt `x64 Native Tools Command prompt for VS2022`:

```
cd %WORKSPACE_STIGMEE%
build.py
```

### Keep Stigmee's workspace up-to-date

**The following steps have to done for updating your code source to get your team
mates latest changes.**

1. Check if you have uncommitted changes:

```bash
cd $WORKSPACE_STIGMEE
tsrc status
# Alternative:
# repo status
```

2. Synchronize your code source from your team mate latest changes:

```bash
cd $WORKSPACE_STIGMEE
tsrc sync
# Alternative:
# repo sync
```

**WARNING:**

If you have uncommitted changes on a branch `tsrc sync` or `repo sync` may
failed. You have to commit your changes first (and eventually commit them on
GitHub) or stash them (`git stash`) or delete them. The sync command will not
delete your local commits **except if you are using git-repo and if your are
working on detached branch** (that is why tsrc is more recommended).

### Bash script helper

Here is a small utility to help initializing or synchronizing your Stigmee
workspace.

```bash
export WORKSPACE_STIGMEE=/your/desired/path/for/workspace_stigmee
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$WORKSPACE_STIGMEE/stigmee/build

function stigmee_sync()
{
    if [ "$WORKSPACE_STIGMEE" == "" ]; then
        echo "Please export WORKSPACE_STIGMEE to refer to your desired folder."
        echo "The save the export command in your .bashrc file"
        exit 1
    fi

    if [ -d "$WORKSPACE_STIGMEE/.tsrc" ]; then
        cd "$WORKSPACE_STIGMEE"
        tsrc sync
    else
        mkdir -p "$WORKSPACE_STIGMEE" || exit 1
        cd "$WORKSPACE_STIGMEE"
        tsrc --verbose init git@github.com:stigmee/manifest.git
        tsrc sync
    fi
}
```

### How to update a tsrc manifest safely ?

It is not advised to edit the file in `.tsrc/manifest/manifest.yml` directly,
because `tsrc sync` will silently undo any local changes made to this file.  The
good solution is described
[here](https://dmerejkowsky.github.io/tsrc/guide/manifest/#using_the_apply-manifest_command_to_avoid_breaking_developers_workflow)
and commands for our Stigmee project are summarized here by the following Unix
shell commands:

```bash
# Modify Stigmee's manifest
xdg-open $WORKSPACE_STIGMEE/packages/manifest/manifest.yml

# Check that the changes are OK
cd $WORKSPACE_STIGMEE
tsrc apply-manifest $WORKSPACE_STIGMEE/packages/manifest/manifest.yml

# If so, commit and push manifest changes:
cd $WORKSPACE_STIGMEE/packages/manifest
git commit -a -m "..."
git push

# Now you know that everyone can safely run `tsrc sync`
```
