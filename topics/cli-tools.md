# tldr
```bash
tldr tldr 
```

# Networks
See also network [tools](<./networks/tools.md>)
```sh
nmap -p 3000-3500 # scan open ports 3000-3500
tcpdump -i any port 80 # inspect traffic on port 80
ss -tulp # show listening ports and PIDs
dig +short [domain] # DNS lookup
ip a # IP addresses
curl -vI [url] # fetch HTTP headers
```

# System
```sh
systemctl list-units --failed
journalctl -u [service_name] -f
tail -n 50 -f /var/log/syslog # follow the last 50 lines

chown
chmod

tar xvf file.tar[.gz|.bz.xz]

ln -s file/or/directory symlink # symlinks

ls -1 | wc -l # count within dir
find . -maxdepth 1 -type f | wc -l # count files within dir
```


# Process
```bash
pgrep -l firefox
19117 firefox
19188 firefox

ps aux | awk '/firefox/ {print $2}' | xargs kill -9
pgrep firefox | xargs kill -9
pkill -9 firefox
```

# SCP/SFTP
```bash
scp localfile user@remote_host:destination
scp -r user@host:source/dir user@remote_host:destination

sftp user@remote_host
ls # remote ls
lls # prepend l for local
get -r remote_dir
put local_file 
```

# find
Adapted from `tldr` 

```bash
#  Find python not within external
find . -name '*.py' ! -path 'external/*'

# Find files matching multiple path/name patterns:
find . -path '*/path/*/*.ext' -o -iname '*pattern*'

# Find files >500kb and <10M, limiting the recursive depth to "2":
# 2 includes the dir and 1 level of subdir
find . -maxdepth 2 -size +500k -size -10M

# Run ./process.py a.in > a.out on each file
# Note that `sh -c` lets you set $0, so use _ to ignore it 
# Use \; to end a command
find . -name '*.in' -exec sh -c './process.py "$1" > "${1%.in}.out"' _ {} \;

# Find all files modified today and pass the results to a single command as arguments (+):
find . -daystart -mtime -1 -exec tar -cvf archive.tar {} +

# Search for either empty files or directories and delete them verbosely:
find . -type f|d -empty -delete -print
```

