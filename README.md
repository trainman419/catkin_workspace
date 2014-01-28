# catkin_workspace - A Friendly tool for managing catkin workspaces

catkin_workspace is intended to be a friendly tool for creating and managing catkin workspaces and rosinstall files.

In particular, it should:
 * Create the `src` directory automatically
 * Create and update the rosinstall file
 * Update the workspace whenever the rosinstall file changes
 * Install dependencies automatically
 * Use system packages as dependencies where possible
  * This means installing from debs whenever possible
  * When asking for a specific package, it should still download the source,
    even if debs are available
 * Warn the user if they try to use their home directory as a workspace

It should not:
 * invoke catkin_make or otherwise try to do the build
 * create new packages

## Sample workflows

Check out, build and use package from github:

    . /opt/ros/hydro/setup.bash
    cd catkin_ws
    catkin_workspace create https://github.com/sample/ros-package.git
    # create src
    # add https://github.com/sample/ros-package.git to the rosinstall
    # invoke wstool to update the workspace
    # rosdep install --from-paths src --ignore-src
    catkin_make
    . devel/setup.bash

Check out navigation from source, for experimentation:

    . /opt/ros/hydro/setup.bash
    cd catkin_ws
    catkin_workspace create navigation
    # create src
    # add the src entry for navigation to the rosinstall file
    # invoke wstool to update the workspace
    # install dependencies using rosdep
    # resolve any remaining dependencies using rosinstall_generator
    # Tell the user where navigation has been checked out to:
    echo "navigation has been checked out to src/navigation/"

    # USER modifies src/navigation/
    catkin_make
    . devel/setup.bash

Update the workspace and rebuild it:

    cd catkin_ws
    catkin_workspace upgrade
    catkin_make

## Open questions

 * Should catkin_workspace be able to maintain a full source tree?
 * If checking out individual packages, will catkin_workspace ever need to use rosinstall_generator to find dependencies?
 * How do we resolve binary vs source-only dependencies?
  1. invoke rosdep to figure out which dependencies are installable from binaries
  2. invoke rosinstall_generator to resolve remaining source dependencies
  3. invoke wstool to check out source dependencies
  4. repeat 1-3 until all dependencies are satisfiable
  5. invoke rosdep to install dependencies from binaries
 * It will be nearly impossible to resolve dependencies for packages that depend on other user packages that aren't listed in rosdistro
 * How should we handle checking out a user's fork of a project?
  * What if they've checked out upstream, made changes, and then want to switch to their fork?
 * Should we make it easy to check out a different branch of a project?
