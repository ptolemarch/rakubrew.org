                         (lk(__)lk)        (lb(___)lb)                         
          (lr(_______)lr)(la(_____)la)  (lk(|  | __)lk)(lu(__ _)lu)(lb(\  |_________   ______  _  __)lb)
          (lr(\_  __ \)lr)(la(__  \)la) (lk(|  |/ /)lk)(lu(  |  \)lu)(lb( __ \_  __ \_/ __ \ \/ \/ /)lb)
          (lr( |  | \/)lr)(la(/ __ \)la)(lk(|    <)lk)(lu(|  |  /)lu)(lb( \_\ \  | \/\  ___/\     / )lb)
          (lr( |__|)lr)  (la((______/)la)(lk(__|__\)lk)(lu(____/)lu)(lb(|_____/__|    \_____>\/\_/  )lb)
                                                           v0.01

(h1(Intro
=====)h1)

rakubrew (called rakudobrew in a previous life) is a (link([Raku][1])link) installation
tool. It allows to have multiple versions of different Raku implementations
installed in parallel and switch between them. It's a (link([perlbrew][2])link) and
(link([plenv][3])link) look alike and supports both flavours of commands.


(h1(Installation
============)h1)

(platform-pp(
    curl https://rakubrew.org/pp/rakubrew > ~/rakubrew
    chmod +x ~/rakubrew
    RAKUBREW_HOME=`~/rakubrew home`
    mv ~/rakubrew $RAKUBREW_HOME/bin
    echo 'eval "$($RAKUBREW_HOME/bin/rakubrew init auto)"' >> ~/.profile
)platform-pp)
(platform-win(
On Windows CMD do:

    curl https://rakubrew.org/win/rakubrew > %USERPROFILE%/rakubrew
    # TODO: The following is not right
    SET RAKUBREW_HOME=`~/rakubrew home`
    mv %USERPROFILE%/rakubrew %RAKUBREW_HOME%/bin
    %RAKUBREW_HOME%/bin init # Further installation instructions

On Windows PowerShell do:

    curl https://rakubrew.org/win/rakubrew > $Env:USERPROFILE/rakubrew
    # TODO: The following is not right
    RAKUBREW_HOME = "$Env:USERPROFILE\rakubrew home"
    mv %USERPROFILE%/rakubrew %RAKUBREW_HOME%/bin
    Add-Content -Force -Path \$PROFILE -Value '$RAKUBREW_HOME/bin/rakubrew init PowerShell | Out-String | Invoke-Expression'
)platform-win)
(platform-macos(
    curl https://rakubrew.org/macos/rakubrew > ~/rakubrew
    chmod +x ~/rakubrew
    RAKUBREW_HOME=`~/rakubrew home`
    mv ~/rakubrew $RAKUBREW_HOME/bin
    echo 'eval "$($RAKUBREW_HOME/bin/rakubrew init auto)"' >> ~/.profile
)platform-macos)


(h1(How
===)h1)

    (c(# list available versions)c)
    rakubrew list
    
    (c(# download and install the latest Rakudo on MoarVM version)c)
    rakubrew download

    (c(# switch to use that version (substitute the version you just installed))c)
    rakubrew switch moar-2019.11

    raku -e 'say "Now running {$*RAKU.compiler.version}!"'


(h1(global, shell, local
====================)h1)

rakubrew knows three different versions that can be set separately.

The (code(`global`)code) version is the one that is selected when neither the (code(`shell`)code)
version nor the (code(`local`)code) version are active.

The (code(`shell`)code) version changes the active Raku version just in the current shell.
Closing the current shell also looses the (code(`shell`)code) version.

The (code(`local`)code) version is specific to a folder. When CWD is in that folder or a sub folder
that version of Raku is used. Only works in (code(`shim`)code) mode. To unset a local version
one must delete the (code(`.RAKU_VERSION`)code) file in the respective folder.


(h1(Modes
=====)h1)

rakubrew can work in two distinct modes: (code(`env`)code) and (code(`shim`)code)

In (code(`env`)code) mode rakubrew modifies the (code(`$PATH`)code) variable as needed when switching
between versions. This is neat because one then runs the executables directly.
This is the default mode on *nix.

In (code(`shim`)code) mode rakubrew generates wrapper scripts called shims for all
executables it can find in all the different Raku installations. These
shims forward to the actual executable when called. This mechanism allows for
some advanced features, such as local versions. When installing a module that
adds scripts one must make rakubrew aware of these new scripts. This is done
with

    rakubrew rehash

In (code(`env`)code) mode this is not necessary.


(h1(Registering external versions
=============================)h1)

To add a Raku installation to rakubrew that was created outside of rakubrew one
should do:

    rakubrew register name-of-version /path/to/raku/install/directory


(h1(Upgrading
=========)h1)

    rakubrew self-upgrade


(h1(Uninstall
=========)h1)

To remove rakubrew and any Perl 6 implementations it has installed on your
system, delete the  (code(`~/.rakubrew`)code) and (code(`~/.local/share/rakubrew`)code) directories.


(h1(Git annoyances
==============)h1)

(h2(Git not found
-------------)h2)

In case your git binary is not in the (code(`PATH`)code) on your system, you can specify
a custom path to the git binary using the (code(`GIT_BINARY`)code) environment variable:

    GIT_BINARY="%USERPROFILE%\Local Settings\Application Data\GitHub\PORTAB~1\bin\git.exe" rakubrew build all


(h2(Git clone failing
-----------------)h2)

When git fails to clone any repositories, it might be you are sitting behind a
firewall that blocks the network protocol / port git uses by default.
You can change the protocol to something that will usually be acceptable to
firewalls by setting the (code(`GIT_PROTOCOL`)code) environment variable:

    (c(# for https)c)
    GIT_PROTOCOL=https rakubrew list-available
    (c(# for ssh)c)
    GIT_PROTOCOL=ssh rakubrew list-available


(h1(Command-line switches
=====================)h1)

(h2(`version` or `current`
----------------------)h2)

Show the currently active Raku version.


(h2(`versions` or `list`
--------------------)h2)

List all installed Raku installations.


(h2(`global [version]` or `switch [version]`
----------------------------------------)h2)

Show or set the globally configured Raku version.


(h2(`shell [--unset|version]`
-------------------------)h2)

Show, set or unset the shell version.


(h2(`local [version]`
-----------------)h2)

Show or set the local version.


(h2(`nuke [version]` or `unregister [version]`
------------------------------------------)h2)

Removes an installed or registered version. Versions built by rakubrew are
actually deleted, registered versions are only unregistered but not deleted.


(h2(`rehash`
--------)h2)

Regenerate all shims. Newly installed scripts will not work unless this is
called. This is only necessary in (code(`shim`)code) mode.


(h2(`list-available`
----------------)h2)

List all Raku versions that can be installed.


(h2(`build [jvm|moar|moar-blead|all] [tag|branch|sha-1] [--configure-opts=]`
------------------------------------------------------------------------)h2)

Build a Raku version. The arguments are:
(i(-)i) The backend.
    (i(-)i) (code(`moar-blead`)code) is the moar and nqp backends at the master branch.
    (i(-)i) (code(`all`)code) will build all backends.
(i(-)i) The version to build. Call (code(`list-available`)code) to see a list of available
  versions. When left empty the latest release is built.
  It is also possible to specify a commit sha or branch to build.
(i(-)i) Configure options.


(h2(`triple [rakudo-ver [nqp-ver [moar-ver]]]`
------------------------------------------)h2)

Build a specific set of Rakudo, NQP and MoarVM commits.


(h2(`register <name> <path>`
------------------------)h2)

Register an externaly built / installed Raku version with rakubrew.


(h2(`build-zef`
-----------)h2)

Install Zef into the current Raku version.


(h2(`exec <command> [command-args]`
-------------------------------)h2)

Explicitly call an executable. You normally shouldn't need to do this.


(h2(`which <command>`
-----------------)h2)

Show the full path to the executable.


(h2(`whence [--path] <command>`
---------------------------)h2)

List all versions that contain the given command. when (code(`--path`)code) is given the
path of the executables is given instead.


(h2(`mode [env|shim]`
-----------------)h2)

Show or set the mode of operation.


(h2(`self-upgrade`
--------------)h2)

Upgrade rakubrew.


(h2(`init`
------)h2)

Show installation instructions.


(h2(`test [version|all]`
--------------------)h2)

Run tests in the current or given version.


(h1(Bugs'n'Development
==================)h1)

rakubrew is developed on (link([Github][4])link). To report a bug, head over there and
create a new issue.


(h1(Authors
=======)h1)

Patrick Böker (full rewrite and current maintainer)
Tadeusz Sośnierz (original author)


(url([1]: https://raku.org/)url)
(url([2]: https://perlbrew.pl/)url)
(url([3]: https://github.com/tokuhirom/plenv)url)
(url([4]: https://github.com/rakubrew/App-Rakubrew/)url)
