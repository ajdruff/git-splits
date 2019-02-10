# git-splits

## NAME

git-splits - Extracts directories into a new branch with re-written history containing only those directories.

## SYNOPSIS

`git splits  -b <branch> <directories>...`

`git splits  -f -b <branch> <directories>...>`

## OPTIONS

`-b <branch>`:
    The name of the new branch to be created.

`-f`:
    Deletes backups at `.git/refs/original`, allowing a consecutive `git splits` operation to be run.

## DESCRIPTION

`git splits` is a git extension that extracts multiple directories of a git repo into a new branch, allowing you to optionally push the new branch to a standalone repo.

Creates a new branch `<branch>` from the current branch, modifies it to only retain the directories in `<directories>` and rewrites the history of the branch to include only the files within each directory listed.

This command is a wrapper around `git filter-branch` and as such, all behavior and warnings applicable to git [filter-branch](http://git-scm.com/docs/git-filter-branch) apply to `git splits`.

Note that this operation is very I/O expensive. If your repository is large, it might be a good idea to modify the code for your situation. See the git filter-branch documentation for details on using the `-d` option. 

## INSTALLATION

`git splits` is an unofficial git extension that can be easily added to your git installation by adding the script to a directory within your shell's path.

### To install using wget

Execute the following one-liner from a bash shell on your local machine.

    sudo wget -o $(dirname $(which git))/git-splits https://raw.githubusercontent.com/ajdruff/git-splits/master/git-splits;sudo chmod a+x $(dirname $(which git))/git-splits;

This will download a copy to the same directory that the git binary is located in, and set the executable bit for the owner.

### To install manually

1. Download the `git-splits` file from the latest tagged release or clone the repo. The latest version will always be in branch `master`. [All `git-splits` releases are here](https://github.com/simpliwp/git-splits/releases).

        cp git-splits $(dirname $(which git))/

2. Once the script is in place, you can call it via git (don't call it directly.

        git splits

> Note there is no dash `-` between 'git' and 'splits'.

## BE SAFE

***Please take the time to make a complete backup of both the remote and local repositories and associated working directories prior to using `git splits`.  
Its also recommended that you operate on a clone of your local repo.***

Since `git splits` is simply a wrapper around the built-in `git filter-branch` extension, using it carries the same risk as using `git filter-branch` directly. As always, when working with git, results may work as designed, but not as expected. It is for these reasons that it is strongly advised that you first make a complete backup of both the remote and local repositories and associated working directories prior to using `git splits`.

## EXAMPLES

### EXAMPLE 1. Split multiple directories into a new branch (first use)

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

    git splits -b MyWebsite public_html config

### EXAMPLE 2. Repeating a git splits operation on the same repo

    git splits   -b database mysql

You'll get an error similar to this :

    Cannot create a new backup.
    A previous backup already exists in refs/original/
    Force overwriting the backup with -f

If you check in `.git/refs/original`, you'll see the backup.

To enable the operation to proceed, you must delete the backup by using the `-f` flag as the first option (the order of options is important):

    git splits -f -b database mysql

Note that because git splits rewrites the history for only the new branch that is created, the backup is extraneous.  All the same, it is cautioned to make a complete backup of both your origin and your local repositories before using `git splits`.

### EXAMPLE 3. Split directories into a new repository

This example is exactly like Example 1 except that we go one step further and make the new branch into its own repo.

See example 1 for directory structure.

    # checkout the current dev branch that holds the directories that we want
    git checkout dev
    # use `git splits` to create a new branch that holds only our website files and not anything we don't want
    git splits -b branch_my_website public_html config

This creates a new branch `branch_my_website` and adds the `public_html` and `config` directories to it.

Now create a new empty repo either locally or on a remote server. For this example, we'll assume we've created an empty repo called `repo_my_website` on GitHub that has path: `git@github.com:simpliwp/repo_my_website.git`

After the successful split, and after you created the remote empty repo, run the following commands within the local repo you ran `git splits` on:

add a new remote origin for the empty repo so we can push to the empty repo

    git remote add origin_my_website git@github.com:simpliwp/repo_my_website.git

push the branch to the empty repo's master branch

    git push origin_my_website branch_my_website:master

Now change current directory out of the old repo

    cd /path/to/where/you/want/the/new/local/repo

clone the remote repo you just pushed to 

    git clone  git@github.com:simpliwp/repo_my_website.git

Done.

## License

[MIT License](https://github.com/ajdruff/git-splits/blob/master/LICENSE)

## Credits

core filter-branch code taken from  a [Stackoverflow post](http://stackoverflow.com/a/6006679/3306354)  by [jkeating](http://stackoverflow.com/users/691627/jkeating)

## AUTHOR

Written by Andrew Druffner <andrew@nomstock.com>
