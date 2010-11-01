---
layout: post
title: Git-Up With Git-Flow to Get Down
published: true
---

After working exclusively with [Git][git] for a year or two, I have *finally* found my stride with
some **must-have** tools for Git.

## Git Completion

Git tab-completion will give you access to:

* local and remote branch names
* local and remote tag names
* .git/remotes file names (useful when doing a `git pull <forked_repo> master`)
* git `subcommands`
* tree paths within `ref:path/to/file` expressions
* `common --long-options`

### Install

1. Copy [git-source](git://git.kernel.org/pub/scm/git/git.git)`/contrib/completion/git-completion.bash` to `~/.git-completion.sh`.
2. Added the following line to your .bashrc: `source ~/.git-completion.sh`

## Bash Colors/Prompt

In your `~/.bashrc` file, there should be a section that looks like:

    if [ "$color_prompt" = yes ]; then
        PS1=...
    else
        PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
    fi

Modify it as follows:

    if [ "$color_prompt" = yes ]; then
        PS1='${debian_chroot:+($debian_chroot)}\[\033[01;32m\]\u@\h\[\033[00m\]:\[\033[01;34m\]\w\[\033[36m\]$(__git_ps1 " [%s]")\[\033[00m\]\$ '
        
        GIT_PS1_SHOWDIRTYSTATE="auto"
        GIT_PS1_SHOWUPSTREAM="auto"
    else
        PS1='${debian_chroot:+($debian_chroot)}\u@\h:\w\$ '
    fi
    
This will give you a sexy prompt that looks like:
    
    eric@gob:/var/www/myrepo [develop *+]$
    
* eric: **Username**
* gob: **Hostname** *(Yes, my VM is named after Gob from Arrested Development.  COME ON!!!)*
* /var/www/myrepo: **Path**
* develop: **Branch**
* \*: **Dirty State** *Changes not committed*
* +: **Behind Upstream** *Changes needing commit*

## Git Flow

Your team has standardized on their framework, on their development environment, on deployment
procedures, so why not standardize their Git workflow?

*Git Flow* is nothing more than a wrapper for common Git subcommands, such as `checkout`,
`pull`, etc.  At the end of the day, Git still works the same, but everyone's using the same
branching/merging conventions.  For example:

    [develop] $ git flow feature start gmail-auth
    [feature/gmail-auth] $ # make some changes...
    [feature/gmail-auth] $ git flow feature finish gmail-auth
    [develop] $ # now contains the `gmail-auth` feature
    
    [develop] $ git checkout master
    [master] $ git merge develop && git push
    
    [develop] $ git flow hotfix start gmail-auth-syntax-error
    [hotfix/gmail-auth-syntax-error] $ # make some fixes...
    [hotfix/gmail-auth-syntax-error] $ git flow hotfix finish gmail-auth-syntax-error
    [master] $ git push
    
    [develop] $ git flow track another-persons-feature
    [feature/another-persons-feature] $ # make some changes

Your team can stick with using vanilla *git*, but the added convention makes it easy for you to have
wildcard domains for previewing features & hotfixes:

    http://www.mydomain.com/                ---> master
    http://stage.mydomain.com/              ---> develop
    http://gmail-auth.preview.mydomain.com/ ---> feature/my-feature
    http://syntax-error.hotfix.mydomain.com ---> hotfix/syntax-error
    
All of this works on the convention that `develop` is always stable and ready to deploy, `master`
contains nothing more than snapshots (*tags*) of deployments.

### Git Flow Completion

*Git Flow* is incredibly useful for standardizing a workflow, but it can be verbose without
tab-completion, especially when you need to work on an untracked remote feature.  This can be remedied
by installing *git-flow-completion*.

With [git-flow](http://github.com/nvie/gitflow) installed and *git-completion* (following the
instructions above), clone [git-flow-completion](http://github.com/bobthecow/git-flow-completion.git)
and copy `git-flow-completion.sh` to `~/.git-flow-completion.sh` and add `source ~/.git-flow-completion.sh`.

If you're using a version of Git < 1.71, then add `flow)  _git_flow ;;` to your `~/.git-completion.sh`
so it looks like like:
    
    if [ -z "$command" ]; then
        case "${COMP_WORDS[COMP_CWORD]}" in
        --*)   __gitcomp "
            --paginate
            --no-pager
            --git-dir=
            --bare
            --version
            --exec-path
            --html-path
            --work-tree=
            --help
            "
            ;;
        *)     __git_compute_porcelain_commands
               __gitcomp "$__git_porcelain_commands $(__git_aliases)" ;;
               
        flow)  _git_flow ;;
        
        esac
        return
    fi

## Git Up

Often you're tracking several remote branches and need to bring them up to speed before you begin
your work.

With [Git Up](http://github.com/aanand/git-up), you can simply run `$ git up` and rebase any remote
branch you're tracking:

    $ gem install git-up
    
    $ cd /path/to/my/repo
    /path/to/my/repo $ git up
    master                  fast-forwarding...
    develop                 fast-forwarding...
    feature/gmai-auth       fast-forwarding...
    returning to develop
    

### bump\_version

With web applications, the need for the usual `major.minor.bugfix` tags go away.  With changes always
going out the door, tagging & releasing needed to be a bit simpler, especially since we're not
using continuous deployment just yet.

<script src="http://gist.github.com/657409.js?file=bump_version"></script>

[git]:http://git-scm.com/ "Git SCM"