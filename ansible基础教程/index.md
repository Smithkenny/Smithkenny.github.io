# ansible基础教程


## ansible的配置文件

### 全局配置文件

ansible默认的全局配置文件是`/etc/ansible/ansible.cfg`，可认为是全局配置的入口。

Ansible支持4种方式指定配置文件，它们的解析顺序从上到下

- ANSIBLE_CFG 环境变量指定的配置文件
- ansible.cfg 当前目录下的ansible.cfg
- ~/.ansible.cfg 家目录下的.ansible.cfg
- `/etc/ansible/ansible.cfg` 默认全局配置文件

修改默认配置文件/etc/ansible/ansible.cfg：

```bash
$ cat /etc/ansible/ansible.cfg | grep -vE "^$|^#"
[defaults]
inventory      = /etc/ansible/hosts			# inventory文件：ansible管理的主机清单
library        = /usr/share/my_modules/
forks          = 5					# ansbile的并发连接数
sudo_user      = root
remote_port    = 22
host_key_checking = False
timeout = 10
log_path = /var/log/ansible.log
```

文件中其它的可配置项：

```bash
stdout_callback = debug        # 可将输出变得人性化；默认输出会挤在一行，配置后会换行输出；
```

### inventory主机文件

`inventory`文件默认路径是`/etc/ansible/hosts`，在这里配置目标主机，ansible便可以对其进行控制。

在`/etc/ansible/hosts`文件里配置node主机名或ip：

```bash
$ cat hosts
[default]				#	主机分组
node1					#	172.18.0.101
node2					#	172.18.0.102
```

可以通过`/etc/ansible/ansible.cfg`文件修改inventory的默认路径：`inventory = /etc/ansible/hosts`。如果将该配置指定为目录，便可以使用多个inventory文件来管理节点，一般很少动这个。

通常，不会修改默认的路径。如果有自定义的inventory文件，可以直接在ansible命令行中使用`-i`选项指定:

```bash
# ansible -i /tmp/my_inventory.ini ...
# ansible-playbook -i /tmp/my_inventory.ini ...
```

### 查看inventory

列出ansiblek可管理的所有主机

```bash
$ ansible-inventory --graph all			# 指定文件：ansible-inventory -i /etc/ansible/hosts --graph all
@all:						# all是默认的主机组，包含所有主机
  |--@default:
  |  |--node1
  |  |--node2
  |--@ungrouped:
```

## 运行ansible命令

完成上述的基本配置后，即可以开始使用ansible来批量管理主机了，这里的管理方式为命令行方式（又称为`Ad-hoc`方式）。

Ad-hoc方式运行ansible的命令格式：`ansible 主机组/主机 -m 模块 -a 参数`

选项解析：

```bash
-m:	指定调用的模块
-a:	向模块传递的参数，模块不需要则可省略;参数需要使用引号包围
```

此外ansible命令还可以带上其它选项：

```bash
-i:	指定本次的inventory路径，指定该参数则后面不加主机组/主机
-e:	设置变量，格式为'var1="aaa" var="bbb"'
-v/vv/vvv:	命令输出的打印级别
```

ansible的批量管理功能依靠各个模块来完成，ansible提供了几千个模块（其中ansible团队自己维护大约100多个核心模块），每个模块完成各自的作用。

下面用ping模块和debug模块来演示一下ansible的基础功能。

### ping模块

`ping模块`是ansible最基础模块之一，可用于检测远程主机是否在线。

命令：`ansible 主机组/主机 -m ping`

返回值：changed、ping

命令：`ansible all -m ping`

```bash
[root@master ansible]$ ansible all -m ping
node2 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
node1 | SUCCESS => {
    "ansible_facts": {
        "discovered_interpreter_python": "/usr/bin/python"
    },
    "changed": false,
    "ping": "pong"
}
```

### debug模块

官网说明：https://docs.ansible.com/ansible/latest/collections/ansible/builtin/debug_module.html

`debug模块`用于输出或调试一些变量或数据。该模块共有三个参数msg、var、verbosity。

```none
msg：打印配置的信息
var：打印变量值
verbosity：运行级别，设置为3，则-vvv或更高才会打印输出
```

**命令：**

```
ansible all -m debug -a 'msg="hello world"'
ansible all -e 'str="hello world"' -m debug -a 'var=str'
ansible all -v -m debug -a 'msg="hello world" verbosity=1'
```

**示例：**

```bash
[root@master ansible]$ ansible all -m debug -a 'msg="hello world"'
node1 | SUCCESS => {
    "msg": "hello world"
}
node2 | SUCCESS => {
    "msg": "hello world"
}
[root@master ansible]$ ansible all -e 'str="hello world"' -m debug -a 'var=str'
node1 | SUCCESS => {
    "str": "hello world"
}
node2 | SUCCESS => {
    "str": "hello world"
}

[root@master ansible]$ ansible all -m debug -a 'msg="hello world" verbosity=1'
node1 | SKIPPED
node2 | SKIPPED
[root@master ansible]$ ansible all -v -m debug -a 'msg="hello world" verbosity=1'
Using /etc/ansible/ansible.cfg as config file
node1 | SUCCESS => {
    "msg": "hello world"
}
node2 | SUCCESS => {
    "msg": "hello world"
}
```

## playbook

`playbook`是一个`yaml`格式的文件，由一个或多个`play`按顺序列表的方式组成。每个`play`运行一个或多个`task`，每个`task`调用一个模块`module`。

### playbook、play、task的关系

- playbook中可以定义一个或多个play
- 每个play中可以定义一个或多个task
  - 其中还可以定义两类特殊的task：pre_tasks和post_tasks
  - pre_tasks表示执行执行普通任务之前执行的任务列表
  - post_tasks表示普通任务执行完之后执行的任务列表
