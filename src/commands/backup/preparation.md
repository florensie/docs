# Preparing a new repository

The place where your backups will be saved is called a "repository". This
chapter explains how to create ("init") such a repository. The repository can be
stored locally, or on some remote server or service. We'll first cover using a
local repository; the remaining sections of this chapter cover all the other
options. You can skip to the next chapter once you've read the relevant section
here.

For automated backups, rustic accepts the repository location in the environment
variable `RUSTIC_REPOSITORY`.

For the password, several options exist:

- Setting the environment variable `RUSTIC_PASSWORD`

- Specifying the path to a file with the password via the option
  `--password-file` or the environment variable `RUSTIC_PASSWORD_FILE`

- Configuring a program to be called when the password is needed via the option
  `--password-command` or the environment variable `RUSTIC_PASSWORD_COMMAND`

The `init` command has an option called `--set-version` which can be used to
explicitly set the version for the new repository.

The below table shows which rustic version is required to use a certain
repository version and shows new features introduced by the repository format.

Now we're ready to backup some data. The contents of a directory at a specific
point in time is called a "snapshot" in rustic. Run the following command and
enter the repository password you chose above again:

```console
$ rustic -r /srv/rustic-repo --verbose backup ~/work
open repository
enter password for repository:
password is correct
lock repository
load index files
start scan
start backup
scan finished in 1.837s
processed 1.720 GiB in 0:12
Files:        5307 new,     0 changed,     0 unmodified
Dirs:         1867 new,     0 changed,     0 unmodified
Added:      1.200 GiB
snapshot 40dc1520 saved
```

As you can see, rustic created a backup of the directory and was pretty fast!
The specific snapshot just created is identified by a sequence of hexadecimal
characters, `40dc1520` in this case.

You can see that rustic tells us it processed 1.720 GiB of data, this is the
size of the files and directories in `~/work` on the local file system. It also
tells us that only 1.200 GiB was added to the repository. This means that some
of the data was duplicate and rustic was able to efficiently reduce it.

If you don't pass the `--verbose` option, rustic will print less data. You'll
still get a nice live status display. Be aware that the live status shows the
processed files and not the transferred data. Transferred volume might be lower
(due to de-duplication) or higher.

If you run the backup command again, rustic will create another snapshot of your
data, but this time it's even faster and no new data was added to the repository
(since all data is already there). This is de-duplication at work!

```console
$ rustic -r /srv/rustic-repo --verbose backup ~/work
open repository
enter password for repository:
password is correct
lock repository
load index files
using parent snapshot d875ae93
start scan
start backup
scan finished in 1.881s
processed 1.720 GiB in 0:03
Files:           0 new,     0 changed,  5307 unmodified
Dirs:            0 new,     0 changed,  1867 unmodified
Added:      0 B
snapshot 79766175 saved
```

You can even backup individual files in the same repository (not passing
`--verbose` means less output):

```console
$ rustic -r /srv/rustic-repo backup ~/work.txt
enter password for repository:
password is correct
snapshot 249d0210 saved
```

Now is a good time to run `rustic check` to verify that all data is properly
stored in the repository. You should run this command regularly to make sure the
internal structure of the repository is free of errors.
