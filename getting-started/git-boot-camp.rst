.. _git-boot-camp:
.. _gitbootcamp:

Git Bootcamp and Cheat Sheet
============================

.. highlight:: console

.. note::

   This section provides instructions on common tasks in CPython's
   workflow. It's designed to assist new contributors who have
   some familiarity with git and GitHub.

   If you are new to git and GitHub, please become comfortable with
   these instructions before submitting a pull request. As there are several
   ways to accomplish these tasks using git and GitHub, this section reflects
   one method suitable for new contributors. Experienced contributors may
   desire a different approach.


In this section, we will go over some commonly used Git commands that are
relevant to CPython's workflow.

.. note::
   Setting up git aliases for common tasks can be useful to you. You can
   get more information about that in
   `git documentation <https://git-scm.com/book/en/v2/Git-Basics-Git-Aliases>`_

.. _fork-cpython:

Forking CPython GitHub Repository
---------------------------------

You will only need to do this once.

1. Go to https://github.com/python/cpython.

2. Press ``Fork`` on the top right.

3. When asked where to fork the repository, choose to fork it to your username.

4. Your forked CPython repository will be created at https://github.com/<username>/cpython.

.. _clone-your-fork:

Cloning a Forked CPython Repository
-----------------------------------

You will only need to do this once per machine.  From your command line::

   git clone git@github.com:<username>/cpython.git

It is also recommended to configure an ``upstream`` remote repository::

   cd cpython
   git remote add upstream https://github.com/python/cpython

You can also use SSH-based or HTTPS-based URLs.

Configure the Remotes
---------------------

.. These steps are duplicated in setup-building in step 6 and 7.
   Please update these there as well.

Configure ``git`` to pull ``main`` from the ``upstream`` remote::

   git config --local branch.main.remote upstream

Since one should never attempt to push to ``upstream``, configure
``git`` to push always to ``origin``::

   git remote set-url --push upstream git@github.com:<username>/cpython.git

Listing the Remote Repositories
-------------------------------

To list the remote repositories that are configured, along with their URLs::

   git remote -v

You should have two remote repositories: ``origin`` pointing to your forked CPython repository,
and ``upstream`` pointing to the official CPython repository::

   origin  git@github.com:<username>/cpython.git (fetch)
   origin  git@github.com:<username>/cpython.git (push)
   upstream        https://github.com/python/cpython (fetch)
   upstream        git@github.com:<username>/cpython.git (push)

To verify the upstream for ``main``::

   git config branch.main.remote

It should emit ``upstream``, indicating to track/pull changes for ``main`` from the
``upstream`` remote.


.. _set-up-name-email:

Setting Up Your Name and Email Address
--------------------------------------

.. code-block:: bash

   git config --global user.name "Your Name"
   git config --global user.email your.email@example.com

The ``--global`` flag sets these parameters globally while
the ``--local`` flag sets them only for the current project.

.. _autocrlf:

Enabling ``autocrlf`` on Windows
--------------------------------

The ``autocrlf`` option will fix automatically any Windows-specific line endings.
This should be enabled on Windows, since the public repository has a hook which
will reject all changesets having the wrong line endings::

    git config --global core.autocrlf input

Creating and Switching Branches
-------------------------------

.. important::
   Never commit directly to the ``main`` branch.

Create a new branch from ``main`` and switch to it::

   git switch -c <branch-name> main

This is equivalent to::

   # create a new branch from main
   git branch <branch-name> main
   # switch to the new branch
   git switch <branch-name>

To find the branch you are currently on::

   git branch

The current branch will have an asterisk next to the branch name.  Note, this
will only list all of your local branches.

To list all the branches, including the remote branches::

   git branch -a

To switch to a different branch::

   git switch <another-branch-name>

Other releases are just branches in the repository.  For example, to work
on the 2.7 release from the ``upstream`` remote::

   git switch -c 2.7 upstream/2.7

.. _deleting_branches:

Deleting Branches
-----------------

To delete a **local** branch that you no longer need::

   git switch main
   git branch -D <branch-name>

To delete a **remote** branch::

   git push origin -d <branch-name>

You may specify more than one branch for deletion.


Renaming Branch
---------------