- 每个play都需要通过hosts指令指定要执行该play的目标主机
- 每个play都可以设置一些该play的环境控制行为，比如定义play级别的变量

### 编写一个playbook并执行

`playbook`使用`yaml`语法格式组织各种`play`和`task`规则。

下面使用ping模块和debug模块编写一个playbook文件如下：

```yaml
# cat test.yaml
---
- name: play1						# play的名称，非必须
  hosts: all						# 指定目标主机
  gather_facts: false					# 收集目标主机信息，默认值true，非必须
  tasks:						# tasks声明任务列表
    - name: task1					# task任务名称，非必须
      ping:						# 模块
        data: "pong task1"				# 模块参数
    - name: task2
      ping:
        data: "pong task2"
- name: play2
  hosts: all
  gather_facts: false
  tasks:
    - name: task1
      debug:
        msg: "hello task1 in play2"
    - name: task2
      debug:
        msg: "hello task2 in play2"
```

> 注意所有的`-`和`:`符号后面均需要接一个空格

执行playbook的命令是`ansible-playbook test.yaml`：

该命令同样支持像ansile命令一样的多个选项，如`-e`、`-i`、`-v`等

```bash
$ ansible-playbook -v test.yaml			
Using /etc/ansible/ansible.cfg as config file

PLAY [play1] **********************************************************************************************************************

TASK [task1] **********************************************************************************************************************
ok: [node2] => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "changed": false, "ping": "pong task1"}
ok: [node1] => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "changed": false, "ping": "pong task1"}

TASK [task2] **********************************************************************************************************************
ok: [node1] => {"changed": false, "ping": "pong task2"}
ok: [node2] => {"changed": false, "ping": "pong task2"}

PLAY [play2] **********************************************************************************************************************

TASK [task1] **********************************************************************************************************************
ok: [node1] => {
    "msg": "hello task1 in play2"
}
ok: [node2] => {
    "msg": "hello task1 in play2"
}

TASK [task2] **********************************************************************************************************************
ok: [node1] => {
    "msg": "hello task2 in play2"
}
ok: [node2] => {
    "msg": "hello task2 in play2"
}

PLAY RECAP ************************************************************************************************************************
node1                      : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=4    changed=0    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### playbook文件各配置指令及含义

`yaml`文件中，使用`-`表示一个列表元素，多个`key：value`表示一个字典。

每个`playbook`使用列表来组织多个`play`，`play`内同样使用列表来组织多个`task`；play和task自身则采用字典的方式组织，即多个键值对。

在playbook顶层使用`- xxx:`表示这是一个play；每个play中必须包含`hosts`和`tasks`指令。

`hosts`指令用来指定要执行该play的目标主机，可以是主机名、主机组或者其它多种方式。

`tasks`指令用来指定该play中包含的任务列表，每个任务使用`- xxx:`方式表示。

`name`指令用来设置play和task的名称，值具有唯一性。

`gather_facts`指令用来收集目标主机的信息，由setup模块提供。默认情况下，每个play都先执行这个特殊任务，收集完信息才开始其它任务。如果后续任务中用不到该信息，则可以禁止掉该任务，提升效率。

### 向模块传递参数

yaml中，向模块传递参数的方式总结为字符串和数组两种方式。

还是以debug模块为例。

数组方式如上面test.yaml文件里的传参，即`key: value`的形式：

```yaml
......
- name: task1
      debug:
        msg: "hello task1 in play2"
        verbosity=1
```

字符串方式，由于yaml的语法规则（字符串换行将自动转换为空格），又有不同的书写形式：

```yaml
---
- name: debug
  hosts: all
  gather_facts: false
  tasks:
    - name: task1
      debug:
        msg="hello task1" verbosity=1			# 参数写成一行
    - name: task2	
      debug:
        msg="hello task2"				# 参数写成多行
        verbosity=1
    - name: task3
      debug: |		# 竖线|，将保留字符串的换行符，否则将自动转换成空格，在一些模块中很有用，如shell
        msg="hello task3"						
        verbosity=1
    - name: task4
      debug: >						# 符号>，效果和直接写成多行一样
        msg="hello task4"
        verbosity=1
```

还可以直接使用指令args指明参数：

```yaml
......
- name: task1
      debug:
      args:
        msg: "hello task1 in play2"
```

### 默认的任务执行策略

/etc/ansible/ansible.cfg文件的`forks`配置，决定ansible执行任务的并发连接数。

假如forks配置为5，那么ansible第一次将同时连接5个node节点执行任务。其中若有节点提前执行完任务， 则ansible会新建一个新进程，来连接下一个节点执行任务。

forks是保证最多有N个节点同时执行任务。

## 常见模块

### shell模块

**说明：**

`shell`模块接收shell命令，命令后可跟空格分割的参数列；

必须传入自由格式的命令，或者cmd参数；

它十分类似command模块，但是它在远程主机上通过shell（比如/bin/bash）来运行命令；

shell模块相比command模块，支持解析特殊符号`<`、`>`、`|`、`;`、`&`等。

**参数：**

| Parameter                     | Choices/Defaults                               | 说明                                                         |
| ----------------------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| **`chdir`** path              |                                                | 运行脚本前，切换到相关目录                                   |
| **`cmd`** string              |                                                | 需要运行的命令，后跟可选的参数                               |
| **`creates`** path            |                                                | 若一个文件或目录存在，则跳过该步骤                           |
| **`removes`** path            |                                                | 若一个文件或目录不存在，则跳过该步骤                         |
| **`executable`** path         |                                                | 改变执行命令的解释器，如/bin/bash、/usr/bin/expect、/usr/bin/python；绝对路径 |
| **free_form** string          |                                                | 自由格式的命令（即命令字符串，没有相关参数，直接写在shell模块后面即可） |
| **stdin** string              |                                                | Set the stdin of the command directly to the specified value. |
| **stdin_add_newline** boolean | **Choices:**<br />no<br />**yes** ←（default） | Whether to append a newline to stdin data.                   |
| **warn** boolean              | **Choices:**<br />no<br />**yes** ←(default)   | Whether to enable task warnings.                             |

**示例：**

```yaml
---
- name: shell
  hosts: all
  gather_facts: no
  tasks:
    - name: task1
      shell:
        hostname
    - name: task2
      shell:
        cmd: date +"%F %T"
    - name: task3
      shell: 
        cmd: pwd
        chdir: /etc/sysconfig
    - name: task4
      shell:
        cmd: ls /tmp
        creates: /tmp
    - name: task5
      shell:
        cmd: print('hello world')
        executable: /usr/bin/python
