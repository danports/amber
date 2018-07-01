# Amber
A ComputerCraft package manager

## Getting started
There are several ways you can get started with Amber.

### Install an Amber package without installing Amber (from Pastebin)
`> pastebin run ACtzcSMD install <package>`

To fully install Amber with all of its libraries: `> pastebin run ACtzcSMD install amber`

### Install an Amber package (from Pastebin)
You can use this method to install a package onto a computer or a disk.

`> pastebin get ACtzcSMD amber`
`> amber install <package>`

To fully install Amber with all of its libraries: `> amber install amber`

### Install an Amber package (without Pastebin)
You can use this method to install a package onto a computer or a disk. This method is especially useful when running Amber on older versions of ComputerCraft (`pastebin` on old versions like 1.74 is broken; see https://github.com/dan200/ComputerCraft/issues/64).

1. `> edit amber`
2. Type this, save the file, and exit: `load(http.get("http://ambernuggets.cc/get/latest").readAll(), "amber", "t", _ENV)(...)`
3. `> amber install <package>`

To fully install Amber with all of its libraries: `> amber install amber`

### Run Amber from a disk and install a package onto the computer
`> cd /`
`> /disk/amber install <package>`

## Client commands
The Amber client supports several commands; run `> amber help <command>` for more details and usage instructions. The client operates on packages based on the current directory, so `cd` to the directory in which you want to install or update packages before running Amber.

### install
Installs or updates specified packages and their dependencies.

`> amber install <package1> <package2>...`

Examples:
- Install net library: `> amber install net`
- Run Amber from a disk and install a package into the current directory: `> /disk/amber install net`
- Install multiple packages: `> amber install amber-client amber-repo-github`

### update
Updates specified packages, or all installed packages (if no packages are specified).

`> amber update [<package1> <package2>...]`

Examples:
- Update two packages: `> amber update net amber-client`
- Update all installed packages: `> amber update`

### remove
Removes specified packages. Automatically removes empty directories created as a result. Does not remove the dependencies of the specified packages or check whether the packages you are removing are needed by other packages.

`> amber remove <package1> <package2>...`

Examples:
- Remove two packages: `> amber remove amber-client amber-core`

### show
Shows detailed information about a package, including its description, the version currently installed (if any), the latest version available, the packages it depends on, and a list of files installed as part of the package (if any).

`> amber show <package>`

Examples:
- Show details of the amber-client package: `> amber show amber-client`

### list
Lists packages, optionally filtering by name and state (installed or available).

`> amber list [name:<pattern>] [state:<installed|available>]`

Examples:
- List all installed packages: `> amber list state:installed`
- List all available (i.e. not installed) packages whose names start with "amber": `> amber list name:amber* state:available`
- List all packages, whether installed or not: `> amber list`

### nugget save
Builds a nugget (a self-contained package that contains all of its dependencies) and saves it to a file, optionally with additional packages and files included. File paths are assumed to be relative to the current directory.

`> amber nugget save <package> [package:<name>] [file:<path>]...`

Examples:
- Build a nugget for the amber package: `> amber nugget save amber`
- Build a nugget for the amber-client package, including one other package and two additional files: `> amber nugget save amber-client package:amber-repo-github file:apis/amber/sources/myrepo1 file:apis/amber/sources/myrepo2`

### nugget run
Runs a nugget with the provided arguments without saving it. Useful for trying out packages before you install them.

`> amber nugget run <package> [...]`

Examples:
- Run the amber-client package without installing it: `> amber nugget run amber-client list name:amber*`

## Client configuration
You can configure the repositories where the Amber client will look for available packages. By default, the Amber client will look only in the https://github.com/danports/amber and https://github.com/danports/prism-core repositories. To add other repositories, create or edit the .repositories file in the same directory as the Amber client, or add a new file in the `apis/amber/sources` directory (again, relative to the Amber client's directory). When resolving a package, the first repository to return a valid package wins. The entries in the .repositories file take precedence over the entries in the `apis/amber/sources` files. The format of a repository list file is simple:

```
{
	<repository1>,
	<repository2>,
	...
}
```

where a repository is a simple table with a `type` key and other keys depending on the repository type. Here are the different types of repositories available:

### GitHub
This repository type requires the `amber-repo-github` package (which automatically installed with the `amber` package).

Configuration:
```
{
	type = "GitHub",
	user = <repository-owner>,
	name = <repository-name>,
	[defaultVersion = <branch>,]
	[auth = {
		type = "oauth",
		user = <github-user>,
		token = <github-api-token>
	}]
}
```

The `defaultVersion` key is optional; if it is omitted, Amber will fetch the latest released version of the repository by default (unless a particular version of a package is requested).

The `auth` key is optional, but highly recommended, as the GitHub API imposes rate limits for anonymous users that you will likely run up against fairly quickly. You can create a personal access token on [GitHub](https://github.com/settings/tokens).

Example:
Fetch packages from the bleeding edge (`master` branch) of the `prism-core` repository.
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
This repository type fetches packages from whatever Amber server it can connect to. (See the server section below for more information.) There is no additional configuration required for this repository type.

Example:
```
{
	type = "AmberServer"
}
```

### LocalDirectory
This repository type fetches packages from a local directory. This is especially useful on an Amber server - it allows you to host packages there and make them available to clients without creating a GitHub repository and importing your code.

Configuration:
```
{
	type = "LocalDirectory",
	path = <repository-path>
}
```

It is expected that the repository directory will have the following structure:

```
package1
	version1 (e.g. 1.0)
		file1
		file2
		...
	version2
	...
package2
...
```

Example:
```
{
	type = "LocalDirectory",
	path = "/packages"
}
```

## Server
Configuration, usage, and examples

## Authoring packages
Package layout and manifests

## Building nuggets