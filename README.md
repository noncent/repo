# A Cheatsheet, Everyday commands for developers

### Drupal CMS
---

```html
 <!--Drupal files and folders permissions -->
/default on 755
/default/files including all subfolders and files on 744 (or 755)
/default/themes including all subfolders and files on 755
/default/modules including all subfolders and files on 755
/default/settings.php and /default/default.settings.php on 444

<!-- Go to base project folder -->
cd /opt/www-site-com

<!-- Change drupal folder permissions -->
chown -R nginx: drupal && chmod -R 777 drupal

<!-- Go inside drupal -->
cd drupal

<!-- Install composer -->
sudo php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');"
sudo HASH="$(wget -q -O - https://composer.github.io/installer.sig)"
HASH="$(wget -q -O - https://composer.github.io/installer.sig)"
sudo php -r "if (hash_file('SHA384', 'composer-setup.php') === '$HASH') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;"
sudo php composer-setup.php --install-dir=/usr/local/bin --filename=composer
composer -V

<!-- Composer self update -->
composer self-update

<!-- Find composer or run direct from path -->
sudo $(which composer) require 'drupal/minifyhtml:^1.11'

<!-- Run composer -->
composer require 'drupal/key:^1.16' 'drupal/menu_item_extras:^2.19' 'drupal/paragraphs:^1.15' 'drupal/queue_ui:^3.1' 'drupal/real_aes:^2.5' 'drupal/s3fs:^3.1' 'drupal/services:^4.0@beta' 'drupal/smtp:^1.2' --with-all-dependencies

<!-- Create drush symlink if not exists -->
ln  -s vendor/bin/drush drush

<!-- Run update and cache clear -->
./drush updatedb && ./drush cr

<!-- Go back to project folder -->
cd ../

<!-- Revert permissions -->
chown -R www-data: drupal && chmod -R 755 drupal && chmod -R 444 drupal/sites/default/settings.php

<!-- Revert permissions if inside drupal -->
chown -R www-data: $(pwd) && chmod -R 755 $(pwd) && chmod -R 444 $(pwd)/sites/default/settings.php
```

## MySQL Commands
---

