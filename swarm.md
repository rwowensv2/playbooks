# Ansible Swarm Facts
Inspect is deprecated, use `docker_swarm_info`.  Below is an example of extracting the worker key for join.  


```
  tasks:
    - name: inspect
      docker_swarm_info:
        nodes: yes
      register: result
      # OLD Way
      # "{{ hostvars['ansible_hostname']['token']['swarm_facts']['JoinTokens']['Worker'] }}"
      when: role is defined and role == "leader"
    - debug:
        var: result.swarm_facts.JoinTokens.Worker
```

# reminder commands
```
docker node inspect  
docker node update
```
