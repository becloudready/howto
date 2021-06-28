# How to check if you are in a Container/VM or Physical box.

## Method 1

```
cat /proc/1/cgroup
11:memory:/
10:blkio:/
9:pids:/
8:hugetlb:/
7:cpuacct,cpu:/
6:cpuset:/
5:net_prio,net_cls:/
4:perf_event:/
3:freezer:/
2:devices:/
1:name=systemd:/
```
#  Method 2

```
grep -q ^flags.*\ hypervisor\  /proc/cpuinfo && echo "This machine is a VM" || echo "This is Physical box"
```