```html
<!-- Access monitor: -->
mysql -u [username] -p; (will prompt for password)

<!-- Show all databases: -->
show databases;

<!-- Access database: -->
mysql -u [username] -p [database] (will prompt for password)

<!-- Create new database: -->
create database [database];

<!-- Select database: -->
use [database];

<!-- Determine what database is in use: -->
select database();

<!-- Show all tables: -->
show tables;

<!-- Show table structure: -->
describe [table];

<!-- List all indexes on a table: -->
show index from [table];

<!-- Create new table with columns: -->
CREATE TABLE [table] ([column] VARCHAR(120), [another-column] DATETIME);

<!-- Adding a column: -->
ALTER TABLE [table] ADD COLUMN [column] VARCHAR(120);

<!-- Adding a column with an unique, auto-incrementing ID: -->
ALTER TABLE [table] ADD COLUMN [column] int NOT NULL AUTO_INCREMENT PRIMARY KEY;

<!-- Inserting a record: -->
INSERT INTO [table] ([column], [column]) VALUES ('[value]', '[value]');

<!-- MySQL function for datetime input: -->
NOW()

<!-- Selecting records: -->
SELECT * FROM [table];

<!-- Explain records: -->
EXPLAIN SELECT * FROM [table];

<!-- Selecting parts of records: -->
SELECT [column], [another-column] FROM [table];

<!-- Counting records: -->
SELECT COUNT([column]) FROM [table];

<!-- Counting and selecting grouped records: -->
SELECT *, (SELECT COUNT([column]) FROM [table]) AS count FROM [table] GROUP BY [column];

<!-- Selecting specific records: (Selectors: <, >, !=; combine multiple selectors with AND, OR) -->
SELECT * FROM [table] WHERE [column] = [value];

<!-- Select records containing [value]: -->
SELECT * FROM [table] WHERE [column] LIKE '%[value]%';

<!-- Select records starting with [value]: -->
SELECT * FROM [table] WHERE [column] LIKE '[value]%';

<!-- Select records starting with val and ending with ue: -->
SELECT * FROM [table] WHERE [column] LIKE '[val_ue]';

<!-- Select a range: -->
SELECT * FROM [table] WHERE [column] BETWEEN [value1] and [value2];

<!-- Select with custom order and only limit:  (Order: DESC, ASC) -->
SELECT * FROM [table] WHERE [column] ORDER BY [column] ASC LIMIT [value];

<!-- Updating records: -->
UPDATE [table] SET [column] = '[updated-value]' WHERE [column] = [value];

<!-- Deleting records: -->
DELETE FROM [table] WHERE [column] = [value];

<!-- Delete all records from a table (without dropping the table itself): -->
<!-- (This also resets the incrementing counter for auto generated columns like an id column.) -->
DELETE FROM [table];

<!-- Delete all records in a table: -->
truncate table [table];

<!-- Removing table columns: -->
ALTER TABLE [table] DROP COLUMN [column];

<!-- Editing COLLATE value -->
ALTER TABLE `media_master`
CHANGE `media_text` `media_text` varchar(85) COLLATE 'utf8_general_ci' NULL COMMENT 'Photo Text or Label' AFTER `caption_visible`;


<!-- Deleting tables: -->
DROP TABLE [table];

<!-- Deleting databases: -->
DROP DATABASE [database];

<!-- Custom column output names: -->
SELECT [column] AS [custom-column] FROM [table];

<!-- Export a database dump (more info here): -->
<!-- Use --lock-tables=false option for locked tables (more info here). -->
mysqldump -u [username] -p [database] -h localhost > db_backup.sql

<!-- Import a database dump (more info here): -->
mysql -u [username] -p -h localhost [database] < db_backup.sql

<!-- Logout: -->
exit;

<!-- Database export as GZip -->
mysqldump -u root -p -h localhost db_name | gzip > db_backup.sql.gz
mysqldump -u root -p -h localhost db_name | gzip > db_backup.sql.gz

<!-- mysqldump: Error 2020: Got packet bigger than 'max_allowed_packet'
bytes when dumping table `mailbox_backup` at row: 3369 -->
mysqldump --max_allowed_packet=512M -u root -p -h localhost db_name | gzip > db_backup.sql.gz

<!-- $(date +'%d%m%Y') means current date in dmY format e.g. for 30 Dec 2022, print 30122022 -->

<!-- mysqldump throws: Unknown table 'COLUMN_STATISTICS' in information_schema (1109) -->

mysqldump --column-statistics=0 -h localhost -u root -p | gzip -c > ./db_dumps/sql-$(date +'%d%m%Y').gz
mysqldump --column-statistics=0 -h localhost -u root -p | gzip -c > ./db_dumps/$(date +'%d%m%Y').gz
mysqldump -u root -p db_name -h localhost | gzip > dbsql$(date +%d%m%y).sql.gz

<!--
mysqldump: Error: 'Access denied; you need (at least one of) the PROCESS privilege(s)
for this operation' when trying to dump tablespaces
 -->
 mysqldump -u root -p db_name -h localhost --column-statistics=0 --no-tablespaces --set-gtid-purged=OFF | gzip > dump-$(date +'%m%d%Y').sql.gz

<!-- Database import from GZip, 7z and zip -->
zcat db_backup.sql.gz | mysql -u 'root' -p db_name -h localhost
gunzip < db_backup.sql.gz | mysql -u root -p db_name -h localhost
zcat < db_backup.sql.gz | mysql -u root -p db_name -h localhost
zcat ./db_backup.sql.gz | mysql -u root -p db_name -h localhost
7z x -so db_backup.sql.7z | mysql -u root -p db_name -h localhost
unzip -p ./db_backup.sql.zip | mysql -u root -p db_name -h localhost

<!-- Import MySQL file to database: -->
mysql -u <user-name> -p < </full/path/database_import.sql>

<!-- Export MySQL file to database: -->
mysql -u <user-name> -p database > </full/path/database_export.sql>

<!-- Show all database sizes: -->
SELECT table_schema "<MY-DATABASE-NAME>", sum( data_length + index_length ) / 1024 / 1024 "Data Base Size in MB" FROM information_schema.TABLES GROUP BY table_schema;

<!-- Show all tables sizes for database: -->
SELECT table_name AS `Table`, round(((data_length + index_length) / 1024 / 1024), 2) `Size in MB` FROM information_schema.TABLES WHERE table_schema = "<MY-DATABASE-NAME>";

<!-- Size in MB -->
SELECT table_name, table_rows, data_length, index_length, round(((data_length + index_length) / 1024 / 1024), 2) as "Size in MB" FROM information_schema.TABLES WHERE table_schema = "stg_drupal_sl" ORDER BY `Size in MB` DESC;

<!-- Size in GB -->
SELECT table_schema "DB Name", sum(round(((data_length + index_length) / 1024 / 1024 / 1024), 2)) "DB Size in GB" FROM information_schema.tables GROUP BY table_schema ORDER BY `DB Size in GB` DESC;
```

## Zip Commands
---

