indeponent
==========

- command: /usr/bin/create-database.sh creates=/path/to/databaseb

```yml
- name: Put docker package on hold
  shell: >
         apt-mark showholds | grep -q docker-ce
         && echo -n HOLDED
         || apt-mark hold docker-ce
  register: docker_hold
  changed_when: docker_hold.stdout != 'HOLDED'
  ```
  
  ``````