The CPython repository's default branch was renamed from ``master`` to
``main`` after the Python 3.10b1 release.

If you have a fork on GitHub (as described in :ref:`fork-cpython`) that was
created before the rename, you should visit the GitHub page for your fork to
rename the branch there. You only have to do this once. GitHub should
provide you with a dialog for this. If it doesn't (or the dialog was already
dismissed), you can rename the branch in your fork manually `by following
these GitHub instructions <https://github.com/github/renaming#renaming-existing-branches>`__

After renaming the branch in your fork, you need to update any local clones
as well. This only has to be done once per clone::

    git branch -m master main
    git fetch origin
    git branch -u origin/main main
    git remote set-head origin -a

(GitHub also provides these instructions after you rename the branch.)

If you do not have a fork on GitHub, but rather a direct clone of the main
repo created before the branch rename, you still have to update your local
clones. This still only has to be done once per clone. In that case, you can
rename your local branch as follows::

    git branch -m master main
    git fetch upstream
    git branch -u upstream/main main


.. _commit-changes:

Staging and Committing Files
----------------------------

1. To show the current changes::

      git status

2. To stage the files to be included in your commit::

      git add -p  # to review and add changes to existing files
      git add <filename1> <filename2>  # to add new files

3. To commit the files that have been staged (done in step 2):

   .. code-block:: bash

      git commit -m "This is the commit message."

Reverting Changes
-----------------

To revert changes to a file that has not been committed yet::

   git checkout <filename>

If the change has been committed, and now you want to reset it to whatever
the origin is at::

   git reset --hard HEAD

Stashing Changes
----------------

To stash away changes that are not ready to be committed yet::

   git stash

To re-apply the last stashed change::

   git stash pop

.. _diff-changes:

Comparing Changes
-----------------

View all non-commited changes::

   git diff

Compare to the ``main`` branch::

   git diff main

Exclude generated files from diff using an ``attr``
`pathspec <https://git-scm.com/docs/gitglossary#def_pathspec>`_ (note the
single quotes)::

   git diff main ':(attr:!generated)'

Exclude generated files from diff by default::

   git config diff.generated.binary true

The ``generated`` `attribute <https://git-scm.com/docs/gitattributes>`_ is
defined in :cpy-file:`.gitattributes`, found in the repository root.

.. _push-changes:

Pushing Changes
---------------

Once your changes are ready for a review or a pull request, you will need to push
them to the remote repository.

::

   git switch <branch-name>
   git push origin <branch-name>

Creating a Pull Request
-----------------------

1. Go to https://github.com/python/cpython.

2. Press the ``New pull request`` button.

3. Click the ``compare across forks`` link.

4. Select the base repository: ``python/cpython`` and base branch: ``main``.

5. Select the head repository: ``<username>/cpython`` and head branch: the branch
   containing your changes.

6. Press the ``Create pull request`` button.

You should include the issue number in the title of the PR,
in the format ``gh-NNNNN: <PR Title>``.

Linking to Issues and Pull Requests
-----------------------------------

