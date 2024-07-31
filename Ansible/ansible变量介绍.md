## Ansible变量介绍



## 一、ansible的变量分类

在Ansible中，变量可以分为以下几种类型：

1. **主机变量（Host Variables）：**
   - 这些变量是与特定主机关联的，可以在清单文件中为每个主机定义。主机变量通常用于存储与该主机直接相关的配置信息，如主机的IP地址、SSH端口、用户名等。

   ```ini
   [web_servers]
   server1 ansible_host=192.168.1.10 ansible_user=myuser
   ```

2. **组变量（Group Variables）：**
   - 这些变量是与主机组关联的，可以在清单文件中为整个组定义。组变量适用于将相似的配置信息应用于一组主机，例如所有Web服务器或所有数据库服务器。

   ```ini
   [web_servers]
   server1
   server2
   
   [web_servers:vars]
   http_port=80
   ```

3. **全局变量（Global Variables）：**
   - 这些变量是在整个Ansible环境中全局可用的。全局变量通常用于存储与整个环境相关的配置信息，例如代理设置、日志级别等。

   ```yaml
   # 在 ansible.cfg 或者 inventory 文件中定义
   [defaults]
   my_global_variable=123
   ```

4. **事实（Facts）：**
   - 事实是由Ansible在执行任务时自动收集的系统信息，例如操作系统类型、发行版、IP地址等。这些信息作为变量在后续任务中可用。

   ```yaml
   - name: Display facts
     debug:
       var: ansible_distribution
   ```

5. **Playbook变量：**
   - 在Playbook中定义的变量，通常用于在Playbook中传递参数、存储配置信息等。这些变量可以在整个Playbook中使用，并且可以被传递到角色中。

   ```yaml
   - hosts: web_servers
     vars:
       http_port: 80
     tasks:
       - name: Ensure web server is running
         service:
           name: apache2
           state: started
   ```

这些变量类型提供了不同级别的作用域和范围，使得在不同层次上管理和传递配置信息变得更加灵活。

## 二、主机变量

在Ansible中，主机变量是与特定主机关联的变量。这些变量可以在清单文件中定义，用于存储主机特定的配置信息。主机变量可以包含主机的属性、设置、角色等信息。以下是一些主机变量的例子以及它们的用途：

### 在清单文件中定义主机变量

在清单文件中，你可以为每个主机定义变量，如下所示：

```ini
[web_servers]
server1 ansible_host=192.168.1.10 ansible_user=myuser

[web_servers:vars]
http_port=80
database_server=database.example.com
```

在上述例子中，`http_port`和`database_server`是`web_servers`组中所有主机的共享变量。而`ansible_host`和`ansible_user`是`server1`主机的特定变量。

### 主机变量的用途

1. **自定义配置信息：** 主机变量用于存储主机特定的配置信息，如IP地址、SSH用户名、端口等。

   ```ini
   [web_servers]
   server1 ansible_host=192.168.1.10 ansible_user=myuser ansible_ssh_port=2222
   ```

2. **角色分配：** 你可以使用主机变量为主机分配不同的角色。

   ```ini
   [web_servers]
   server1 ansible_host=192.168.1.10 ansible_user=myuser
   server2 ansible_host=192.168.1.11 ansible_user=myuser

   [web_servers:vars]
   role=web
   ```

3. **组共享变量：** 可以将一些变量定义在组级别，以便所有属于该组的主机共享。

   ```ini
   [web_servers:vars]
   http_port=80
   ```

4. **动态清单：** 在使用动态清单时，你可以通过脚本生成主机变量，以根据主机的动态属性设置变量。

### 主机变量的获取

在Ansible任务中，你可以使用`hostvars`来获取主机变量。例如，如果你在Playbook中需要使用某个主机的变量，可以这样：

```yaml
- name: Example playbook using host variables
  hosts: web_servers
  tasks:
    - name: Display HTTP port
      debug:
        var: hostvars[inventory_hostname].http_port
```

在这个例子中，`hostvars[inventory_hostname]`用于获取当前主机的变量，然后通过`.`语法访问`http_port`变量。

主机变量是Ansible中组织和管理配置信息的重要工具，可以根据需要合理使用，以简化和灵活地配置主机。

## 三、组变量

在Ansible中，组变量是与主机组关联的变量，用于存储整个组的共享配置信息。组变量是在清单文件中定义的，并影响该组中的所有主机。通过使用组变量，你可以方便地为一组主机指定相同的配置，而不必在每个主机上都进行单独设置。

以下是一个基本的示例，演示了如何在Ansible清单文件中定义组变量：

