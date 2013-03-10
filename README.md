Simple Backup System
====================
The Simple Backup System is a shell-script based backup framework to provide a simple and flexible way to manage custom backup scripts. It is flexible, unit-oriented, and provides a very basic framework for managing the scripts and performing periodic backups and rotations. The goal is to allow for arbitrary backup scripts to be managed and an easy way to consolidate archives into one place.

The restrictions imposed on the individual backup scripts are almost non-existent, but it easy for them to share common settings.

Simple Backup System tries not to duplicate unnecessary functionality and can be integrated with scheduling utilities like `cron`.

This README also serves as the documentation.


Motivation
----------
I wrote SBS to give me an easy way to manage my own backup scripts. I develop SBS as I need it.

Within the framework are all my own personal backup scripts. I don't see a point (yet) in seperating the project from my own backup scripts.


Architecture
------------
The backup system is divided into three sections:

* **actions** are singular backup scripts that perform one task, act atomically, and can be invoked individually.

* the **manager** is a set of scripts that manages the individual actions. It keeps track of which actions to execute, what groups to have, etc. It also rotates and cleans up old backup archives.

* **configuration** is a set of config files that provide settings and libraries for the actions and manager.

The filesystem heirarchy is as follows:

     * actions/         # Scripts that perform backups.

     * config/          # All global and individual config files.

     * manager/         # All the manager scripts.

Backup archives produced by the actions can easily go to the same configurable archive root.


###Actions
Actions exist as one script in `actions/` that performs the backup, an optional config file for that script, and an output folder that contains the backup output. Each action's optional config file goes in "config/" with the same name.

A action's name is the *exact* same as its script name.

Actions can be individually enabled/disabled by their filesystem execute permissions; scripts without the execute permission are always skipped by any managing script.


###Manager
The manager has functionality for executing actions and archiving and rotating the backups generated by the actions.

The manager is basically just a library of functions. Manager scripts are placed in `manager/` and then usually just consist of a long list of function calls acting on specified actions.

A common example would be a manager script for performing daily backups, and then a manager script for performing weekly archiving and rotations. (Executing actions and archiving their output are decoupled to easily allow for a "father-grandfather" archiving scheme.


###Backup Archive Structure
All archives are saved in a location

    `/path_to_root_backup/action_name/`

where

* `path_to_root_backup is` the global backup archive root path.

* `action_name` is the name of the backup action.

Backup archives may or may not be created by action scripts.

A action that generates an archive outputs its latest backup into a folder called `action_name/current` which is then seperately archived and rotated by the backup manager. The action's archives are all kept within `action_name/` and named based on the date they were archived.


TO-DO
-----
* Add logging of success/failure/error for action scripts.
* Add logging output of action scripts.
