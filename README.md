# rsync-time-machine
Automatically takes snapshots of your directory trees using rsync --link-dest

No matter how careful and meticulous we are, we eventually make mistakes, erasing or altering some important file.
rsync-time-machine will maintain versioned copies of your files and directories, using rsync's --link-dest option, so you will be able to recover from eventual mistakes in no time.

Just copy the script to a suitable location, like "~ / bin /", give it execute permission, and create a proper configuration file in your homedir or in the same dir as the script itself. If there are configuration files in both locations, the configuration file in your homedir takes precedence.

The configuration file can be written using ".rstm.config" file as template:

----------------------------------------------
 #.rstm.config
----------------------------------------------
    <LOGFILE>       log.txt
    <BACKUPROOT>    ~/better_safe_than_sorry
    <TARGET>        ~/Miscelanea/move_android_data
    <TARGET>        ~/abcde.conf
----------------------------------------------

In the config file:
* Lines starting with a "#" are ignored;
* Lines starting with the tags shown in the sample config file are mandatory;
* <LOGFILE> tagged lines set the name of the log file to be mantained by the script;
* <BACKUPROOT> tagged lines set the pathname of the directory that will contain de backups (and the log file)
* <TARGET> tagged lines (multiple targets are allowed) list the pathnames of directories or files to be backed up.

Finally, create a cronjob in your crontab to periodically run the script (each 15m in the exemple below):

\*/15 * * * * ~/bin/rstm

Every time the script runs, it will create a new time stamped copy of each target. For each unchanged files within each target (recursively, if the target is a directory), hardlinks are created to the same file in the previous latest backup (if any), to save space. After copying all targets, the script will examine existing copies and remove older ones.

The script will keep 6 monthly copies, 4 weekly copies, 30 daily copies, 24 hourly copies and all copies created in the latest 60 minutes, and will remove all remainig copies.

By definition:
* monthly backups are backups created at 0h:00m on the first day of each month;
* weekly backups are backups created at 0h:00m on each sunday;
* dayly backups are backups created at 0h:00m on every day;
* hourly backups are backups created at 00m of every hour;

For the scheme above to work well, we need to make sure that copies are created at round times - the cronjob mentioned above must be created keeping that in mind. Se create teh conjob to run the script each 5m, 10m, 15m, 20m or 30m.
