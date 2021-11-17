# Azure Cloud Commands

#### Fresh Installation of Jumpbox
- `sudo apt update` - check to see what programs are being offered
- `sudo apt install docker.io` - installs docker
- `sudo systemctl status docker` - check to see if docker is running
- `sudo docker pull cyberxsecurity/ansible` - downloads a customized cyberxsecurity from the docker repository.
- `docker run -ti cyberxsecurity/ansible:latest bash` - starts the container up for the first time
- `sudo docker ps -a` - lists all the containers
- `sudo docker start <container-name-or-id>` - starts the container name or id
- `sudo docker attach <container-name-or-id>` - attaches yourself to the container starting the terminal

#### Ansible Playbook
Once you are inside the ansible container, we need to create `Ansible scripts` to help automate installation of software across multiple machines.
- We need to first change our username by going into the `ansible.cfg` file.
  - `nano /etc/ansible/ansible.cfg`
  - We need to search for the option called `remote_user`.
  - Hit `Ctrl+W` to use the `Where_is` command to find that specific option.
- Next, we need to edit the hosts file.
  - `nano /etc/ansible/hosts`
  - We will need specify the specific hosts `[webservers]` and its IP address.
    - Don't forget to add `ansible_python_interpreter=/usr/bin/python3` and the end of the IP address.
    - Test the connectivity with `ansible -m ping all`

#### Creating the ansible script
Create a blank file with nano or touch and open the file.
- We will create a file called `webservers.yml`. This script will be responsible of installing all the dependencies and requirements that are needed to create a webserver on our Web-1 and Web-2 VM.

```
---
- name: Config Web VM with Docker
  hosts: webservers
  become: true
  tasks:
  - name: docker.io
    apt:
      force_apt_get: yes
      update_cache: yes
      name: docker.io
      state: present

  - name: Install pip3
    apt:
      force_apt_get: yes
      name: python3-pip
      state: present

  - name: Install Docker python module
    pip:
      name: docker
      state: present

  - name: download and launch a docker web container
    docker_container:
      name: dvwa
      image: cyberxsecurity/dvwa
      state: started
      published_ports: 80:80

  - name: Enable docker service
    systemd:
      name: docker
      enabled: yes
```

- After copying this script, go ahead and run this script with:
  - `ansible-playbook <name-of-file>`
  - Results should show that IPs have changed which indicate successful installation.