```

注意chdir参数只支持下面的方式：

```yml
- name: task3
      shell: 
        cmd: pwd
        chdir: /etc/sysconfig
- name: task3
      shell: pwd				# free_from格式需要采用args参数，显式指定chdir参数
      args:
        chdir: /etc/sysconfig
# 下面的方式将发生错误
- name: task3
      shell: pwd
        chdir: /etc/sysconfig
```

**Ad-hoc方式：**

```bash
$ ansible all -m shell -a "ls -l | wc -l"
$ ansible all -v -m shell -a "ls chdir=/tmp "
```

> https://docs.ansible.com/ansible/latest/collections/ansible/builtin/shell_module.html#ansible-collections-ansible-builtin-shell-module

### script模块

**说明：**

`script`模块接受一个脚本名称，后面可跟空格分割的参数列；

支持自由格式的命令，或者cmd参数；

将本地脚本传输到远程主机上执行；

在远程主机上使用shell环境执行脚本；

该模块不需要python，类似raw模块。

**参数：**

| Parameter          | Choices/Defaults | 说明                                 |
| ------------------ | ---------------- | ------------------------------------ |
| **`chdir`** path   |                  | 运行脚本前，切换到远程主机的相关目录 |
| **`cmd`** string   |                  | 需要运行的脚本路径，后跟可选的参数   |
| **`creates`** path |                  | 若一个文件或目录存在，则跳过该步骤   |
| **`removes`** path |                  | 若一个文件或目录不存在，则跳过该步骤 |

**示例：**

`sctipt-simple.yml`

```yml
---
- name: script
  hosts: web
  tasks: 
    - name: list files
      script: /etc/ansible/test-yml/trans_exec.sh
```

`trans_exec.sh`

```bash
#!/bin/bash
date && hostname 
```

**Ad-hoc：**

```bash
$ ansible all -m script -a "/tmp/hello.sh world"
$ ansible all -m script -a "/tmp/hello.sh world creates=/tmp"
```

https://docs.ansible.com/ansible/latest/collections/ansible/builtin/script_module.html#ansible-collections-ansible-builtin-script-module

### hostname模块

**说明：**

设置系统的主机名

**参数：**

| Parameter                    | Choices/Defaults | 说明       |
| ---------------------------- | ---------------- | ---------- |
| **`name`** string / required |                  | 设置主机名 |

**示例：**

```yaml
- name: Set a hostname
  ansible.builtin.hostname:
    name: web01

- name: Set a hostname specifying strategy
  ansible.builtin.hostname:
    name: web01
    use: systemd
```

> https://docs.ansible.com/ansible/latest/collections/ansible/builtin/hostname_module.html

## 在playbook中设置变量

`vars`指令可在play或task中设置变量，可以设置一个或多个。可以采用字典或列表的形式定义变量。

### **字典变量的定义和引用**

**定义：**

```yaml
vars:
    foo1:
      a: hello
      b: world
    foo2:
      a: aaa
      b: bbb
```

**引用：**

使用点号或方括号，在yaml文件使用jinja2语法引用，需要加单双引号，否则解析yaml的时候将报错

```yml
- name: task1
  debug:
    msg: "{{ foo1.a }} {{ foo1['b'] }}"		# 注意{{}}是jinja2的语法，在yaml文件中需要使用引号引起来，单双引号都行
- name: task2
  debug:
    var: foo1.a,foo1['b']			# debug模块的var参数，多个值使用逗号分隔，且无需花括号，前后加不加引号均可
- name: task3
  debug:
    msg: '{{ foo2.a }} {{ foo2.b }}'
```

**示例：**

```yaml
---
- name: vars play
  hosts: all
  gather_facts: no
  vars:
    foo1:
      a: hello
      b: world
    foo2:
      a: aaa
      b: bbb
  tasks:
    - name: task1
      debug:
        msg: "{{ foo1.a }} {{ foo1['b'] }}"				
    - name: task2
      debug:
        var: foo1.a,foo1['b']
    - name: task3
      debug:
        msg: '{{ foo2.a }} {{ foo2.b }}'
```

### **列表变量的定义和引用**

**定义：**

```yml
vars:
    foo:
      - a: hello
        b: world
      - a: aaa
        b: bbb
```

**引用：**

```yml
- name: task1
  debug:
    msg: "{{ foo[0].a }} {{ foo[0].b }}"
- name: task2
  debug:
    msg: "{{foo[1]['a']}} {{foo[1]['b']}}"
```

> 引用变量时，使用点号比较方便，但如果变量名本身带点，则尽量选择方括号的方式。

## when指令进行条件判断

`when`指令是ansible提供的唯一一个通用条件指令。`when`指令后的变量引用不需要双花括号，当when指令的值为true时，执行任务。

**yaml文件如下：**

```yml
---
- name: when
  hosts: all
  gather_facts: no
  vars:
    foo: test
  tasks:
    - name: task1
      when: foo == "test"			# when指令的变量可直接引用
      debug:
        msg: "hello"
    - name: task2
      when: foo == "dev"
      debug:
        msg: "world"
