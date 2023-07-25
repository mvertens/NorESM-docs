.. _fork_workflow:

Workflow with GitHub forks
==========================

The recommended workflow for NorESM code contributions is based on using a
`personal fork repositories
<https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/about-forks>`_
of the main NorESM repository in GitHub, which is then cloned to a location were
code development and testing can be performed (local computer or HPC server).
This procedure follows the guidelines set out in the coderefinery tutorial on
`distributed version control and forking workflow
<https://coderefinery.github.io/git-collaborative/03-distributed>`_.

.. _(Fig. 1):
.. figure:: ../img/git_fork_3_users.png
   :width: 600
   :alt: Git fork and clone layout

   **Figure 1:** Collaborative fork-based workflow with three users.

:ref:`Figure 1<(Fig. 1)>` shows an example where three users are collaborating
with code contributions to a main repository using a fork-based workflow.
The workflow passes through the following steps:

#. `Fork the main repository
   <https://docs.github.com/en/get-started/quickstart/fork-a-repo>`_ to a new
   fork repository on GitHub. (first time)
#. `Clone the fork repository
   <https://docs.github.com/en/get-started/quickstart/fork-a-repo#cloning-your-forked-repository>`_
   to a local computer or HPC server. (first time)
#. After making code changes, push code changes to fork repository.
#. Create a pull request to push code changes from fork repository to the main
   repository.
#. Synchronize local clone (and/or fork repository) with recent changes from the
   main repository.


Creating a NorESM Sandbox
'''''''''''''''''''''''''

