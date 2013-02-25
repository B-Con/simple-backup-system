TO-DO List
===

Known Issues
---
* **Cannot execute any of the scripts via symbolic links.** - Scripts rely on knowing their absolute execution path, since everything is self-contained and resources only find each other via relative pathes. This is due to the current simplifying decision to keep everything contained in one folder.

To-Do
---
* **Logging** - Logging is spotty and inconsistent. Need to make all scripts inherit log file properly.
