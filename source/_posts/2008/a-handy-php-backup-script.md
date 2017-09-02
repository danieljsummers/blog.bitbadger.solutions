---
layout: post
title: A Handy PHP Backup Script
author: Daniel
date: 2008-03-28 23:06:37
categories:
- [ Databases, MySQL ]
- [ Databases, PostgreSQL ]
- [ Programming, PHP ]
tags:
- backup
- database
- php
- script
summary: A PHP script to create a zip archive and e-mail it
---

I found a script over on the Lunarpages Forums about [using PHP to back up your site][forum].  I have taken it, modified it a little, beefed up the documentation a lot, and am now posting it here.  You can copy and paste it from below to customize it for your own use.

{% codeblock backup.php lang:php %}
<?php
/**
 * Generic Backup Script.
 *
 * To configure this script for your purposes, just edit the parameters below.
 * Once you have the parameters set properly, when the script executes, it will
 * create an archive file, gzip it, and e-mail it to the address specified.  It
 * can be executed through cron with the command
 *
 * php -q [name of script]
 *
 * You are free to use this, modify it, copy it, etc.  However, neither DJS
 * Consulting nor Daniel J. Summers assume any responsibility for good or bad
 * things that happen when modifications of this script are run.
 *
 * @author Daniel J. Summers <daniel@djs-consulting.com>
 */

// --- SCRIPT PARAMETERS ---

/*  -- File Name --
	This is the name of the file that you're backing up, and should contain no
	slashes.  For example, if you're backing up a database, this might look
	something like...
$sFilename = "backup-my_database_name-" . date("Y-m-d") . ".sql"; */
$sFilename = "backup-[whatever-it-is]-" . date("Y-m-d") . ".[extension]";

/*  -- E-mail Address --
	This is the e-mail address to which the message will be sent. */
$sEmailAddress = "[your e-mail address]";

/*  -- E-mail Subject --
	This is the subject that will be on the e-mail you receive. */
$sEmailSubject = "[something meaningful]";

/*  -- E-mail Message --
	This is the text of the message that will be sent. */
$sMessage = "Compressed database backup file $sFilename.gz attached.";

/*  -- Backup Command --
	This is the command that does the work.

  A note on the database commands - your setup likely requires a password
	for these commands, and they each allow you to pass a password on the
	command line.  However, this is very insecure, as anyone who runs "ps" can
	see your password!  For MySQL, you can create a ~&#47;.my.cnf file - it is
	detailed at //dev.mysql.com/doc/refman/4.1/en/password-security.html .
	For PostgreSQL, the file is ~&#47;.pgpass, and it is detailed at
	//www.postgresql.org/docs/8.0/interactive/libpq-pgpass.html .  Both of
	these files should be chmod-ded to 600, so that they can only be viewed by
	you, the creator.

  That being said, some common commands are...

  - Backing Up a MySQL Database
$sBackupCommand = "mysqldump -u [user_name] [db_name] > $sFilename";

  - Backing Up a PostgreSQL Database
$sBackupCommand = "pg_dump [db_name] -h localhost -U [user_name] -d -O > $sFilename";

  - Backing Up a set of files (tar and gzip)
$sBackupCommand = "tar cvf $sFilename [directory]

  Whatever command you use, this script appends .gz to the filename after the command is executed.  */
$sBackupCommand = "[a backup command]";

// --- END OF SCRIPT PARAMETERS ---
//
// Edit below at your own risk.  :)

// Do the backup.
$sResult = passthru($sBackupCommand . "; gzip $sFilename");
$sFilename .= ".gz";

// Create the message.
$sMessage = "Compressed database backup file $sFilename attached.";
$sMimeBoundary = "<<<:" . md5(time());
$sData = chunk_split(base64_encode(implode("", file($sFilename))));

$sHeaders = "From: $sEmailAddress\r\n"
		. "MIME-Version: 1.0\r\n"
		. "Content-type: multipart&#47;mixed;\r\n"
		. " boundary=\"$sMimeBoundary\"\r\n";

$sContent = "This is a multi-part message in MIME format.\r\n\r\n"
		. "--$sMimeBoundary\r\n"
		. "Content-Type: text/plain; charset=\"iso-8859-1\"\r\n"
		. "Content-Transfer-Encoding: 7bit\r\n\r\n"
		. $sMessage."\r\n"
		. "--$sMimeBoundary\r\n"
		. "Content-Disposition: attachment;\r\n"
		. "Content-Type: Application/Octet-Stream; name=\"$sFilename\"\r\n"
		. "Content-Transfer-Encoding: base64\r\n\r\n"
		. $sData."\r\n"
		. "--$sMimeBoundary\r\n";

// Send the message.
mail($sEmailAddress, $sEmailSubject, $sContent, $sHeaders);

// Delete the file - we don't need it any more.
unlink($sFilename);
{% endcodeblock %}


[forum]: //www.lunarforums.com/lunarpages_how_tos/site_and_mysql_backups_via_cron-t22118.0.html "Site and MySQL backups via cron - Lunarforums"