```

**示例：**

从输出中可看出，任务task2由于条件不满足自动跳过

```bash
[root@master ansible]# ansible-playbook -v when.yaml
Using /etc/ansible/ansible.cfg as config file

PLAY [when] ***********************************************************************************************************************

TASK [task1] **********************************************************************************************************************
ok: [node1] => {
    "msg": "hello"
}
ok: [node2] => {
    "msg": "hello"
}

TASK [task2] **********************************************************************************************************************
skipping: [node1] => {}
skipping: [node2] => {}

PLAY RECAP ************************************************************************************************************************
node1                      : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
node2                      : ok=1    changed=0    unreachable=0    failed=0    skipped=1    rescued=0    ignored=0
```

**多个判断条件：**

```yml
- name: task3
  when: foo == "dev" or foo == "test"			# 逻辑或
  debug:
    msg: "hello world"
- name: task4
  when: (foo == "dev") or (foo == "test")		# 支持括号括起来
  debug:
    msg: "hello world"
- name: task5
  when: (foo == "dev") and (foo == "test")		# 逻辑与
  debug:
    msg: "hello world"
- name: task6
  when:							
    - foo == "dev"					# 逻辑与的另一种组织方式
    - foo == "test"
  debug:
    msg: "hello world"
- name: task7
  when: foo != "dev"					# 逻辑否
  debug:
    msg: "hello world"
```

## loop循环结构

loop指令中的各项元素将以item变量名进行迭代。

### 直接迭代列表

**迭代简单列表：**

列表元素为字符串

```yml
- name: task1
  shell: "{{ item }}"
  loop:
    - hostname
    - "uptime -p"
```

示例：

```bash
[root@master ansible]# cat loop.yaml
---
- name: loop
  hosts: all
  gather_facts: no
  tasks:
    - name: task1
      shell: "{{ item }}"
      loop:
        - hostname
        - "uptime -p"
[root@master ansible]# ansible-playbook -v loop.yaml
Using /etc/ansible/ansible.cfg as config file

PLAY [loop] ***********************************************************************************************************************

TASK [task1] **********************************************************************************************************************
changed: [node2] => (item=hostname) => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "ansible_loop_var": "item", "changed": true, "cmd": "hostname", "delta": "0:00:00.020436", "end": "2022-01-09 15:05:17.126971", "item": "hostname", "rc": 0, "start": "2022-01-09 15:05:17.106535", "stderr": "", "stderr_lines": [], "stdout": "node2", "stdout_lines": ["node2"]}
changed: [node1] => (item=hostname) => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "ansible_loop_var": "item", "changed": true, "cmd": "hostname", "delta": "0:00:00.361005", "end": "2022-01-09 15:05:17.409020", "item": "hostname", "rc": 0, "start": "2022-01-09 15:05:17.048015", "stderr": "", "stderr_lines": [], "stdout": "node1", "stdout_lines": ["node1"]}
changed: [node2] => (item=uptime -p) => {"ansible_loop_var": "item", "changed": true, "cmd": "uptime -p", "delta": "0:00:00.006628", "end": "2022-01-09 15:05:17.610052", "item": "uptime -p", "rc": 0, "start": "2022-01-09 15:05:17.603424", "stderr": "", "stderr_lines": [], "stdout": "up 3 weeks, 2 days, 19 hours, 42 minutes", "stdout_lines": ["up 3 weeks, 2 days, 19 hours, 42 minutes"]}
changed: [node1] => (item=uptime -p) => {"ansible_loop_var": "item", "changed": true, "cmd": "uptime -p", "delta": "0:00:00.074039", "end": "2022-01-09 15:05:18.006283", "item": "uptime -p", "rc": 0, "start": "2022-01-09 15:05:17.932244", "stderr": "", "stderr_lines": [], "stdout": "up 3 weeks, 2 days, 19 hours, 42 minutes", "stdout_lines": ["up 3 weeks, 2 days, 19 hours, 42 minutes"]}

PLAY RECAP ************************************************************************************************************************
node1                      : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

**迭代hash哈希列表：**

列表元素为字典

```yml
- name: task1
  shell:
    cmd: "{{ item.cmd }}"
    creates: "{{ item['condition'] }}"				# 两种引用方式均可
  loop:
    - { cmd: 'hostname', condition: '/tmp' }			# 这个循环将被跳过
    - { cmd: 'uptime', condition: '/aaa' }					
```

示例：

