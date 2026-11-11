# --------------
# Dynatrace setup
# --------------
# 	1. Dynatrace free trial - Launch instance in AWS

# --------------
# Create EC2 
# --------------
# 	1. Create EC2
# 	2. Login EC2 or SSH EC2
# 	chmod 400 dynatrace.pem
# 	ssh -i dynatrace.pem ec2-user@34.229.229.139
# 	yum update -y # after first time instance creation

# ------------------------------------------
# Install environment Activegate - Via dynatrace UI
# ------------------------------------------
# 	a. Got to infrastructure and operations (earlier Deployment status)
# 	b. Click on "add host" as shown below

# 	c. 
	
# 	d. But First Generate token via access token () like PagerDuty API token
# 		i. None - API token but go with "Pass" token
# 		ii. Save token
# 	e. Network zone
# 		i. Environment Active Gate
# 	f. Create active gate once command available

# 	g. 
# 	h. 
# Download script using below command :
# wget -O Dynatrace-ActiveGate-Linux-x86-1.347.42.20261002-103138-petclinic.sh "https://efm55963.live.dynatrace.com/api/v1/deployment/installer/gateway/unix/latest?arch=x86" --header="Authorization: Api-Token <YOUR_DYNATRACE_API_TOKEN>"

# Verify script:
# wget https://ca.dynatrace.com/dt-root.cert.pem ; ( echo 'Content-Type: multipart/signed; protocol="application/x-pkcs7-signature"; micalg="sha-256"; boundary="--SIGNED-INSTALLER"'; echo ; echo ; echo '----SIGNED-INSTALLER' ; cat Dynatrace-ActiveGate-Linux-x86-1.347.42.20261002-103138-petclinic.sh ) | openssl cms -verify -CAfile dt-root.cert.pem > /dev/null

# Final install command:
# /bin/bash Dynatrace-ActiveGate-Linux-x86-1.347.42.20261002-103138-petclinic.sh --set-network-zone=petclinic --set-group=ag01