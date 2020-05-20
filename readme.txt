######################
#Reference
##########################

https://blog.marcosbarbero.com/centralized-authorization-jwt-spring-boot2/

https://github.com/marcosbarbero/spring-boot2-oauth2-jwt/tree/master/oauth2-jwt-server


########################
#To generate certs and keys
######################

$openssl genrsa -des3 -passout pass:password -out server.pass.key 2048
Generating RSA private key, 2048 bit long modulus (2 primes)
...........+++++
....+++++
e is 65537 (0x010001)


$ openssl rsa -passin pass:password -in server.pass.key -out server.key
writing RSA key


$ openssl req -new -key server.key -out server.csr
Can't load /home/esah2111/.rnd into RNG
140446898291136:error:2406F079:random number generator:RAND_load_file:Cannot open file:../crypto/rand/randfile.c:88:Filename=/home/esah2111/.rnd
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:IN
State or Province Name (full name) [Some-State]:
Locality Name (eg, city) []:
Organization Name (eg, company) [Internet Widgits Pty Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (e.g. server FQDN or YOUR name) []:localhost
Email Address []:

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:



$ cat v3.ext 
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = localhost



$ openssl x509 -req -sha256 -extfile v3.ext -days 365 -in server.csr -signkey server.key -out server.crt
Signature ok
subject=C = IN, ST = Some-State, O = Internet Widgits Pty Ltd, CN = localhost
Getting Private key



$ openssl pkcs12 -export -name servercert -in server.crt -inkey server.key -out myp12keystore.p12
Enter Export Password:
Verifying - Enter Export Password:



$ keytool -importkeystorore.jks -srckeystore myp12keystore.p12 -srcstoretype pkcs12 -alias servercert
Importing keystore myp12keystore.p12 to mykeystore.jks...
Enter destination keystore password:  
Re-enter new password: 
Enter source keystore password:  


$ keytool -list -rfc --keystore mykeystore.jks | openssl x509 -inform pem -pubkey -noout
Enter keystore password:  password
-----BEGIN PUBLIC KEY-----
MIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAv+wzWx0iPXOryJL+tqOo
0GxbA7HVftza9Ccb9Ny1rNButPRT2ysEsicS1g2AhKxs8EOBTVAGHkjH7UTvslmS
gHhqBZL3+3RF1K16cBsh4yaSZnYMlNSvignBh/XAXNcsH/as/GyGS9avP3h3Ihsu
Tw9Epw48xfsASMbQNlAOM+HH6ZyIntAdPnDDUfd2/whbHTtCEFlAT3DAEFOLDQEz
X/n5xwXzlc+QnCxqphRcO4Zc+JVZ8Onzy4Kp40nC3ul+/BEpTyg4hmws6h3e/3+f
lkfur6+6EGNhvHGlfMTvQ9cDTnnuqHaNyaVkoDUJck/gwZgdvgTFTYwgsgAtW4ld
hQIDAQAB
-----END PUBLIC KEY-----



#######################
#Endpoints
######################

curl --location --request POST 'localhost:9010/oauth/token' \
--header 'Authorization: Basic Y2xpZW50SWQ6c2VjcmV0' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'grant_type=password' \
--data-urlencode 'username=user' \
--data-urlencode 'password=pass'



POST /oauth/token HTTP/1.1
Host: localhost:9010
Authorization: Basic Y2xpZW50SWQ6c2VjcmV0
Content-Type: application/x-www-form-urlencoded
grant_type=password&username=user&password=pass


POST call to localhost:9010/oauth/token to generate the token
Basic Auth with clientId:secret
url-Body:
grant_type=password
username=user
password=pass
scope=<optional>