```bash
[root@master ansible]# cat loop.yaml
---
- name: loop
  hosts: all
  gather_facts: no
  tasks:
    - name: task1
      shell:
        cmd: "{{ item.cmd }}"
        creates: "{{ item.condition }}"
      loop:
        - { cmd: 'hostname', condition: '/tmp' }		# 这个循环将被跳过，["skipped, since /tmp exists"]，任务没有跳过
        - { cmd: 'uptime', condition: '/aaa' }				
[root@master ansible]#
[root@master ansible]# ansible-playbook -v loop.yaml
Using /etc/ansible/ansible.cfg as config file

PLAY [loop] ***********************************************************************************************************************

TASK [task1] **********************************************************************************************************************
ok: [node2] => (item={u'cmd': u'hostname', u'condition': u'/tmp'}) => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "ansible_loop_var": "item", "changed": false, "cmd": "hostname", "item": {"cmd": "hostname", "condition": "/tmp"}, "rc": 0, "stdout": "skipped, since /tmp exists", "stdout_lines": ["skipped, since /tmp exists"]}
changed: [node2] => (item={u'cmd': u'uptime', u'condition': u'/aaa'}) => {"ansible_loop_var": "item", "changed": true, "cmd": "uptime", "delta": "0:00:00.024675", "end": "2022-01-09 15:21:23.286758", "item": {"cmd": "uptime", "condition": "/aaa"}, "rc": 0, "start": "2022-01-09 15:21:23.262083", "stderr": "", "stderr_lines": [], "stdout": " 15:21:23 up 23 days, 19:58,  2 users,  load average: 0.13, 0.14, 0.20", "stdout_lines": [" 15:21:23 up 23 days, 19:58,  2 users,  load average: 0.13, 0.14, 0.20"]}
ok: [node1] => (item={u'cmd': u'hostname', u'condition': u'/tmp'}) => {"ansible_facts": {"discovered_interpreter_python": "/usr/bin/python"}, "ansible_loop_var": "item", "changed": false, "cmd": "hostname", "item": {"cmd": "hostname", "condition": "/tmp"}, "rc": 0, "stdout": "skipped, since /tmp exists", "stdout_lines": ["skipped, since /tmp exists"]}
changed: [node1] => (item={u'cmd': u'uptime', u'condition': u'/aaa'}) => {"ansible_loop_var": "item", "changed": true, "cmd": "uptime", "delta": "0:00:00.386491", "end": "2022-01-09 15:21:30.029184", "item": {"cmd": "uptime", "condition": "/aaa"}, "rc": 0, "start": "2022-01-09 15:21:29.642693", "stderr": "", "stderr_lines": [], "stdout": " 15:21:30 up 23 days, 19:58,  2 users,  load average: 0.90, 0.51, 0.35", "stdout_lines": [" 15:21:30 up 23 days, 19:58,  2 users,  load average: 0.90, 0.51, 0.35"]}

PLAY RECAP ************************************************************************************************************************
node1                      : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

### 迭代字典

`loop`指令无法直接迭代字典，需要使用过滤器`dict2items`进行转换，如下：

```yml
- name: Using dict2items
  ansible.builtin.debug:
    msg: "{{ item.key }} - {{ item.value }}"
  loop: "{{ tag_data | dict2items }}"
  vars:
    tag_data:
      Environment: dev
      Application: payment
```

上例中，通过迭代tag_data来打印它的key和value。

> `when`指令和`loop`指令同时使用时，先进行循环，再在每个循环中进行条件判断。

## notify和handlers

ansible中的一个重要概念`changed`，它表示目标状态是否发生改变，即本次任务是否执行、执行后是否影响结果。如果changed=1，则表示目标状态发生改变；如果changed=0，则表示目标状态未发生改变，或者任务没有执行。

ansible若监视到`changed=1`，就会触发`notify`指令所定义的`handler`。handler，也是一个task，只是定义在`handlers`中，需要notify来触发执行。

handlers的使用与tasks使用一样，**notify和hanlders中任务名称必须一样**。

**当一个play中所有任务都执行完成后，handler才会执行。**好处是可以多次触发notify，但最后只执行一次对应的handler。

示例：

```yml
---
- name: notify and handlers
  hosts: all
  gather_facts: no
  tasks:
    - name: task1
      shell: uptime
      notify: hello
  handlers:
    - name: hello
      debug:
        msg: "hello world"
```

执行结果：

```bash
[root@master ansible]# ansible-playbook notify.yaml

PLAY [notify and handlers] ********************************************************************************************************

TASK [task1] **********************************************************************************************************************
changed: [node2]
changed: [node1]

RUNNING HANDLER [hello] ***********************************************************************************************************
ok: [node2] => {}

MSG:

hello world
ok: [node1] => {}

MSG:

hello world

PLAY RECAP ************************************************************************************************************************
node1                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
node2                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## 组织playbook

将所有play全部写在一个yaml文件中，固然可行，但是可读性、维护性太差。

比较好的做法是，将同类任务的play放在一个文件中。多个任务，则写成多个文件，最后使用一个入口文件来引用这些任务文件。

假设入口文件名为main.yaml，在该文件中使用`import_playbook`指令引用其它playbook：

```yml
---
- import_playbook: "init_server/aaa.yaml"
- import_playbook: "init_server/bbb.yaml"
- import_playbook: "init_server/ccc.yaml"
- import_playbook: "init_server/ddd.yaml"
```

执行方式不变：`ansible-playbook main.yaml`

## 组织各类内容：task、handler、变量

> 上面介绍了使用playbook指令来引入多个playbook文件，从而提高可读性和维护性。
>
> 其实，ansible还提供了更加规范的方式，来组织更多的内容，即`role`和`colllection`，collection这里暂不涉及。

ansible可组织的内容包括：

- `playbook`
- `task`
- `variable`
- `handler`（handler也是一个task，只是编写在handlers内部）
- `role`

可组织的意思是说，可将相同内容定义在同一个文件中，然后使用相关指令来引入指定文件内容。

- 引入`palybook`：`import_playbook`
- 引入`task`或`handler`：`import_tasks`、`include_tasks`
- 引入`variable`：`vars_files`、`include_vars`
- 引入`role`：`import_role`、`include_role`、`roles`

import和include两种引入方式有所区别，前者为`静态加载`（在playbook解析的阶段，内容将写入到引入的位置），后者为`动态加载`（解析阶段不引入，而是在执行阶段才引入）。

### 组织tasks

可将task单独写在一个文件中，然后在play里使用`import_tasks`、`include_tasks`模块引入。

**示例：**

编写一个tasks文件tasks.yaml：

```yml
---
- name: task1
  debug:
    msg: "hello"
- name: task2
  debug:
    msg: "world"
```

