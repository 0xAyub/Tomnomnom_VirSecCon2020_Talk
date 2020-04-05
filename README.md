# Tomnomnom_VirSecCon2020_Talk

#Subdomainbrute<br>
subdomains.txt <br>
admin<br>
test<br>
qa<br>
dev<br>
www<br>
m<br>
blog<br>
<br>
<br>
cat subdomains.txt<br>
while read sub; do echo "sub.abcd.com"; done < subdomains.txt[input from the subdomains]<br>
<br>
while read sub; do if host "$sub.abcd.com" &> /dev/null; then echo "$sub.abcd.com"; fi; done < subdomains.txt<br>
<br>
cat subdomains.txt | while read sub; do if host "$sub.abcd.com" &> /dev/null; then echo "$sub.abcd.com"; fi; done<br>
<br>
./brute.sh domain.com<br>
--content of brute.sh--<br>
#!/usr/bin/bash<br>
domain=$1<br>
while read sub; do <br>
	if host "$sub.$domain" &>/dev/null;<br>
		then echo "$sub.$domain";<br>
	fi;<br>
done<br>
-content of brute.sh--<br>
[cat subdomains.txt | ./brute.sh domain.com]<br>
<br>
#Dangling cname[check for subdomain takeover]<br>
host -t CNAME domain.com<br>
host -t CNAME sub.domain.com | grep 'an alias' | awk '{print $NF}'<br>
cname=$(host -t CNAME sub.domain.com | grep 'an alias' | awk '{print $NF}')<br>
<br>
./cname.sh domain.com<br>
--content of cname.sh--<br>
#!/usr/bin/bash<br>
domain=$1<br>
while read sub; do<br>
	cname=$(host -t CNAME $sub.$domain | grep 'an alias' | awk '{print $NF}')<br>
	if [ -z "$cname"]; then<br>
		continue<br>
	fi<br>
	if ! host $cname &> /dev/null; then<br>
		echo "$cname did not resolve ($sub.$domain)";<br>
	fi<br>
done<br>
--content of cname.s--<br>
[cat subdomains.txt| ./cname.sh domain.com]<br>
<br>
#bunch of target<br>
cat subdomains.txt | ./brute.sh domain.com | awk '{print "https://" $1}' > urls<br>
<br>
#speeding up the subdomain parsing process<br>
./sub.sh domain.com<br>
--content of sub.sh--<br>
#!/usr/bin/bash<br>
domain=$1<br>
if host $domain &>/dev/null; then<br>
	echo "$domain"<br>
fi<br>
--content of sub.sh--<br>
[./sub.sh domain.com]<br>
#cli xargs<br>
cat subdomains.txt | awk '{print $1 ".domain.com"}' | xargs -n1 -P10 ./sub.sh<br>
<br>
./parsub.sh domain.com<br>
--content of parsub.sh--<br>
#!/usr/bin/bash<br>
domain=$1<br>
while read sub; do<br>
	echo $sub.$domain<br>
done | xargs -n1 -P10 ./sub.sh<br>
<br>
[cat subdomains.txt | .parsub.sh domain.com]<br>
