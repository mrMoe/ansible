https://securitytrails.com/blog/mitigating-ssh-based-attacks-top-15-best-security-practices
https://hackertarget.com/ssh-blacklist/
iptable-persistent

http://ipdeny.com/ipblocks/
https://www.dshield.org/ssh.html
http://iplists.firehol.org/#about
https://www.spamhaus.org/drop/drop.txt
https://www.spamhaus.org/drop/edrop.txt
https://www.spamhaus.org/drop/dropv6.txt
https://www.nirsoft.net/countryip/cn.html
https://ipinfo.io/
https://db-ip.com/54.38.175.113
https://docs.ansible.com/ansible/latest/modules/iptables_module.html
http://www.blocklist.de/en/index.html

#!/bin/bash
_input="/root/block.ip.address.list.txt"
IPT=/sbin/iptables
$IPT -N droplist
egrep -v "^#|^$" x | while IFS= read -r ip
do
	$IPT -A droplist -i eth1 -s $ip -j LOG --log-prefix " myBad IP BlockList  "
	$IPT -A droplist -i eth1 -s $ip -j DROP
done < "$_input"
# Drop it 
$IPT -I INPUT -j droplist
$IPT -I OUTPUT -j droplist
$IPT -I FORWARD -j droplist


block france, korea, china, usa, russia,
$IPT -A INPUT -m limit --limit 5/m --limit-burst 7 -j LOG
http://mikhailian.mova.org/node/147


https://www.cyberciti.biz/faq/block-entier-country-using-iptables/
#!/bin/bash
# Purpose: Block all traffic from AFGHANISTAN (af) and CHINA (CN). Use ISO code. #
# See url for more info - http://www.cyberciti.biz/faq/?p=3402
# Author: nixCraft <www.cyberciti.biz> under GPL v.2.0+
# -------------------------------------------------------------------------------
ISO="af cn" 
 
### Set PATH ###
IPT=/sbin/iptables
WGET=/usr/bin/wget
EGREP=/bin/egrep
 
### No editing below ###
SPAMLIST="countrydrop"
ZONEROOT="/root/iptables"
DLROOT="http://www.ipdeny.com/ipblocks/data/countries"
 
cleanOldRules(){
$IPT -F
$IPT -X
$IPT -t nat -F
$IPT -t nat -X
$IPT -t mangle -F
$IPT -t mangle -X
$IPT -P INPUT ACCEPT
$IPT -P OUTPUT ACCEPT
$IPT -P FORWARD ACCEPT
}
 
# create a dir
[ ! -d $ZONEROOT ] && /bin/mkdir -p $ZONEROOT
 
# clean old rules
cleanOldRules
 
# create a new iptables list
$IPT -N $SPAMLIST
 
for c  in $ISO
do 
	# local zone file
	tDB=$ZONEROOT/$c.zone
 
	# get fresh zone file
	$WGET -O $tDB $DLROOT/$c.zone
 
	# country specific log message
	SPAMDROPMSG="$c Country Drop"
 
	# get 
	BADIPS=$(egrep -v "^#|^$" $tDB)
	for ipblock in $BADIPS
	do
	   $IPT -A $SPAMLIST -s $ipblock -j LOG --log-prefix "$SPAMDROPMSG"
	   $IPT -A $SPAMLIST -s $ipblock -j DROP
	done
done
 
# Drop everything 
$IPT -I INPUT -j $SPAMLIST
$IPT -I OUTPUT -j $SPAMLIST
$IPT -I FORWARD -j $SPAMLIST
 
# call your other iptable script
# /path/to/other/iptables.sh
 
exit 0




root@denk:/var/www# journalctl -p err -p warning --since today