在playbook中引入：

```yml
---
- name: play1
  hosts: node1
  gather_facts: no
  tasks:
    - name: task1 & task2
      import_tasks: tasks.yaml		# include_tasks模块也行
```

**在循环中引入tasks文件，必须使用include_tasks指令**

```yml
---
- name: play1
  hosts: node1
  gather_facts: no
  tasks:
    - name: loop tasks
      include_tasks: tasks.yaml
      loop:
        - one
        - two
```

### 组织handlers

一般情况下playbook的handlers如下，在task中使用handler的任务名来触发

```yml
---
- name: play1
  hosts: node1
  gather_facts: no
  tasks:
    - name: task1
      shell: uptime
      notify: h1
    - name: task2
      shell: date
      notify: h2
  handlers:
    - name: h1
      debug:
        msg: "run h1"
    - name: h2
      debug:
        msg: "run h2"
```

可将handler单独编写在一个文件中，如下：

```bash
$ cat handler1.yaml
---
- name: handler1
  debug:
    msg: "run handler1"
$ cat handler2.yaml
---
- name: handler2
  debug:
    msg: "run handler2"
```

然后在playbook中使用`import_tasks`或`include_tasks`指令来引入，同时在`notify`中修改对应的`handler`名：

```yml
---
- name: play1
  hosts: node1
  gather_facts: no
  tasks:
    - name: task1
      shell: uptime
      notify: handler1				#	handler1为静态引入，notify使用handler名
    - name: task2
      shell: date
      notify: h2				#	handler2为动态引入，notify使用引入的任务名
  handlers:
    - name: h1
      import_tasks: handler1.yaml		#	import_tasks静态引入
    - name: h2
      include_tasks: handler2.yaml		#	include_tasks动态引入
```

### 组织变量

前面已经介绍了如何在playbook中使用`var`指令直接设置变量，除了这个方法，ansible还支持将变量单独放在一个文件中，然后在play中使用`vars_files`指令或`include_vars`模块来引入该变量文件。也可以在命令行中，使用`-e`选项（--extra_vars）来设置变量或引入变量文件。

`vars_files`是`play`级别的指令，用于play中，在playbook解析阶段引入变量文件；

`include_vars`是任务模块（类似模块一样），用在tasks中定义一个引入变量的任务，只有该任务执行之后，才会创建变量；

`-e`选项在命令行中，全局有效；

#### vars_files示例

变量文件varfile.yaml，变量的定义一样，使用yaml或json格式，可采用字典或列表的形式。

```yml
---
foo:
  a: hello
  b: world
```

playbook文件如下，使用vars_files指令来引入：

```yml
---
- name: play1
  hosts: node1
  gather_facts: no
  vars_files:
     - varfile.yaml				#	多个变量文件，使用列表形式即可
  tasks:
    - name: task1
      debug:
        msg: "{{foo.a}} {{foo.b}}"
```

#### include_vars模块

include_vars是模块提供的功能，它是一个手动创建的任务，和shell、debug等模块一样。所以只有当任务执行完后，相关变量才会创建。

下面介绍几个用法。

引入一个文件：

```yaml
---
- name: play1
  hosts: node1
  gather_facts: no
  vars_files:
     - varfile.yaml
  tasks:
    - name: task1
      include_vars: varfile.yaml		# 引入之后，可在后续的任务中使用
    - name: task2
      debug:
        msg: "{{foo.a}} {{foo.b}}"
```

引入多个文件，可采用循环：

```yml
- name: task1
  include_vars: "{{ item }}"
  loop:
  	- varfile1.yaml
  	- varfile2.yaml
```

还可以引入目录，使用条件和其它参数控制引入的变量文件。这里不展开了，该模块有很多参数和用法，具体可参考官网：

> https://docs.ansible.com/ansible/latest/collections/ansible/builtin/include_vars_module.html#ansible-collections-ansible-builtin-include-vars-module

#### -e选项

ansible-playbook命令的-e选项或--extra-vars选项也可以用来定义变量或引入变量文件：

```bash
# 定义单个变量
$ ansible-playbook -e 'var1="value1"' xxx.yml

# 定义多个变量
$ ansible-playbook -e 'var1="value1" var2="value2"' xxx.yml

# 引入单个变量文件
$ ansible-playbook -e '@varfile1.yml' xxx.yml

# 引入多个变量文件
$ ansible-playbook -e '@varfile1.yml' -e '@varfile2.yml' xxx.yml
```

## 使用role

上面将各类内容放在单独的文件中，然后使用相关指令或模块将其引入。ansible中，有一种更为规范的组织方式，即`role`。

使用`role`，即可无需手动使用这些指令或模块了。按照role指定的文件或目录存放对应的内容，ansible就会自动引入。

### role的文件结构

`ansible-galaxy init role1`命令，可以快速创建一个`role`框架。

```bash
$ cd /etc/ansible/roles
$ ansible-galaxy init role01
$ tree role01
role01
├── defaults
│   └── main.yml
├── files		    # 外部文件，放入此处的文件，在role的各种任务中直接无需使用全路径
├── handlers
│   └── main.yml	    # 存放handler
├── meta
│   └── main.yml	    # 该role依赖的先行role。定义在此处的role将在该role运行前执行
├── README.md
├── tasks
│   └── main.yml	    # 存放任务
├── templates		    # 模板文件，放入此处的文件，在role的各种任务中无需使用全路径		
├── tests
│   ├── inventory
│   └── test.yml
└── vars
    └── main.yml	    # 存放变量
```

在相应目录及文件下编写对应内容，然后还需要提供一个入口playbook文件。在入口playbook文件中，使用`import_roles`、`include_role`、`roles`指令来引入role。最后使用ansible-playbook命令执行入口文件，即可执行定义在role中的各种任务了。

