# tldr
```bash
tldr tldr 
```

# Networks
See also network [tools](<./networks/tools.md>)
```sh
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

ps aux | grep docker
kill -9 [pid]
htop # see https://codeahoy.com/2017/01/20/hhtop-explained-visually/

chown
chmod

tar xvf file.tar[.gz|.bz.xz]

scp localfile user@remote_host:destination



ln -s file/ordirectory symlink # symlinks
ls -1 | wc -l # count within dir
find . -maxdepth 1 -type f | wc -l # count files within dir
```

# SSH keys




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


bash
htop
jless
xh
nmap/rustscan





# Bash Readline Shortcuts

## Tricks
| Command | Description              |
| ------- | ------------------------ |
| cd -    | Last dir                 |
| !!      | Last command             |
|         | Start a line with # and  |

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