# sed
```bash
sed '/ERROR/d' file # delete error lines
sed '10,20d' file # delete lines 10 to 20 inclusive
sed '/^$/d' file # delete empty lines

# in lines with ERROR, replace foo with bar
sed '/ERROR/s/foo/bar/g' file
sed 's/foo/bar/g' file
sed 's:path/to/dir:path/to/newdir/gi' # use : as delimter
```
# awk
Adapted from [Catonmat](https://catonmat.net/blog/wp-content/uploads/2008/09/awk1line.txt)

```bash
awk '{print $1,$3}' file        # Print first and third fields
awk -F, '{print $1}' file       # Comma delimiter

# Sum ave of col 1, ignoring header 
# NR = row number
awk -F, 'NR>1 {sum+=$1} END {print sum/(NR-1)}' file.csv


# filter CPU% > 10, print PID, CPU%, command
ps aux | awk '$3 > 10 {print $2, $3, $11}'

# print and sort the login names of all users
awk -F: '{ print $1 | "sort" }' /etc/passwd


# From table, freq of IP addr, then reverse numeric sort
# 152 192.168.1.10
#  85 192.168.1.25
cat access.log | awk '{print $9}' | sort | uniq -c | sort -nr
cat access.log | awk '{ count[$9]++ } END { for (ip in count) print count[ip], ip }' 
```



# xargs

```bash
# parallelize 
cat images.txt | xargs -n1 -P4 ./resize.sh

# Count changes
git diff --name-only | xargs wc -l
```


# SSH keys



# ps aux
[Akamai](https://www.akamai.com/cloud/guides/use-the-ps-aux-command-in-linux)
```bash
~ ❯ ps aux
USER       PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root         1  0.0  0.0    892   572 ?        Sl   Nov28   0:00 /init
root       227  0.0  0.0    900    80 ?        Ss   Nov28   0:00 /init
root       228  0.0  0.0    900    88 ?        S    Nov28   0:00 /init
```

| Field | Name              | Desc                                                                   |
| ----- | ----------------- | ---------------------------------------------------------------------- |
| VSZ   | Virtual mem size  | Total virt mem allocated (including code, data, swapped memory) in KiB |
| RSS   | Resident set size | Amount of physical RAM (non-swapped) process is using in KiB           |
| TTY   | TeleTypewriter    | Connected terminal. `?` means background daemon                        |
| STAT  | Process state     | Status code                                                            |
| START | Start time        | time process was first launched                                        |
| TIME  | CPU time          | amount of CPU time process has used since start                        |

| Status | Name                                         |
| ------ | -------------------------------------------- |
| s      | session leader                               |
| S      | interruptible sleep state, waiting for event |
| R      | running                                      |
| T      | stopped (e.g. due to Ctrl-z)                 |
| +      | foreground process                           |




# htop
[Image credits](https://codeahoy.com/2017/01/20/hhtop-explained-visually/)
![](<./assets/Pasted image 20260330161939.png>)
![](<./assets/Pasted image 20260330161946.png>)

# jq
```bash
# Examples taken from https://navendu.me/posts/jq-interactive-guide/

# Use single quotes!
jq '.' file.json   # pretty print
jq '.name.address' # get object.name.address

jq '.[]'    # "a" "b" "c" # json strings
jq '.[0]'   # indexing
jq '.[-6:]' # slicing
jq -r '.[]' # a b c # raw strings
jq '[.[]]'  # ["a", "b", "c"] # a json array

jq '{"name": .name, "contact": .email}' # make object using fields

# Some useful functions:
jq '.[] | length'
jq '. | keys'

# Select by filter
jq '.[] | select(.address.city == "South Christy") | {name, username, email}'

# Example
jq
'group_by(.address.city) |
map({
  city: .[0].address.city,
  user_count: length,
  users: [.[] | {
    name: .name,
    slug: ((.name + "-" + .address.city | gsub(" "; "-") | ascii_downcase))
  }]
})'

# [
#   {
#     "city": "Aliyaview",
#     "user_count": 2,
#     "users": [
#       {
#         "name": "Nicholas Runolfsdottir V",
#         "slug": "nicholas-runolfsdottir-v-aliyaview"
#       },
#       {...}
#     ]
#   },
#   ...
# ]

# Other examples
aws secretsmanager get-secret-value --secret-id db_app |
  jq -r '.SecretString | fromjson.password'
# .SecretString gives "{\"username\":\"admin\",\"password\":\"my_secret_password\"}"
# must use -r so chars are not escaped
# pipe into fromjson which parses the escaped json string
# then get password
```




# Bash Readline Shortcuts

## Tricks
| Command | Description                                                  |
| ------- | ------------------------------------------------------------ |
| cd -    | Last dir                                                     |
| !!      | Last command                                                 |
| #       | Start a line with # and then enter to add to command history |
| $@      | last arg of last command                                     |

## Bash Readline


[Image source](https://gist.github.com/tuxfight3r/60051ac67c5f0445efee)

![](<./assets/Pasted image 20260330171332.png>)


| Description                                        | Command                                |
| -------------------------------------------------- | -------------------------------------- |
| Open in `$EDITOR`                                  | Ctrl+X Ctrl+E                          |
| Undo                                               | Ctrl+/<br>Ctrl+_                       |
| Back/forward                                       | Alt/Ctrl Arrow keys<br>Alt B/F         |
| Send as comment <br>(i.e. keep in command history) | Alt+#<br>(or just comment it manually) |
| Delete back word                                   | Ctrl+W                                 |
| vim d^                                             | Ctrl+U                                 |
| vim d$                                             | Ctrl+K                                 |
| Home                                               | Ctrl+A / Home                          |
| End                                                | Ctrl+E / End                           |
| Delete forward word                                | Alt+D                                  |
| Insert last arg                                    | Alt+.                                  |