The repositories in the NorESM organization
(https://github.com/NorESMHub) are read-only for the majority of
contributors. In order to contribute to these repositories and
collaborate with other NorESM developers you should create a fork or
forks of the relevant component repositories you want to work with. As
an example, if you want to work with NorESM/CAM got to
https://github.com/NorESMhub/CAM and click the "Fork" button at the
top right. This will then create a fork in your personal GitHub user
account. You only need to do this once: you'll have one GitHub fork to
hold all of your branches.

All the repositories in https://github.com/NorESMhub are
public. This means that your fork will also be publicly
readable. However, it will only be writeable by you, UNLESS you
add collaborators. If you want to keep your developments
private for some time, you can avoid pushing them to your
fork. One option is to just commit your changes to the local
repository that exists on your machine and wait to push them
until you're ready to share them. Alternatively, you can create
a private repository on GitHub or elsewhere - but you cannot
use GitHub's "fork" functionality to do this. The latter choice
will help you share your changes with a small group of people
and at the same time keep them private.

NorESM checkout consists of a set of external repositories that are individually cloned by a utility called
``checkout_externals``. An ``Externals.cfg`` file provides the necessary description of each external repository.

The ``checkout_externals`` command then downloads the external
components needed to build and run NorESM. For more details on this
tool, including instructions for changing the versions of these
externals, see the file README_EXTERNALS.rst at the top level of your
NorESM checkout described below.

First, make a new clone of the main NorESMhub repository and checkout
the relevant starting point (i.e. tagname). 

First clone NorESM: ::
   
   > git clone --origin  https://github.com/NorESMhub/NorESM 
   > cd NorESM

Next determine the available tags: ::

   > git tag

Check out out the tag you want: ::
   
   > git checkout <TAGNAME>

Create the sandbox by running ``checkout_externals``. Executing
``checkout_externals`` will now give you a NorESM code sandbox. ::

   > ./manage_externals/checkout_externals

You now have a sandbox that you can use to carry out experiments and scientific development.

Basic development workflow for NorESM
'''''''''''''''''''''''''''''''''''''

All developers working with NorESM are strongly encouraged to FIRST
open an issue in relevant repository before starting development. This
will make other developers aware of your upcoming work, permit
feedback from others that would be valuable and most importantly avoid
duplication of work.

To start putting in changes into a NorESMF component you should first
create a new local branch of your target component.  Lets say you
wanted to implement new science or infrastructure in NorESM CAM,
you would want to do the following:

::
   
   > cd components/cam
   > git checkout -b MYBRANCH

**The above** ``git checkout`` command assumes that you want to create
your branch starting from whatever tag was part of the NorESM
checkout. Before creating your branch, it is a good idea to know what
tag your branch is up-to-date with and what the latest tag is on the
main development branch.  This can be done with ::

   > git describe

**Do not commit changes directly back to the main branch (or any similar
branches that you got, such as release branches).**

You can then use the standard git workflow to commit your changes::

  [Make some changes]
  git status
  git add ...
  git commit

If/when you want to share your changes, you can do so with [#]_::

  git remote add GITUSER git@github.com:GITUSER/CTSM.git
  git push -u GITUSER MYBRANCH

replacing ``GITUSER`` with your GitHub username.

.. [#] Once you have become comfortable with git, a good alternative to
       having separate clones is to use `git worktree
       <https://git-scm.com/docs/git-worktree>`_. This gives you a
       separate working directory that shares branches, remotes,
       etc. with your original clone.

.. [#] Do the initial clone of the main NorESMhub repository (rather
       than of your personal fork).

       The instructions here will give you two remotes whose names have
       unambiguous meanings: one named ``noresmhub`` that points to the
       main repository, and one named with your GitHub username. Many
       git tutorials refer to remotes named ``origin`` and
       ``upstream``. With the above recommendations, ``noresmhub``
       corresponds to ``upstream``, and the remote named with your
       username corresponds to ``origin``. 

.. [#] You only need to do the ``git remote add`` command once per
       clone. The URL used here assumes that you have set up ssh keys,
       as described in `Recommended git setup
       <https://github.com/ESCOMP/CTSM/wiki/Recommended-git-setup>`_. If
       you have not done so, you can use the URL
       ``https://github.com/GITUSER/CTSM.git``.

       The ``-u`` (or ``--set-upstream``) argument to ``git push`` sets
       the upstream of MYBRANCH to GITUSER/MYBRANCH. This way, you can
       run ``git push`` and ``git pull`` from this branch in the future
       without having to specify any other arguments.


Git lets you manage multiple branches within a single local clone.
However, if you have already starting experiments with your cloned
repository then checking out a different branch in an existing clone
can interfere with any ongoing cases that you created from that clone,
and can make it hard to reproduce what you've done later. So, its a
good idea to have a separate clone for each branch.


Keep local clone in sync with main repository
'''''''''''''''''''''''''''''''''''''''''''''

When cloning a GitHub repository, the local clone will refer to the original
source as ``origin`` and branches in the original source by ``origin/<branch>``,
(e.g. ``origin/master``). For the fork-based workflow, ``origin`` will refer to
the personal fork, but it is also useful to be able to sync with the main
repository, often referred to as ``upstream`` (we keep to these naming
conventions hereafter). Connections to remote repositories are managed by ``git
remote``
::

  git remote -v    # List all remote repositories

The ``git remote`` command supports both adding and deleting references to
external git repositories. In additions to ``upstream`` it is also possible to
include references to the forks used by collaborators, for short code testing or
longer term collaboration. To add a reference to ``upstream``
::

  git remote add upstream https://github.com/NorESMhub/NorESM.git

To get the latest version of ``master`` from ``upstream``
::

   git pull upstream master

Make code changes
'''''''''''''''''

A typical cycle for making code changes have the follow steps:

#. Make sure your code base is updated with the latest source files
#. Pull changes from ``upstream`` if necessary
#. Check out a feature branch for your code changes
#. Make your changes and commit to the feature branch
#. Push the feature branch to ``origin`` repository
#. Create a pull request to ``upstream`` repository
#. Remove temporary feature branches after this has been merged with ``upstream``

In a practical session, this can involve running the following commands
::

   git fetch --all               # Update commit log for all remote git repositories
   git pull upstream master      # pull latest from the upstream master branch; do it often if possible
   git push origin master        # do this when upstream is ahead of you local (origin) repos, to stay in sync.
   git checkout -b my-feature    # create and switch to a new branch "my-feature".
   ...                           # edit some code
   git commit -a -m "Add first draft of my feature"
   git push


Make changes directly in GitHub
'''''''''''''''''''''''''''''''

For changes that do not need testing (e.g. changes to documentation), it can be
more convenient to edit files directly in GitHub. It is still recommend to work
on a personal fork, make a feature branch and create a pull request to merge the
changes with the main repository, as you would do when working from a local
clone repository. See `GitHub docs <https://docs.github.com/en>`_ for procedures
on how to `sync a fork from the web user interface
<https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/working-with-forks/syncing-a-fork#syncing-a-fork-from-the-web-ui>`_,
and how to `edit a file in GitHub
<https://docs.github.com/en/repositories/working-with-files/managing-files/editing-files>`_.


Pull requests and code merging
''''''''''''''''''''''''''''''

You are now ready to make a pull request of my-feature branch. This can by done
from github after you pushed. Remember that after the PR is created and reviewed
by others, you may need to go back and fix things before it is accepted and can
be merged. After merging the PR, you should normally delete the feature branch
and update your local repos. to keep things clean. It will still be seen as a
(merged) branch in the upstream repository.

**Note**: If your feature branch has many commits, it may be smart to "squash"
 the history before creating the PR, so that it is easier to review the full
 changes by others (and yourself). This can be done using the **git rebase**
 command, but this will not be covered here.


Synchronize local repositories through a GitHub fork repository
'''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''''

.. _(Fig. 2):
.. figure:: ../img/git_fork_sync.png
   :width: 500
   :alt: Synchronize through git fork

   **Figure 2:** Synchronize code changes between local git repositories through a shared GitHub fork repository.

A GitHub fork repository can serve as a synchronization point for code testing,
if this is carried out on multiple platforms (e.g. a local machine in addition
to one or more HPC systems). Normally development will take place on only one of
the platforms (e.g. a local machine), and all other platforms are used only for
testing (solid line arrows in :ref:`Fig. 2<(Fig. 2)>`). However, the system also allows
code changes to travel the other direction, e.g. if a bug fix is introduced
during a test run (stippled arrows in :ref:`Fig. 2<(Fig. 2)>`).


Removing feature branches after merging
'''''''''''''''''''''''''''''''''''''''

After a feature branch has been merged with ``master`` in the main repository,
it is useful to clean up local clones and the GitHub fork repository by removing
the feature branch. The fork copy can be removed with an option from the pull
request, after the branch merging has completed, but the clone will still have a
log reference to the remote feature branch. References to outdated remote
branches are removed by using the ``--prune`` flag when fetching remote updates
::

   git fetch --all --prune

If the feature branch was merged using the ``squash and merge`` or ``rebase and
merge`` option, there will not be a merge commit in the log history to indicate
that the feature branch was merged. In this case the feature branch should be
deleted using the ``-D`` flag (short for ``--delete --force``)
::

   git branch -D my-feature