```html

<!-- How to ZIP Files and Directories -->
zip archivename.zip filename1 filename2 filename3

<!-- Folder and subfolders zip -->
zip -r archivename.zip directory_name

<!-- add multiple files and directories in the same archive -->
zip -r archivename.zip directory_name1 directory_name2 file1 file1

<!-- Creating a Password Protected ZIP file -->
zip -e  archivename.zip directory_name

<!-- Split the zip in 1GB each -->
zip -s 1g -r archivename.zip directory_name

<!-- Everything in current folder -->
zip archivename.zip *
zip archivename.zip .* *

<!-- Unzip a ZIP file -->
unzip latest.zip

<!-- Unzip a ZIP File to a Different Directory -->
unzip filename.zip -d /path/to/directory

<!-- Unzip a Password Protected ZIP file -->
unzip -P PasswOrd filename.zip

<!-- Unzip Multiple ZIP Files -->
unzip '*.zip'

<!-- Exclude Files when Unzipping a ZIP File -->
unzip filename.zip -x file1-to-exclude file2-to-exclude

<!-- If you want to overwrite existing files without prompting, use the -o option: -->
unzip -o filename.zip
```

## GZip Commands
---

```html
<!-- Compress file/s and delete the original -->
gzip file.txt
gzip file1.txt file2.txt file3.txt
gzip *.txt

<!-- Compress a single file and keep the original -->
gzip -c file.txt > file.txt.gz
gzip -k file.txt > file.txt.gz

<!-- Compress all files recursively -->
gzip -r *

<!-- Decompress a gzip compressed file -->
gzip -d file.txt.gz
gunzip file.txt.gz

<!-- List of content file.txt, notes.txt, etc. -->
zcat test.txt.gz

<!-- Find / search in contents , will show example.pdf, etc. -->
zgrep exa test.txt.gz

```

## TAR Commands
---

```html

<!-- Create tar archive files & folders -->
tar -cvzf code.tar.gz ./code

<!-- Creating tgz backup of /www.site.in excluding some folders -->
tar --exclude='/var/www/db-backups' --exclude='/var/www/json_data' \
--exclude='/var/www/vendor' -zcvf code.$(date +'%d%m%Y').tgz /var/www/www.site.in

<!-- Creating tgz backup in /backup folder excluding some folders -->
tar --exclude='./folder' --exclude='./upload' -zcvf /backup/filename.tgz .

<!-- Extract tar archive files in a folder -->
mkdir sample && tar -xf sample.tar.gz -C ./sample

<!-- List Content of tar contents -->
tar -tvf sample.tar.gz

<!-- Extract a single file from tar contents -->
tar -xvf sample.tar home.html
tar -zxvf sample.tar.gz home.html
tar -xvf sample.tar "file1" "file2" "..."
tar -xvf sample.tar --wildcards '*.php'

<!-- Append a file or folder in tar -->
tar -rvf sample.tar robots.txt

<!-- Check the Size of the tar File -->
tar -czf - sample.tar | wc -c

<!-- Exclude files and directories when creating tar file -->
tar --exclude='robots.txt' -zcvf backup.tar.gz /home/source
tar --exclude='*.txt' -zcvf backup.tar.gz /home/source

<!-- Remove file and directory from tar archive -->
tar --delete -f backup.tar.gz sample.txt
tar --delete -f backup.tar.gz '/home/source/uploads'
```


## Linux Commands
---

```
STRONGLY SUGGESTING TO VISIT - https://www.explainshell.com/explain?cmd=mkdir+-p
explainshell.com, will explain your command in easy format
```

```
Top Linux Commands You Must Know as a Regular User
```