**enter.yml文件如下：**

```yml
---
- name: enter for all roles
  hosts: node1
  gather_facts: no
  roles:
    - role01					# 多个role可使用列表一起列出
```

### 编写一个role

#### 定义role的变量

`etc/ansible/roles/role01/vars/main.yml`文件是role定义变量或引入变量文件的地方。

`etc/ansible/roles/role01/defaults/main.yml`文件是role定义默认变量的地方，优先级较低，当然也可以引入文件。

```yml
---
# vars file for role01
foo1:
  a: hello
  b: world
```

#### 定义role的task

`/etc/ansible/roles/role01/tasks/main.yml`文件是role定义task或者引入task文件的地方。

```yml
---
# tasks file for role01
- name: task1
  debug:
    msg: "{{ foo1.a }} {{ foo1.b }}"
  notify: handler1
  changed_when: true				# 该指令使得chenged=1，触发notify
```

#### 定义role的handler

`/etc/ansible/roles/role01/handles/main.yml`文件是role定义handler或者引入handler文件的地方。

```yml
---
# handlers file for role01
- name: handler1
  debug:
    msg: "run handler1"
```

**执行：**

```bash
$ ansible-playbook  enter.yml

PLAY [enter for all roles] **********************************************************************************************************

TASK [role01 : task1] ***************************************************************************************************************
changed: [node1] => {}

MSG:

hello world

RUNNING HANDLER [role01 : handler1] *************************************************************************************************
ok: [node1] => {}

MSG:

run handler1

PLAY RECAP **************************************************************************************************************************
node1                      : ok=2    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0
```

## playbook的执行顺序

`playbook`从上往下执行各个`play`，在每个`play`内，从上往下执行各个`task`。

一个节点执行任务的顺序如下：

- 解析配置/etc/ansible/ansible.cfg
- 解析inventory
- gather_facts任务
- pre_tasks任务
- pre_tasks任务触发的handler
- roles指令加载的role
- task指令中的任务
- roles和tasks触发的handler
- post_tasks指令中的任务
- post_tasks中任务触发的handler

多个节点时，`ansible`在所有节点上执行完成前一个任务后，才进入下一个任务的执行流程。`handlers`是在所有节点上的所有任务执行完成后，开始执行。

> - 从inventory中选择执行play的相关主机
> - 连接到远程主机，通常使用ssh方式
> - 拷贝相关模块到远程主机，并开始执行

### 理解委托任务

委托是指将原本在一个节点上执行的任务，委托给另一个节点执行。

要想理解委托，需要先了解ansible任务的执行过程：默认情况下，ansible先选择执行任务的主机，后连接该主机，再拷贝相关模块，并在该远程主机上执行模块。

但是在任务中进行了委托后，实际连接主机和执行模块动作将发生改变。比如将node1主机的任务委托给node2，ansible会根据hosts指令选择远程主机node1后，然后根据`delegate_to`指令，连接到node2节点，并将相关模块拷贝到node2并在node2上执行。

以下使用`dalegate_to`指令做个演示

test.yaml文件：

```yml
---
- name: play1
  hosts: node1
  gather_facts: no
  tasks:
    - name: task1
      shell: hostname
      delegate_to: node2			# 委托node2执行shell模块
```

执行`ansible-playbook -vvv test.yaml`，然后查看具体的执行日志。

再将`delegate_to`指令去除，执行`ansible-playbook -vvv test.yaml`命令查看不委托的执行过程，两者对比很容易发现ansible连接的主机为委托的主机。

```bash
$ ansible-playbook -vvv test.yaml
```

打印输出日志如下，也可在`/var/log/ansible.log`中查看。

