PRM
===

PRM (Package Repository Manager) allows you to quickly build package repositories, inspired by Jordan Sissels' FPM.

Why Use PRM
===

PRM can quickly build and regenerate apt repositories without the need of apt-ftparchive or crazy shell scripts. This allows you to build
apt package repositories on any OS. The goal of PRM is to be able to deploy apt, rpm and solaris packages. Currently only apt repositories are supported
but future releases are expected to contain rpm and solaris updates.

PRM for apt quickly regenerates package repositories by caching md5 hashes and checking against the cache each time Packages.gz is generated. Usually
this is unnecessary, but when there are large packages in a repository this can slow down generation times to 20-30 minutes. PRM proactively md5 caches.

The --directory (-d) flag can be used to move packages from a directory into your package repository. PRM will look through the location passed into
the -d flag and move any matching packages into their respective location. Packages are moved based on their architecture (amd64, i386, etc). 

Alternatively, you may choose to place your packages into path/dists/release/component/arch/.

Syncing
===

PRM supports syncing your repository to DreamObjects, an object storage service, using the S3 API.

```
prm -t sync -p pool -r squeeze -a amd64 -c main -g -e my_access_key -s my_secret_key
```

Buckets are created based on the path (-p) flag. In the previous case, pool would be a bucket and the contents of your repository would be objects. If the 
bucket does not exist, PRM will create it for you.

To use DreamObjects as your apt repository, add the following to your sources.list

```
deb http://objects.dreamhost.com/my_bucket_name/ my_release my_component
```

If the local apt repository has packages removed, PRM will remove these from your bucket. All objects synced are set by default to be public.

Snapshots
===

Snapshots can be used to promote components within Debian package repositories. The components are named [snapshot-name]-[date] and is symlinked to [snapshot-name].

```
prm -t deb -p pool -r precise -a amd64 -c unstable -s stable -g
```

Todo List
===

* Cleanup code [variables, functions, etc]
* Convert md5 caching into JSON
* Enable RPM support
* Enable Solaris support

Install
===
```
gem install prm
```

Commands
===
```
--type, -t <s>:   		Type of repository to create
--path, -p <s>:   		Path to future repository location
--component, -c <s>:   	Component name for repository (Multi component supported by comma)
--release, -r <s>:   	Release name for repository (Multi release supported by comma)
--arch, -a <s>:   		Architecture for repository (Multi arch supported by comma)
--gpg:   				Sign release files with a GPG key (Expects GPG key to be available)
--generate, -g:   		Create new repository
--version, -v:   		Print version and exit
--help, -h:   			Show this message
--accesskey, -e <s>:   Access Key for DreamObjects
--secretkey, -s <s>:   Secret Key for DreamObjects
-d, --directory DIRECTORY     Move packages from directory to target
-s, --snapshot COMPONENT      Creates a snapshot of a component
```

Example
===
```
prm --type deb --path pool --component dev,staging --release precise --arch amd64 --gpg --generate

prm -t deb -p pool -c stable -r precise -a amd64 --directory unstable -g
```
