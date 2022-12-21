# Red Hat Package Manager (RPM)
- install and remove local `.rpms`
- ability to download from the internet, but URL is needed 
- cannot resolve dependencies 
- query the package database to list details about installed packages

## Querying
- query package database 
- query individual package 
- query a file
- `rpm -qa` to query all packages from the package database
  - get a list of all installed software
  - `-q` for query 
  - `-a` for all 
- `rpm -qi` to query information about a file, package or program
- `rpm -qa Group="[group]"` to query all packages tagged as part of the specified group
- `rpm -qa --last` to view packages sorted by install date
- `rpm -ql [package]` to see where all the files from a package have been installed on the system
  - add `-d` to see just documentation file locations 
  - add `-c` to see just configuration file locations
- `rpm -qf [/path/to/file]` determine which package a file comes from
- `rpm -qdf [/path/to/file]` determine documentation for a file
- `rpm -q --provides [package]` determine what features a package provides
- `rpm -q --requires [package]` determine what a package requires, or it's dependencies
- `rpm -q --changelog [package]` to view the changelog for a package\
- `rpm -qip [rpm file]` to query a `.rpm`
  - `-l` to get all files in the package
  - `-p` option for package

### Query formating
- [Query formats](https://rpm-software-management.github.io/rpm/manual/queryformat.html)
- better than piping output to awk
- good for shell scripts
- boring to lean, look it up on the fly

## Installing/removing packages
- `rpm -i[vh] [rpm archive]`
  - `-i` for install 
  - `-v` for verbose
  - `-h` for hashes - progress bar
- if you are missing dependencies, you will get an error telling you the dependencies that you are missing so that you can install them
- rpmfind to find the dependency you need
- `rpm -e[v] [program name]` to uninstall

## Upgrading packages
- when you try to install a new version of the same package you may get a problem where the two conflict with each other
- could uninstall the old package and then install the new one
- or the solution may be to upgrade rather than install anew
- `rpm -U[vh] [new rpm]` to perform the upgrade
- may have to upgrade dependencies before upgrading a package
- this could lead to a problem of the dependency circle
  - best way to handle this is to tell rpm to ignore conflicting dependencies with `--nodeps` when upgrading
  - this will allow you to avoid running into the circular dependency problem
  - upgrade while ignoring dependencies that need also upgrading. then, upgrade dependencies. this sequence should avoid problems
- `rpm -F [rpm]` to upgrade package only if it is already installed 
  - will ignore rpms that haven't been installed

## Reinstalling packages
- `rpm -ivh --force [rpm]` to over-write the old installation
- `rpm -qcp [rpm]` to query a list of configuration files for a program
- `rpm2cpio [rpm] | cpio -id` to extract an rpm
  - this first converts it to a cpio archive, allowing us to extract any file

## Verify package attributes
- `rpm -V yum` in the package configuration directory
- `rpm -Va` to verify entire OS

## Validate package integrity
- `rpm -K --no-signature [rpm]`
- uses checksum
- `rpm -K [rpm]` to get signature

## Troubleshooting
- backup rpm database 
- rpm dir in `/var/lib/rpm`
- so you can restore it if it ever gets corrupted
- could set it up as a chron job
- restore rpm database with `sudo rpm --rebuilddb`
  - name the archive `rpmdb.tar.gz`
  - keep a copy in `/var/lib`
- restore permissions for a package `rpm --setperms [program name]`
  - use `--ugids` to reset user and group owners