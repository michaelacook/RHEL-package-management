# YUM
- repository-centric
- created by Yellow Dog Linux and now widely adopted by rpm-based distros
- resolves dependencies automatically
- software package groups - stuff installed together
- yum repositories contain RPM software packages
- easier front-end for rpm that allows automatic connection to repositories

## Select package names
- `yum --showduplicates list [package]` to see all available versions of a package
  - to search by name, simply give package name
  - to search by version give name-version
  - to search by release give name-version.release
  - search by architecture by name-version-release.arch
  - search by epoch number by name-epoch:version-release.arch
    - most packages do not have an epoch number
      - versions prefixed by a colon have an epoch number

## Get info on packages
- `yum list installed` to get a list of all installed
  - three columns: 
    - package name
    - version number
    - repo
      - anaconda repo means it was installed from the DVD/ISO during OS install
      - thus did not come from a repo
  - package coloring:
    - bold - update in repo
    - bold and underline - current kernel
    - red - package that doesn't exist in repo
    - yellow - newer than package in repo
- `yum list updates` to see newer versions of installed packages that will be updated at next update
- `yum list available` to see available packages that are not installed
  - coloring: 
    - blue - an update to an installed package
    - cyan - a downgrade to an installed package
    - green and underlined - current version of an installed package
- `yum list all` view all packages in one list
  - same as `yum list`
- `yum list obsoletes` to view all obsolete packages that could be replaced with a different package
- `yum info [package]` to view information on a package
- `yum info updates` to view info on updates 
- you can use `yum info` anywhere you would use `yum list`, you just get more information
- `yum deplist [package]` to view dependencies of a package
- `yum whatprovides */[package]` to determine which package provides the given software

## Get info on groups
- package groups are preconfigured packages that can be installed or removed at once
- ie. development tools
- `yum group list` to see all groups
  - same as `yum grouplist`
- environment group exists but hidden by default
  - view with `yum group list hidden`
- `yum group info "[group]"` to see info on a group
- yum group info prefixes

|Prefix|Description|
|-|-|
|`-`|Package was not installed and won't be installed as part of the group|
|`+`|Package was not installed but will be installed on the next upgrade|
|`=`|Package was not installed as part of the group|
|`[blank space]`|Package was installed but not as part of the group|

- packages won't always be managed under their group
- to install packages to be managed as a group:
  - `yum groups mark install [group]`  
  - then `yum groups mark convert [same group]`

## Search for packages
- `yum search [package name` to search for a package name
- searches name and summary info only by default
- case insensitive
- `yum search all [package name]` to include all metadata in search
- `yum list all | grep [package name]` as alternative
- `yum provides [command]` to search packages that provide the given command

## Install/remove packages
- yum can install local packages just like rpm, but will download and install dependencies from repositories
- `yum localinstall [rpm]` to install a local rpm package
- `yum -y [package]` to install without having to confirm
- `yum install yum-plugin-downloadonly`
  - this installs the plugin for yum to download only a package
  - `yum install --downloadonly --downloaddir=/path/to/dir [package]`
    - this uses the downloadonly plugin to download a package to a specified directory
- `yum reinstall [package]` to download a new rpm from the repos and reinstall it over the old one
- `yum [install|reinstall] --skip-broken [package]` to ignore broken dependencies when installing/reinstalling
- `yum upgrade [package]` to upgrade a package
- `yum remove [package]` to uninstall a package
  - leaves the config files 
  - leaves behind dependencies
    - removing dependencies may break other applications
- `yum autoremove [package]` to remove a package and dependencies
  - don't use `-y` option, could break operating system
- `yum install -y yum-utils` to install yum utilities 
  - `package-cleanup --leaves` to identify orphan packages
    - up to you to manually delete them

## Install/remove package groups
- `yum group list ids` to get all groups with names and group IDs
  - can manage groups by name or ID
- different ways to install groups
  - `yum group install "Group Name"`
  - `yum group install group-name`
  - `yum install @"Group Name"`
  - `yum install @group-name`
  - if a group is already installed, you will get an error telling you that no packages in any requested group are available to install or update
- `yum group autoremove @[group-name]` to remove all packages in a group
- `yum group update [group id]` to update all packages in a group
- `yum group install [group id] --setopt=group_package_types=mandatory,default,optional`
  - this will ensure all packages in a group are installed
  - this is useful if a group has no mandatory packages
  - to set this configuration by default edit `/etc/yum.conf`

## Manage OS updates
- `yum check-update` to check for updates
  - indendent packages are obsolete and will be replaced with the unindented package above it at next update
  - very with `yum list obsoletes`
- `yum update [package]` to update a package and it's dependencies
- `yum update` to update the entire operating system
- `yum update -x [package*]` to update all except the specified
- `yum install yum-plugin-versionlock` to install the version lock package 
  - `yum versionlock [package]` to permanently prevent a package from being updated
  - `yum versionlock` to view a list of version-locked packages
  - `yum versionlock delete [output from yum versionlock]` to remove a package from version lock
  - `yum versionlock clear` to clear all version locks
- `yum update --[type]` to update only the specified type of packages
- note: configuration files may be renamed when a new package is installed
  - when modified configuration is not forward-compatible it will be saved with a `/etc/<filename>.rmpsave` name
  - newly installed config file may be saved as `/etc/<filename>.rpmnew`
  - message: `saving /etc/configuration_file.conf as /etc/configuration_file.conf.rpmsave`
- `yum install yum-plugin-changelog` to install yum changelogs
  - `yum changelog recent` to view recent changes
    - `yum changelog updates` to view changelogs for packages listed to be updated

## Yum client configuration
- `/etc/yum.conf`
  - `keepcache` setting to 0 means yum won't keep downloaded headers or files
    - 1 will keep them
    - 0 is recommended
  - `logfile` location
    - should be `/var/log/yum.log`
  - `gbgcheck=1` should exist
    - turning it to 0 turns off gbg signature checking on packages - needs to be on
  - `plugins=1` allows plugins
    - should be set to 1
    - `yum info yum` to view which plugins are installed
  
### Manange repositories
- repo config is stored in `/etc/yum.repos.d`
  - all files must end in `.rpo` or yum ignores them
- `yum repolist` to get a list of repositories
  - status column is number of packages in the repo
  - `yum repolist [enabled/disabled]` to view enabled/disabled repositories
- `yum repoinfo` to get information on repositories
- `yum repost list disabled` to get a list of disabled repositories
  - pass the `--enablerepo="repo name"` when you need to install a package from a disabled repo but don't want to change the configuration
    - this temporarily enables the repo to install just that package

## Troubleshoot YUM
- `yum install --skip-broken [package name]`
- `yum install --disablerepo=repo [package name]`
- `yum clean [expire-cache|packages|headers|metadata|dbcache|rpmdb|plugins|all]` to clean various yum caches
  - if using `yum clean all` use `yum repo list` to download new metadata for each repository