```html


<!--
Convert Non UTF Latin ANSI ASCII Character in Linux
Sometimes we need to find and replace the UNICODE Characters like Latin, French, Chines, etc

e.g. I want to find and replace 'АБЦ' which is non ASCII Character
Check - https://www.branah.com/unicode-converter
-->
echo -e '\u0410\u0411\u0426'
printf '%s%b%s\n' "$(tput setaf 118)" "\u0410\u0411\u0426" "$(tput sgr0)"

<!-- Find files by there mime types  charset -->
find . -type f -exec file --mime {} \; | grep "charset=utf-16"

<!-- replace space with a underscore in files -->
for file in *; do mv "$file" `echo $file | tr ' ' '_'` ; done
for i in *' '*; do   mv "$i" `echo $i | sed -e 's/ /_/g'`; done

<!-- find files has spaces in names -->
find . -name '*[[:space:]]*'

<!-- find multiple types of files -->
find . '.*\.(jpeg|jpg|gif|png)$'

<!-- if want to count file -->
find . '.*\.(jpeg|jpg|gif|png)$' | wc -l

<!-- find non ascii character -->
find . | perl -ne 'print if /[^[:ascii:]]/'
find . -print0 | perl -n0e 'chomp; print $_, "\n" if /[[:^ascii:][:cntrl:]]/'

<!-- remove non ascii character file -->
find . -print0 | perl -MFile::Path=remove_tree -n0e 'chomp; remove_tree($_, {verbose=>1}) if /[[:^ascii:][:cntrl:]]/'

<!-- Other find examples -->
find . -type f -name "[[:upper:]]*"
find . -type f -name "*[[:upper:]]*"
find . -name '*[#U2013*]*'
find . -type f -name '*[â€“*]*'

<!-- Delete file older than 7 days -->
find . -name "*.txt" -type f -mtime +7 -exec rm -f {} \;

<!-- remove all non ascii characters in file name with underscore -->
for file in *; do mv "$file" $(echo "$file" | sed -e 's/[^A-Za-z0-9._-]/_/g'); done

<!-- rename all png files in sequence order 1,2,3, ... -->
ls *.png | cat -n | while read n f; do mv "$f" "$n.png"; done


<!-- ls - The most frequently used command in Linux to list directories -->
ls -la, ls -lart

<!-- pwd - Print working directory command in Linux -->
cd - Linux command to navigate through directories
cd folder
cd ../folder

<!-- mkdir - Command used to create directories in Linux -->
mkdir fruits

<!-- will create nested folders like fruits > red > apple -->
mkdir -p fruits/red/apple

<!-- mv - Move or rename files in Linux -->
mv sitemap.xml sitemap-backup.xml

<!-- will rename source folder as _source -->
mv source _source

<!-- cp - Similar usage as mv but for copying files in Linux -->
cp /backup/settings.php .
cp /backup/settings.php ./settings.php

<!-- rm - Delete files or directories -->
rm -rf unused newfolder test backup

<!-- touch - Create blank/empty files -->
touch README.txt

<!-- ln - Create symbolic links (shortcuts) to other files -->
ln -s ./vendor/bin/drush drush

<!-- cat - Display file contents on the terminal -->
<!-- clear - Clear the terminal display -->
<!-- echo - Print any text that follows the command -->

<!-- less - Linux command to display paged outputs in the terminal -->
less long-logs.log

<!-- man - Access manual pages for all Linux commands -->
<!-- uname - Linux command to get basic information about the OS -->
<!-- whoami - Get the active username -->
<!-- tar - Command to extract and compress files in Linux -->
<!-- Compress -->
tar -cvf <archive name> <files seperated by space>

<!-- Extract -->
tar -xvf <archive name>
zip <archive name> <file names separated by space>
unzip <archive name>

<!-- grep - Search for a string within an output -->
<!-- <Any command with output> | grep "<string to find>" -->
history | grep ssh
grep -rnw '/path/to/somewhere/' -e "rgxptrn"
<!-- Find word drupal in current folder in all files -->
grep -rnw . -e "drupal"

<!-- Grep find a string in each file: -->
grep -Hnir zend_extension /usr/local/php5
grep -in database sites/default/settings.php | grep "=>" | grep -v "*"


<!-- find command to find the filles -->
<!-- find the files and folders name contains backup -->
find .  -name "*backup*"

<!-- find the files contains DS_Store and then delete them -->
find . -type f -name ".DS_Store"  -delete
<!-- Find file type and if name contains backup -- delete the file -->
find . -type f -name "*backup*"  -delete
<!-- If name contains any number -->
find . -type f \( -name '[0-9]*' -o -name '[0-9]*' \)

<!-- Linux 'sed' command stands for stream editor.
It is used to edit streams (files) using regular expressions -->
<!-- SED command, Find and Replace in files-->
sed -i '' 's|https://www.site.com|https://site.com|g' ./db.sql

<!-- head - Return the specified number of lines from the top -->
<!-- tail - Return the specified number of lines from the bottom -->
<!-- diff - Find the difference between two files -->
diff <file 1> <file 2>

<!-- cmp - Allows you to check if two files are identical -->
<!-- comm - Combines the functionality of diff and cmp -->
<!-- sort - Linux command to sort the content of a file while outputting -->
<!-- export - Export environment variables in Linux -->
<!-- zip - Zip files in Linux -->
<!-- unzip - Unzip files in Linux -->

<!-- cron - commands -->
<!-- Must visit - https://crontab.guru/examples.html -->
sudo cron -e
<!-- Make an entry - Execute command every day @ 12pm, 4pm, and 8pm -->
0 12 * * * /var/home/routine/update.sh >> /var/home/routine/update-cron.log 2>&1
0 16 * * * /var/home/routine/update.sh >> /var/home/routine/update-cron.log 2>&1
0 20 * * * /var/home/routine/update.sh >> /var/home/routine/update-cron.log

<!-- ssh - Secure Shell command in Linux -->
ssh username@hostname
<!-- SSH using pem file -->
ssh -i /source/secrets/key.pem user@34.12.12.12

<!-- service - Linux command to start and stop services -->
<!-- ps - Display active processes -->
<!-- kill and killall - Kill active processes by process ID or name -->
ps
kill <process ID>
killall <process name>

<!-- df - DISK FREE | Display disk filesystem information -->
<!-- mount - Mount file systems in Linux -->
<!-- chmod - Command to change file permissions or make them as executable -->
chmod +x loop.sh
<!-- setting recursive folder permission 655 -->
chmod -R 655 web-folder

<!-- chown - CHANGE OWNER: Command for granting ownership of files or folders -->
chown www-data: folder
chown -R www-data: folders

<!-- ifconfig - Display network interfaces and IP addresses -->
<!-- traceroute - Trace all the network hops to reach the destination -->

<!-- wget - Direct download files from the internet -->
<!-- Using wget as Spider -->
wget --spider -r -nd -nv -H -l 1 -w 2 -o spider.log  https://www.site.com/

<!-- ufw - Firewall command -->
iptables - Base firewall for all other firewall utilities to interface with
iptables -A INPUT -p tcp -m tcp --dport 80 -j ACCEPT
ufw allow 80

<!-- apt, pacman, yum, rpm - Package managers depending on the distro -->
<!-- Debian and Debian-based distros - apt install <package name> -->
<!-- Arch and Arch-based distros - pacman -S <package name> -->
<!-- Red Hat and Red Hat-based distros - yum install <package name> -->
<!-- Fedora and CentOS - yum install <package> -->
<!-- sudo - Command to escalate privileges in Linux -->
<!-- cal - View a command-line calendar -->
<!-- alias - Create custom shortcuts for your regularly used commands -->
alias lsl="ls -l"
OR
alias rmd="rm -r"

<!-- dd - Majorly used for creating bootable USB sticks -->
<!-- whereis - Locate the binary, source, and manual pages for a command -->
<!-- whatis - Find what a command is used for -->
<!-- top - View active processes live with their system usage -->
<!-- useradd and usermod - Add new user or change existing users data -->
<!-- passwd - Create or update passwords for existing users -->
```


