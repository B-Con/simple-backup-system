Simple Backup System
====================
The Simple Backup System is a shell-script based backup framework to provide a simple and flexible way to manage custom backup scripts. It is flexible, unit-oriented, and provides a very basic framework for managing the scripts and performing periodic backups and rotations. The goal is to allow for arbitrary backup scripts to be managed and for archives to be easily consolidated into one place.

The restrictions imposed on the individual backup scripts are almost non-existent, but configruation makes it easy for them to share common parameters.

Simple Backup System tries not to duplicate unnecessary functionality and can be integrated with scheduling utilities like `cron`.

This README serves as the documentation, for now.


Motivation
----------
I wrote SBS to give me an easy way to manage my own backup scripts. I develop SBS as I need it.

Within the framework are all my own personal backup scripts. I don't see a point (yet) in seperating the project from my own backup scripts.


Types of Backups
----------------
Backup scripts perform a single task and may or may not produce an archive. Actions can backup skeleton filesystems or webpages, they can synchronize filesystems, or whatever.

Actions are allowed to do anything they want, although ones that generate an archive should place it in the backup root directory in a folder with the action's name.


Architecture
------------
The backup system is divided into three sections:

* **actions** are singular backup scripts that perform one task, act atomically, and can be invoked individually.

* the **manager** is a set of scripts that manages the individual actions. It keeps track of which actions to execute, what groups to have, etc. It also rotates and cleans up old backup archives.

* **configuration** is a set of config files that provide settings for the actions and manager.

The filesystem heirarchy is as follows:

     * actions/         # Scripts that perform backups.

     * config/          # All global and individual config files.

     * manager/         # All the manager scripts.

     * manager/_GROUP_  # Where "_GROPU_" is an action group, namely a folder containing symlinks
                        # to all the actions associated with the group.

All backup archives produced by the actions go to the same configurable archive root.


###Actions
Actions exist as one script in "actions/" that performs the backup, an optional config file for that script, and an output folder that contains the backup output. Each action's optional config file goes in "config/" with the same name.

A action's name is the *exact same as its script name*.

Actions can be individually enabled/disabled by their filesystem execute permissions; scripts without the execute permission are always skipped by any managing script.


###Manager
The manager has various scripts to perform archiving and rotating the backups generated by the actions.

For organization and automation, actions can be added to groups. A group of actions is just a folder with symlinks to all the action scripts included in the group. (These scripts are canonically action scripts, but need not be; they can be any script.) A common example would be a group called "weekly" with symlinks to all the backup scripts to be run weekly. A manager script that calls all of them can then be copied from the

There is one master manager script provided by default that takes as an argument the group name and then calls all the scripts in it. Following the above example, this script with an argument of "weekly" would execute the scripts in the folder "weekly" and could be added to a weekly cron job.


###Backup Archive Structure
All archives are saved in a location

    `/path_to_root_backup/group_name/action_name/`

where

* `path_to_root_backup is` the global backup archive root path.

* `group_name` is a name set within the action script to group similar types of backups.

* `action_name` is the name of the backup action.

The `group_name` doesn't really matter for now. It only serves to conveniently group similar types of backups. (It has no relation to the manager groups.) It may be removed in the future if deemed not worthwhile.

Backup archives may or may not be created by action scripts.

A action script that generates an archive outputs its latest backup into a folder called `action_name/current` which is then archived and rotated by the backup manager. The action's archives are all kept within `action_name/` and named based on the date they were archived.

A backup archive not created by a action script will simply omits the `action_name/current` directory. This will cause it to be be ignored by the automated archiving and rotating process. Tt can store anything in and be untouched by automation. (For example, this might be something that is only updated rarely and only by hand.)


TO-DO
---
* Add logging of success/failure/error for action scripts.
* Add logging output of action scripts.
