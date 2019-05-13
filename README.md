# Amber
A ComputerCraft package manager

A package manager is a software system that allows you to break complex programs down into reusable bits of code called packages. With Amber, you can build your own packages, use packages that others have written, and host repositories of packages on in-game computers or GitHub. You can also build a self-contained program called a _nugget_: a single file that contains a program and all of the dependencies (APIs, etc.) it needs to run.

## Getting started
There are several ways you can get started with Amber.

### Install an Amber package (without installing Amber)
Let's say you want to install an Amber package withot installing Amber itself. Just run this command:

`> pastebin run ACtzcSMD install <package>`

To fully install Amber with all of its libraries, run: `> pastebin run ACtzcSMD install amber`

### Install an Amber package (with a basic Amber installation)
Let's say you want to save Amber to your computer for later use. Run these commands:

`> pastebin get ACtzcSMD amber`

`> amber install <package>`

To fully install Amber with all of its libraries, run: `> amber install amber`

### Install an Amber package (without Pastebin)
This method is especially useful when running Amber on older versions of ComputerCraft (`pastebin` on old versions like 1.74 is broken; see [ComputerCraft#64](https://github.com/dan200/ComputerCraft/issues/64)).

1. `> edit amber`
2. Type this, save the file, and exit: `load(http.get("http://ambernuggets.cc/get/latest").readAll(), "amber", "t", _ENV)(...)`
3. `> amber install <package>`

To fully install Amber with all of its libraries, run: `> amber install amber`

### Run Amber from a disk and install a package onto the computer
Let's say you've installed Amber on a disk using one of the methods above. Now you can insert that disk into a disk drive attached to another computer and run the following commands to install a package on that computer:

`> cd /`

`> /disk/amber install <package>`

## Client commands
The Amber client supports several commands; run `> amber help <command>` for more details and usage instructions. The client operates on packages based on the current directory, so `cd` to the directory in which you want to install or update packages before running Amber.

### install
Installs or updates specified packages and their dependencies.

`> amber install <package1> <package2>...`

**Examples:**
- Install net library: `> amber install net`
- Run Amber from a disk and install a package into the current directory: `> /disk/amber install net`
- Install multiple packages: `> amber install amber-client amber-repo-github`

### update
Updates specified packages, or all installed packages (if no packages are specified).

`> amber update [<package1> <package2>...]`

**Examples:**
- Update two packages: `> amber update net amber-client`
- Update all installed packages: `> amber update`
- Run Amber from a disk and update all packages installed in the current directory: `> /disk/amber update`

### remove
Removes specified packages. Automatically removes empty directories left behind as a result of the operation. Does not remove the dependencies of the specified packages or check whether the packages you are removing are needed by other packages.

`> amber remove <package1> <package2>...`

**Examples:**
- Remove two packages: `> amber remove amber-client amber-core`

### show
Shows detailed information about a package, including its description, the version currently installed (if any), the latest version available, the packages it depends on, and a list of files installed as part of the package (if any).

`> amber show <package>`

**Examples:**
- Show details of the amber-client package: `> amber show amber-client`

### list
Lists packages, optionally filtering by name and state (installed or available).

`> amber list [name:<pattern>] [state:<installed|available>]`

**Examples:**
- List all installed packages: `> amber list state:installed`
- List all available (i.e. not installed) packages whose names start with "amber": `> amber list name:amber* state:available`
- List all packages, whether installed or not: `> amber list`

### repository add
Adds a repository to the client's configuration.

`> amber repository add <url>`

**Examples:**
- Add the [prism-rails](https://github.com/danports/prism-rails) [GitHub repository](#github): `> amber repository add https://github.com/danports/prism-rails`
- Add a [local repository](#localdirectory) for the mypackages directory: `> amber repository add file://mypackages`
- Add an [Amber server](#amberserver) as a remote repository: `> amber repository add amber://`

### nugget save
Builds a nugget (a self-contained package that contains all of its dependencies) and saves it to a file, optionally with additional packages and files included. File paths are assumed to be relative to the current directory.

`> amber nugget save <package> [package:<name>] [file:<path>]...`

**Examples:**
- Build a nugget for the amber package: `> amber nugget save amber`
- Build a nugget for the amber-client package, including one other package and two additional files: `> amber nugget save amber-client package:amber-repo-github file:apis/amber/sources/myrepo1 file:apis/amber/sources/myrepo2`

### nugget run
Runs a nugget with the provided arguments without saving it to a file. Useful for trying out packages before you install them.

`> amber nugget run <package> [...]`

**Examples:**
- Run the amber-client package without installing it: `> amber nugget run amber-client list name:amber*`

### deploy
Instructs an Amber server to broadcast the latest available version of a package to the network. Any computers with the [autoupdater](#autoupdater) running with the specified package installed will automatically install the latest version and reboot. The server address, if not specified, will default to `amber://` (i.e. any Amber server the computer can contact).

`> amber deploy <package> [<server>]`

**Examples:**
- Broadcast an update for the `net` package to the network: `> amber deploy net`
- Connect to a specific Amber server named "updates" and broadcast an update to the `mypackage` package to that server's network: `> amber deploy mypackage amber://updates`

## Client configuration
You can configure the repositories where the Amber client will look for available packages. By default, the Amber client will look only in the [amber](https://github.com/danports/amber) and [prism-core](https://github.com/danports/prism-core) repositories. To add other repositories, create or edit the `.repositories` file in the same directory as the Amber client, or add a new file in the `apis/amber/sources` directory (again, relative to the Amber client's directory). When resolving a package, the first repository in the configuration to return a valid package wins. The entries in the `.repositories` file take precedence over the entries in the `apis/amber/sources` files. The format of a repository list file is simple:

```
{
	<repository1>,
	<repository2>,
	...
}
```

where each repository is a table with a `type` key and possibly other keys depending on the repository type. Here are the different types of repositories available:

### GitHub
This repository type pulls packages from GitHub repositories. It requires that the `amber-repo-github` package be installed. (This package is automatically installed if you run the `> amber install amber` command.)

**Configuration:**
```
{
	type = "GitHub",
	user = <repository-owner:string>,
	name = <repository-name:string>,
	[defaultVersion = <string>,]
	[path = <string>,]
	[auth = {
		type = "oauth",
		user = <github-username:string>,
		token = <github-api-token:string>
	}]
}
```

The optional `defaultVersion` key specifies the branch, tag, or commit of the repository to fetch packages from by default (unless a particular version of a package is requested). If it is omitted, Amber will fetch the version associated with the latest published release of the repository by default.

The `path` key specifies the directory within the repository where Amber should expect to find a list of packages. It defaults to `src` if not specified. See [Authoring packages](#authoring-packages) for more information on the structure of packages.

The `auth` key is optional, but highly recommended, as the GitHub API imposes rate limits for anonymous users that you will likely run up against fairly quickly. You can create a personal access token on [GitHub](https://github.com/settings/tokens).

**Example:** Fetch packages from the bleeding edge (`master` branch) of the `prism-core` repository using an access token for the GitHub user named danports:
```
{
	type = "GitHub",
	user = "danports",
	name = "prism-core",
	defaultVersion = "master",
	auth = {
		type = "oauth",
		user = "danports",
		token = "4543b2a..."
	}
}
```

### AmberServer
This repository type fetches packages from an [Amber server](#server). The optional `server` key specifies a specific server to connect to (defaults to `"amber://"`, i.e. any Amber server the client can connect to).

**Example:** Fetch packages from the Amber server with computer ID 7:
```
{
	type = "AmberServer",
	server = "amber://7"
}
```

### LocalDirectory
This repository type fetches packages from a local directory on your in-game computer, which is especially useful on an Amber server - it allows you to host packages there and make them available to other computers on your network without creating a GitHub repository and importing your code there. It requires that the `amber-repo-local` package be installed.

**Configuration:**
```
{
	type = "LocalDirectory",
	[path = <string>]
}
```

The optional `path` key (which defaults to `/packages` if not specified) indicates the location of the package repository on your computer. Amber expects that the package repository directory will have the following structure:

```
packagename1
	version1 (e.g. 1.0)
		file1
		file2
		...
	version2
	...
packagename2
...
```

**Example:** Fetch packages from the local `/repo` directory:
```
{
	type = "LocalDirectory",
	path = "/repo"
}
```

## Server
An Amber server is essentially just a computer that provides packages to any computer that requests them over the network. Running an Amber server, while not necessary, provides several benefits, especially for larger ComputerCraft setups:
- A server allows you to distribute packages to multiple computers without having to configure the repositories individually on each computer.
- A server caches some packages, speeding up package delivery.
- A server supports broadcasting package updates to all computers on the network, which saves you from having to update each computer manually. (Handy when you have dozens or hundreds of computers to maintain!)

To set up a computer as an Amber server, run the following command:

`> amber install amber-server`

Once the server is installed, you can configure the repositories it pulls packages from the same way you would for any Amber client. If you want the server to retrieve packages from a GitHub repository or local directory, make sure to install the appropriate packages as well (`amber-repo-github` or `amber-repo-local`, respectively).

Once your server is up and running, configure your clients to fetch packages from it by adding a `{type = "AmberServer"}` entry to their repository configuration. If you install the `amber-client` package, the client will fetch packages from the server by default. Note that, as with any rednet communications, the client will need to be connected to the same network as the server and within communication range to retrieve packages.

## Autoupdater
If you build a program with the [Prism framework](https://github.com/danports/prism-core) and deploy it with Amber, you can integrate the automatic updates functionality Amber provides through the `amber-autoupdater` package to easily update the computers on which the program is installed.

### Integrating the API into your programs
First, add a dependency on the `amber-autoupdater` package in your package manifest:
```
{
	...
	dependencies = {
		...
		{name = "amber-autoupdater"}
	}
}
```

Then, in your program, add the following lines somewhere before you call `events.runMessageLoop()`:
```
os.loadAPI("apis/autoupdater")
autoupdater.initialize()
```

### Deploying package updates locally
To update all packages installed on a computer, simply walk up to it and press the `u` key. The computer will automatically install the latest version of all installed packages and reboot. This will work provided that a program with the autoupdater API is running and provided that you haven't added your own handler for the `char` event in your program.

### Deploying package updates remotely
To broadcast a package update to your entire network and automatically update all computers with that package installed, run the [`deploy` command](#deploy).

## Authoring packages
An Amber package is simply a directory with an optional manifest file. The name of the directory is the name of the package. When installed with the Amber client, all of the files in that directory (with the exception of the manifest) are copied to the appropriate directory relative to the current directory.

### Manifests
Providing a manifest for your package allows you to specify a description, dependencies, and other properties of your package. To provide a manifest, create a file named `.manifest` in your package directory with the following contents:
```
{
	[description = <string>,]
	[entryPoint = <string>,]
	[installWithoutTracking = <bool>,]
	[dependencies = {
		{name = <string>, [version = <string>]},
		{name = <string>, [version = <string>]},
		...
	}]
}
```

`description`, if provided, should be a brief summary of the purpose of your package. It is displayed to users when they run the `amber show` command.

`entryPoint` specifies the main program in your package and is used only when [building nuggets](#building-nuggets).

`installWithoutTracking`, when set to `true`, specifies that installation of the package should not be recorded in the computer's package catalog. This is useful when renaming a package: you can create a package with the new name and remove all of the contents of the old package, setting `installWithoutTracking` to `true` and creating a dependency on the package with the new name. When clients update the old package, they will install the package with the new name and remove the package with the old name.

`dependencies` specifies a list of packages that this package depends on. These packages (if available) will automatically be installed with this package. A dependency is specified as a package name with an optional package version. If a version is provided, Amber will install that specific version of the package; otherwise, Amber will install the latest available version of that package.

**Example:** A manifest for a package that depends on the latest versions of the `events`, `net`, and `amber-core` packages and specifies that the program named `amber` is the program that should be called when a nugget built for this package is run.
```
{
	description = "Amber command line client",
	entryPoint = "amber",
	dependencies = {
		{name = "events"},
		{name = "net"},
		{name = "amber-core"}
	}
}
```

### Template files
Sometimes it is convenient for a package to install files that you expect the user to modify - configuration files, for example. To include such a file in your package, create a file with a name ending in `.t`. Let's say you add a file named `config.t` to your package. When Amber installs your package, it will copy that `config.t` file to its destination (overwriting any existing `config.t` file). It will also copy it to `config` - but only if that file does not already exist - and, if Amber was run interactively, will immediately open that new `config` file for editing using the default `edit` program.

## Building nuggets
A nugget is a single file that contains a package and all of its dependencies (and can optionally include other packages and files). They can be built and saved to disk with the [`nugget save` command](#nugget-save) or run directly without saving using the [`nugget run` command](#nugget-run). The primary benefit a nugget provides is portability: users can run or share nuggets by copying a single file.

To build a nugget, the primary package (or one of the other packages in the nugget) must have a entry point specified. An entry point is the program that will be executed when the nugget is run, with all of the arguments provided to the nugget. An entry point is specified using the `entryPoint` key in a package manifest. If the primary package in a nugget has a entry point specified, that will be chosen; if it does not, Amber will look for an entry point in the other packages in the nugget (and will return an error if it finds zero candidates or more than one candidate).

Internally, nuggets work by intercepting calls to the `fs` API, making it appear as if all of the files packaged into the nugget are actually installed on the computer - in essence, creating a virtual file system layered on top of the computer's existing file system. Files in the nugget's file system will take priority over files on the computer's file system. Programs should be able to run and make calls to `dofile`, `loadfile`, `os.loadAPI`, and other functions and everything should just work. However, only `fs.open`, `fs.isDir`, and `fs.list` calls are implemented at present; if your package relies on other parts of the `fs` API like `fs.exists`, it may not function correctly as a nugget (see [#48](https://github.com/danports/amber/issues/48)).