## Amazon Linux 2 PHP 8.1 installation
---
```html
<!-- Find all installed PHP versions in repo -->
yum list installed | grep php
amazon-linux-extras | grep php

<!-- Remove all installed PHP versions from repo -->
yum remove php*

<!-- Install packages from epel or remi -->
yum install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
yum install https://rpms.remirepo.net/enterprise/remi-release-7.rpm
yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

<!-- Is used to download and make usable all the metadata for the currently enabled yum repos. -->
yum makecache

<!-- Install utility tools -->
yum -y install yum-utils

<!-- Disable the current active version from repo, other wise when you will run yum install php
it will always install php 7.4 version -->
amazon-linux-extras disable php7.4

<!-- Post disabling the PHP 7.4 enable the PHP 8.1 to install -->
yum-config-manager --enable remi-php81
amazon-linux-extras enable php8.1

<!-- Clean the yum cache -->
yum clean metadata

<!-- Now it will install PHP 8.1 version -->
yum install php

<!-- Check installed PHP version -->
php -v

<!-- Install PHP PEAR -->
yum install php-pear

<!-- Install PHP dev Utilities -->
yum install php-devel

<!-- Install image magic with PECL -->
pecl install imagick

<!-- Install other essential PHP extensions -->
yum install php-{pear,cgi,pdo,common,curl,mbstring,gd,mysqlnd,gettext,bcmath,json,xml,fpm,intl,zip,imagick,mcrypt:}

<!-- Restart PHP FPM service -->
service php-fpm restart

<!-- Restart PHP service -->
systemctl php restart

<!-- Check if PHP and FPM services are running -->
ps aux | grep php

<!-- In case you want to install New relic for monitoring -->
newrelic-install

<!-- Restart PHP FPM service -->
systemctl restart php-fpm.service

<!-- Check for which version of PHP mcrypt installed -->
yum list installed | grep php | grep mcrypt

<!-- Update mcrypt for newer PHP version in cse already installed -->
yum update php-mcrypt

<!-- Install PHP extensions -->
yum install -y php-mbstring mcrypt

<!-- To see the FPM config e.g. www.conf -->
cd /etc/php-fpm.d
vi www.conf

<!-- Reload Nginx Reverse Proxy -->
service nginx reload

<!-- Restart Apache service in case not Nginx -->
service httpd reload

<!-- Check if Nginx or Apache configs has any error/syntax error -->
httpd -t
nginx -t

<!-- Reload Nginx Reverse Proxy -->
systemctl reload nginx.service

<!-- Check if PHP is running -->
systemctl | grep php

<!-- How much disk is free in human readable format -->
df -h

<!-- Check Disk Usages -->
du -sh

<!-- If you want to update all the installed linux packages -->
yum check-update

<!-- Check status of the services -->
systemctl status php-fpm.service
systemctl status nginx.service

<!-- The locate command finds files in Linux using the file name. -->
locate beauty-and-beast

<!-- See list of files visit https://man7.org/linux/man-pages/man1/ls.1.html-->
ls -lhrt

tail -f  /opt/www-site-com/log/error.log

<!--
    Copy all images from one step back to current images folder

    -r To copy a directory along with its sub dirctories.
    -p Preserves attribute of a file.
    -f Forcefully
 -->
cp -rpf ../public_html/*.jpg ./images

<!--  Delete folder name backups and all files ends with .gz -->
rm -rf backups *.tar.gz
```

