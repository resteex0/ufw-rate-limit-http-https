# ufw-rate-limit-http-https
Rate limit traffic to your webserver with UFW.
This is the UFW rules we add to our \etc\ufw\before.rules in UFW to prevent DDoS attacks on our webservers.
Adjustments can be made depending on ligitimate traffic to the webserver.
Works with Debian 9 / 10 Servers and Ubuntu 18.04 & 20.04 Server.
# Usage:
Add these lines to /etc/ufw/before.rules after<br>
<code># End required lines</code>
1. Add these lines</br>
<code># Start CUSTOM UFW added by clusterednetworks 2020-10-20</code><br>
<code># Limit to 20 concurrent connections on port 80/443 per IP</code><br>
<code>-A ufw-before-input -p tcp --syn --dport 80 -m connlimit --connlimit-above 20 -j DROP</code><br>
<code>-A ufw-before-input -p tcp --syn --dport 443 -m connlimit --connlimit-above 20 -j DROP</code><br>
<code># Limit to 20 connections on port 80/443 per 2 seconds per IP</code><br>
<code>-A ufw-before-input -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --set</code><br>
<code>-A ufw-before-input -p tcp --dport 80 -i eth0 -m state --state NEW -m recent --update --seconds 2 --hitcount 20 -j DROP</code><br>
<code>-A ufw-before-input -p tcp --dport 443 -i eth0 -m state --state NEW -m recent --set</code><br>
<code>-A ufw-before-input -p tcp --dport 443 -i eth0 -m state --state NEW -m recent --update --seconds 2 --hitcount 20 -j DROP</code><br>
<code># End Custom UFW by clusterednetworks</code><br>

2. Reload the filewall rules<br>
<code>ufw logging on</code><br>
<code>sudo ufw allow 1194/udp</code><br>
<code>sudo ufw reload</code><br>
<code>sudo ufw enable</code><br>
<code>sudo ufw status numbered</code><br>
<code>iptables-save>firewall.out</code><br>
<code>iptables-restore<firewall.out</code><br>
<code>sudo apt-get install iptables-persistent</code><br>
<code>iptables-save|grep -i 'google'</code><br>
  
examples of delete some of iptables rules<br>
<code>iptables --delete INPUT -p tcp -m tcp --dport 80 -m string --string "google.com" --algo kmp --to 65535 -j DROP</code><br>
<code>iptables -t nat -F</code>
  
 example of delete all iptables rules
  
<code>iptables -P INPUT ACCEPT</code><br>
<code>iptables -P FORWARD ACCEPT</code><br>
<code>iptables -P OUTPUT ACCEPT</code><br>
<code>iptables -t nat -F</code><br>
<code>iptables -t mangle -F</code><br>
<code>iptables -F</code><br>
<code>iptables -X</code><br>

<code>ip6tables -P INPUT ACCEPT</code><br>
<code>ip6tables -P FORWARD ACCEPT</code><br>
<code>ip6tables -P OUTPUT ACCEPT</code><br>
<code>ip6tables -t nat -F</code><br>
<code>ip6tables -t mangle -F</code><br>
<code>ip6tables -F</code><br>
<code>ip6tables -X</code><br>

<code>iptables -nvL</code>

iptables honeyports<br>
<code>iptables -t nat -A PREROUTING -p tcp --dport 1:65534 -j REDIRECT --to-ports 10000</code><br>
<code>iptables-save|grep -i '10000'</code><br>
<code>iptables -t nat -F</code>

some iptables command for helping<br>
<code>sudo iptables -I INPUT -t filter -s www.facebook.com -j DROP</code><br>
<code>iptables -L INPUT</code><br>
<code>iptables -D INPUT 1</code><br>
<code>iptables-save > iptables.dump</code><br>
<code>iptables-restore < iptables.dump</code><br>
