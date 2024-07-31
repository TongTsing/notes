Ansible Playbook是用于定义一系列任务和配置的文档，它允许你自动化服务器配置和管理。以下是一些Ansible Playbook的高级用法：

### 1. **变量的高级用法：**
   - **变量文件：** 使用变量文件（通常以 `.yml` 或 `.yaml` 后缀结尾）来组织和存储变量，以提高可维护性。在Playbook中通过 `vars_files` 引入变量文件。

     ```yaml
     # playbook.yml
     - name: Example playbook with variable file
       hosts: all
       vars_files:
         - vars/web_servers.yml
       tasks:
         - name: Display variable from file
           debug:
             var: my_variable
     ```

   - **Prompting用户输入：** 使用 `vars_prompt` 从用户那里获取变量输入。

     ```yaml
     # playbook.yml
     - name: Example playbook with user input
       hosts: localhost
       vars_prompt:
         - name: user_input_var
           prompt: "Enter a value for the variable"
       tasks:
         - name: Display user input
           debug:
             var: user_input_var
     ```

### 2. **条件语句的高级用法：**
   - **`when`关键字：** 使用更复杂的条件判断，结合多个条件，使任务更灵活。

     ```yaml
     # playbook.yml
     - name: Example playbook with advanced condition
       hosts: all
       tasks:
         - name: Install package on Debian 10 or Ubuntu 20.04
           apt:
             name: my_package
           when: "'Debian' in ansible_distribution and ansible_distribution_version == '10' or 'Ubuntu' in ansible_distribution and ansible_distribution_version == '20.04'"
     ```

### 3. **循环的高级用法：**
   - **`loop`关键字：** 使用 `loop` 执行循环，可以是列表、字典或范围。

     ```yaml
     # playbook.yml
     - name: Example playbook with loop
       hosts: all
       tasks:
         - name: Create multiple directories
           file:
             path: "{{ item }}"
             state: directory
           loop:
             - /path/dir1
             - /path/dir2
             - /path/dir3
     ```

### 4. **错误处理和故障转移：**
   - **`ignore_errors`：** 使用 `ignore_errors` 忽略某些任务的错误，继续执行其他任务。

     ```yaml
     # playbook.yml
     - name: Example playbook with error handling
       hosts: all
       tasks:
         - name: Task that may fail
           command: /path/to/failing-command
           ignore_errors: yes
         - name: Another task
           debug:
             msg: "This will be executed even if the previous task fails."
     ```

   - **`failed_when`：** 使用 `failed_when` 指定自定义条件，以确定任务是否被认为是失败。

     ```yaml
     # playbook.yml
     - name: Example playbook with custom failure condition
       hosts: all
       tasks:
         - name: Task with custom failure condition
           command: /path/to/command
           register: result
           failed_when: "'specific_error_message' in result.stderr"
     ```

### 5. **角色的高级用法：**
   - **角色依赖：** 使用 `dependencies` 关键字在角色之间创建依赖关系。

     ```yaml
     # playbook.yml
     - name: Example playbook with role dependencies
       hosts: all
       roles:
         - role: common
         - role: web_server
           dependencies:
             - { role: database_server, when: "database_required == true" }
     ```

   - **动态角色名称：** 使用变量来动态指定要使用的角色。

     ```yaml
     # playbook.yml
     - name: Example playbook with dynamic role name
       hosts: all
       tasks:
         - name: Include role dynamically
           include_role:
             name: "{{ my_dynamic_role }}"
     ```

这些是一些Ansible Playbook的高级用法，它们可以帮助你更灵活、高效地管理和配置服务器。在实际应用中，结合这些技术可以构建复杂的自动化工作流程。