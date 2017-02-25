# Ansible 2 FTW
![](./bigpanda_logo.png)

---

### Why ansible 2 - faster, stronger, better
![](giphy.gif)

---

## "Finally" moments

----

### Task Blocks

logical grouping of tasks and even in-play error handling

```
tasks:
     - block:
              - apt: name={{ item }} state=installed
                with_items:
                    - bigpanda
                    - cowsay
                                                            
              - service: name=bigpanda state=started enabled=True
                when: ansible_distribution == 'CentOS'
        become: true
        become_user: root
        tag: bigpanda
```

----

### Error handling

```
 tasks:
   # Executes command
   - block:
         - debug: msg='RUN EL COMMAND!'
         - command: /bin/false
    # Executes if error
     rescue:
         - debug: msg='HO NO! thus i seeth an error!'
         - command: /bin/false
    # Execute always
     always:
         - debug: msg="i'm just here for the food"
```

----

### ansible container (upcoming meetup)

----

### Include with Loops

```
  - include: "{{item}}.yml"
    with_items:
      - this
      - is
      - sparta
```

----

### meta

New in 2.0 - flush_handlers, **refresh_inventory**, noop, clear_facts, clear_host_errors, **end_play**

---

## THE OBSTACLES

----

### root dir when running a command 

 Running a command from anywhere else than the root dir will keep the root dir as the executaion dir. 
 
 WAT

 *(relevant only to command)*

----

### vars in "inner" app gets priority

----

### deprecated syntax

* Bare variables in loops:
```
    command: echo item
     command: echo "{{item}}"
```
* Bare variables in 'args' and 'environment'
* Deprecation of 'args' with variables (not yet enforced)
* Tags as parameters to include
* Variables as include attributes. now should come as a 'vars' attribute to include
* Become instead of sudo (become_user w00tz)

---

## bigpanda's process 

---

* why not 2.0 and 2.1 - bug, hagai knows why
* why not straight go to 2.2 - hagai knows why
* etc