```ini
[web_servers]
server1 ansible_host=192.168.1.10 ansible_user=myuser
server2 ansible_host=192.168.1.11 ansible_user=myuser

[web_servers:vars]
http_port=80
document_root=/var/www/html
```

在上述示例中，`[web_servers:vars]` 下定义的 `http_port` 和 `document_root` 就是组变量。这意味着所有属于 `web_servers` 组的主机都会共享这些变量。

### 组变量的用途

1. **共享配置信息：** 组变量允许你在整个组中共享相同的配置信息，避免在每个主机上都进行单独设置。

2. **角色分配：** 你可以使用组变量来指定一组主机的角色或功能。

    ```ini
    [web_servers:vars]
    role=web
    ```

3. **动态清单：** 在使用动态清单时，你可以通过脚本生成组变量，以根据组的动态属性设置变量。

### 组变量的获取

在Ansible任务中，你可以通过`group_vars`目录来组织组变量。Ansible会在执行时自动查找并加载这些变量。目录结构如下：

```
ansible_project/
|-- inventory/
|   |-- hosts
|-- group_vars/
|   |-- web_servers.yml
|-- playbook.yml
```

在 `web_servers.yml` 中，你可以定义组变量：

```yaml
# group_vars/web_servers.yml
http_port: 80
document_root: /var/www/html
```

在Playbook中，你不需要显式引用组变量，Ansible会自动根据组的层次结构找到并应用它们。

```yaml
- name: Example playbook using group variables
  hosts: web_servers
  tasks:
    - name: Display HTTP port
      debug:
        var: http_port
```

这个任务会自动使用 `web_servers` 组的组变量中的 `http_port` 变量。

组变量是Ansible中一种非常有用的配置管理工具，它简化了在一组主机上配置相同设置的流程。

## 四、全局变量

在Ansible中，全局变量是一类与整个Ansible环境相关的变量，可以在配置文件中定义。这些变量对所有的Playbook和任务都是可见的，提供了一种在整个Ansible配置中共享信息的方式。全局变量通常在 `ansible.cfg` 文件中定义，这是Ansible的主配置文件。

以下是一些常见的全局变量示例：

1. **`ansible.cfg` 中定义全局变量：**

   ```ini
   [defaults]
   my_global_variable=123
   another_global_variable=value
   ```

   在这个例子中，`my_global_variable` 和 `another_global_variable` 是全局变量，可以在所有Playbook中使用。

2. **环境变量：**

   你还可以使用环境变量来定义全局变量，这对于在不同的环境中传递参数非常有用。例如，在终端中设置环境变量：

   ```bash
   export MY_GLOBAL_VARIABLE=123
   ```

   在Playbook中使用：

   ```yaml
   - name: Example playbook using global variable
     hosts: all
     tasks:
       - name: Display global variable
         debug:
           var: lookup('env', 'MY_GLOBAL_VARIABLE')
   ```

   使用 `lookup('env', 'MY_GLOBAL_VARIABLE')` 来检索环境变量。

全局变量的使用是为了在整个Ansible环境中提供一致的配置信息。但请注意，如果有需要，你仍然可以在Playbook中使用其他层次的变量，如主机变量、组变量和任务变量。这种层次结构允许你在不同的范围内定义和覆盖变量，使配置管理更为灵活。

## 五、事实

在Ansible中，事实（Facts）是指由Ansible在远程主机上自动收集的系统信息。这些信息包括有关操作系统、硬件、网络等方面的数据。Ansible将这些数据存储为事实，并在后续的任务中可以使用这些事实进行条件判断、变量设置等操作。

Ansible通过模块 `setup` 来收集事实，这个模块会在每次运行Playbook时自动执行。以下是一些事实的示例：

```yaml
- name: Display gathered facts
  hosts: all
  tasks:
    - name: Show all facts
      debug:
        var: ansible_facts
```

这个任务将显示所有已经收集的事实。以下是一些常见的事实及其用途：

1. **ansible_distribution：** 用于识别主机上的操作系统发行版，例如 "Ubuntu" 或 "CentOS"。
   
2. **ansible_os_family：** 指定操作系统家族，如 "Debian" 或 "RedHat"。

3. **ansible_architecture：** 主机的体系结构，例如 "x86_64"。

4. **ansible_eth0.ipv4.address：** 主机的IPv4地址。

5. **ansible_date_time.date：** 当前日期。

6. **ansible_userspace_bits：** 用户空间的位数，例如 "64"。

7. **ansible_processor_cores：** 处理器核心数。