## AWS CLI Commands
---

```html
<!-- AWS S3 Command -->

<!-- I want to delete all .DS_STORE files from everywhere in bucket -->
aws s3 rm s3://myaws-s3/backups/ --recursive --exclude "*" --include "*.DS_Store" --dryrun

<!-- --dryrun will execute the command but not in real, hence once changes are correct, run again -->
aws s3 rm s3://myaws-s3/backups/ --recursive --exclude "*" --include "*.DS_Store"

<!-- I want to sync my local images folder from S3 bucket folder -->
aws s3 sync s3://myaws-s3/backups/images/ /var/www/html/www-site-com/web/images/

<!-- Copying local sql dump backup on to s3 folder -->
aws s3 cp ./dump-$(date +%Y%m%d%H%M%S).sql.gz s3://myaws-s3/backups/sqldump/
```

## GIT Commands
---

```
<!-- How to delete a remote branch -->

<!-- deleting local branch -->
git branch -D <my-branch>

<!-- deleting remote branch -->
git push -d origin <my-branch>

<!-- How to delete a file if committed in git remote/local -->

<!-- Using 3rd party tool BFG 10x to 700x time faster but need Java runtime to installed -->
1 - Download the tool from https://rtyley.github.io/bfg-repo-cleaner/
2 - Create a separate folder and leave original git folder as backup and to keep safe
3 - I am creating folder name 'cleaner'
4 - Going inside cleaner using 'cd cleaner'
5 - Once in, cloning my git repo e.g. https://github.com/somerepo.git
6 - My cloning is completed and created a folder 'somerepo'
7 - Going inside 'somerepo' folder
8 - Copying downloaded 'bfg-1.14.0.jar' here
9 - Now checking my git branch using 'git branch' command, shows I am in master branch, cool!
10 - Now need to run command one by one

    <!-- first take a checkout of remote branch here, master -->
    [x] git checkout master
    <!-- Now run cleaner to delete a sensetie file commited name 'azdeploy.sh' here .git is local git folder -->
    [x] java -jar bfg-1.14.0.jar .git --delete-files "azdeploy.sh"
    <!-- Expire all your git ref logs -->
    [x] git reflog expire --expire=now --all
    <!-- git gc will internally trigger git prune to delete/clean expired/unwanteed logs data/history -->
    [x] git gc --prune=now --aggressive
    <!-- Force update remote with new history -->
    [x] git push --force

That's it!

<!-- Using git native command -->

1 - Create a separate folder and leave original git folder as backup and to keep safe
2 - I am creating folder name 'cleaner'
3 - Going inside cleaner using 'cd cleaner'
4 - Once in, cloning my git repo e.g. https://github.com/somerepo.git
5 - My cloning is completed and created a folder 'somerepo'
6 - Going inside 'somerepo' folder
7 - Now checking my git branch using 'git branch' command, shows I am in master branch, cool!
8 - Now need to run command one by one

    [x] git filter-branch -f --tree-filter 'rm -f azdeploy.sh' HEAD
    [x] git update-ref -d refs/original/refs/heads/<branch-name>
    [x] git reflog expire --expire=now --all
    [x] git gc --prune=now

That's it!

```

## FFMPEG Commands
---

```html
<!-- WebM to MP4: -->
ffmpeg -i xss.webm -strict experimental video.mp4
ffmpeg -i xss.webm -movflags faststart -profile:v high -level 4.2 xss.mp4

<!-- MP4 to Image: -->
ffmpeg -i video.mp4 -r 1/1 $filename%03d.jpg
```

