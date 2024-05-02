---
layout:
  title:
    visible: true
  description:
    visible: false
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# How to use Duplicacy to backup to Storj cloud storage

## Introduction

[Duplicacy](https://duplicacy.com) is a cross-platform cloud backup tool with client-side encryption and fast performance.

* Available as a web-based GUI or a command line tool.
* Excellent performance and uses [Lock Free Deduplication](https://github.com/gilbertchen/duplicacy/wiki/Lock-Free-Deduplication).
* A software license is required but the **command-line interface** (CLI) version is free for personal use.
* Extensive [storage](https://forum.duplicacy.com/t/supported-storage-backends/1107) backend support.

We'll use the CLI version for this tutorial to back up to [Storj](https://www.storj.io/) cloud storage.

## Installation

### Packages

Pre-compiled binaries are available for Linux, macOS, and Windows directly from the Duplicacy [GitHub](https://github.com/gilbertchen/duplicacy/releases) repository.

Download the latest version for your system. The current version is `3.2.3`.

```plaintext
$ sudo wget -O /usr/local/bin/duplicacy https://github.com/gilbertchen/duplicacy/releases/download/v3.1.0/duplicacy_linux_x64_3.1.0
```

Change the file mode bit.

```plaintext
$ sudo chmod 755 /usr/local/bin/duplicacy
```

## Preparing a new repository

The [**duplicacy init**](https://github.com/gilbertchen/duplicacy/wiki/init) command initializes the storage.

```plaintext
duplicacy init [command options] <snapshot id> <storage url>
```

The `<snapshot id>` refers to the name of the backup job.\
The `<storage url>` refers to the storage location of the backup job.

The duplicacy `init` command has multiple options, but we'll only be using the following:

```plaintext
-storage-name <name>  The name assigned to the storage.
-repository <path>   The directory to back up.
```

We'll initialize the repository using the duplicacy `init` command and these options in the next section.

## Storj cloud storage

The [Storj](https://www.storj.io/) cloud provider offers decentralized storage built for performance with an open-source architecture.

The free tier offers 150 GB of free storage.

To backup data via `Storj`, create a storage bucket and **API** key from their website.

1. Sign up for a free account.
2. Create a new storage bucket and passphrase. The passphrase is not used for `Duplicacy` because you have an **API** key. However, it is a requirement for the storage bucket.
3. Create an [API key](https://docs.storj.io/dcs/getting-started/quickstart-uplink-cli/generate-access-grants-and-tokens/generate-a-token). Navigate to the **Access** page and click the **Create Keys for CLI** link (rightmost option). Provide name, permissions, and optionally buckets, and select **Create Keys**.
4. Copy and save the **Satellite Address** and **API** key in a safe place or download them because they will only appear once.

You will need the **Satellite Address** and **API key** to initialize your repository.

Here is the format:

```plaintext
$ duplicacy init -storage-name <storage name> -repository <path to directory to backup> <storage id> <storage url>
```

The `<storage url>` for `Storj` uses the following format:

```plaintext
storj://satellite_address/bucket
```

#### Optional performance parameters

`Duplicacy` uses a unique pack-and-split method to split files into [chunks](https://forum.duplicacy.com/t/chunk-size-details/1082) with a default `chunk size` of 4 `MiB`.

Increase the `chunk size` for better performance with `Storj` buckets.

```
-chunk-size 32M
-max-chunk-size 64M
```

Here is the complete command for this example:

```plaintext
$  duplicacy init -chunk-size 32M -max-chunk-size 64M -storage-name storjphotos -repository /home/poochie/Photos photosbackup storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup
```

Here is the output:

```plaintext
$ duplicacy init -chunk-size 32M -max-chunk-size 64M -storage-name storjphotos -repository /home/poochie/Photos photosbackup storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup
Enter the API access key:
Enter the passphrase:
/home/poochie/Photos will be backed up to storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup with id photosbackup
```

Please note the following prompts:

```plaintext
Enter the API access key:
Enter the passphrase:
```

1. Copy and paste in your **API** key.
2. Copy and paste in a passphrase you would like to use.

## Saving configuration information

`Duplicacy` automatically creates a configuration folder named `.duplicacy` when the initialization is complete. The preferences file in this directory contains the configuration data.

The [duplicacy set](https://github.com/gilbertchen/duplicacy/wiki/set) command allows you to store your keys and passphrase in the configuration file to avoid manually inputting them when backing up or restoring files.

Setting this will allow you to automate your backups with bash scripts or schedule them via cron jobs.

```plaintext
duplicacy set -storage <storage name> -key -value
```

Go to the official **Duplicacy** [wiki](https://github.com/gilbertchen/duplicacy/wiki/Managing-Passwords) for a complete list of environment variables.

We'll be setting the following variable keys:

```plaintext
<storagename>_storj_key
<storagename>_storj_passphrase
```

Here is the complete command for our example:

```plaintext
$ duplicacy set -storage storjphotos -key 'storjphotos_storj_key' -value 'MyApiKey'
```

```plaintext
$ duplicacy set -storage storjphotos -key 'storjphotos_storj_passphrase' -value 'Mypassphrase'
```

You can run the backup command without being prompted to provide your credentials when finished.

## Backing up

Use the [duplicacy backup](https://github.com/gilbertchen/duplicacy/wiki/backup) command to back up your data.

```plaintext
duplicacy backup -storage <storagename>
```

Here is the complete command using our example:

```plaintext
$ duplicacy backup -storage storjphotos
```

Here is the output:

```plaintext
$ duplicacy backup -storage storjphotos
Repository set to /home/poochie/Photos
Storage set to storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup
No previous backup found
Indexing /home/poochie/Photos
Parsing filter file /home/poochie/.duplicacy/filters
Loaded 0 include/exclude pattern(s)
Listing all chunks
Packed profile.png (133686)
Backup for /home/poochie/Photos at revision 1 completed
```

`Duplicacy` assigns a revision number starting with one and increments it every time you run the backup.

Here is what a second backup will look like if there are no file changes.

```plaintext
$ duplicacy backup -storage storjphotos
Repository set to /home/poochie/Photos
Storage set to storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup
Last backup at revision 1 found
Indexing /home/poochie/Photos
Parsing filter file /home/poochie/.duplicacy/filters
Loaded 0 include/exclude pattern(s)
Backup for /home/poochie/Photos at revision 2 completed
```

## Restoring files

Use the [duplicacy restore](https://github.com/gilbertchen/duplicacy/wiki/restore) command to restore files from your backups.

```plaintext
duplicacy restore [command options] [--] [pattern] ...
```

The duplicacy `restore` command has multiple options, but we'll only be discussing the following:

```plaintext
-r <revision> - revision number of the snapshot
-stats - show statistics
-overwrite - overwrite existing files
-storage <storage name> - name of storage
```

To restore the entire snapshot from the initial backup, run the following command:

```plaintext
$ duplicacy restore -storage storjphotos -stats -r 1
```

Here is the output:

```plaintext
$ duplicacy restore -storage storjphotos -stats -r 1
Repository set to /home/poochie/Photos
Storage set to storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup
Loaded 0 include/exclude pattern(s)
Forcing in-place mode with a non-default preference path
Parsing filter file /home/poochie/.duplicacy/filters
Loaded 0 include/exclude pattern(s)
Restoring /home/poochie/Photos to revision 1
Downloaded chunk 1 size 133686, 131KB/s 00:00:01 100.0%
Downloaded profile.png (133686)
Restored /home/poochie/Photos to revision 1
Files: 1 total, 131K bytes
Downloaded 1 file, 131K bytes, 1 chunks
Skipped 0 file, 0 bytes
Total running time: 00:00:01
```

* Only restores missing files.
* The **overwrite** option is needed to overwrite existing files.
* The **overwrite** option only overwrites existing files that have changed.
* The **overwrite** option will be ignored if there are no file changes.

To restore specific files, specify a pattern.

To restore all files with the **.png** extension:

```plaintext
$ duplicacy restore -storage storjphotos -stats -r 1 -- '*.png'
```

To restore a file named **profile.png**:

```plaintext
$ duplicacy restore -storage storjphotos -stats -r 1 profile.png
```

See more details about the [restore](https://github.com/gilbertchen/duplicacy/wiki/restore) option.

## Listing files in the backups

Use the [duplicacy list](https://github.com/gilbertchen/duplicacy/wiki/list) command is used to list files in the backup snapshots.

```plaintext
duplicacy list [command options]
```

The duplicacy `list` command has multiple options, but we'll only be discussing the following:

```plaintext
-r - revision number of the snapshot 
-files - print the file list in each snapshot
-storage <storage name> - name of storage
```

To list the files from a 4th backup:

```plaintext
$ duplicacy list -storage storjphotos -r 4 -files
```

Here is the output:

```plaintext
$ duplicacy list -storage storjphotos -r 4 -files 
Repository set to /home/poochie/Photos 
Storage set to storj://PrseFhdxYxKicsiFmxK@us1.storj.io:7777/photosbackup
Snapshot photosbackup revision 4 created at 2022-11-11 15:28 
Files: 3 
fbf8fb3184a7e2c55875707f  profile.png 
298cefc3919c3ad78c5a50   redhat.png 
a23217febc1e446aa47812    test.log 
Total size: 183974, file chunks: 3, metadata chunks: 3
```

## Conclusion

`Duplicacy` is a powerful backup tool and this tutorial is only a brief introduction of what it has to offer. Check out the Duplicacy [forum](https://forum.duplicacy.com/) for complete documentation and helpful community support.