```/var/log/ansible.log
ansible-playbook 2.9.25
  config file = /etc/ansible/ansible.cfg
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible
  executable location = /usr/bin/ansible-playbook
  python version = 2.7.5 (default, Oct 14 2020, 14:45:30) [GCC 4.8.5 20150623 (Red Hat 4.8.5-44)]
Using /etc/ansible/ansible.cfg as config file
host_list declined parsing /etc/ansible/hosts as it did not pass its verify_file() method
script declined parsing /etc/ansible/hosts as it did not pass its verify_file() method
auto declined parsing /etc/ansible/hosts as it did not pass its verify_file() method
Parsed /etc/ansible/hosts inventory source with ini plugin
Skipping callback 'actionable', as we already have a stdout callback.
Skipping callback 'counter_enabled', as we already have a stdout callback.
Skipping callback 'debug', as we already have a stdout callback.
Skipping callback 'dense', as we already have a stdout callback.
Skipping callback 'dense', as we already have a stdout callback.
Skipping callback 'full_skip', as we already have a stdout callback.
Skipping callback 'json', as we already have a stdout callback.
Skipping callback 'minimal', as we already have a stdout callback.
Skipping callback 'null', as we already have a stdout callback.
Skipping callback 'oneline', as we already have a stdout callback.
Skipping callback 'selective', as we already have a stdout callback.
Skipping callback 'skippy', as we already have a stdout callback.
Skipping callback 'stderr', as we already have a stdout callback.
Skipping callback 'unixy', as we already have a stdout callback.
Skipping callback 'yaml', as we already have a stdout callback.

PLAYBOOK: test.yaml *****************************************************************************************************************
1 plays in test.yaml

PLAY [play1] ************************************************************************************************************************
META: ran handlers

TASK [task1] ************************************************************************************************************************
task path: /etc/ansible/test.yaml:6
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c node2 '/bin/sh -c '"'"'echo ~ && sleep 0'"'"''
<node2> (0, '/root\n', '')
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c node2 '/bin/sh -c '"'"'( umask 77 && mkdir -p "` echo /root/.ansible/tmp `"&& mkdir "` echo /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554 `" && echo ansible-tmp-1642857067.57-71643-202799162287554="` echo /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554 `" ) && sleep 0'"'"''
<node2> (0, 'ansible-tmp-1642857067.57-71643-202799162287554=/root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554\n', '')
<node1> Attempting python interpreter discovery
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c node2 '/bin/sh -c '"'"'echo PLATFORM; uname; echo FOUND; command -v '"'"'"'"'"'"'"'"'/usr/bin/python'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'python3.7'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'python3.6'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'python3.5'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'python2.7'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'python2.6'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'/usr/libexec/platform-python'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'/usr/bin/python3'"'"'"'"'"'"'"'"'; command -v '"'"'"'"'"'"'"'"'python'"'"'"'"'"'"'"'"'; echo ENDFOUND && sleep 0'"'"''
<node2> (0, 'PLATFORM\nLinux\nFOUND\n/usr/bin/python\n/usr/bin/python2.7\n/usr/libexec/platform-python\n/usr/bin/python\nENDFOUND\n', '')
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c node2 '/bin/sh -c '"'"'/usr/bin/python && sleep 0'"'"''
<node2> (0, '{"osrelease_content": "NAME=\\"CentOS Linux\\"\\nVERSION=\\"7 (Core)\\"\\nID=\\"centos\\"\\nID_LIKE=\\"rhel fedora\\"\\nVERSION_ID=\\"7\\"\\nPRETTY_NAME=\\"CentOS Linux 7 (Core)\\"\\nANSI_COLOR=\\"0;31\\"\\nCPE_NAME=\\"cpe:/o:centos:centos:7\\"\\nHOME_URL=\\"https://www.centos.org/\\"\\nBUG_REPORT_URL=\\"https://bugs.centos.org/\\"\\n\\nCENTOS_MANTISBT_PROJECT=\\"CentOS-7\\"\\nCENTOS_MANTISBT_PROJECT_VERSION=\\"7\\"\\nREDHAT_SUPPORT_PRODUCT=\\"centos\\"\\nREDHAT_SUPPORT_PRODUCT_VERSION=\\"7\\"\\n\\n", "platform_dist_result": ["centos", "7.9.2009", "Core"]}\n', '')
Using module file /usr/lib/python2.7/site-packages/ansible/modules/commands/command.py
<node2> PUT /root/.ansible/tmp/ansible-local-71634Zeeqqp/tmpWHppzz TO /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554/AnsiballZ_command.py
<node2> SSH: EXEC sftp -b - -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c '[node2]'
<node2> (0, 'sftp> put /root/.ansible/tmp/ansible-local-71634Zeeqqp/tmpWHppzz /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554/AnsiballZ_command.py\n', '')
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c node2 '/bin/sh -c '"'"'chmod u+x /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554/ /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554/AnsiballZ_command.py && sleep 0'"'"''
<node2> (0, '', '')
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c -tt node2 '/bin/sh -c '"'"'/usr/bin/python /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554/AnsiballZ_command.py && sleep 0'"'"''
<node2> (0, '\r\n{"changed": true, "end": "2022-01-22 21:11:09.433351", "stdout": "node2", "cmd": "hostname", "rc": 0, "start": "2022-01-22 21:11:09.333195", "stderr": "", "delta": "0:00:00.100156", "invocation": {"module_args": {"creates": null, "executable": null, "_uses_shell": true, "strip_empty_ends": true, "_raw_params": "hostname", "removes": null, "argv": null, "warn": true, "chdir": null, "stdin_add_newline": true, "stdin": null}}}\r\n', 'Shared connection to node2 closed.\r\n')
<node2> ESTABLISH SSH CONNECTION FOR USER: None
<node2> SSH: EXEC ssh -C -o ControlMaster=auto -o ControlPersist=60s -o Port=22 -o KbdInteractiveAuthentication=no -o PreferredAuthentications=gssapi-with-mic,gssapi-keyex,hostbased,publickey -o PasswordAuthentication=no -o ConnectTimeout=10 -o ControlPath=/root/.ansible/cp/0d67807d8c node2 '/bin/sh -c '"'"'rm -f -r /root/.ansible/tmp/ansible-tmp-1642857067.57-71643-202799162287554/ > /dev/null 2>&1 && sleep 0'"'"''
<node2> (0, '', '')
changed: [node1 -> node2] => {
    "changed": true, 
    "cmd": "hostname", 
    "delta": "0:00:00.100156", 
    "end": "2022-01-22 21:11:09.433351", 
    "invocation": {
        "module_args": {
            "_raw_params": "hostname", 
            "_uses_shell": true, 
            "argv": null, 
            "chdir": null, 
            "creates": null, 
            "executable": null, 
            "removes": null, 
            "stdin": null, 
            "stdin_add_newline": true, 
            "strip_empty_ends": true, 
            "warn": true
        }
    }, 
    "rc": 0, 
    "start": "2022-01-22 21:11:09.333195"
}

STDOUT:

node2
META: ran handlers
META: ran handlers

PLAY RECAP **************************************************************************************************************************
node1                      : ok=1    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0   
```

------

到此为止，ansible的基础算是过了一遍，对ansible的基础概念、用法有了一个大概的了解，作为入门是够了。

但是ansible的内容和细节还有很多，包括不限于：

- inventory更加复杂的定义方式
- 各种级别的指令
- 众多类型的变量和其作用
- 各种条件判断
- 各类循环，以及其它控制流程
- 文件如何加载解析、任务执行的顺序及方式
- 更多的模块、插件
- role、vault、jinja2
- 配置文件
- 二次开发

