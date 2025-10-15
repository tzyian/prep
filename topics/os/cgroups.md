### cgroups (Control Groups) — TL;DR

#### What they are
- A **kernel feature** that lets you **limit, isolate, and monitor** resource usage of a set of processes.  
- Used by **containers**, **systemd**, and **Kubernetes** to enforce CPU/memory/I/O/network limits.

---

#### What they control
| Resource     | Controlled by… | Examples                              |
|---------------|----------------|----------------------------------------|
| CPU time      | `cpu`, `cpuacct` | Limit to 2 cores, set shares          |
| Memory        | `memory`         | Cap at 512 MB, kill on OOM            |
| I/O bandwidth | `blkio`, `io`    | Limit disk read/write rates           |
| Network       | `net_cls`, `net_prio` | Tag traffic, prioritize flows   |
| Processes     | `pids`           | Limit number of processes/threads     |

---

#### How it works
1. Kernel exposes `/sys/fs/cgroup/...` directories.  
2. You create a new **cgroup** (a folder).  
3. Write process IDs (`pid`) into `cgroup.procs`.  
4. Set limits by writing to control files (e.g., `memory.max`, `cpu.max`).
5. Kernel enforces these limits automatically.

---

#### Example
```bash
# Create new cgroup
sudo mkdir /sys/fs/cgroup/myapp
echo $$ | sudo tee /sys/fs/cgroup/myapp/cgroup.procs    # add current shell

# Limit CPU to half a core
echo "50000 100000" | sudo tee /sys/fs/cgroup/myapp/cpu.max

# Limit memory to 256MB
echo $((256*1024*1024)) | sudo tee /sys/fs/cgroup/myapp/memory.max
```