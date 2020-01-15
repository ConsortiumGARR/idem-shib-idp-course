# Corso IDEM su Shibboleth Identity Provider del 22 Gennaio 2020

## Docenti
* Giuseppe De Marco (giuseppe.demarco@unical.it)
* Marco Malavolti (marco.malavolti@garr.it)
* Maurizio Festi (qui-email)

## Playbook setup

````
sudo apt update
sudo apt upgrade
sudo apt install python3-dev python3-setuptools python3-pip easy-rsa expect-dev git
sudo pip3 install ansible
git clone https://github.com/ConsortiumGARR/ansible-slapd-eduperson2016.git
cd ansible-slapd-eduperson2016/
sudo ansible-playbook -i "localhost," -c local playbook.yml
cd ..
apt install -y python3-pip python-dev libffi-dev libssl-dev libxml2-dev libxslt1-dev libjpeg-dev zlib1g-dev ldap-utils
git clone https://github.com/ConsortiumGARR/Ansible-Shibboleth-IDP-SP-Debian.git
cd Ansible-Shibboleth-IDP-SP-Debian/
ansible-playbook -i "localhost," -c local playbook.yml
````

Test 
````
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-amazon-corretto/jre
/opt/shibboleth-idp/bin/aacli.sh   -n luigi -r  https://coco.release-check.edugain.org/shibboleth --saml2
````

## Presentazioni

* Prima Parte
* Seconda Parte
* Terza Parte
* Quarta Parte
