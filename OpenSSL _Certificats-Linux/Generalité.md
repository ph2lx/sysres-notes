•	Si le certif est ok depuis l’intérieur mais pas de l’extérieur voir avec le réseau  pour qu’il mette en place le certif sur le Reverse Proxy WAF  



•	Pour rechercher un certif non trouvé :

•	sudo find / -type f \\( -iname "\*.pem" -o -iname "\*.crt" -o -iname "\*.cer" -o -iname "\*.key" -o -iname "\*.jks" \\) 2>/dev/null  

•	sudo find / -name "\*.jks" -o -name "\*.p12" 2>/dev/null  



