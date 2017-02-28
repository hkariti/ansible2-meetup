# Ansible 2 FTW
![](./bigpanda_logo.png)

Noa  Kurman & Hagai Kariti

---

# Who Are We?

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

### Include with Loops (aka dynamic include)

```
  - include: "{{item}}.yml"
    with_items:
      - this
      - is
      - sparta
```

----

### Better error messages (somewhat)

```
error while evaluating conditional: some_var == 1
```

vs

```
fatal: [localhost]: FAILED! => {"failed": true,
"msg": "The conditional check 'some_var == 1' failed.
The error was: error while evaluating conditional (some_var == 1):
'some_var' is undefined
...
```

----

### meta

Instead of:

```
- command: rm {{my_fact_cache_dir}}/{{ansible_hostname}}

- ec2_create: ...
  with_items: ...

- add_host: name=new_host groups=group1,group2,...
```

do:

```
- meta: clear_facts

- ec2_create: ...
  with_items: ...

- meta: refresh_inventory
```

----

### meta

As of 2.2 - flush_handlers, refresh_inventory, noop, clear_facts, clear_host_errors, end_play

Check out the meta module page in the docs for more info.

----

### ansible containers - upcoming meetup 
![](excited.gif)

---

## The Obstacles
![](mad.gif)

----

### Syntax changes

* Bare variables in loops, 'args' and 'environment':
```
    with_items: list # Deprecated
    with_items: "{{ list }}" # Current
```

* Become instead of sudo (become_user w00tz)

* Octal params need leading zero

----

### More Syntax changes

* Tags and variables in includes

```
    - include: file.yml tags=asd var=value #Wrong

    - inculde: file.yml
      tags: asd
      vars:
       var: value
```

* Many other small things (see porting guide in docs)
    - quoting, vars_prompt, callback plugins, and more

----

### root dir when running a command 

 Running the command module on the control server will keep the root dir (not playbook dir) as the execution dir. 

---

## BigPanda's Process 
![](panda2.gif)

----

### Easy!

* Go over the porting doc, and fix obvious things
    * `git grep -l | xargs sed` galore

* Use `ansible-lint` and `ansible-playbook --syntax-check`

* Use playbook tests (i.e. provision a new temp machine)

----

### Oh noes

* Tags and handlers don't work with includes

```
tasks:
    - template: something
      notify: restart
handlers:
    - include: ../code/reuse/is/good/handlers.yml
```

----

### No ohes

* Bug with multiple plays and unreachable hosts

```
- hosts: all
  tasks:
     - ping:

- hosts: reachable_server_1
  tasks:
    - debug: msg="I'm still alive!!"
```

----

### Wait what?

* Issues were spread over several bug reports
* A fix should've come soon(tm)
* Eventally it did

----

### Skip 2.0

![](dont.gif)

----

### Harder changes

* Be ready to debug odd things failing
    * Example: Small variable precedence changes in roles

* A few weeks of weird problems now and then
    * Weird usecases, rarely used scripts, etc

---

### Future releases and other goodies

* Ability to control what to print in loops
* Multiple vault passwords
* `listen` attribute, for generic handler names
* include_role
* Better vpc modules

---

# Questions?