You can link to issues and pull requests using ``gh-NNNNN`` (this form is
preferred over ``#NNNNN``).  If the reference appears in a list, the link
will be expanded to show the status and title of the issue/PR.

When you create a PR that includes ``gh-NNNNN`` in the title, `bedevere`_
will automatically add a link to the issue in the first message.

In addition, pull requests support `special keywords`_ that can be used to
link to an issue and automatically close it when the PR is merged.
However, issues often require multiple PRs before they can be closed (e.g.
backports to other branches), so this features is only useful if
you know for sure that a single PR is enough to address and close the issue.

.. _bedevere: https://github.com/python/bedevere
.. _special keywords: https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword

Updating your CPython Fork
--------------------------

Scenario:

- You forked the CPython repository some time ago.
- Time passes.
- There have been new commits made in the upstream CPython repository.
- Your forked CPython repository is no longer up to date.
- You now want to update your forked CPython repository to be the same as
  the upstream CPython repository.

Please do not try to solve this by creating a pull request from
``python:main`` to ``<username>:main`` as the authors of the patches will
get notified unnecessarily.

Solution::

   git switch main
   git pull upstream main
   git push origin main

.. note:: For the above commands to work, please follow the instructions found
          in the :ref:`checkout` section

Another scenario:

- You created ``some-branch`` some time ago.
- Time passes.
- You made some commits to ``some-branch``.
- Meanwhile, there are recent changes from the upstream CPython repository.
- You want to incorporate the recent changes from the upstream CPython
  repository into ``some-branch``.

Solution::

   git switch some-branch
   git fetch upstream
   git merge upstream/main
   git push origin some-branch

You may see error messages like "CONFLICT" and "Automatic merge failed;" when
you run ``git merge upstream/main``.

When it happens, you need to resolve conflict.  See these articles about resolving conflicts:

- `About merge conflicts <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/about-merge-conflicts>`_
- `Resolving a merge conflict using the command line <https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/addressing-merge-conflicts/resolving-a-merge-conflict-using-the-command-line>`_

.. _git_from_patch:

Applying a Patch to Git
-----------------------

Scenario:

- A patch exists but there is no pull request for it.

Solution:

1. Download the patch locally.

2. Apply the patch::

       git apply /path/to/patch.diff

   If there are errors, update to a revision from when the patch was
   created and then try the ``git apply`` again:

   .. code-block:: bash

       git checkout $(git rev-list -n 1 --before="yyyy-mm-dd hh:mm:ss" main)
       git apply /path/to/patch.diff

   If the patch still won't apply, then a patch tool will not be able to
   apply the patch and it will need to be re-implemented manually.

3. If the apply was successful, create a new branch and switch to it.

4. Stage and commit the changes.

5. If the patch was applied to an old revision, it needs to be updated and
   merge conflicts need to be resolved::

       git rebase main
       git mergetool

   For very old changes, ``git merge --no-ff`` may be easier than a rebase,
   with regards to resolving conflicts.

6. Push the changes and open a pull request.

.. _git_pr:

Downloading Other's Patches
---------------------------

Scenario:

- A contributor made a pull request to CPython.
- Before merging it, you want to be able to test their changes locally.

If you've got `GitHub CLI <https://cli.github.com>`_ or
`hub <https://hub.github.com>`_ installed, you can simply do::

   $ gh pr checkout <pr_number>   # GitHub CLI
   $ hub pr checkout <pr_number>  # hub

Both of these tools will configure a remote URL for the branch, so you can
``git push`` if the pull request author checked "Allow edits from maintainers"
when creating the pull request.

If you don't have GitHub CLI or hub installed, you can set up a git alias. On
Unix and macOS::

   $ git config --global alias.pr '!sh -c "git fetch upstream pull/${1}/head:pr_${1} && git checkout pr_${1}" -'

On Windows, reverse the single (``'``) and double (``"``) quotes:

.. code-block:: bash

   git config --global alias.pr "!sh -c 'git fetch upstream pull/${1}/head:pr_${1} && git checkout pr_${1}' -"

The alias only needs to be done once.  After the alias is set up, you can get a
local copy of a pull request as follows::

   git pr <pr_number>

.. _accepting-and-merging-a-pr:

Accepting and Merging a Pull Request
------------------------------------

Pull requests can be accepted and merged by a Python Core Developer.
You can read more about what to look for before accepting a change
:ref:`here <committing>`.

All pull requests have required checks that need to pass before a change
can be merged. At any point, a core developer can schedule an automatic merge
of the change by
clicking the gray ``Enable auto-merge (squash)`` button. You will find
it at the bottom of the pull request page. The auto-merge will only
happen if all the required checks pass, but the PR does not need to have been
approved for a successful auto-merge to take place.

If all required checks are already finished on a PR you're reviewing,
in place of the gray ``Enable auto-merge`` button you will find a green
``Squash and merge`` button.

In either case, adjust and clean up the commit message.

Here's an example of a **good** commit message::

   gh-12345: Improve the spam module (GH-777)

   * Add method A to the spam module
   * Update the documentation of the spam module

Here's an example of a **bad** commit message::

   gh-12345: Improve the spam module (#777)

   * Improve the spam module
   * merge from main
   * adjust code based on review comment
   * rebased

The bad example contains bullet points that are a direct effect of the
PR life cycle, while being irrelevant to the final change.

.. note::
   `How to Write a Git Commit Message <https://cbea.ms/git-commit/>`_
   is a nice article describing how to write a good commit message.

Finally, press the ``Confirm squash and merge`` button.

Cancelling an Automatic Merge
-----------------------------

If you notice a problem with a pull request that was accepted and where
auto-merge was enabled, you can still cancel the workflow before GitHub
automatically merges the change.

Press the gray "Disable auto-merge" button on the bottom of the
pull request page to disable automatic merging entirely. This is the
recommended approach.

To pause automatic merging, apply the "DO-NOT-MERGE" label to the PR or
submit a review requesting changes. The latter will put an "awaiting
changes" label on the PR, which pauses the auto-merge similarly to
"DO-NOT-MERGE". After the author submits a fix and re-requests review, you can
resume the auto-merge process either by submitting an approving review or by
dismissing your previous review that requested changes.

Note that pushing new changes after the auto-merge flow was enabled
does **NOT** stop it.

Backporting Merged Changes
--------------------------

A pull request may need to be backported into one of the maintenance branches
after it has been accepted and merged into ``main``.  It is usually indicated
by the label ``needs backport to X.Y`` on the pull request itself.

Use the utility script
`cherry_picker.py <https://github.com/python/cherry-picker>`_
from the `core-workflow  <https://github.com/python/core-workflow>`_
repository to backport the commit.

The commit hash for backporting is the squashed commit that was merged to
the ``main`` branch.  On the merged pull request, scroll to the bottom of the
page.  Find the event that says something like::

   <core_developer> merged commit <commit_sha1> into python:main <sometime> ago.

By following the link to ``<commit_sha1>``, you will get the full commit hash.

Alternatively, the commit hash can also be obtained by the following git
commands:

.. code-block:: bash

   git fetch upstream
   git rev-parse ":/gh-12345"

The above commands will print out the hash of the commit containing
``"gh-12345"`` as part of the commit message.

When formatting the commit message for a backport commit: leave the original
one as is and delete the number of the backport pull request.

Example of good backport commit message::

    gh-12345: Improve the spam module (GH-777)

    * Add method A to the spam module
    * Update the documentation of the spam module

    (cherry picked from commit 62adc55)

Example of bad backport commit message::

    gh-12345: Improve the spam module (GH-777) (#888)

    * Add method A to the spam module
    * Update the documentation of the spam module

Editing a Pull Request Prior to Merging
---------------------------------------

When a pull request submitter has enabled the `Allow edits from maintainers`_
option, Python Core Developers may decide to make any remaining edits needed
prior to merging themselves, rather than asking the submitter to do them. This
can be particularly appropriate when the remaining changes are bookkeeping
items like updating ``Misc/ACKS``.

.. _Allow edits from maintainers: https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/allowing-changes-to-a-pull-request-branch-created-from-a-fork

To edit an open pull request that targets ``main``:

1. In the pull request page, under the description, there is some information
   about the contributor's forked CPython repository and branch name that will be useful later::

      <contributor> wants to merge 1 commit into python:main from <contributor>:<branch_name>

2. Fetch the pull request, using the :ref:`git pr <git_pr>` alias::

      git pr <pr_number>

   This will checkout the contributor's branch at ``<pr_number>``.

3. Make and commit your changes on the branch.  For example, merge in changes
   made to ``main`` since the PR was submitted (any merge commits will be
   removed by the later ``Squash and Merge`` when accepting the change):

   .. code-block:: bash

      git fetch upstream
      git merge upstream/main
      git add <filename>
      git commit -m "<message>"

4. Push the changes back to the contributor's PR branch::

      git push git@github.com:<contributor>/cpython <pr_number>:<branch_name>

5. Optionally, :ref:`delete the PR branch <deleting_branches>`.


GitHub CLI
----------

`GitHub CLI <https://cli.github.com>`_ is a command-line
interface that allows you to create, update, and check GitHub
issues and pull requests.

You can install GitHub CLI `by following these instructions
<https://github.com/cli/cli#installation>`_. After installing,
you need to authenticate::

    gh auth login

Examples of useful commands:

* Create a PR::

      gh pr create

* Check out another PR::

      gh pr checkout <pr-id>

* Set ``ssh`` as the Git protocol::

      gh config set git_protocol ssh

* Set the browser::

      gh config set browser <browser-path>
