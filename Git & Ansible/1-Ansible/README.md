
ansible-playbook site.yml -i inventory.ini -e "welcome_message='Hi from CLI'"

docker run -it  -u root -v ${PWD}:/code alpine/ansible bash 