你可以在Playbook中使用这些事实来根据不同的主机和条件执行任务。例如，根据操作系统的不同执行不同的配置任务，或者根据主机的硬件特性来选择适当的安装步骤。

```yaml
- name: Configure web server based on OS
  hosts: web_servers
  tasks:
    - name: Install Apache on Debian
      apt:
        name: apache2
      when: ansible_os_family == 'Debian'

    - name: Install Apache on Red Hat
      yum:
        name: httpd
      when: ansible_os_family == 'RedHat'
```

上述示例中，根据主机的操作系统家族选择不同的包管理器以安装Apache软件。这展示了如何使用事实进行条件判断以实现更灵活的配置管理。

## 六、注册变量

在Ansible中，`register` 是一个关键字，用于将任务执行的结果保存到一个变量中，以便后续任务使用。这允许你在Playbook中捕获命令的输出、状态或其他信息，并在后续的任务中使用这些信息。以下是 `register` 的基本使用示例：

```yaml
- name: Run a command and register the output
  command: echo "Hello, World!"
  register: command_output

- name: Display the registered variable
  debug:
    var: command_output.stdout
```

在上面的例子中，`command_output` 是一个注册变量，它包含了运行 `echo "Hello, World!"` 命令的输出。你可以使用 `debug` 模块来查看注册变量的内容，通过 `command_output.stdout` 访问命令的标准输出。

除了 `stdout`，`register` 变量还可以包含其他信息，例如 `stderr`、`rc`（返回代码）、`changed` 等，具体取决于任务的执行结果。你可以根据需要使用这些属性。

```yaml
- name: Run a command with error and register the output
  command: /path/to/some/command
  register: command_result
  ignore_errors: yes

- name: Display the registered variable
  debug:
    var: command_result
```

在上面的例子中，`command_result` 包含了运行的命令的所有信息，包括标准输出、标准错误、返回代码等。使用 `ignore_errors: yes` 可以忽略命令执行的错误，继续执行Playbook。

你还可以将注册变量用于条件判断，根据命令执行的结果来决定是否执行下一步操作：

```yaml
- name: Run a command and register the output
  command: echo "Hello, World!"
  register: command_output

- name: Display a message based on the command output
  debug:
    msg: "Output is {{ command_output.stdout }}"
  when: command_output.stdout == "Hello, World!\n"
```

通过使用 `register`，你可以更灵活地处理任务执行的结果，并根据需要采取不同的措施。







# Ansible中声明变量的方式

在Ansible中，有多种方式可以定义变量。变量可以在不同的层次中定义，包括主机组、主机、角色、任务等。以下是几种定义变量的方式：

### 1. 主机组和主机级别的变量：

在`inventory`目录下的`group_vars/`和`host_vars/`目录中，你可以分别创建 YAML 文件来定义主机组和主机级别的变量。

#### `inventory/group_vars/my_group.yml`:

```yaml
---
my_group_variable: "This is a group variable"
```

#### `inventory/host_vars/my_host.yml`:

```yaml
---
my_host_variable: "This is a host variable"
```

### 2. 在Playbook中定义变量：

你可以在Playbook中使用 `vars` 关键字定义变量，这些变量将在Playbook的全局范围内可用。

```yaml
---
- hosts: my_servers
  vars:
    playbook_variable: "This is a playbook variable"
  tasks:
    - name: Display variables
      debug:
        msg: "{{ playbook_variable }}"
```

### 3. 在角色中定义变量：

在Ansible角色的 `defaults/main.yml` 文件中，你可以定义角色级别的默认变量。

#### `roles/my_role/defaults/main.yml`:

```yaml
---
my_role_variable: "This is a role variable"
```

### 4. 在任务中定义变量：

在任务中使用 `vars` 关键字定义变量，这些变量将在该任务的范围内可用。

```yaml
---
- name: Set task-specific variable
  hosts: my_servers
  tasks:
    - name: Define task variable
      vars:
        task_variable: "This is a task variable"
      debug:
        msg: "{{ task_variable }}"
```

### 5. 使用 `set_fact` 动态设置变量：

使用 `set_fact` 模块可以在Playbook中动态设置变量。

```yaml
---
- name: Set a dynamic variable
  hosts: my_servers
  tasks:
    - name: Set dynamic variable
      set_fact:
        dynamic_variable: "This is a dynamic variable"
    - name: Display dynamic variable
      debug:
        msg: "{{ dynamic_variable }}"
```

这些方法提供了多种途径来定义变量，可以根据需要选择适合你的场景的方式。请注意，变量的优先级也取决于定义的位置，所以在使用时请确保了解变量的优先级规则。