## OpenSSL Commands
---

```html
<!-- Encrypt: Use openssl to encrypt the file: -->
openssl aes-256-cbc -a -salt -in secrets.txt -out secrets.txt.enc

<!-- Decrypt: Use openssl to decrypt the file: -->
openssl aes-256-cbc -d -a -in secrets.txt.enc -out secrets.txt.new

<!-- Integrate Subresource Integrity Check -->
openssl dgst -sha384 -binary README.md | openssl base64 -A
```

## Nginx Drupal settings
---
```html
# -----------------------------------------------
# Default server configuration www.example.com
# -----------------------------------------------

# Redirecting https://site.com to https://www.site.com
server {
    listen 8080;
    server_name example.com;
    #return 301 $scheme://www.$host$request_uri;
    return 301 https://www.example.com$request_uri;
}

# Main server block
server {
    listen 8080 default_server;
    listen [::]:8080 default_server;

    # root folder path
    root /var/www/html/www-example-com/web;

    # Add index.php to the list if you are using PHP
    index index.php index.html index.htm index.nginx-debian.html;

    # server name
    server_name www.example.com g65rteds43szmu.cloudfront.net;
    #return 301 $scheme://www.example.com$request_uri;

    # access and error log
    access_log /var/log/www-example-com/access/access.log combined;
    error_log /var/log/www-example-com/error/error.log;

    # Setting a var for site name
    set $mysite https://www.example.com;

    # Removing trailing slash and 301 redirect
    rewrite ^(.+)/+$ $mysite$1 permanent;

    # Remove ?amp parameter
    if ($args ~* "/?amp") {
        return 301 https://$host$uri;
    }

    # general settings
    charset UTF-8;
    sendfile on;
    tcp_nopush on;
    server_tokens off;
    autoindex on;
    client_max_body_size 65M;
    client_body_buffer_size 12k;
    client_header_buffer_size 1k;
    large_client_header_buffers 2 4k;

    # gzip compression settings
    gzip on;
    gzip_disable "msie6";
    gzip_vary on;
    gzip_proxied expired no-cache no-store private auth;
    gzip_comp_level 6;
    gzip_buffers 16 8k;
    gzip_http_version 1.1;
    gzip_min_length 256;
    gzip_types text/plain text/css text/xml text/javascript application/javascript application/x-javascript application/xml;

    # Essential Security Headers
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-Content-Type-Options nosniff;
    add_header Referrer-Policy "strict-origin";
    add_header Permissions-Policy "interest-cohort=()";
    add_header Strict-Transport-Security "max-age=31536000";
    add_header Content-Security-Policy "upgrade-insecure-requests";
    add_header X-Content-Type-Options nosniff;
    add_header Source-Monitoring launcher01;
    add_header Access-Control-Allow-Origin *;

    # allow only GET, HEAD and POST
    if ($request_method !~ ^(GET|HEAD|POST)$ ) {
        return 444;
    }

    # Custom 401 web auth page
    error_page 401 /401.html;
    location = /401.html {
        root /var/www/html/www-example-com;
        internal;
    }

    # Custom 4xx error
    #error_page 403 404 /4xx.html;
    #location = /4xx.html
    #{
    # root /var/www/html/www-example-com;
    # internal;
    #}

    # Custom 5xx error
    error_page 500 502 503 504 /5xx.html;
    location = /5xx.html {
        root /var/www/html/www-example-com;
        internal;
    }

    # favicon 404 off
    location = /favicon.ico {
        log_not_found off;
        access_log off;
    }

    # SEO robots.txt
    location = /robots.txt {
        allow all;
        log_not_found off;
        access_log off;
    }

    # Prevent unauthorized execution
    location ~ \..*/.*\.php$ {
        return 403;
    }

    # Prevent unauthorized execution
    location ~ ^/sites/.*/private/ {
        return 403;
    }

    # Block access to scripts in site files directory
    location ~ ^/sites/[^/]+/files/.*\.php$ {
        deny all;
    }

    #Allow "Well-Known URIs" as per RFC 5785
    location ~* ^/.well-known/ {
        allow all;
    }

    # Web auth for Drupal CMS admin login
    location /user {
        auth_basic "Restricted Content";
        auth_basic_user_file /var/www/html/www-example-com/.htpasswd;
        try_files $uri $uri/ /index.php?$query_string;
    }

    # Block     access to "hidden" files and directories whose names begin with a
    # period.   This includes directories used by version control systems such
    # as        Subversion or Git to store control files.
    location ~ (^|/)\. {
        return 403;
    }

    # Don't allow direct access to PHP files in the vendor directory.
    location ~ /vendor/.*\.php$ {
        deny all;
        return 404;
    }

    # Protect files and directories from prying eyes.
    location ~* \.(engine|inc|install|make|module|profile|po|sh|.*sql|theme|twig|tpl(\.php)?|xtmpl|yml)(~|\.sw[op]|\.bak|\.orig|\.save)?$|^(\.( ?!well-known).*|Entries.*|Repository|Root|Tag|Template|composer\.(json|lock)|web\.config)$|^#.*#$|\.php(~|\.sw[op]|\.bak|\.orig|\.save)$ {
        deny all;
        return 404;
    }

    location ~* \.(js|css|png|jpg|jpeg|gif|webp|ico|svg|woff|woff2|mp4|webm)$ {
        try_files $uri @rewrite;
        expires max;
        log_not_found off;
    }
    # Fighting  with Styles? This little gem is amazing.
    # location ~ ^/sites/.*/files/imagecache/ { # For Drupal <= 6
    location ~ ^/sites/.*/files/styles/ {
        try_files $uri @rewrite;
    }

    # Handle    private files through Drupal. Private file's path can come
    # with      a language prefix.
    # location ~ ^(/[a-z\-]+)?/system/files/ { # For Drupal >= 7
    location ~ ^(/[a-z\-]+)?/system/files/ {
        try_files $uri /index.php?$query_string;
    }

    # Enforce   clean URLs
    # Removes   index.php from urls like www.example.com/index.php/my-page --> www.example.com/my-page
    # Could     be done with 301 for permanent or other redirect codes.
    if ($request_uri ~* "^(.*/)index\.php/(.*)") {
        return 307 $1$2;
    }

    location / {
        # Disable directory listing
        autoindex off;
        # First attempt to serve request as file, then
        # as directory, then fall back to displaying a 404.
        # try_files $uri $uri/ =404;
        try_files $uri $uri/ /index.php?$query_string;
        # Fixing Cloudfront 504 issue
        proxy_read_timeout 3600;
    }

    location @rewrite {
        #rewrite ^/(.*)$ /index.php?q=$1; # For Drupal <= 6
        rewrite ^ /index.php; # For Drupal >= 7
    }

    # pass PHP scripts to FastCGI server
    #
    location ~ \.php$ {
        include snippets/fastcgi-php.conf;

        # try_files $fastcgi_script_name =404;

        fastcgi_param HTTP_PROXY "";
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        fastcgi_param QUERY_STRING $query_string;
        fastcgi_intercept_errors on;
        fastcgi_buffer_size 128k;
        fastcgi_buffers 4 256k;
        fastcgi_busy_buffers_size 256k;
        fastcgi_temp_file_write_size 256k;

        # Proxy settings FastCGI
        fastcgi_connect_timeout 120;
        fastcgi_send_timeout 120;
        fastcgi_read_timeout 120;

        # With php-fpm (or other unix sockets):
        fastcgi_pass unix:/var/run/php/php-fpm.sock;
    }

    # SSL configuration
    #
    # listen 443 ssl default_server;
    # listen [::]:443 ssl default_server;
    #
    # Note: You should disable gzip for SSL traffic.
    # See: https://bugs.debian.org/773332
    #
    # Read up on ssl_ciphers to ensure a secure configuration.
    # See: https://bugs.debian.org/765782
    #
    # Self signed certs generated by the ssl-cert package
    # Don't use them in a production server!
    #
    # include snippets/snakeoil.conf;
}
```

## PHP FPM | INI config
---
<!-- Ubuntu Custom PHP INI Path /etc/php/{version}/fpm/conf.d/custom.ini -->

```html
[PHP]
memory_limit=256M
upload_max_filesize=20M
post_max_size=25M
max_execution_time=30
display_errors=off
date.timezone=Asia/Kolkata
error_reporting=E_ALL & ~E_DEPRECATED & ~E_STRICT
magic_quotes_gpc=off
allow_url_fopen=on
allow_url_include=off
max_input_time=30
disable_functions=exec,passthru,shell_exec,system,popen
asp_tags=off
display_startup_errors=off
engine=on
expose_php=off
ignore_repeated_errors=off
implicit_flush=off
Log_errors_max_len=1024
log_errors=on
mysqlnd.collect_memory_statistics=off
mysqlnd.collect_statistics=off
opcache.enable=1
opcache.jit_buffer_size=100M
output_buffering=4096
realpath_cache_size=512k
realpath_cache_ttl=300
serialize_precision=17
short_open_tag=on
unserialize_callback_func=
zend.enable_gc=on
```