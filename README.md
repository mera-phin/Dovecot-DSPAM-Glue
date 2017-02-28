# Dovecot-DSPAM-Glue

Dovecot-DSPAM-Glue is a pair of small scripts to act as a bridge between dovecot IMAP service and the dspam daemon for easy training and retraining of the dspam message filter by end users via simple movement of messages to and from a specific 'junk' message folder. It is a duplication of the older "dovecot Antispam" plugin but works directly from the current IMAPSieve functions of dovecot.

The first script is a simple perl script to capture and write-to-file the streamed output of each message that is moved by the user as it is supplied by sieve via STDIO and to sort each message into the proper category for spam/innocent in a temporary directory, typically alongside the mail storage. It is a replacement for the 'spool2dir' function of the antispam plugin.

The second script is a ..most likely overly complex.. perl script to sort through the temporary spool, collect all the messages of both spam and innocent types, extract the destination user, whether the file is being doubly processed, is for reprocessing or is for corpus-training, gathers the dspam signature and checks whether the message is valid for processing at all. 

The list of commands is then fed to dspam or the the despam-client for actual reprocessing and the temporary spool is emptied of the files that have been processed. If any new files have been added then they are left and are processed next time. The script is typically called by a systemd-timer/cron-job or systemd-pathwatch/inotify. This means that dovecot can rapidly stream the messages into the filesystem without having to wait for dspam to complete its reproccesing. This decouples the user actions from the backend daemon workload.

Both scripts are called by sieve, so need access as the mail proccess user/group ( typically a fixed user in a virtual mail user installation, if you are using system users for the mail process owners this needs taking into account.) 

The code works, but perlcritic confirms that it isn't the prettiest. If this helps somebody, great :) No warrenties for bleeding eyes or your immortal soul being pulled into the dark pit, express or implied.
