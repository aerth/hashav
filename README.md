```shell

#!/bin/sh
#   aerth ##
############
 #########################
# MIT LICENSE - WTFPL - GPL3
# hashav version 95
# simple /bin integrity check
# adds minute or two to startup, okay with me.
# usage:
# Edit the bottom of this script. 
# Anything below Line #150 is scanned.
# You can use # to comment out lines
# And use * for wildcards.
# Gotcha: Wildcards only go one level 
#         (ex: /usr/src/* is not /usr/src/*/*)
##########################
# install:
# ./scan --install
# if running slackware, add --slack to add to /etc/rc.d/rc.local
# ./scan --install --slack
###########################
# initialize (only once!):
# /usr/local/hashav/scan -c
###########################
# scan:
# /usr/local/hashav/scan
###########################
# default dbdir
HASHDB=/usr/local/hashav/hashdb

# These files are OK to change (IMO)
# ignoring adjtime inittab motd mtab and /etc/random-seed
OKTOFAIL="/etc/adjtime /etc/random-seed /etc/mtab"

# Exit on any errors
set -e

# hashav -c
do_db() {
echo Creating *NEW* hashdb
echo Checking if old one exists at $HASHDB/current
if [ -f $HASHDB/current ]; then
echo "Backing up old hashdb (and ourself) to $HASHDB"
/usr/bin/tar cJf $HASHDB/hashdb-$(date +%s).tar.xz $HASHDB/current $0
fi
echo Creating original sha384sums of listed files past Line 150 of this script.
echo Add more like so:
echo "  # L150"
echo "  /home/.ssh/config"
echo "  /etc/ssh/*"
echo "  /sbin/*"
mkdir -p $HASHDB || true
for i in $(cat $0 | tail -n +150); do

if [ -f $i ]; then
echo Calculating sha384sum for $i
/usr/bin/sha384sum $i >> $HASHDB/current
sleep 0.005
fi
done
}


# hashav --install
do_install() {
echo INSTALL ? Press Ctrl C to quit!
read INSTALLQUESTION
echo Installing to /usr/local/hashav
mkdir -p /usr/local/hashav
echo Attempting to copy hashdb
cp -R hashdb $HASHDB || true
echo Installing...
cp $0 /usr/local/hashav/scan
echo Installed to /usr/local/hashav
}

# hashav --install --slack
do_slack() {
if [ -z $(cat /etc/rc.d/rc.local | grep hashav) ]; then
echo Adding to /etc/rc.d/rc.local
echo "/usr/local/hashav/scan" >> /etc/rc.d/rc.local
fi
}



# hashav
do_hunt() {
if [ ! -f $HASHDB/current ]; then
echo No hashdb. Creating one now.
do_db
fi
NUMSUM=$(cat $HASHDB/current | wc -l)
echo Checking $NUMSUM sha384sums...
/usr/bin/sha384sum $HASHDB/current -c | grep -v OK | awk '{print $1}' | awk 'BEGIN { FS = ":"} {print $1 }' > $HASHDB/latest
BADRESULTS="none"
if [ ! $(cat $HASHDB/latest) == "" ]; then
	BADRESULTS=$(cat $HASHDB/latest | grep -v $OKTOFAIL)
fi
# uh-oh
if [ ! "$BADRESULTS" == "none" ]; then
	echo Found Bad Results. Rescanning $(wc -l $BADRESULTS).
	BADSUMS=$(for i in $BADRESULTS; do echo hashing $i && sha384sum $i; done)
	GOODSUMS=$(grep $BADRESULTS $HASHDB/current)
	echo
	echo '*** WARNING ***'
	echo
	echo Wanted:
	echo $GOODSUMS
	echo Got:
	echo $BADSUMS

	for i in $(cat $HASHDB/current | grep $HASHDB/latest-results.txt); do echo $i; sleep 0.5; done
	echo
	echo '*** WARNING ***'
	echo ""
	echo "File Integrity Check: FAIL"

else
	echo ""
	echo "File Integrity Check: PASS"
fi
echo ""
exit 0
}
# HASHAV version 95
	printf "\n[hashav v95]\n" && \
	if [ "$1" == "-c" ]; then
		do_db
	else if [ "$1" == "--install" ]; then
		do_install
		if [ "$2" == "--slack" ]; then
		do_slack
		fi
	fi
	
	echo "Scanning"
	do_hunt
	echo PRESS ENTER TO EXIT
	read EXIT

	exit
	die
	exit 1
	fi
	exit 
	exit 1
	exit 0
	sleep 10000
#!/bin/echo
# EVERYTHING BELOW THIS LINE IS BEING SCANNED AND CHANGES WILL ALERT ROOT USER BY EMAIL


# Can now comment
/usr/bin/sha384sum
/bin/sh
/bin/bash
/sbin/init
/sbin/getty
/bin/*
/sbin/*
/boot/*
/boot/*/*
/usr/sbin/*
#/usr/bin/*
/usr/local/bin/*
/var/lib/*

```
