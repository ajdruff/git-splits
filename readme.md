git-splits
==============

NAME
----
git-splits - Extracts multiple directories of a git repo into a new branch. 


SYNOPSIS
--------
[verse] 

`git splits  -b <branch> <dir1> <dir2...>`

`git splits  -f -b <branch> <dir1> <dir2...>`


Disclaimer and Warnings
---------------------

It is strongly advised to make a complete backup of both the remote and local repositories and associated working directories prior to using `git splits`.

It is also recommended that you operate on a clone of your local repo.

DESCRIPTION
-----------
Creates a new branch `<branch>` from the current branch, modifies it to only retain the directories in `<dirX>` , and rewrites the history of the branch to include only the files within each directory listed.

This command is a wrapper around git filter-branch and as such, all behavior and warnings applicable to git filter-branch applies to `git splits`. 

Note that this operation is very I/O expensive. If your repository is large, it might be a good idea to modify the code for your situation. See the git filter-branch documentation for details on using the `-d` option. 



OPTIONS
-------
-b `<branch>`::
   Specify the new branch to be created.

-f::
    Deletes backups at .git/refs/original , allowing a consecutive `git splits` operation to be run.


EXAMPLE 1. Split multiple directories into a new branch (first use)
----------------------
You have a local repository called 'dev' that looks like this : 


      #dev repo
      public_html
      config
      docs
      admin
      mysql


You would like to extract `public_html` and `config` directories into a new branch called 'MyWebsite'.

checkout the `dev` branch

    git checkout dev

create a new branch from the current one, adding only the directories you want: 

    git splits   -b MyWebsite public_html config

When finished you'll have (2) branches, dev and MyWebsite. MyWebsite will contain public_html and config, and retain all their history.



EXAMPLE 2. Repeating a git splits operation on the same repo
---------------------------------------------------------
Because git filter-branch retains a backup of the branch that was re-written, it will throw an error when you run a new operation such as this on the same directory twice in a row: 

       git splits   -b database mysql

You'll get an error similar to this : 

    Cannot create a new backup.
    A previous backup already exists in refs/original/
    Force overwriting the backup with -f

If you check in .git/refs/original, you'll see the backup. 

To go ahead and delete the backup to allow the operation to proceed, add the -f flag as the first option: 

     git splits   -f -b database mysql

Note that because git splits rewrites the history for only the new branch that is created, the backup is extraneous.  All the same, it is strongly cautioned to make a complete backup of both your origin and your local repositories before using git splits.

Installation
------------

`git splits` is a bash script and requires bash support. 

The script can be placed in any location that is in your path, but its recommended that you copy it to the same directory as your git binary. 

    cp git-splits $(dirname $(which git))/

Credits
-------

The script was inspired by the a [Stackoverflow post](http://stackoverflow.com/a/6006679/3306354)  by [jkeating](http://stackoverflow.com/users/691627/jkeating)

AUTHOR
------
Written by Andrew Druffner <andrew@nomstock.com>


