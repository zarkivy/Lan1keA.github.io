---
title: "Linux系统调用大全"
description: "multi-instruction set"
date: 2022-05-08T21:45:00+08:00
tags: [ "Linux" ]
imagelink: "https://s2.loli.net/2022/05/08/1vXerd2a3qCgcfQ.jpg"
---



## 传参规则

| arch   | syscall NR | return | arg0 | arg1 | arg2 | arg3 | arg4 | arg5 |
| ------ | :--------: | :----: | :--: | :--: | :--: | :--: | :--: | :--: |
| arm    |     r7     |   r0   |  r0  |  r1  |  r2  |  r3  |  r4  |  r5  |
| arm64  |     x8     |   x0   |  x0  |  x1  |  x2  |  x3  |  x4  |  x5  |
| x86    |    eax     |  eax   | ebx  | ecx  | edx  | esi  | edi  | ebp  |
| x86_64 |    rax     |  rax   | rdi  | rsi  | rdx  | r10  |  r8  |  r9  |

## x86_64 系统调用表

> 以下调用表皆基于linux4.14.0

| NR   |      syscall name      | %rax  |            arg0 (%rdi)            |             arg1 (%rsi)              |                  arg2 (%rdx)                  |
| :--- | :--------------------: | :---: | :-------------------------------: | :----------------------------------: | :-------------------------------------------: |
| 0    |          read          | 0x00  |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 1    |         write          | 0x01  |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 2    |          open          | 0x02  |       const char *filename        |              int flags               |                 umode_t mode                  |
| 3    |         close          | 0x03  |          unsigned int fd          |                  -                   |                       -                       |
| 4    |          stat          | 0x04  |       const char *filename        |  struct __old_kernel_stat *statbuf   |                       -                       |
| 5    |         fstat          | 0x05  |          unsigned int fd          |  struct __old_kernel_stat *statbuf   |                       -                       |
| 6    |         lstat          | 0x06  |       const char *filename        |  struct __old_kernel_stat *statbuf   |                       -                       |
| 7    |          poll          | 0x07  |        struct pollfd *ufds        |          unsigned int nfds           |                  int timeout                  |
| 8    |         lseek          | 0x08  |          unsigned int fd          |             off_t offset             |              unsigned int whence              |
| 9    |          mmap          | 0x09  |                 ?                 |                  ?                   |                       ?                       |
| 10   |        mprotect        | 0x0a  |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 11   |         munmap         | 0x0b  |        unsigned long addr         |              size_t len              |                       -                       |
| 12   |          brk           | 0x0c  |         unsigned long brk         |                  -                   |                       -                       |
| 13   |      rt_sigaction      | 0x0d  |                int                |       const struct sigaction *       |              struct sigaction *               |
| 14   |     rt_sigprocmask     | 0x0e  |              int how              |            sigset_t *set             |                sigset_t *oset                 |
| 15   |      rt_sigreturn      | 0x0f  |                 ?                 |                  ?                   |                       ?                       |
| 16   |         ioctl          | 0x10  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 17   |        pread64         | 0x11  |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 18   |        pwrite64        | 0x12  |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 19   |         readv          | 0x13  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 20   |         writev         | 0x14  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 21   |         access         | 0x15  |       const char *filename        |               int mode               |                       -                       |
| 22   |          pipe          | 0x16  |            int *fildes            |                  -                   |                       -                       |
| 23   |         select         | 0x17  |               int n               |             fd_set *inp              |                 fd_set *outp                  |
| 24   |      sched_yield       | 0x18  |                 -                 |                  -                   |                       -                       |
| 25   |         mremap         | 0x19  |        unsigned long addr         |        unsigned long old_len         |             unsigned long new_len             |
| 26   |         msync          | 0x1a  |        unsigned long start        |              size_t len              |                   int flags                   |
| 27   |        mincore         | 0x1b  |        unsigned long start        |              size_t len              |              unsigned char * vec              |
| 28   |        madvise         | 0x1c  |        unsigned long start        |              size_t len              |                 int behavior                  |
| 29   |         shmget         | 0x1d  |             key_t key             |             size_t size              |                   int flag                    |
| 30   |         shmat          | 0x1e  |             int shmid             |            char *shmaddr             |                  int shmflg                   |
| 31   |         shmctl         | 0x1f  |             int shmid             |               int cmd                |             struct shmid_ds *buf              |
| 32   |          dup           | 0x20  |        unsigned int fildes        |                  -                   |                       -                       |
| 33   |          dup2          | 0x21  |        unsigned int oldfd         |          unsigned int newfd          |                       -                       |
| 34   |         pause          | 0x22  |                 -                 |                  -                   |                       -                       |
| 35   |       nanosleep        | 0x23  |  struct __kernel_timespec *rqtp   |    struct __kernel_timespec *rmtp    |                       -                       |
| 36   |       getitimer        | 0x24  |             int which             | struct __kernel_old_itimerval *value |                       -                       |
| 37   |         alarm          | 0x25  |       unsigned int seconds        |                  -                   |                       -                       |
| 38   |       setitimer        | 0x26  |             int which             | struct __kernel_old_itimerval *value |     struct __kernel_old_itimerval *ovalue     |
| 39   |         getpid         | 0x27  |                 -                 |                  -                   |                       -                       |
| 40   |        sendfile        | 0x28  |            int out_fd             |              int in_fd               |                 off_t *offset                 |
| 41   |         socket         | 0x29  |                int                |                 int                  |                      int                      |
| 42   |        connect         | 0x2a  |                int                |          struct sockaddr *           |                      int                      |
| 43   |         accept         | 0x2b  |                int                |          struct sockaddr *           |                     int *                     |
| 44   |         sendto         | 0x2c  |                int                |                void *                |                    size_t                     |
| 45   |        recvfrom        | 0x2d  |                int                |                void *                |                    size_t                     |
| 46   |        sendmsg         | 0x2e  |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 47   |        recvmsg         | 0x2f  |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 48   |        shutdown        | 0x30  |                int                |                 int                  |                       -                       |
| 49   |          bind          | 0x31  |                int                |          struct sockaddr *           |                      int                      |
| 50   |         listen         | 0x32  |                int                |                 int                  |                       -                       |
| 51   |      getsockname       | 0x33  |                int                |          struct sockaddr *           |                     int *                     |
| 52   |      getpeername       | 0x34  |                int                |          struct sockaddr *           |                     int *                     |
| 53   |       socketpair       | 0x35  |                int                |                 int                  |                      int                      |
| 54   |       setsockopt       | 0x36  |              int fd               |              int level               |                  int optname                  |
| 55   |       getsockopt       | 0x37  |              int fd               |              int level               |                  int optname                  |
| 56   |         clone          | 0x38  |           unsigned long           |            unsigned long             |                     int *                     |
| 57   |          fork          | 0x39  |                 -                 |                  -                   |                       -                       |
| 58   |         vfork          | 0x3a  |                 -                 |                  -                   |                       -                       |
| 59   |         execve         | 0x3b  |       const char *filename        |       const char *const *argv        |            const char *const *envp            |
| 60   |          exit          | 0x3c  |          int error_code           |                  -                   |                       -                       |
| 61   |         wait4          | 0x3d  |             pid_t pid             |            int *stat_addr            |                  int options                  |
| 62   |          kill          | 0x3e  |             pid_t pid             |               int sig                |                       -                       |
| 63   |         uname          | 0x3f  |       struct old_utsname *        |                  -                   |                       -                       |
| 64   |         semget         | 0x40  |             key_t key             |              int nsems               |                  int semflg                   |
| 65   |         semop          | 0x41  |             int semid             |         struct sembuf *sops          |                unsigned nsops                 |
| 66   |         semctl         | 0x42  |             int semid             |              int semnum              |                    int cmd                    |
| 67   |         shmdt          | 0x43  |           char *shmaddr           |                  -                   |                       -                       |
| 68   |         msgget         | 0x44  |             key_t key             |              int msgflg              |                       -                       |
| 69   |         msgsnd         | 0x45  |             int msqid             |         struct msgbuf *msgp          |                 size_t msgsz                  |
| 70   |         msgrcv         | 0x46  |             int msqid             |         struct msgbuf *msgp          |                 size_t msgsz                  |
| 71   |         msgctl         | 0x47  |             int msqid             |               int cmd                |             struct msqid_ds *buf              |
| 72   |         fcntl          | 0x48  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 73   |         flock          | 0x49  |          unsigned int fd          |           unsigned int cmd           |                       -                       |
| 74   |         fsync          | 0x4a  |          unsigned int fd          |                  -                   |                       -                       |
| 75   |       fdatasync        | 0x4b  |          unsigned int fd          |                  -                   |                       -                       |
| 76   |        truncate        | 0x4c  |         const char *path          |             long length              |                       -                       |
| 77   |       ftruncate        | 0x4d  |          unsigned int fd          |         unsigned long length         |                       -                       |
| 78   |        getdents        | 0x4e  |          unsigned int fd          |     struct linux_dirent *dirent      |              unsigned int count               |
| 79   |         getcwd         | 0x4f  |             char *buf             |          unsigned long size          |                       -                       |
| 80   |         chdir          | 0x50  |       const char *filename        |                  -                   |                       -                       |
| 81   |         fchdir         | 0x51  |          unsigned int fd          |                  -                   |                       -                       |
| 82   |         rename         | 0x52  |        const char *oldname        |         const char *newname          |                       -                       |
| 83   |         mkdir          | 0x53  |       const char *pathname        |             umode_t mode             |                       -                       |
| 84   |         rmdir          | 0x54  |       const char *pathname        |                  -                   |                       -                       |
| 85   |         creat          | 0x55  |       const char *pathname        |             umode_t mode             |                       -                       |
| 86   |          link          | 0x56  |        const char *oldname        |         const char *newname          |                       -                       |
| 87   |         unlink         | 0x57  |       const char *pathname        |                  -                   |                       -                       |
| 88   |        symlink         | 0x58  |          const char *old          |           const char *new            |                       -                       |
| 89   |        readlink        | 0x59  |         const char *path          |              char *buf               |                  int bufsiz                   |
| 90   |         chmod          | 0x5a  |       const char *filename        |             umode_t mode             |                       -                       |
| 91   |         fchmod         | 0x5b  |          unsigned int fd          |             umode_t mode             |                       -                       |
| 92   |         chown          | 0x5c  |       const char *filename        |              uid_t user              |                  gid_t group                  |
| 93   |         fchown         | 0x5d  |          unsigned int fd          |              uid_t user              |                  gid_t group                  |
| 94   |         lchown         | 0x5e  |       const char *filename        |              uid_t user              |                  gid_t group                  |
| 95   |         umask          | 0x5f  |             int mask              |                  -                   |                       -                       |
| 96   |      gettimeofday      | 0x60  |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 97   |       getrlimit        | 0x61  |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 98   |       getrusage        | 0x62  |              int who              |          struct rusage *ru           |                       -                       |
| 99   |        sysinfo         | 0x63  |       struct sysinfo *info        |                  -                   |                       -                       |
| 100  |         times          | 0x64  |         struct tms *tbuf          |                  -                   |                       -                       |
| 101  |         ptrace         | 0x65  |           long request            |               long pid               |              unsigned long addr               |
| 102  |         getuid         | 0x66  |                 -                 |                  -                   |                       -                       |
| 103  |         syslog         | 0x67  |             int type              |              char *buf               |                    int len                    |
| 104  |         getgid         | 0x68  |                 -                 |                  -                   |                       -                       |
| 105  |         setuid         | 0x69  |             uid_t uid             |                  -                   |                       -                       |
| 106  |         setgid         | 0x6a  |             gid_t gid             |                  -                   |                       -                       |
| 107  |        geteuid         | 0x6b  |                 -                 |                  -                   |                       -                       |
| 108  |        getegid         | 0x6c  |                 -                 |                  -                   |                       -                       |
| 109  |        setpgid         | 0x6d  |             pid_t pid             |              pid_t pgid              |                       -                       |
| 110  |        getppid         | 0x6e  |                 -                 |                  -                   |                       -                       |
| 111  |        getpgrp         | 0x6f  |                 -                 |                  -                   |                       -                       |
| 112  |         setsid         | 0x70  |                 -                 |                  -                   |                       -                       |
| 113  |        setreuid        | 0x71  |            uid_t ruid             |              uid_t euid              |                       -                       |
| 114  |        setregid        | 0x72  |            gid_t rgid             |              gid_t egid              |                       -                       |
| 115  |       getgroups        | 0x73  |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 116  |       setgroups        | 0x74  |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 117  |       setresuid        | 0x75  |            uid_t ruid             |              uid_t euid              |                  uid_t suid                   |
| 118  |       getresuid        | 0x76  |            uid_t *ruid            |             uid_t *euid              |                  uid_t *suid                  |
| 119  |       setresgid        | 0x77  |            gid_t rgid             |              gid_t egid              |                  gid_t sgid                   |
| 120  |       getresgid        | 0x78  |            gid_t *rgid            |             gid_t *egid              |                  gid_t *sgid                  |
| 121  |        getpgid         | 0x79  |             pid_t pid             |                  -                   |                       -                       |
| 122  |        setfsuid        | 0x7a  |             uid_t uid             |                  -                   |                       -                       |
| 123  |        setfsgid        | 0x7b  |             gid_t gid             |                  -                   |                       -                       |
| 124  |         getsid         | 0x7c  |             pid_t pid             |                  -                   |                       -                       |
| 125  |         capget         | 0x7d  |     cap_user_header_t header      |       cap_user_data_t dataptr        |                       -                       |
| 126  |         capset         | 0x7e  |     cap_user_header_t header      |      const cap_user_data_t data      |                       -                       |
| 127  |     rt_sigpending      | 0x7f  |           sigset_t *set           |          size_t sigsetsize           |                       -                       |
| 128  |    rt_sigtimedwait     | 0x80  |      const sigset_t *uthese       |           siginfo_t *uinfo           |      const struct __kernel_timespec *uts      |
| 129  |    rt_sigqueueinfo     | 0x81  |             pid_t pid             |               int sig                |               siginfo_t *uinfo                |
| 130  |     rt_sigsuspend      | 0x82  |         sigset_t *unewset         |          size_t sigsetsize           |                       -                       |
| 131  |      sigaltstack       | 0x83  |   const struct sigaltstack *uss   |       struct sigaltstack *uoss       |                       -                       |
| 132  |         utime          | 0x84  |          char *filename           |        struct utimbuf *times         |                       -                       |
| 133  |         mknod          | 0x85  |       const char *filename        |             umode_t mode             |                 unsigned dev                  |
| 134  |         uselib         | 0x86  |        const char *library        |                  -                   |                       -                       |
| 135  |      personality       | 0x87  |     unsigned int personality      |                  -                   |                       -                       |
| 136  |         ustat          | 0x88  |           unsigned dev            |          struct ustat *ubuf          |                       -                       |
| 137  |         statfs         | 0x89  |         const char * path         |          struct statfs *buf          |                       -                       |
| 138  |        fstatfs         | 0x8a  |          unsigned int fd          |          struct statfs *buf          |                       -                       |
| 139  |         sysfs          | 0x8b  |            int option             |          unsigned long arg1          |              unsigned long arg2               |
| 140  |      getpriority       | 0x8c  |             int which             |               int who                |                       -                       |
| 141  |      setpriority       | 0x8d  |             int which             |               int who                |                  int niceval                  |
| 142  |     sched_setparam     | 0x8e  |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 143  |     sched_getparam     | 0x8f  |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 144  |   sched_setscheduler   | 0x90  |             pid_t pid             |              int policy              |           struct sched_param *param           |
| 145  |   sched_getscheduler   | 0x91  |             pid_t pid             |                  -                   |                       -                       |
| 146  | sched_get_priority_max | 0x92  |            int policy             |                  -                   |                       -                       |
| 147  | sched_get_priority_min | 0x93  |            int policy             |                  -                   |                       -                       |
| 148  | sched_rr_get_interval  | 0x94  |             pid_t pid             |  struct __kernel_timespec *interval  |                       -                       |
| 149  |         mlock          | 0x95  |        unsigned long start        |              size_t len              |                       -                       |
| 150  |        munlock         | 0x96  |        unsigned long start        |              size_t len              |                       -                       |
| 151  |        mlockall        | 0x97  |             int flags             |                  -                   |                       -                       |
| 152  |       munlockall       | 0x98  |                 -                 |                  -                   |                       -                       |
| 153  |        vhangup         | 0x99  |                 -                 |                  -                   |                       -                       |
| 154  |       modify_ldt       | 0x9a  |                 ?                 |                  ?                   |                       ?                       |
| 155  |       pivot_root       | 0x9b  |       const char *new_root        |         const char *put_old          |                       -                       |
| 156  |        _sysctl         | 0x9c  |                 ?                 |                  ?                   |                       ?                       |
| 157  |         prctl          | 0x9d  |            int option             |          unsigned long arg2          |              unsigned long arg3               |
| 158  |       arch_prctl       | 0x9e  |                 ?                 |                  ?                   |                       ?                       |
| 159  |        adjtimex        | 0x9f  |   struct __kernel_timex *txc_p    |                  -                   |                       -                       |
| 160  |       setrlimit        | 0xa0  |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 161  |         chroot         | 0xa1  |       const char *filename        |                  -                   |                       -                       |
| 162  |          sync          | 0xa2  |                 -                 |                  -                   |                       -                       |
| 163  |          acct          | 0xa3  |         const char *name          |                  -                   |                       -                       |
| 164  |      settimeofday      | 0xa4  |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 165  |         mount          | 0xa5  |          char *dev_name           |            char *dir_name            |                  char *type                   |
| 166  |        umount2         | 0xa6  |                 ?                 |                  ?                   |                       ?                       |
| 167  |         swapon         | 0xa7  |      const char *specialfile      |            int swap_flags            |                       -                       |
| 168  |        swapoff         | 0xa8  |      const char *specialfile      |                  -                   |                       -                       |
| 169  |         reboot         | 0xa9  |            int magic1             |              int magic2              |               unsigned int cmd                |
| 170  |      sethostname       | 0xaa  |            char *name             |               int len                |                       -                       |
| 171  |     setdomainname      | 0xab  |            char *name             |               int len                |                       -                       |
| 172  |          iopl          | 0xac  |                 ?                 |                  ?                   |                       ?                       |
| 173  |         ioperm         | 0xad  |        unsigned long from         |          unsigned long num           |                    int on                     |
| 174  |     create_module      | 0xae  |                 ?                 |                  ?                   |                       ?                       |
| 175  |      init_module       | 0xaf  |            void *umod             |          unsigned long len           |               const char *uargs               |
| 176  |     delete_module      | 0xb0  |       const char *name_user       |          unsigned int flags          |                       -                       |
| 177  |    get_kernel_syms     | 0xb1  |                 ?                 |                  ?                   |                       ?                       |
| 178  |      query_module      | 0xb2  |                 ?                 |                  ?                   |                       ?                       |
| 179  |        quotactl        | 0xb3  |         unsigned int cmd          |         const char *special          |                   qid_t id                    |
| 180  |       nfsservctl       | 0xb4  |                 ?                 |                  ?                   |                       ?                       |
| 181  |        getpmsg         | 0xb5  |                 ?                 |                  ?                   |                       ?                       |
| 182  |        putpmsg         | 0xb6  |                 ?                 |                  ?                   |                       ?                       |
| 183  |      afs_syscall       | 0xb7  |                 ?                 |                  ?                   |                       ?                       |
| 184  |        tuxcall         | 0xb8  |                 ?                 |                  ?                   |                       ?                       |
| 185  |        security        | 0xb9  |                 ?                 |                  ?                   |                       ?                       |
| 186  |         gettid         | 0xba  |                 -                 |                  -                   |                       -                       |
| 187  |       readahead        | 0xbb  |              int fd               |            loff_t offset             |                 size_t count                  |
| 188  |        setxattr        | 0xbc  |         const char *path          |           const char *name           |               const void *value               |
| 189  |       lsetxattr        | 0xbd  |         const char *path          |           const char *name           |               const void *value               |
| 190  |       fsetxattr        | 0xbe  |              int fd               |           const char *name           |               const void *value               |
| 191  |        getxattr        | 0xbf  |         const char *path          |           const char *name           |                  void *value                  |
| 192  |       lgetxattr        | 0xc0  |         const char *path          |           const char *name           |                  void *value                  |
| 193  |       fgetxattr        | 0xc1  |              int fd               |           const char *name           |                  void *value                  |
| 194  |       listxattr        | 0xc2  |         const char *path          |              char *list              |                  size_t size                  |
| 195  |       llistxattr       | 0xc3  |         const char *path          |              char *list              |                  size_t size                  |
| 196  |       flistxattr       | 0xc4  |              int fd               |              char *list              |                  size_t size                  |
| 197  |      removexattr       | 0xc5  |         const char *path          |           const char *name           |                       -                       |
| 198  |      lremovexattr      | 0xc6  |         const char *path          |           const char *name           |                       -                       |
| 199  |      fremovexattr      | 0xc7  |              int fd               |           const char *name           |                       -                       |
| 200  |         tkill          | 0xc8  |             pid_t pid             |               int sig                |                       -                       |
| 201  |          time          | 0xc9  |     __kernel_old_time_t *tloc     |                  -                   |                       -                       |
| 202  |         futex          | 0xca  |            u32 *uaddr             |                int op                |                    u32 val                    |
| 203  |   sched_setaffinity    | 0xcb  |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 204  |   sched_getaffinity    | 0xcc  |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 205  |    set_thread_area     | 0xcd  |                 ?                 |                  ?                   |                       ?                       |
| 206  |        io_setup        | 0xce  |         unsigned nr_reqs          |          aio_context_t *ctx          |                       -                       |
| 207  |       io_destroy       | 0xcf  |         aio_context_t ctx         |                  -                   |                       -                       |
| 208  |      io_getevents      | 0xd0  |       aio_context_t ctx_id        |             long min_nr              |                    long nr                    |
| 209  |       io_submit        | 0xd1  |           aio_context_t           |                 long                 |                  struct iocb                  |
| 210  |       io_cancel        | 0xd2  |       aio_context_t ctx_id        |          struct iocb *iocb           |            struct io_event *result            |
| 211  |    get_thread_area     | 0xd3  |                 ?                 |                  ?                   |                       ?                       |
| 212  |     lookup_dcookie     | 0xd4  |           u64 cookie64            |              char *buf               |                  size_t len                   |
| 213  |      epoll_create      | 0xd5  |             int size              |                  -                   |                       -                       |
| 214  |     epoll_ctl_old      | 0xd6  |                 ?                 |                  ?                   |                       ?                       |
| 215  |     epoll_wait_old     | 0xd7  |                 ?                 |                  ?                   |                       ?                       |
| 216  |    remap_file_pages    | 0xd8  |        unsigned long start        |          unsigned long size          |              unsigned long prot               |
| 217  |       getdents64       | 0xd9  |          unsigned int fd          |    struct linux_dirent64 *dirent     |              unsigned int count               |
| 218  |    set_tid_address     | 0xda  |            int *tidptr            |                  -                   |                       -                       |
| 219  |    restart_syscall     | 0xdb  |                 -                 |                  -                   |                       -                       |
| 220  |       semtimedop       | 0xdc  |             int semid             |         struct sembuf *sops          |                unsigned nsops                 |
| 221  |       fadvise64        | 0xdd  |              int fd               |            loff_t offset             |                  size_t len                   |
| 222  |      timer_create      | 0xde  |       clockid_t which_clock       |  struct sigevent *timer_event_spec   |          timer_t * created_timer_id           |
| 223  |     timer_settime      | 0xdf  |         timer_t timer_id          |              int flags               | const struct __kernel_itimerspec *new_setting |
| 224  |     timer_gettime      | 0xe0  |         timer_t timer_id          | struct __kernel_itimerspec *setting  |                       -                       |
| 225  |    timer_getoverrun    | 0xe1  |         timer_t timer_id          |                  -                   |                       -                       |
| 226  |      timer_delete      | 0xe2  |         timer_t timer_id          |                  -                   |                       -                       |
| 227  |     clock_settime      | 0xe3  |       clockid_t which_clock       |  const struct __kernel_timespec *tp  |                       -                       |
| 228  |     clock_gettime      | 0xe4  |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 229  |      clock_getres      | 0xe5  |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 230  |    clock_nanosleep     | 0xe6  |       clockid_t which_clock       |              int flags               |     const struct __kernel_timespec *rqtp      |
| 231  |       exit_group       | 0xe7  |          int error_code           |                  -                   |                       -                       |
| 232  |       epoll_wait       | 0xe8  |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 233  |       epoll_ctl        | 0xe9  |             int epfd              |                int op                |                    int fd                     |
| 234  |         tgkill         | 0xea  |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 235  |         utimes         | 0xeb  |          char *filename           | struct __kernel_old_timeval *utimes  |                       -                       |
| 236  |        vserver         | 0xec  |                 ?                 |                  ?                   |                       ?                       |
| 237  |         mbind          | 0xed  |        unsigned long start        |          unsigned long len           |              unsigned long mode               |
| 238  |     set_mempolicy      | 0xee  |             int mode              |      const unsigned long *nmask      |             unsigned long maxnode             |
| 239  |     get_mempolicy      | 0xef  |            int *policy            |         unsigned long *nmask         |             unsigned long maxnode             |
| 240  |        mq_open         | 0xf0  |         const char *name          |              int oflag               |                 umode_t mode                  |
| 241  |       mq_unlink        | 0xf1  |         const char *name          |                  -                   |                       -                       |
| 242  |      mq_timedsend      | 0xf2  |            mqd_t mqdes            |         const char *msg_ptr          |                size_t msg_len                 |
| 243  |    mq_timedreceive     | 0xf3  |            mqd_t mqdes            |            char *msg_ptr             |                size_t msg_len                 |
| 244  |       mq_notify        | 0xf4  |            mqd_t mqdes            | const struct sigevent *notification  |                       -                       |
| 245  |     mq_getsetattr      | 0xf5  |            mqd_t mqdes            |     const struct mq_attr *mqstat     |            struct mq_attr *omqstat            |
| 246  |       kexec_load       | 0xf6  |        unsigned long entry        |      unsigned long nr_segments       |        struct kexec_segment *segments         |
| 247  |         waitid         | 0xf7  |             int which             |              pid_t pid               |             struct siginfo *infop             |
| 248  |        add_key         | 0xf8  |         const char *_type         |       const char *_description       |             const void *_payload              |
| 249  |      request_key       | 0xf9  |         const char *_type         |       const char *_description       |           const char *_callout_info           |
| 250  |         keyctl         | 0xfa  |              int cmd              |          unsigned long arg2          |              unsigned long arg3               |
| 251  |       ioprio_set       | 0xfb  |             int which             |               int who                |                  int ioprio                   |
| 252  |       ioprio_get       | 0xfc  |             int which             |               int who                |                       -                       |
| 253  |      inotify_init      | 0xfd  |                 -                 |                  -                   |                       -                       |
| 254  |   inotify_add_watch    | 0xfe  |              int fd               |           const char *path           |                   u32 mask                    |
| 255  |    inotify_rm_watch    | 0xff  |              int fd               |               __s32 wd               |                       -                       |
| 256  |     migrate_pages      | 0x100 |             pid_t pid             |        unsigned long maxnode         |           const unsigned long *from           |
| 257  |         openat         | 0x101 |              int dfd              |         const char *filename         |                   int flags                   |
| 258  |        mkdirat         | 0x102 |              int dfd              |        const char * pathname         |                 umode_t mode                  |
| 259  |        mknodat         | 0x103 |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 260  |        fchownat        | 0x104 |              int dfd              |         const char *filename         |                  uid_t user                   |
| 261  |       futimesat        | 0x105 |              int dfd              |         const char *filename         |      struct __kernel_old_timeval *utimes      |
| 262  |       newfstatat       | 0x106 |              int dfd              |         const char *filename         |             struct stat *statbuf              |
| 263  |        unlinkat        | 0x107 |              int dfd              |        const char * pathname         |                   int flag                    |
| 264  |        renameat        | 0x108 |            int olddfd             |         const char * oldname         |                  int newdfd                   |
| 265  |         linkat         | 0x109 |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 266  |       symlinkat        | 0x10a |       const char * oldname        |              int newdfd              |             const char * newname              |
| 267  |       readlinkat       | 0x10b |              int dfd              |           const char *path           |                   char *buf                   |
| 268  |        fchmodat        | 0x10c |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 269  |       faccessat        | 0x10d |              int dfd              |         const char *filename         |                   int mode                    |
| 270  |        pselect6        | 0x10e |                int                |               fd_set *               |                   fd_set *                    |
| 271  |         ppoll          | 0x10f |          struct pollfd *          |             unsigned int             |          struct __kernel_timespec *           |
| 272  |        unshare         | 0x110 |    unsigned long unshare_flags    |                  -                   |                       -                       |
| 273  |    set_robust_list     | 0x111 |   struct robust_list_head *head   |              size_t len              |                       -                       |
| 274  |    get_robust_list     | 0x112 |              int pid              |   struct robust_list_head head_ptr   |                size_t *len_ptr                |
| 275  |         splice         | 0x113 |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 276  |          tee           | 0x114 |             int fdin              |              int fdout               |                  size_t len                   |
| 277  |    sync_file_range     | 0x115 |              int fd               |            loff_t offset             |                 loff_t nbytes                 |
| 278  |        vmsplice        | 0x116 |              int fd               |       const struct iovec *iov        |             unsigned long nr_segs             |
| 279  |       move_pages       | 0x117 |             pid_t pid             |        unsigned long nr_pages        |               const void pages                |
| 280  |       utimensat        | 0x118 |              int dfd              |         const char *filename         |       struct __kernel_timespec *utimes        |
| 281  |      epoll_pwait       | 0x119 |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 282  |        signalfd        | 0x11a |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 283  |     timerfd_create     | 0x11b |            int clockid            |              int flags               |                       -                       |
| 284  |        eventfd         | 0x11c |        unsigned int count         |                  -                   |                       -                       |
| 285  |       fallocate        | 0x11d |              int fd               |               int mode               |                 loff_t offset                 |
| 286  |    timerfd_settime     | 0x11e |              int ufd              |              int flags               |    const struct __kernel_itimerspec *utmr     |
| 287  |    timerfd_gettime     | 0x11f |              int ufd              |   struct __kernel_itimerspec *otmr   |                       -                       |
| 288  |        accept4         | 0x120 |                int                |          struct sockaddr *           |                     int *                     |
| 289  |       signalfd4        | 0x121 |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 290  |        eventfd2        | 0x122 |        unsigned int count         |              int flags               |                       -                       |
| 291  |     epoll_create1      | 0x123 |             int flags             |                  -                   |                       -                       |
| 292  |          dup3          | 0x124 |        unsigned int oldfd         |          unsigned int newfd          |                   int flags                   |
| 293  |         pipe2          | 0x125 |            int *fildes            |              int flags               |                       -                       |
| 294  |     inotify_init1      | 0x126 |             int flags             |                  -                   |                       -                       |
| 295  |         preadv         | 0x127 |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 296  |        pwritev         | 0x128 |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 297  |   rt_tgsigqueueinfo    | 0x129 |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 298  |    perf_event_open     | 0x12a | struct perf_event_attr *attr_uptr |              pid_t pid               |                    int cpu                    |
| 299  |        recvmmsg        | 0x12b |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 300  |     fanotify_init      | 0x12c |        unsigned int flags         |      unsigned int event_f_flags      |                       -                       |
| 301  |     fanotify_mark      | 0x12d |          int fanotify_fd          |          unsigned int flags          |                   u64 mask                    |
| 302  |       prlimit64        | 0x12e |             pid_t pid             |        unsigned int resource         |        const struct rlimit64 *new_rlim        |
| 303  |   name_to_handle_at    | 0x12f |              int dfd              |           const char *name           |          struct file_handle *handle           |
| 304  |   open_by_handle_at    | 0x130 |          int mountdirfd           |      struct file_handle *handle      |                   int flags                   |
| 305  |     clock_adjtime      | 0x131 |       clockid_t which_clock       |      struct __kernel_timex *tx       |                       -                       |
| 306  |         syncfs         | 0x132 |              int fd               |                  -                   |                       -                       |
| 307  |        sendmmsg        | 0x133 |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 308  |         setns          | 0x134 |              int fd               |              int nstype              |                       -                       |
| 309  |         getcpu         | 0x135 |           unsigned *cpu           |            unsigned *node            |          struct getcpu_cache *cache           |
| 310  |    process_vm_readv    | 0x136 |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 311  |   process_vm_writev    | 0x137 |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 312  |          kcmp          | 0x138 |            pid_t pid1             |              pid_t pid2              |                   int type                    |
| 313  |      finit_module      | 0x139 |              int fd               |          const char *uargs           |                   int flags                   |
| 314  |     sched_setattr      | 0x13a |             pid_t pid             |       struct sched_attr *attr        |              unsigned int flags               |
| 315  |     sched_getattr      | 0x13b |             pid_t pid             |       struct sched_attr *attr        |               unsigned int size               |
| 316  |       renameat2        | 0x13c |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 317  |        seccomp         | 0x13d |          unsigned int op          |          unsigned int flags          |                  void *uargs                  |
| 318  |       getrandom        | 0x13e |             char *buf             |             size_t count             |              unsigned int flags               |
| 319  |      memfd_create      | 0x13f |       const char *uname_ptr       |          unsigned int flags          |                       -                       |
| 320  |    kexec_file_load     | 0x140 |           int kernel_fd           |            int initrd_fd             |           unsigned long cmdline_len           |
| 321  |          bpf           | 0x141 |              int cmd              |         union bpf_attr *attr         |               unsigned int size               |
| 322  |        execveat        | 0x142 |              int dfd              |         const char *filename         |            const char *const *argv            |
| 323  |      userfaultfd       | 0x143 |             int flags             |                  -                   |                       -                       |
| 324  |       membarrier       | 0x144 |              int cmd              |          unsigned int flags          |                  int cpu_id                   |
| 325  |         mlock2         | 0x145 |        unsigned long start        |              size_t len              |                   int flags                   |
| 326  |    copy_file_range     | 0x146 |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 327  |        preadv2         | 0x147 |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 328  |        pwritev2        | 0x148 |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 329  |     pkey_mprotect      | 0x149 |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 330  |       pkey_alloc       | 0x14a |        unsigned long flags        |        unsigned long init_val        |                       -                       |
| 331  |       pkey_free        | 0x14b |             int pkey              |                  -                   |                       -                       |
| 332  |         statx          | 0x14c |              int dfd              |           const char *path           |                unsigned flags                 |
| 333  |   *not implemented*    | 0x14d |                                   |                                      |                                               |
| 334  |   *not implemented*    | 0x14e |                                   |                                      |                                               |
| 335  |   *not implemented*    | 0x14f |                                   |                                      |                                               |
| 336  |   *not implemented*    | 0x150 |                                   |                                      |                                               |
| 337  |   *not implemented*    | 0x151 |                                   |                                      |                                               |
| 338  |   *not implemented*    | 0x152 |                                   |                                      |                                               |
| 339  |   *not implemented*    | 0x153 |                                   |                                      |                                               |
| 340  |   *not implemented*    | 0x154 |                                   |                                      |                                               |
| 341  |   *not implemented*    | 0x155 |                                   |                                      |                                               |
| 342  |   *not implemented*    | 0x156 |                                   |                                      |                                               |
| 343  |   *not implemented*    | 0x157 |                                   |                                      |                                               |
| 344  |   *not implemented*    | 0x158 |                                   |                                      |                                               |
| 345  |   *not implemented*    | 0x159 |                                   |                                      |                                               |
| 346  |   *not implemented*    | 0x15a |                                   |                                      |                                               |
| 347  |   *not implemented*    | 0x15b |                                   |                                      |                                               |
| 348  |   *not implemented*    | 0x15c |                                   |                                      |                                               |
| 349  |   *not implemented*    | 0x15d |                                   |                                      |                                               |
| 350  |   *not implemented*    | 0x15e |                                   |                                      |                                               |
| 351  |   *not implemented*    | 0x15f |                                   |                                      |                                               |
| 352  |   *not implemented*    | 0x160 |                                   |                                      |                                               |
| 353  |   *not implemented*    | 0x161 |                                   |                                      |                                               |
| 354  |   *not implemented*    | 0x162 |                                   |                                      |                                               |
| 355  |   *not implemented*    | 0x163 |                                   |                                      |                                               |
| 356  |   *not implemented*    | 0x164 |                                   |                                      |                                               |
| 357  |   *not implemented*    | 0x165 |                                   |                                      |                                               |
| 358  |   *not implemented*    | 0x166 |                                   |                                      |                                               |
| 359  |   *not implemented*    | 0x167 |                                   |                                      |                                               |
| 360  |   *not implemented*    | 0x168 |                                   |                                      |                                               |
| 361  |   *not implemented*    | 0x169 |                                   |                                      |                                               |
| 362  |   *not implemented*    | 0x16a |                                   |                                      |                                               |
| 363  |   *not implemented*    | 0x16b |                                   |                                      |                                               |
| 364  |   *not implemented*    | 0x16c |                                   |                                      |                                               |
| 365  |   *not implemented*    | 0x16d |                                   |                                      |                                               |
| 366  |   *not implemented*    | 0x16e |                                   |                                      |                                               |
| 367  |   *not implemented*    | 0x16f |                                   |                                      |                                               |
| 368  |   *not implemented*    | 0x170 |                                   |                                      |                                               |
| 369  |   *not implemented*    | 0x171 |                                   |                                      |                                               |
| 370  |   *not implemented*    | 0x172 |                                   |                                      |                                               |
| 371  |   *not implemented*    | 0x173 |                                   |                                      |                                               |
| 372  |   *not implemented*    | 0x174 |                                   |                                      |                                               |
| 373  |   *not implemented*    | 0x175 |                                   |                                      |                                               |
| 374  |   *not implemented*    | 0x176 |                                   |                                      |                                               |
| 375  |   *not implemented*    | 0x177 |                                   |                                      |                                               |
| 376  |   *not implemented*    | 0x178 |                                   |                                      |                                               |
| 377  |   *not implemented*    | 0x179 |                                   |                                      |                                               |
| 378  |   *not implemented*    | 0x17a |                                   |                                      |                                               |
| 379  |   *not implemented*    | 0x17b |                                   |                                      |                                               |
| 380  |   *not implemented*    | 0x17c |                                   |                                      |                                               |
| 381  |   *not implemented*    | 0x17d |                                   |                                      |                                               |
| 382  |   *not implemented*    | 0x17e |                                   |                                      |                                               |
| 383  |   *not implemented*    | 0x17f |                                   |                                      |                                               |
| 384  |   *not implemented*    | 0x180 |                                   |                                      |                                               |
| 385  |   *not implemented*    | 0x181 |                                   |                                      |                                               |
| 386  |   *not implemented*    | 0x182 |                                   |                                      |                                               |
| 387  |   *not implemented*    | 0x183 |                                   |                                      |                                               |
| 388  |   *not implemented*    | 0x184 |                                   |                                      |                                               |
| 389  |   *not implemented*    | 0x185 |                                   |                                      |                                               |
| 390  |   *not implemented*    | 0x186 |                                   |                                      |                                               |
| 391  |   *not implemented*    | 0x187 |                                   |                                      |                                               |
| 392  |   *not implemented*    | 0x188 |                                   |                                      |                                               |
| 393  |   *not implemented*    | 0x189 |                                   |                                      |                                               |
| 394  |   *not implemented*    | 0x18a |                                   |                                      |                                               |
| 395  |   *not implemented*    | 0x18b |                                   |                                      |                                               |
| 396  |   *not implemented*    | 0x18c |                                   |                                      |                                               |
| 397  |   *not implemented*    | 0x18d |                                   |                                      |                                               |
| 398  |   *not implemented*    | 0x18e |                                   |                                      |                                               |
| 399  |   *not implemented*    | 0x18f |                                   |                                      |                                               |
| 400  |   *not implemented*    | 0x190 |                                   |                                      |                                               |
| 401  |   *not implemented*    | 0x191 |                                   |                                      |                                               |
| 402  |   *not implemented*    | 0x192 |                                   |                                      |                                               |
| 403  |   *not implemented*    | 0x193 |                                   |                                      |                                               |
| 404  |   *not implemented*    | 0x194 |                                   |                                      |                                               |
| 405  |   *not implemented*    | 0x195 |                                   |                                      |                                               |
| 406  |   *not implemented*    | 0x196 |                                   |                                      |                                               |
| 407  |   *not implemented*    | 0x197 |                                   |                                      |                                               |
| 408  |   *not implemented*    | 0x198 |                                   |                                      |                                               |
| 409  |   *not implemented*    | 0x199 |                                   |                                      |                                               |
| 410  |   *not implemented*    | 0x19a |                                   |                                      |                                               |
| 411  |   *not implemented*    | 0x19b |                                   |                                      |                                               |
| 412  |   *not implemented*    | 0x19c |                                   |                                      |                                               |
| 413  |   *not implemented*    | 0x19d |                                   |                                      |                                               |
| 414  |   *not implemented*    | 0x19e |                                   |                                      |                                               |
| 415  |   *not implemented*    | 0x19f |                                   |                                      |                                               |
| 416  |   *not implemented*    | 0x1a0 |                                   |                                      |                                               |
| 417  |   *not implemented*    | 0x1a1 |                                   |                                      |                                               |
| 418  |   *not implemented*    | 0x1a2 |                                   |                                      |                                               |
| 419  |   *not implemented*    | 0x1a3 |                                   |                                      |                                               |
| 420  |   *not implemented*    | 0x1a4 |                                   |                                      |                                               |
| 421  |   *not implemented*    | 0x1a5 |                                   |                                      |                                               |
| 422  |   *not implemented*    | 0x1a6 |                                   |                                      |                                               |
| 423  |   *not implemented*    | 0x1a7 |                                   |                                      |                                               |
| 424  |   *not implemented*    | 0x1a8 |                                   |                                      |                                               |
| 425  |     io_uring_setup     | 0x1a9 |            u32 entries            |      struct io_uring_params *p       |                       -                       |
| 426  |     io_uring_enter     | 0x1aa |          unsigned int fd          |            u32 to_submit             |               u32 min_complete                |
| 427  |   *not implemented*    | 0x1ab |                                   |                                      |                                               |
| 428  |   *not implemented*    | 0x1ac |                                   |                                      |                                               |
| 429  |   *not implemented*    | 0x1ad |                                   |                                      |                                               |
| 430  |   *not implemented*    | 0x1ae |                                   |                                      |                                               |
| 431  |   *not implemented*    | 0x1af |                                   |                                      |                                               |
| 432  |   *not implemented*    | 0x1b0 |                                   |                                      |                                               |
| 433  |   *not implemented*    | 0x1b1 |                                   |                                      |                                               |
| 434  |   *not implemented*    | 0x1b2 |                                   |                                      |                                               |
| 435  |   *not implemented*    | 0x1b3 |                                   |                                      |                                               |
| 436  |   *not implemented*    | 0x1b4 |                                   |                                      |                                               |
| 437  |   *not implemented*    | 0x1b5 |                                   |                                      |                                               |
| 438  |   *not implemented*    | 0x1b6 |                                   |                                      |                                               |
| 439  |       faccessat2       | 0x1b7 |              int dfd              |         const char *filename         |                   int mode                    |

## x86系统调用表

| NR   |         syscall name         | %eax  |            arg0 (%ebx)            |             arg1 (%ecx)              |                  arg2 (%edx)                  |
| :--- | :--------------------------: | :---: | :-------------------------------: | :----------------------------------: | :-------------------------------------------: |
| 0    |       restart_syscall        | 0x00  |                 -                 |                  -                   |                       -                       |
| 1    |             exit             | 0x01  |          int error_code           |                  -                   |                       -                       |
| 2    |             fork             | 0x02  |                 -                 |                  -                   |                       -                       |
| 3    |             read             | 0x03  |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 4    |            write             | 0x04  |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 5    |             open             | 0x05  |       const char *filename        |              int flags               |                 umode_t mode                  |
| 6    |            close             | 0x06  |          unsigned int fd          |                  -                   |                       -                       |
| 7    |           waitpid            | 0x07  |             pid_t pid             |            int *stat_addr            |                  int options                  |
| 8    |            creat             | 0x08  |       const char *pathname        |             umode_t mode             |                       -                       |
| 9    |             link             | 0x09  |        const char *oldname        |         const char *newname          |                       -                       |
| 10   |            unlink            | 0x0a  |       const char *pathname        |                  -                   |                       -                       |
| 11   |            execve            | 0x0b  |       const char *filename        |       const char *const *argv        |            const char *const *envp            |
| 12   |            chdir             | 0x0c  |       const char *filename        |                  -                   |                       -                       |
| 13   |             time             | 0x0d  |     __kernel_old_time_t *tloc     |                  -                   |                       -                       |
| 14   |            mknod             | 0x0e  |       const char *filename        |             umode_t mode             |                 unsigned dev                  |
| 15   |            chmod             | 0x0f  |       const char *filename        |             umode_t mode             |                       -                       |
| 16   |            lchown            | 0x10  |       const char *filename        |              uid_t user              |                  gid_t group                  |
| 17   |            break             | 0x11  |                 ?                 |                  ?                   |                       ?                       |
| 18   |           oldstat            | 0x12  |                 ?                 |                  ?                   |                       ?                       |
| 19   |            lseek             | 0x13  |          unsigned int fd          |             off_t offset             |              unsigned int whence              |
| 20   |            getpid            | 0x14  |                 -                 |                  -                   |                       -                       |
| 21   |            mount             | 0x15  |          char *dev_name           |            char *dir_name            |                  char *type                   |
| 22   |            umount            | 0x16  |            char *name             |              int flags               |                       -                       |
| 23   |            setuid            | 0x17  |             uid_t uid             |                  -                   |                       -                       |
| 24   |            getuid            | 0x18  |                 -                 |                  -                   |                       -                       |
| 25   |            stime             | 0x19  |     __kernel_old_time_t *tptr     |                  -                   |                       -                       |
| 26   |            ptrace            | 0x1a  |           long request            |               long pid               |              unsigned long addr               |
| 27   |            alarm             | 0x1b  |       unsigned int seconds        |                  -                   |                       -                       |
| 28   |           oldfstat           | 0x1c  |                 ?                 |                  ?                   |                       ?                       |
| 29   |            pause             | 0x1d  |                 -                 |                  -                   |                       -                       |
| 30   |            utime             | 0x1e  |          char *filename           |        struct utimbuf *times         |                       -                       |
| 31   |             stty             | 0x1f  |                 ?                 |                  ?                   |                       ?                       |
| 32   |             gtty             | 0x20  |                 ?                 |                  ?                   |                       ?                       |
| 33   |            access            | 0x21  |       const char *filename        |               int mode               |                       -                       |
| 34   |             nice             | 0x22  |           int increment           |                  -                   |                       -                       |
| 35   |            ftime             | 0x23  |                 ?                 |                  ?                   |                       ?                       |
| 36   |             sync             | 0x24  |                 -                 |                  -                   |                       -                       |
| 37   |             kill             | 0x25  |             pid_t pid             |               int sig                |                       -                       |
| 38   |            rename            | 0x26  |        const char *oldname        |         const char *newname          |                       -                       |
| 39   |            mkdir             | 0x27  |       const char *pathname        |             umode_t mode             |                       -                       |
| 40   |            rmdir             | 0x28  |       const char *pathname        |                  -                   |                       -                       |
| 41   |             dup              | 0x29  |        unsigned int fildes        |                  -                   |                       -                       |
| 42   |             pipe             | 0x2a  |            int *fildes            |                  -                   |                       -                       |
| 43   |            times             | 0x2b  |         struct tms *tbuf          |                  -                   |                       -                       |
| 44   |             prof             | 0x2c  |                 ?                 |                  ?                   |                       ?                       |
| 45   |             brk              | 0x2d  |         unsigned long brk         |                  -                   |                       -                       |
| 46   |            setgid            | 0x2e  |             gid_t gid             |                  -                   |                       -                       |
| 47   |            getgid            | 0x2f  |                 -                 |                  -                   |                       -                       |
| 48   |            signal            | 0x30  |              int sig              |        __sighandler_t handler        |                       -                       |
| 49   |           geteuid            | 0x31  |                 -                 |                  -                   |                       -                       |
| 50   |           getegid            | 0x32  |                 -                 |                  -                   |                       -                       |
| 51   |             acct             | 0x33  |         const char *name          |                  -                   |                       -                       |
| 52   |           umount2            | 0x34  |                 ?                 |                  ?                   |                       ?                       |
| 53   |             lock             | 0x35  |                 ?                 |                  ?                   |                       ?                       |
| 54   |            ioctl             | 0x36  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 55   |            fcntl             | 0x37  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 56   |             mpx              | 0x38  |                 ?                 |                  ?                   |                       ?                       |
| 57   |           setpgid            | 0x39  |             pid_t pid             |              pid_t pgid              |                       -                       |
| 58   |            ulimit            | 0x3a  |                 ?                 |                  ?                   |                       ?                       |
| 59   |         oldolduname          | 0x3b  |                 ?                 |                  ?                   |                       ?                       |
| 60   |            umask             | 0x3c  |             int mask              |                  -                   |                       -                       |
| 61   |            chroot            | 0x3d  |       const char *filename        |                  -                   |                       -                       |
| 62   |            ustat             | 0x3e  |           unsigned dev            |          struct ustat *ubuf          |                       -                       |
| 63   |             dup2             | 0x3f  |        unsigned int oldfd         |          unsigned int newfd          |                       -                       |
| 64   |           getppid            | 0x40  |                 -                 |                  -                   |                       -                       |
| 65   |           getpgrp            | 0x41  |                 -                 |                  -                   |                       -                       |
| 66   |            setsid            | 0x42  |                 -                 |                  -                   |                       -                       |
| 67   |          sigaction           | 0x43  |                int                |     const struct old_sigaction *     |            struct old_sigaction *             |
| 68   |           sgetmask           | 0x44  |                 -                 |                  -                   |                       -                       |
| 69   |           ssetmask           | 0x45  |            int newmask            |                  -                   |                       -                       |
| 70   |           setreuid           | 0x46  |            uid_t ruid             |              uid_t euid              |                       -                       |
| 71   |           setregid           | 0x47  |            gid_t rgid             |              gid_t egid              |                       -                       |
| 72   |          sigsuspend          | 0x48  |            int unused1            |             int unused2              |               old_sigset_t mask               |
| 73   |          sigpending          | 0x49  |        old_sigset_t *uset         |                  -                   |                       -                       |
| 74   |         sethostname          | 0x4a  |            char *name             |               int len                |                       -                       |
| 75   |          setrlimit           | 0x4b  |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 76   |          getrlimit           | 0x4c  |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 77   |          getrusage           | 0x4d  |              int who              |          struct rusage *ru           |                       -                       |
| 78   |         gettimeofday         | 0x4e  |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 79   |         settimeofday         | 0x4f  |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 80   |          getgroups           | 0x50  |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 81   |          setgroups           | 0x51  |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 82   |            select            | 0x52  |               int n               |             fd_set *inp              |                 fd_set *outp                  |
| 83   |           symlink            | 0x53  |          const char *old          |           const char *new            |                       -                       |
| 84   |           oldlstat           | 0x54  |                 ?                 |                  ?                   |                       ?                       |
| 85   |           readlink           | 0x55  |         const char *path          |              char *buf               |                  int bufsiz                   |
| 86   |            uselib            | 0x56  |        const char *library        |                  -                   |                       -                       |
| 87   |            swapon            | 0x57  |      const char *specialfile      |            int swap_flags            |                       -                       |
| 88   |            reboot            | 0x58  |            int magic1             |              int magic2              |               unsigned int cmd                |
| 89   |           readdir            | 0x59  |                 ?                 |                  ?                   |                       ?                       |
| 90   |             mmap             | 0x5a  |                 ?                 |                  ?                   |                       ?                       |
| 91   |            munmap            | 0x5b  |        unsigned long addr         |              size_t len              |                       -                       |
| 92   |           truncate           | 0x5c  |         const char *path          |             long length              |                       -                       |
| 93   |          ftruncate           | 0x5d  |          unsigned int fd          |         unsigned long length         |                       -                       |
| 94   |            fchmod            | 0x5e  |          unsigned int fd          |             umode_t mode             |                       -                       |
| 95   |            fchown            | 0x5f  |          unsigned int fd          |              uid_t user              |                  gid_t group                  |
| 96   |         getpriority          | 0x60  |             int which             |               int who                |                       -                       |
| 97   |         setpriority          | 0x61  |             int which             |               int who                |                  int niceval                  |
| 98   |            profil            | 0x62  |                 ?                 |                  ?                   |                       ?                       |
| 99   |            statfs            | 0x63  |         const char * path         |          struct statfs *buf          |                       -                       |
| 100  |           fstatfs            | 0x64  |          unsigned int fd          |          struct statfs *buf          |                       -                       |
| 101  |            ioperm            | 0x65  |        unsigned long from         |          unsigned long num           |                    int on                     |
| 102  |          socketcall          | 0x66  |             int call              |         unsigned long *args          |                       -                       |
| 103  |            syslog            | 0x67  |             int type              |              char *buf               |                    int len                    |
| 104  |          setitimer           | 0x68  |             int which             | struct __kernel_old_itimerval *value |     struct __kernel_old_itimerval *ovalue     |
| 105  |          getitimer           | 0x69  |             int which             | struct __kernel_old_itimerval *value |                       -                       |
| 106  |             stat             | 0x6a  |       const char *filename        |  struct __old_kernel_stat *statbuf   |                       -                       |
| 107  |            lstat             | 0x6b  |       const char *filename        |  struct __old_kernel_stat *statbuf   |                       -                       |
| 108  |            fstat             | 0x6c  |          unsigned int fd          |  struct __old_kernel_stat *statbuf   |                       -                       |
| 109  |           olduname           | 0x6d  |      struct oldold_utsname *      |                  -                   |                       -                       |
| 110  |             iopl             | 0x6e  |                 ?                 |                  ?                   |                       ?                       |
| 111  |           vhangup            | 0x6f  |                 -                 |                  -                   |                       -                       |
| 112  |             idle             | 0x70  |                 ?                 |                  ?                   |                       ?                       |
| 113  |           vm86old            | 0x71  |                 ?                 |                  ?                   |                       ?                       |
| 114  |            wait4             | 0x72  |             pid_t pid             |            int *stat_addr            |                  int options                  |
| 115  |           swapoff            | 0x73  |      const char *specialfile      |                  -                   |                       -                       |
| 116  |           sysinfo            | 0x74  |       struct sysinfo *info        |                  -                   |                       -                       |
| 117  |             ipc              | 0x75  |         unsigned int call         |              int first               |             unsigned long second              |
| 118  |            fsync             | 0x76  |          unsigned int fd          |                  -                   |                       -                       |
| 119  |          sigreturn           | 0x77  |                 ?                 |                  ?                   |                       ?                       |
| 120  |            clone             | 0x78  |           unsigned long           |            unsigned long             |                     int *                     |
| 121  |        setdomainname         | 0x79  |            char *name             |               int len                |                       -                       |
| 122  |            uname             | 0x7a  |       struct old_utsname *        |                  -                   |                       -                       |
| 123  |          modify_ldt          | 0x7b  |                 ?                 |                  ?                   |                       ?                       |
| 124  |           adjtimex           | 0x7c  |   struct __kernel_timex *txc_p    |                  -                   |                       -                       |
| 125  |           mprotect           | 0x7d  |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 126  |         sigprocmask          | 0x7e  |              int how              |          old_sigset_t *set           |              old_sigset_t *oset               |
| 127  |        create_module         | 0x7f  |                 ?                 |                  ?                   |                       ?                       |
| 128  |         init_module          | 0x80  |            void *umod             |          unsigned long len           |               const char *uargs               |
| 129  |        delete_module         | 0x81  |       const char *name_user       |          unsigned int flags          |                       -                       |
| 130  |       get_kernel_syms        | 0x82  |                 ?                 |                  ?                   |                       ?                       |
| 131  |           quotactl           | 0x83  |         unsigned int cmd          |         const char *special          |                   qid_t id                    |
| 132  |           getpgid            | 0x84  |             pid_t pid             |                  -                   |                       -                       |
| 133  |            fchdir            | 0x85  |          unsigned int fd          |                  -                   |                       -                       |
| 134  |           bdflush            | 0x86  |                 ?                 |                  ?                   |                       ?                       |
| 135  |            sysfs             | 0x87  |            int option             |          unsigned long arg1          |              unsigned long arg2               |
| 136  |         personality          | 0x88  |     unsigned int personality      |                  -                   |                       -                       |
| 137  |         afs_syscall          | 0x89  |                 ?                 |                  ?                   |                       ?                       |
| 138  |           setfsuid           | 0x8a  |             uid_t uid             |                  -                   |                       -                       |
| 139  |           setfsgid           | 0x8b  |             gid_t gid             |                  -                   |                       -                       |
| 140  |           _llseek            | 0x8c  |                 ?                 |                  ?                   |                       ?                       |
| 141  |           getdents           | 0x8d  |          unsigned int fd          |     struct linux_dirent *dirent      |              unsigned int count               |
| 142  |          _newselect          | 0x8e  |                 ?                 |                  ?                   |                       ?                       |
| 143  |            flock             | 0x8f  |          unsigned int fd          |           unsigned int cmd           |                       -                       |
| 144  |            msync             | 0x90  |        unsigned long start        |              size_t len              |                   int flags                   |
| 145  |            readv             | 0x91  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 146  |            writev            | 0x92  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 147  |            getsid            | 0x93  |             pid_t pid             |                  -                   |                       -                       |
| 148  |          fdatasync           | 0x94  |          unsigned int fd          |                  -                   |                       -                       |
| 149  |           _sysctl            | 0x95  |                 ?                 |                  ?                   |                       ?                       |
| 150  |            mlock             | 0x96  |        unsigned long start        |              size_t len              |                       -                       |
| 151  |           munlock            | 0x97  |        unsigned long start        |              size_t len              |                       -                       |
| 152  |           mlockall           | 0x98  |             int flags             |                  -                   |                       -                       |
| 153  |          munlockall          | 0x99  |                 -                 |                  -                   |                       -                       |
| 154  |        sched_setparam        | 0x9a  |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 155  |        sched_getparam        | 0x9b  |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 156  |      sched_setscheduler      | 0x9c  |             pid_t pid             |              int policy              |           struct sched_param *param           |
| 157  |      sched_getscheduler      | 0x9d  |             pid_t pid             |                  -                   |                       -                       |
| 158  |         sched_yield          | 0x9e  |                 -                 |                  -                   |                       -                       |
| 159  |    sched_get_priority_max    | 0x9f  |            int policy             |                  -                   |                       -                       |
| 160  |    sched_get_priority_min    | 0xa0  |            int policy             |                  -                   |                       -                       |
| 161  |    sched_rr_get_interval     | 0xa1  |             pid_t pid             |  struct __kernel_timespec *interval  |                       -                       |
| 162  |          nanosleep           | 0xa2  |  struct __kernel_timespec *rqtp   |    struct __kernel_timespec *rmtp    |                       -                       |
| 163  |            mremap            | 0xa3  |        unsigned long addr         |        unsigned long old_len         |             unsigned long new_len             |
| 164  |          setresuid           | 0xa4  |            uid_t ruid             |              uid_t euid              |                  uid_t suid                   |
| 165  |          getresuid           | 0xa5  |            uid_t *ruid            |             uid_t *euid              |                  uid_t *suid                  |
| 166  |             vm86             | 0xa6  |                 ?                 |                  ?                   |                       ?                       |
| 167  |         query_module         | 0xa7  |                 ?                 |                  ?                   |                       ?                       |
| 168  |             poll             | 0xa8  |        struct pollfd *ufds        |          unsigned int nfds           |                  int timeout                  |
| 169  |          nfsservctl          | 0xa9  |                 ?                 |                  ?                   |                       ?                       |
| 170  |          setresgid           | 0xaa  |            gid_t rgid             |              gid_t egid              |                  gid_t sgid                   |
| 171  |          getresgid           | 0xab  |            gid_t *rgid            |             gid_t *egid              |                  gid_t *sgid                  |
| 172  |            prctl             | 0xac  |            int option             |          unsigned long arg2          |              unsigned long arg3               |
| 173  |         rt_sigreturn         | 0xad  |                 ?                 |                  ?                   |                       ?                       |
| 174  |         rt_sigaction         | 0xae  |                int                |       const struct sigaction *       |              struct sigaction *               |
| 175  |        rt_sigprocmask        | 0xaf  |              int how              |            sigset_t *set             |                sigset_t *oset                 |
| 176  |        rt_sigpending         | 0xb0  |           sigset_t *set           |          size_t sigsetsize           |                       -                       |
| 177  |       rt_sigtimedwait        | 0xb1  |      const sigset_t *uthese       |           siginfo_t *uinfo           |      const struct __kernel_timespec *uts      |
| 178  |       rt_sigqueueinfo        | 0xb2  |             pid_t pid             |               int sig                |               siginfo_t *uinfo                |
| 179  |        rt_sigsuspend         | 0xb3  |         sigset_t *unewset         |          size_t sigsetsize           |                       -                       |
| 180  |           pread64            | 0xb4  |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 181  |           pwrite64           | 0xb5  |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 182  |            chown             | 0xb6  |       const char *filename        |              uid_t user              |                  gid_t group                  |
| 183  |            getcwd            | 0xb7  |             char *buf             |          unsigned long size          |                       -                       |
| 184  |            capget            | 0xb8  |     cap_user_header_t header      |       cap_user_data_t dataptr        |                       -                       |
| 185  |            capset            | 0xb9  |     cap_user_header_t header      |      const cap_user_data_t data      |                       -                       |
| 186  |         sigaltstack          | 0xba  |   const struct sigaltstack *uss   |       struct sigaltstack *uoss       |                       -                       |
| 187  |           sendfile           | 0xbb  |            int out_fd             |              int in_fd               |                 off_t *offset                 |
| 188  |           getpmsg            | 0xbc  |                 ?                 |                  ?                   |                       ?                       |
| 189  |           putpmsg            | 0xbd  |                 ?                 |                  ?                   |                       ?                       |
| 190  |            vfork             | 0xbe  |                 -                 |                  -                   |                       -                       |
| 191  |          ugetrlimit          | 0xbf  |                 ?                 |                  ?                   |                       ?                       |
| 192  |            mmap2             | 0xc0  |                 ?                 |                  ?                   |                       ?                       |
| 193  |          truncate64          | 0xc1  |         const char *path          |            loff_t length             |                       -                       |
| 194  |         ftruncate64          | 0xc2  |          unsigned int fd          |            loff_t length             |                       -                       |
| 195  |            stat64            | 0xc3  |       const char *filename        |        struct stat64 *statbuf        |                       -                       |
| 196  |           lstat64            | 0xc4  |       const char *filename        |        struct stat64 *statbuf        |                       -                       |
| 197  |           fstat64            | 0xc5  |         unsigned long fd          |        struct stat64 *statbuf        |                       -                       |
| 198  |           lchown32           | 0xc6  |                 ?                 |                  ?                   |                       ?                       |
| 199  |           getuid32           | 0xc7  |                 ?                 |                  ?                   |                       ?                       |
| 200  |           getgid32           | 0xc8  |                 ?                 |                  ?                   |                       ?                       |
| 201  |          geteuid32           | 0xc9  |                 ?                 |                  ?                   |                       ?                       |
| 202  |          getegid32           | 0xca  |                 ?                 |                  ?                   |                       ?                       |
| 203  |          setreuid32          | 0xcb  |                 ?                 |                  ?                   |                       ?                       |
| 204  |          setregid32          | 0xcc  |                 ?                 |                  ?                   |                       ?                       |
| 205  |         getgroups32          | 0xcd  |                 ?                 |                  ?                   |                       ?                       |
| 206  |         setgroups32          | 0xce  |                 ?                 |                  ?                   |                       ?                       |
| 207  |           fchown32           | 0xcf  |                 ?                 |                  ?                   |                       ?                       |
| 208  |         setresuid32          | 0xd0  |                 ?                 |                  ?                   |                       ?                       |
| 209  |         getresuid32          | 0xd1  |                 ?                 |                  ?                   |                       ?                       |
| 210  |         setresgid32          | 0xd2  |                 ?                 |                  ?                   |                       ?                       |
| 211  |         getresgid32          | 0xd3  |                 ?                 |                  ?                   |                       ?                       |
| 212  |           chown32            | 0xd4  |                 ?                 |                  ?                   |                       ?                       |
| 213  |           setuid32           | 0xd5  |                 ?                 |                  ?                   |                       ?                       |
| 214  |           setgid32           | 0xd6  |                 ?                 |                  ?                   |                       ?                       |
| 215  |          setfsuid32          | 0xd7  |                 ?                 |                  ?                   |                       ?                       |
| 216  |          setfsgid32          | 0xd8  |                 ?                 |                  ?                   |                       ?                       |
| 217  |          pivot_root          | 0xd9  |       const char *new_root        |         const char *put_old          |                       -                       |
| 218  |           mincore            | 0xda  |        unsigned long start        |              size_t len              |              unsigned char * vec              |
| 219  |           madvise            | 0xdb  |        unsigned long start        |              size_t len              |                 int behavior                  |
| 220  |          getdents64          | 0xdc  |          unsigned int fd          |    struct linux_dirent64 *dirent     |              unsigned int count               |
| 221  |           fcntl64            | 0xdd  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 222  |      *not implemented*       | 0xde  |                                   |                                      |                                               |
| 223  |      *not implemented*       | 0xdf  |                                   |                                      |                                               |
| 224  |            gettid            | 0xe0  |                 -                 |                  -                   |                       -                       |
| 225  |          readahead           | 0xe1  |              int fd               |            loff_t offset             |                 size_t count                  |
| 226  |           setxattr           | 0xe2  |         const char *path          |           const char *name           |               const void *value               |
| 227  |          lsetxattr           | 0xe3  |         const char *path          |           const char *name           |               const void *value               |
| 228  |          fsetxattr           | 0xe4  |              int fd               |           const char *name           |               const void *value               |
| 229  |           getxattr           | 0xe5  |         const char *path          |           const char *name           |                  void *value                  |
| 230  |          lgetxattr           | 0xe6  |         const char *path          |           const char *name           |                  void *value                  |
| 231  |          fgetxattr           | 0xe7  |              int fd               |           const char *name           |                  void *value                  |
| 232  |          listxattr           | 0xe8  |         const char *path          |              char *list              |                  size_t size                  |
| 233  |          llistxattr          | 0xe9  |         const char *path          |              char *list              |                  size_t size                  |
| 234  |          flistxattr          | 0xea  |              int fd               |              char *list              |                  size_t size                  |
| 235  |         removexattr          | 0xeb  |         const char *path          |           const char *name           |                       -                       |
| 236  |         lremovexattr         | 0xec  |         const char *path          |           const char *name           |                       -                       |
| 237  |         fremovexattr         | 0xed  |              int fd               |           const char *name           |                       -                       |
| 238  |            tkill             | 0xee  |             pid_t pid             |               int sig                |                       -                       |
| 239  |          sendfile64          | 0xef  |            int out_fd             |              int in_fd               |                loff_t *offset                 |
| 240  |            futex             | 0xf0  |            u32 *uaddr             |                int op                |                    u32 val                    |
| 241  |      sched_setaffinity       | 0xf1  |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 242  |      sched_getaffinity       | 0xf2  |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 243  |       set_thread_area        | 0xf3  |                 ?                 |                  ?                   |                       ?                       |
| 244  |       get_thread_area        | 0xf4  |                 ?                 |                  ?                   |                       ?                       |
| 245  |           io_setup           | 0xf5  |         unsigned nr_reqs          |          aio_context_t *ctx          |                       -                       |
| 246  |          io_destroy          | 0xf6  |         aio_context_t ctx         |                  -                   |                       -                       |
| 247  |         io_getevents         | 0xf7  |       aio_context_t ctx_id        |             long min_nr              |                    long nr                    |
| 248  |          io_submit           | 0xf8  |           aio_context_t           |                 long                 |                  struct iocb                  |
| 249  |          io_cancel           | 0xf9  |       aio_context_t ctx_id        |          struct iocb *iocb           |            struct io_event *result            |
| 250  |          fadvise64           | 0xfa  |              int fd               |            loff_t offset             |                  size_t len                   |
| 251  |      *not implemented*       | 0xfb  |                                   |                                      |                                               |
| 252  |          exit_group          | 0xfc  |          int error_code           |                  -                   |                       -                       |
| 253  |        lookup_dcookie        | 0xfd  |           u64 cookie64            |              char *buf               |                  size_t len                   |
| 254  |         epoll_create         | 0xfe  |             int size              |                  -                   |                       -                       |
| 255  |          epoll_ctl           | 0xff  |             int epfd              |                int op                |                    int fd                     |
| 256  |          epoll_wait          | 0x100 |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 257  |       remap_file_pages       | 0x101 |        unsigned long start        |          unsigned long size          |              unsigned long prot               |
| 258  |       set_tid_address        | 0x102 |            int *tidptr            |                  -                   |                       -                       |
| 259  |         timer_create         | 0x103 |       clockid_t which_clock       |  struct sigevent *timer_event_spec   |          timer_t * created_timer_id           |
| 260  |        timer_settime         | 0x104 |         timer_t timer_id          |              int flags               | const struct __kernel_itimerspec *new_setting |
| 261  |        timer_gettime         | 0x105 |         timer_t timer_id          | struct __kernel_itimerspec *setting  |                       -                       |
| 262  |       timer_getoverrun       | 0x106 |         timer_t timer_id          |                  -                   |                       -                       |
| 263  |         timer_delete         | 0x107 |         timer_t timer_id          |                  -                   |                       -                       |
| 264  |        clock_settime         | 0x108 |       clockid_t which_clock       |  const struct __kernel_timespec *tp  |                       -                       |
| 265  |        clock_gettime         | 0x109 |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 266  |         clock_getres         | 0x10a |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 267  |       clock_nanosleep        | 0x10b |       clockid_t which_clock       |              int flags               |     const struct __kernel_timespec *rqtp      |
| 268  |           statfs64           | 0x10c |         const char *path          |              size_t sz               |             struct statfs64 *buf              |
| 269  |          fstatfs64           | 0x10d |          unsigned int fd          |              size_t sz               |             struct statfs64 *buf              |
| 270  |            tgkill            | 0x10e |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 271  |            utimes            | 0x10f |          char *filename           | struct __kernel_old_timeval *utimes  |                       -                       |
| 272  |         fadvise64_64         | 0x110 |              int fd               |            loff_t offset             |                  loff_t len                   |
| 273  |           vserver            | 0x111 |                 ?                 |                  ?                   |                       ?                       |
| 274  |            mbind             | 0x112 |        unsigned long start        |          unsigned long len           |              unsigned long mode               |
| 275  |        get_mempolicy         | 0x113 |            int *policy            |         unsigned long *nmask         |             unsigned long maxnode             |
| 276  |        set_mempolicy         | 0x114 |             int mode              |      const unsigned long *nmask      |             unsigned long maxnode             |
| 277  |           mq_open            | 0x115 |         const char *name          |              int oflag               |                 umode_t mode                  |
| 278  |          mq_unlink           | 0x116 |         const char *name          |                  -                   |                       -                       |
| 279  |         mq_timedsend         | 0x117 |            mqd_t mqdes            |         const char *msg_ptr          |                size_t msg_len                 |
| 280  |       mq_timedreceive        | 0x118 |            mqd_t mqdes            |            char *msg_ptr             |                size_t msg_len                 |
| 281  |          mq_notify           | 0x119 |            mqd_t mqdes            | const struct sigevent *notification  |                       -                       |
| 282  |        mq_getsetattr         | 0x11a |            mqd_t mqdes            |     const struct mq_attr *mqstat     |            struct mq_attr *omqstat            |
| 283  |          kexec_load          | 0x11b |        unsigned long entry        |      unsigned long nr_segments       |        struct kexec_segment *segments         |
| 284  |            waitid            | 0x11c |             int which             |              pid_t pid               |             struct siginfo *infop             |
| 285  |      *not implemented*       | 0x11d |                                   |                                      |                                               |
| 286  |           add_key            | 0x11e |         const char *_type         |       const char *_description       |             const void *_payload              |
| 287  |         request_key          | 0x11f |         const char *_type         |       const char *_description       |           const char *_callout_info           |
| 288  |            keyctl            | 0x120 |              int cmd              |          unsigned long arg2          |              unsigned long arg3               |
| 289  |          ioprio_set          | 0x121 |             int which             |               int who                |                  int ioprio                   |
| 290  |          ioprio_get          | 0x122 |             int which             |               int who                |                       -                       |
| 291  |         inotify_init         | 0x123 |                 -                 |                  -                   |                       -                       |
| 292  |      inotify_add_watch       | 0x124 |              int fd               |           const char *path           |                   u32 mask                    |
| 293  |       inotify_rm_watch       | 0x125 |              int fd               |               __s32 wd               |                       -                       |
| 294  |        migrate_pages         | 0x126 |             pid_t pid             |        unsigned long maxnode         |           const unsigned long *from           |
| 295  |            openat            | 0x127 |              int dfd              |         const char *filename         |                   int flags                   |
| 296  |           mkdirat            | 0x128 |              int dfd              |        const char * pathname         |                 umode_t mode                  |
| 297  |           mknodat            | 0x129 |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 298  |           fchownat           | 0x12a |              int dfd              |         const char *filename         |                  uid_t user                   |
| 299  |          futimesat           | 0x12b |              int dfd              |         const char *filename         |      struct __kernel_old_timeval *utimes      |
| 300  |          fstatat64           | 0x12c |              int dfd              |         const char *filename         |            struct stat64 *statbuf             |
| 301  |           unlinkat           | 0x12d |              int dfd              |        const char * pathname         |                   int flag                    |
| 302  |           renameat           | 0x12e |            int olddfd             |         const char * oldname         |                  int newdfd                   |
| 303  |            linkat            | 0x12f |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 304  |          symlinkat           | 0x130 |       const char * oldname        |              int newdfd              |             const char * newname              |
| 305  |          readlinkat          | 0x131 |              int dfd              |           const char *path           |                   char *buf                   |
| 306  |           fchmodat           | 0x132 |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 307  |          faccessat           | 0x133 |              int dfd              |         const char *filename         |                   int mode                    |
| 308  |           pselect6           | 0x134 |                int                |               fd_set *               |                   fd_set *                    |
| 309  |            ppoll             | 0x135 |          struct pollfd *          |             unsigned int             |          struct __kernel_timespec *           |
| 310  |           unshare            | 0x136 |    unsigned long unshare_flags    |                  -                   |                       -                       |
| 311  |       set_robust_list        | 0x137 |   struct robust_list_head *head   |              size_t len              |                       -                       |
| 312  |       get_robust_list        | 0x138 |              int pid              |   struct robust_list_head head_ptr   |                size_t *len_ptr                |
| 313  |            splice            | 0x139 |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 314  |       sync_file_range        | 0x13a |              int fd               |            loff_t offset             |                 loff_t nbytes                 |
| 315  |             tee              | 0x13b |             int fdin              |              int fdout               |                  size_t len                   |
| 316  |           vmsplice           | 0x13c |              int fd               |       const struct iovec *iov        |             unsigned long nr_segs             |
| 317  |          move_pages          | 0x13d |             pid_t pid             |        unsigned long nr_pages        |               const void pages                |
| 318  |            getcpu            | 0x13e |           unsigned *cpu           |            unsigned *node            |          struct getcpu_cache *cache           |
| 319  |         epoll_pwait          | 0x13f |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 320  |          utimensat           | 0x140 |              int dfd              |         const char *filename         |       struct __kernel_timespec *utimes        |
| 321  |           signalfd           | 0x141 |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 322  |        timerfd_create        | 0x142 |            int clockid            |              int flags               |                       -                       |
| 323  |           eventfd            | 0x143 |        unsigned int count         |                  -                   |                       -                       |
| 324  |          fallocate           | 0x144 |              int fd               |               int mode               |                 loff_t offset                 |
| 325  |       timerfd_settime        | 0x145 |              int ufd              |              int flags               |    const struct __kernel_itimerspec *utmr     |
| 326  |       timerfd_gettime        | 0x146 |              int ufd              |   struct __kernel_itimerspec *otmr   |                       -                       |
| 327  |          signalfd4           | 0x147 |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 328  |           eventfd2           | 0x148 |        unsigned int count         |              int flags               |                       -                       |
| 329  |        epoll_create1         | 0x149 |             int flags             |                  -                   |                       -                       |
| 330  |             dup3             | 0x14a |        unsigned int oldfd         |          unsigned int newfd          |                   int flags                   |
| 331  |            pipe2             | 0x14b |            int *fildes            |              int flags               |                       -                       |
| 332  |        inotify_init1         | 0x14c |             int flags             |                  -                   |                       -                       |
| 333  |            preadv            | 0x14d |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 334  |           pwritev            | 0x14e |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 335  |      rt_tgsigqueueinfo       | 0x14f |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 336  |       perf_event_open        | 0x150 | struct perf_event_attr *attr_uptr |              pid_t pid               |                    int cpu                    |
| 337  |           recvmmsg           | 0x151 |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 338  |        fanotify_init         | 0x152 |        unsigned int flags         |      unsigned int event_f_flags      |                       -                       |
| 339  |        fanotify_mark         | 0x153 |          int fanotify_fd          |          unsigned int flags          |                   u64 mask                    |
| 340  |          prlimit64           | 0x154 |             pid_t pid             |        unsigned int resource         |        const struct rlimit64 *new_rlim        |
| 341  |      name_to_handle_at       | 0x155 |              int dfd              |           const char *name           |          struct file_handle *handle           |
| 342  |      open_by_handle_at       | 0x156 |          int mountdirfd           |      struct file_handle *handle      |                   int flags                   |
| 343  |        clock_adjtime         | 0x157 |       clockid_t which_clock       |      struct __kernel_timex *tx       |                       -                       |
| 344  |            syncfs            | 0x158 |              int fd               |                  -                   |                       -                       |
| 345  |           sendmmsg           | 0x159 |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 346  |            setns             | 0x15a |              int fd               |              int nstype              |                       -                       |
| 347  |       process_vm_readv       | 0x15b |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 348  |      process_vm_writev       | 0x15c |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 349  |             kcmp             | 0x15d |            pid_t pid1             |              pid_t pid2              |                   int type                    |
| 350  |         finit_module         | 0x15e |              int fd               |          const char *uargs           |                   int flags                   |
| 351  |        sched_setattr         | 0x15f |             pid_t pid             |       struct sched_attr *attr        |              unsigned int flags               |
| 352  |        sched_getattr         | 0x160 |             pid_t pid             |       struct sched_attr *attr        |               unsigned int size               |
| 353  |          renameat2           | 0x161 |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 354  |           seccomp            | 0x162 |          unsigned int op          |          unsigned int flags          |                  void *uargs                  |
| 355  |          getrandom           | 0x163 |             char *buf             |             size_t count             |              unsigned int flags               |
| 356  |         memfd_create         | 0x164 |       const char *uname_ptr       |          unsigned int flags          |                       -                       |
| 357  |             bpf              | 0x165 |              int cmd              |         union bpf_attr *attr         |               unsigned int size               |
| 358  |           execveat           | 0x166 |              int dfd              |         const char *filename         |            const char *const *argv            |
| 359  |            socket            | 0x167 |                int                |                 int                  |                      int                      |
| 360  |          socketpair          | 0x168 |                int                |                 int                  |                      int                      |
| 361  |             bind             | 0x169 |                int                |          struct sockaddr *           |                      int                      |
| 362  |           connect            | 0x16a |                int                |          struct sockaddr *           |                      int                      |
| 363  |            listen            | 0x16b |                int                |                 int                  |                       -                       |
| 364  |           accept4            | 0x16c |                int                |          struct sockaddr *           |                     int *                     |
| 365  |          getsockopt          | 0x16d |              int fd               |              int level               |                  int optname                  |
| 366  |          setsockopt          | 0x16e |              int fd               |              int level               |                  int optname                  |
| 367  |         getsockname          | 0x16f |                int                |          struct sockaddr *           |                     int *                     |
| 368  |         getpeername          | 0x170 |                int                |          struct sockaddr *           |                     int *                     |
| 369  |            sendto            | 0x171 |                int                |                void *                |                    size_t                     |
| 370  |           sendmsg            | 0x172 |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 371  |           recvfrom           | 0x173 |                int                |                void *                |                    size_t                     |
| 372  |           recvmsg            | 0x174 |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 373  |           shutdown           | 0x175 |                int                |                 int                  |                       -                       |
| 374  |         userfaultfd          | 0x176 |             int flags             |                  -                   |                       -                       |
| 375  |          membarrier          | 0x177 |              int cmd              |          unsigned int flags          |                  int cpu_id                   |
| 376  |            mlock2            | 0x178 |        unsigned long start        |              size_t len              |                   int flags                   |
| 377  |       copy_file_range        | 0x179 |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 378  |           preadv2            | 0x17a |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 379  |           pwritev2           | 0x17b |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 380  |        pkey_mprotect         | 0x17c |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 381  |          pkey_alloc          | 0x17d |        unsigned long flags        |        unsigned long init_val        |                       -                       |
| 382  |          pkey_free           | 0x17e |             int pkey              |                  -                   |                       -                       |
| 383  |            statx             | 0x17f |              int dfd              |           const char *path           |                unsigned flags                 |
| 384  |          arch_prctl          | 0x180 |                 ?                 |                  ?                   |                       ?                       |
| 385  |      *not implemented*       | 0x181 |                                   |                                      |                                               |
| 386  |      *not implemented*       | 0x182 |                                   |                                      |                                               |
| 387  |      *not implemented*       | 0x183 |                                   |                                      |                                               |
| 388  |      *not implemented*       | 0x184 |                                   |                                      |                                               |
| 389  |      *not implemented*       | 0x185 |                                   |                                      |                                               |
| 390  |      *not implemented*       | 0x186 |                                   |                                      |                                               |
| 391  |      *not implemented*       | 0x187 |                                   |                                      |                                               |
| 392  |      *not implemented*       | 0x188 |                                   |                                      |                                               |
| 393  |      *not implemented*       | 0x189 |                                   |                                      |                                               |
| 394  |      *not implemented*       | 0x18a |                                   |                                      |                                               |
| 395  |      *not implemented*       | 0x18b |                                   |                                      |                                               |
| 396  |      *not implemented*       | 0x18c |                                   |                                      |                                               |
| 397  |      *not implemented*       | 0x18d |                                   |                                      |                                               |
| 398  |      *not implemented*       | 0x18e |                                   |                                      |                                               |
| 399  |      *not implemented*       | 0x18f |                                   |                                      |                                               |
| 400  |      *not implemented*       | 0x190 |                                   |                                      |                                               |
| 401  |      *not implemented*       | 0x191 |                                   |                                      |                                               |
| 402  |      *not implemented*       | 0x192 |                                   |                                      |                                               |
| 403  |       clock_gettime64        | 0x193 |                 ?                 |                  ?                   |                       ?                       |
| 404  |       clock_settime64        | 0x194 |                 ?                 |                  ?                   |                       ?                       |
| 405  |       clock_adjtime64        | 0x195 |                 ?                 |                  ?                   |                       ?                       |
| 406  |     clock_getres_time64      | 0x196 |                 ?                 |                  ?                   |                       ?                       |
| 407  |    clock_nanosleep_time64    | 0x197 |                 ?                 |                  ?                   |                       ?                       |
| 408  |       timer_gettime64        | 0x198 |                 ?                 |                  ?                   |                       ?                       |
| 409  |       timer_settime64        | 0x199 |                 ?                 |                  ?                   |                       ?                       |
| 410  |      timerfd_gettime64       | 0x19a |                 ?                 |                  ?                   |                       ?                       |
| 411  |      timerfd_settime64       | 0x19b |                 ?                 |                  ?                   |                       ?                       |
| 412  |       utimensat_time64       | 0x19c |                 ?                 |                  ?                   |                       ?                       |
| 413  |       pselect6_time64        | 0x19d |                 ?                 |                  ?                   |                       ?                       |
| 414  |         ppoll_time64         | 0x19e |                 ?                 |                  ?                   |                       ?                       |
| 415  |      *not implemented*       | 0x19f |                                   |                                      |                                               |
| 416  |     io_pgetevents_time64     | 0x1a0 |                 ?                 |                  ?                   |                       ?                       |
| 417  |       recvmmsg_time64        | 0x1a1 |                 ?                 |                  ?                   |                       ?                       |
| 418  |     mq_timedsend_time64      | 0x1a2 |                 ?                 |                  ?                   |                       ?                       |
| 419  |    mq_timedreceive_time64    | 0x1a3 |                 ?                 |                  ?                   |                       ?                       |
| 420  |      semtimedop_time64       | 0x1a4 |                 ?                 |                  ?                   |                       ?                       |
| 421  |    rt_sigtimedwait_time64    | 0x1a5 |                 ?                 |                  ?                   |                       ?                       |
| 422  |         futex_time64         | 0x1a6 |                 ?                 |                  ?                   |                       ?                       |
| 423  | sched_rr_get_interval_time64 | 0x1a7 |                 ?                 |                  ?                   |                       ?                       |
| 424  |      *not implemented*       | 0x1a8 |                                   |                                      |                                               |
| 425  |        io_uring_setup        | 0x1a9 |            u32 entries            |      struct io_uring_params *p       |                       -                       |
| 426  |        io_uring_enter        | 0x1aa |          unsigned int fd          |            u32 to_submit             |               u32 min_complete                |
| 427  |      *not implemented*       | 0x1ab |                                   |                                      |                                               |
| 428  |      *not implemented*       | 0x1ac |                                   |                                      |                                               |
| 429  |      *not implemented*       | 0x1ad |                                   |                                      |                                               |
| 430  |      *not implemented*       | 0x1ae |                                   |                                      |                                               |
| 431  |      *not implemented*       | 0x1af |                                   |                                      |                                               |
| 432  |      *not implemented*       | 0x1b0 |                                   |                                      |                                               |
| 433  |      *not implemented*       | 0x1b1 |                                   |                                      |                                               |
| 434  |      *not implemented*       | 0x1b2 |                                   |                                      |                                               |
| 435  |      *not implemented*       | 0x1b3 |                                   |                                      |                                               |
| 436  |      *not implemented*       | 0x1b4 |                                   |                                      |                                               |
| 437  |      *not implemented*       | 0x1b5 |                                   |                                      |                                               |
| 438  |      *not implemented*       | 0x1b6 |                                   |                                      |                                               |
| 439  |          faccessat2          | 0x1b7 |              int dfd              |         const char *filename         |                   int mode                    |

## arm32系统调用表

| NR     |         syscall name         |   %r7   |            arg0 (%r0)             |              arg1 (%r1)              |                  arg2 (%r2)                   |
| ------ | :--------------------------: | :-----: | :-------------------------------: | :----------------------------------: | :-------------------------------------------: |
| 0      |       restart_syscall        |  0x00   |                 -                 |                  -                   |                       -                       |
| 1      |             exit             |  0x01   |          int error_code           |                  -                   |                       -                       |
| 2      |             fork             |  0x02   |                 -                 |                  -                   |                       -                       |
| 3      |             read             |  0x03   |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 4      |            write             |  0x04   |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 5      |             open             |  0x05   |       const char *filename        |              int flags               |                 umode_t mode                  |
| 6      |            close             |  0x06   |          unsigned int fd          |                  -                   |                       -                       |
| 7      |      *not implemented*       |  0x07   |                                   |                                      |                                               |
| 8      |            creat             |  0x08   |       const char *pathname        |             umode_t mode             |                       -                       |
| 9      |             link             |  0x09   |        const char *oldname        |         const char *newname          |                       -                       |
| 10     |            unlink            |  0x0a   |       const char *pathname        |                  -                   |                       -                       |
| 11     |            execve            |  0x0b   |       const char *filename        |       const char *const *argv        |            const char *const *envp            |
| 12     |            chdir             |  0x0c   |       const char *filename        |                  -                   |                       -                       |
| 13     |      *not implemented*       |  0x0d   |                                   |                                      |                                               |
| 14     |            mknod             |  0x0e   |       const char *filename        |             umode_t mode             |                 unsigned dev                  |
| 15     |            chmod             |  0x0f   |       const char *filename        |             umode_t mode             |                       -                       |
| 16     |            lchown            |  0x10   |       const char *filename        |              uid_t user              |                  gid_t group                  |
| 17     |      *not implemented*       |  0x11   |                                   |                                      |                                               |
| 18     |      *not implemented*       |  0x12   |                                   |                                      |                                               |
| 19     |            lseek             |  0x13   |          unsigned int fd          |             off_t offset             |              unsigned int whence              |
| 20     |            getpid            |  0x14   |                 -                 |                  -                   |                       -                       |
| 21     |            mount             |  0x15   |          char *dev_name           |            char *dir_name            |                  char *type                   |
| 22     |      *not implemented*       |  0x16   |                                   |                                      |                                               |
| 23     |            setuid            |  0x17   |             uid_t uid             |                  -                   |                       -                       |
| 24     |            getuid            |  0x18   |                 -                 |                  -                   |                       -                       |
| 25     |      *not implemented*       |  0x19   |                                   |                                      |                                               |
| 26     |            ptrace            |  0x1a   |           long request            |               long pid               |              unsigned long addr               |
| 27     |      *not implemented*       |  0x1b   |                                   |                                      |                                               |
| 28     |      *not implemented*       |  0x1c   |                                   |                                      |                                               |
| 29     |            pause             |  0x1d   |                 -                 |                  -                   |                       -                       |
| 30     |      *not implemented*       |  0x1e   |                                   |                                      |                                               |
| 31     |      *not implemented*       |  0x1f   |                                   |                                      |                                               |
| 32     |      *not implemented*       |  0x20   |                                   |                                      |                                               |
| 33     |            access            |  0x21   |       const char *filename        |               int mode               |                       -                       |
| 34     |             nice             |  0x22   |           int increment           |                  -                   |                       -                       |
| 35     |      *not implemented*       |  0x23   |                                   |                                      |                                               |
| 36     |             sync             |  0x24   |                 -                 |                  -                   |                       -                       |
| 37     |             kill             |  0x25   |             pid_t pid             |               int sig                |                       -                       |
| 38     |            rename            |  0x26   |        const char *oldname        |         const char *newname          |                       -                       |
| 39     |            mkdir             |  0x27   |       const char *pathname        |             umode_t mode             |                       -                       |
| 40     |            rmdir             |  0x28   |       const char *pathname        |                  -                   |                       -                       |
| 41     |             dup              |  0x29   |        unsigned int fildes        |                  -                   |                       -                       |
| 42     |             pipe             |  0x2a   |            int *fildes            |                  -                   |                       -                       |
| 43     |            times             |  0x2b   |         struct tms *tbuf          |                  -                   |                       -                       |
| 44     |      *not implemented*       |  0x2c   |                                   |                                      |                                               |
| 45     |             brk              |  0x2d   |         unsigned long brk         |                  -                   |                       -                       |
| 46     |            setgid            |  0x2e   |             gid_t gid             |                  -                   |                       -                       |
| 47     |            getgid            |  0x2f   |                 -                 |                  -                   |                       -                       |
| 48     |      *not implemented*       |  0x30   |                                   |                                      |                                               |
| 49     |           geteuid            |  0x31   |                 -                 |                  -                   |                       -                       |
| 50     |           getegid            |  0x32   |                 -                 |                  -                   |                       -                       |
| 51     |             acct             |  0x33   |         const char *name          |                  -                   |                       -                       |
| 52     |           umount2            |  0x34   |                 ?                 |                  ?                   |                       ?                       |
| 53     |      *not implemented*       |  0x35   |                                   |                                      |                                               |
| 54     |            ioctl             |  0x36   |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 55     |            fcntl             |  0x37   |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 56     |      *not implemented*       |  0x38   |                                   |                                      |                                               |
| 57     |           setpgid            |  0x39   |             pid_t pid             |              pid_t pgid              |                       -                       |
| 58     |      *not implemented*       |  0x3a   |                                   |                                      |                                               |
| 59     |      *not implemented*       |  0x3b   |                                   |                                      |                                               |
| 60     |            umask             |  0x3c   |             int mask              |                  -                   |                       -                       |
| 61     |            chroot            |  0x3d   |       const char *filename        |                  -                   |                       -                       |
| 62     |            ustat             |  0x3e   |           unsigned dev            |          struct ustat *ubuf          |                       -                       |
| 63     |             dup2             |  0x3f   |        unsigned int oldfd         |          unsigned int newfd          |                       -                       |
| 64     |           getppid            |  0x40   |                 -                 |                  -                   |                       -                       |
| 65     |           getpgrp            |  0x41   |                 -                 |                  -                   |                       -                       |
| 66     |            setsid            |  0x42   |                 -                 |                  -                   |                       -                       |
| 67     |          sigaction           |  0x43   |                int                |     const struct old_sigaction *     |            struct old_sigaction *             |
| 68     |      *not implemented*       |  0x44   |                                   |                                      |                                               |
| 69     |      *not implemented*       |  0x45   |                                   |                                      |                                               |
| 70     |           setreuid           |  0x46   |            uid_t ruid             |              uid_t euid              |                       -                       |
| 71     |           setregid           |  0x47   |            gid_t rgid             |              gid_t egid              |                       -                       |
| 72     |          sigsuspend          |  0x48   |            int unused1            |             int unused2              |               old_sigset_t mask               |
| 73     |          sigpending          |  0x49   |        old_sigset_t *uset         |                  -                   |                       -                       |
| 74     |         sethostname          |  0x4a   |            char *name             |               int len                |                       -                       |
| 75     |          setrlimit           |  0x4b   |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 76     |      *not implemented*       |  0x4c   |                                   |                                      |                                               |
| 77     |          getrusage           |  0x4d   |              int who              |          struct rusage *ru           |                       -                       |
| 78     |         gettimeofday         |  0x4e   |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 79     |         settimeofday         |  0x4f   |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 80     |          getgroups           |  0x50   |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 81     |          setgroups           |  0x51   |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 82     |      *not implemented*       |  0x52   |                                   |                                      |                                               |
| 83     |           symlink            |  0x53   |          const char *old          |           const char *new            |                       -                       |
| 84     |      *not implemented*       |  0x54   |                                   |                                      |                                               |
| 85     |           readlink           |  0x55   |         const char *path          |              char *buf               |                  int bufsiz                   |
| 86     |            uselib            |  0x56   |        const char *library        |                  -                   |                       -                       |
| 87     |            swapon            |  0x57   |      const char *specialfile      |            int swap_flags            |                       -                       |
| 88     |            reboot            |  0x58   |            int magic1             |              int magic2              |               unsigned int cmd                |
| 89     |      *not implemented*       |  0x59   |                                   |                                      |                                               |
| 90     |      *not implemented*       |  0x5a   |                                   |                                      |                                               |
| 91     |            munmap            |  0x5b   |        unsigned long addr         |              size_t len              |                       -                       |
| 92     |           truncate           |  0x5c   |         const char *path          |             long length              |                       -                       |
| 93     |          ftruncate           |  0x5d   |          unsigned int fd          |         unsigned long length         |                       -                       |
| 94     |            fchmod            |  0x5e   |          unsigned int fd          |             umode_t mode             |                       -                       |
| 95     |            fchown            |  0x5f   |          unsigned int fd          |              uid_t user              |                  gid_t group                  |
| 96     |         getpriority          |  0x60   |             int which             |               int who                |                       -                       |
| 97     |         setpriority          |  0x61   |             int which             |               int who                |                  int niceval                  |
| 98     |      *not implemented*       |  0x62   |                                   |                                      |                                               |
| 99     |            statfs            |  0x63   |         const char * path         |          struct statfs *buf          |                       -                       |
| 100    |           fstatfs            |  0x64   |          unsigned int fd          |          struct statfs *buf          |                       -                       |
| 101    |      *not implemented*       |  0x65   |                                   |                                      |                                               |
| 102    |      *not implemented*       |  0x66   |                                   |                                      |                                               |
| 103    |            syslog            |  0x67   |             int type              |              char *buf               |                    int len                    |
| 104    |          setitimer           |  0x68   |             int which             | struct __kernel_old_itimerval *value |     struct __kernel_old_itimerval *ovalue     |
| 105    |          getitimer           |  0x69   |             int which             | struct __kernel_old_itimerval *value |                       -                       |
| 106    |             stat             |  0x6a   |       const char *filename        |  struct __old_kernel_stat *statbuf   |                       -                       |
| 107    |            lstat             |  0x6b   |       const char *filename        |  struct __old_kernel_stat *statbuf   |                       -                       |
| 108    |            fstat             |  0x6c   |          unsigned int fd          |  struct __old_kernel_stat *statbuf   |                       -                       |
| 109    |      *not implemented*       |  0x6d   |                                   |                                      |                                               |
| 110    |      *not implemented*       |  0x6e   |                                   |                                      |                                               |
| 111    |           vhangup            |  0x6f   |                 -                 |                  -                   |                       -                       |
| 112    |      *not implemented*       |  0x70   |                                   |                                      |                                               |
| 113    |      *not implemented*       |  0x71   |                                   |                                      |                                               |
| 114    |            wait4             |  0x72   |             pid_t pid             |            int *stat_addr            |                  int options                  |
| 115    |           swapoff            |  0x73   |      const char *specialfile      |                  -                   |                       -                       |
| 116    |           sysinfo            |  0x74   |       struct sysinfo *info        |                  -                   |                       -                       |
| 117    |      *not implemented*       |  0x75   |                                   |                                      |                                               |
| 118    |            fsync             |  0x76   |          unsigned int fd          |                  -                   |                       -                       |
| 119    |          sigreturn           |  0x77   |                 ?                 |                  ?                   |                       ?                       |
| 120    |            clone             |  0x78   |           unsigned long           |            unsigned long             |                     int *                     |
| 121    |        setdomainname         |  0x79   |            char *name             |               int len                |                       -                       |
| 122    |            uname             |  0x7a   |       struct old_utsname *        |                  -                   |                       -                       |
| 123    |      *not implemented*       |  0x7b   |                                   |                                      |                                               |
| 124    |           adjtimex           |  0x7c   |   struct __kernel_timex *txc_p    |                  -                   |                       -                       |
| 125    |           mprotect           |  0x7d   |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 126    |         sigprocmask          |  0x7e   |              int how              |          old_sigset_t *set           |              old_sigset_t *oset               |
| 127    |      *not implemented*       |  0x7f   |                                   |                                      |                                               |
| 128    |         init_module          |  0x80   |            void *umod             |          unsigned long len           |               const char *uargs               |
| 129    |        delete_module         |  0x81   |       const char *name_user       |          unsigned int flags          |                       -                       |
| 130    |      *not implemented*       |  0x82   |                                   |                                      |                                               |
| 131    |           quotactl           |  0x83   |         unsigned int cmd          |         const char *special          |                   qid_t id                    |
| 132    |           getpgid            |  0x84   |             pid_t pid             |                  -                   |                       -                       |
| 133    |            fchdir            |  0x85   |          unsigned int fd          |                  -                   |                       -                       |
| 134    |           bdflush            |  0x86   |                 ?                 |                  ?                   |                       ?                       |
| 135    |            sysfs             |  0x87   |            int option             |          unsigned long arg1          |              unsigned long arg2               |
| 136    |         personality          |  0x88   |     unsigned int personality      |                  -                   |                       -                       |
| 137    |      *not implemented*       |  0x89   |                                   |                                      |                                               |
| 138    |           setfsuid           |  0x8a   |             uid_t uid             |                  -                   |                       -                       |
| 139    |           setfsgid           |  0x8b   |             gid_t gid             |                  -                   |                       -                       |
| 140    |           _llseek            |  0x8c   |                 ?                 |                  ?                   |                       ?                       |
| 141    |           getdents           |  0x8d   |          unsigned int fd          |     struct linux_dirent *dirent      |              unsigned int count               |
| 142    |          _newselect          |  0x8e   |                 ?                 |                  ?                   |                       ?                       |
| 143    |            flock             |  0x8f   |          unsigned int fd          |           unsigned int cmd           |                       -                       |
| 144    |            msync             |  0x90   |        unsigned long start        |              size_t len              |                   int flags                   |
| 145    |            readv             |  0x91   |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 146    |            writev            |  0x92   |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 147    |            getsid            |  0x93   |             pid_t pid             |                  -                   |                       -                       |
| 148    |          fdatasync           |  0x94   |          unsigned int fd          |                  -                   |                       -                       |
| 149    |           _sysctl            |  0x95   |                 ?                 |                  ?                   |                       ?                       |
| 150    |            mlock             |  0x96   |        unsigned long start        |              size_t len              |                       -                       |
| 151    |           munlock            |  0x97   |        unsigned long start        |              size_t len              |                       -                       |
| 152    |           mlockall           |  0x98   |             int flags             |                  -                   |                       -                       |
| 153    |          munlockall          |  0x99   |                 -                 |                  -                   |                       -                       |
| 154    |        sched_setparam        |  0x9a   |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 155    |        sched_getparam        |  0x9b   |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 156    |      sched_setscheduler      |  0x9c   |             pid_t pid             |              int policy              |           struct sched_param *param           |
| 157    |      sched_getscheduler      |  0x9d   |             pid_t pid             |                  -                   |                       -                       |
| 158    |         sched_yield          |  0x9e   |                 -                 |                  -                   |                       -                       |
| 159    |    sched_get_priority_max    |  0x9f   |            int policy             |                  -                   |                       -                       |
| 160    |    sched_get_priority_min    |  0xa0   |            int policy             |                  -                   |                       -                       |
| 161    |    sched_rr_get_interval     |  0xa1   |             pid_t pid             |  struct __kernel_timespec *interval  |                       -                       |
| 162    |          nanosleep           |  0xa2   |  struct __kernel_timespec *rqtp   |    struct __kernel_timespec *rmtp    |                       -                       |
| 163    |            mremap            |  0xa3   |        unsigned long addr         |        unsigned long old_len         |             unsigned long new_len             |
| 164    |          setresuid           |  0xa4   |            uid_t ruid             |              uid_t euid              |                  uid_t suid                   |
| 165    |          getresuid           |  0xa5   |            uid_t *ruid            |             uid_t *euid              |                  uid_t *suid                  |
| 166    |      *not implemented*       |  0xa6   |                                   |                                      |                                               |
| 167    |      *not implemented*       |  0xa7   |                                   |                                      |                                               |
| 168    |             poll             |  0xa8   |        struct pollfd *ufds        |          unsigned int nfds           |                  int timeout                  |
| 169    |          nfsservctl          |  0xa9   |                 ?                 |                  ?                   |                       ?                       |
| 170    |          setresgid           |  0xaa   |            gid_t rgid             |              gid_t egid              |                  gid_t sgid                   |
| 171    |          getresgid           |  0xab   |            gid_t *rgid            |             gid_t *egid              |                  gid_t *sgid                  |
| 172    |            prctl             |  0xac   |            int option             |          unsigned long arg2          |              unsigned long arg3               |
| 173    |         rt_sigreturn         |  0xad   |                 ?                 |                  ?                   |                       ?                       |
| 174    |         rt_sigaction         |  0xae   |                int                |       const struct sigaction *       |              struct sigaction *               |
| 175    |        rt_sigprocmask        |  0xaf   |              int how              |            sigset_t *set             |                sigset_t *oset                 |
| 176    |        rt_sigpending         |  0xb0   |           sigset_t *set           |          size_t sigsetsize           |                       -                       |
| 177    |       rt_sigtimedwait        |  0xb1   |      const sigset_t *uthese       |           siginfo_t *uinfo           |      const struct __kernel_timespec *uts      |
| 178    |       rt_sigqueueinfo        |  0xb2   |             pid_t pid             |               int sig                |               siginfo_t *uinfo                |
| 179    |        rt_sigsuspend         |  0xb3   |         sigset_t *unewset         |          size_t sigsetsize           |                       -                       |
| 180    |           pread64            |  0xb4   |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 181    |           pwrite64           |  0xb5   |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 182    |            chown             |  0xb6   |       const char *filename        |              uid_t user              |                  gid_t group                  |
| 183    |            getcwd            |  0xb7   |             char *buf             |          unsigned long size          |                       -                       |
| 184    |            capget            |  0xb8   |     cap_user_header_t header      |       cap_user_data_t dataptr        |                       -                       |
| 185    |            capset            |  0xb9   |     cap_user_header_t header      |      const cap_user_data_t data      |                       -                       |
| 186    |         sigaltstack          |  0xba   |   const struct sigaltstack *uss   |       struct sigaltstack *uoss       |                       -                       |
| 187    |           sendfile           |  0xbb   |            int out_fd             |              int in_fd               |                 off_t *offset                 |
| 188    |      *not implemented*       |  0xbc   |                                   |                                      |                                               |
| 189    |      *not implemented*       |  0xbd   |                                   |                                      |                                               |
| 190    |            vfork             |  0xbe   |                 -                 |                  -                   |                       -                       |
| 191    |          ugetrlimit          |  0xbf   |                 ?                 |                  ?                   |                       ?                       |
| 192    |            mmap2             |  0xc0   |                 ?                 |                  ?                   |                       ?                       |
| 193    |          truncate64          |  0xc1   |         const char *path          |            loff_t length             |                       -                       |
| 194    |         ftruncate64          |  0xc2   |          unsigned int fd          |            loff_t length             |                       -                       |
| 195    |            stat64            |  0xc3   |       const char *filename        |        struct stat64 *statbuf        |                       -                       |
| 196    |           lstat64            |  0xc4   |       const char *filename        |        struct stat64 *statbuf        |                       -                       |
| 197    |           fstat64            |  0xc5   |         unsigned long fd          |        struct stat64 *statbuf        |                       -                       |
| 198    |           lchown32           |  0xc6   |                 ?                 |                  ?                   |                       ?                       |
| 199    |           getuid32           |  0xc7   |                 ?                 |                  ?                   |                       ?                       |
| 200    |           getgid32           |  0xc8   |                 ?                 |                  ?                   |                       ?                       |
| 201    |          geteuid32           |  0xc9   |                 ?                 |                  ?                   |                       ?                       |
| 202    |          getegid32           |  0xca   |                 ?                 |                  ?                   |                       ?                       |
| 203    |          setreuid32          |  0xcb   |                 ?                 |                  ?                   |                       ?                       |
| 204    |          setregid32          |  0xcc   |                 ?                 |                  ?                   |                       ?                       |
| 205    |         getgroups32          |  0xcd   |                 ?                 |                  ?                   |                       ?                       |
| 206    |         setgroups32          |  0xce   |                 ?                 |                  ?                   |                       ?                       |
| 207    |           fchown32           |  0xcf   |                 ?                 |                  ?                   |                       ?                       |
| 208    |         setresuid32          |  0xd0   |                 ?                 |                  ?                   |                       ?                       |
| 209    |         getresuid32          |  0xd1   |                 ?                 |                  ?                   |                       ?                       |
| 210    |         setresgid32          |  0xd2   |                 ?                 |                  ?                   |                       ?                       |
| 211    |         getresgid32          |  0xd3   |                 ?                 |                  ?                   |                       ?                       |
| 212    |           chown32            |  0xd4   |                 ?                 |                  ?                   |                       ?                       |
| 213    |           setuid32           |  0xd5   |                 ?                 |                  ?                   |                       ?                       |
| 214    |           setgid32           |  0xd6   |                 ?                 |                  ?                   |                       ?                       |
| 215    |          setfsuid32          |  0xd7   |                 ?                 |                  ?                   |                       ?                       |
| 216    |          setfsgid32          |  0xd8   |                 ?                 |                  ?                   |                       ?                       |
| 217    |          getdents64          |  0xd9   |          unsigned int fd          |    struct linux_dirent64 *dirent     |              unsigned int count               |
| 218    |          pivot_root          |  0xda   |       const char *new_root        |         const char *put_old          |                       -                       |
| 219    |           mincore            |  0xdb   |        unsigned long start        |              size_t len              |              unsigned char * vec              |
| 220    |           madvise            |  0xdc   |        unsigned long start        |              size_t len              |                 int behavior                  |
| 221    |           fcntl64            |  0xdd   |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 222    |      *not implemented*       |  0xde   |                                   |                                      |                                               |
| 223    |      *not implemented*       |  0xdf   |                                   |                                      |                                               |
| 224    |            gettid            |  0xe0   |                 -                 |                  -                   |                       -                       |
| 225    |          readahead           |  0xe1   |              int fd               |            loff_t offset             |                 size_t count                  |
| 226    |           setxattr           |  0xe2   |         const char *path          |           const char *name           |               const void *value               |
| 227    |          lsetxattr           |  0xe3   |         const char *path          |           const char *name           |               const void *value               |
| 228    |          fsetxattr           |  0xe4   |              int fd               |           const char *name           |               const void *value               |
| 229    |           getxattr           |  0xe5   |         const char *path          |           const char *name           |                  void *value                  |
| 230    |          lgetxattr           |  0xe6   |         const char *path          |           const char *name           |                  void *value                  |
| 231    |          fgetxattr           |  0xe7   |              int fd               |           const char *name           |                  void *value                  |
| 232    |          listxattr           |  0xe8   |         const char *path          |              char *list              |                  size_t size                  |
| 233    |          llistxattr          |  0xe9   |         const char *path          |              char *list              |                  size_t size                  |
| 234    |          flistxattr          |  0xea   |              int fd               |              char *list              |                  size_t size                  |
| 235    |         removexattr          |  0xeb   |         const char *path          |           const char *name           |                       -                       |
| 236    |         lremovexattr         |  0xec   |         const char *path          |           const char *name           |                       -                       |
| 237    |         fremovexattr         |  0xed   |              int fd               |           const char *name           |                       -                       |
| 238    |            tkill             |  0xee   |             pid_t pid             |               int sig                |                       -                       |
| 239    |          sendfile64          |  0xef   |            int out_fd             |              int in_fd               |                loff_t *offset                 |
| 240    |            futex             |  0xf0   |            u32 *uaddr             |                int op                |                    u32 val                    |
| 241    |      sched_setaffinity       |  0xf1   |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 242    |      sched_getaffinity       |  0xf2   |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 243    |           io_setup           |  0xf3   |         unsigned nr_reqs          |          aio_context_t *ctx          |                       -                       |
| 244    |          io_destroy          |  0xf4   |         aio_context_t ctx         |                  -                   |                       -                       |
| 245    |         io_getevents         |  0xf5   |       aio_context_t ctx_id        |             long min_nr              |                    long nr                    |
| 246    |          io_submit           |  0xf6   |           aio_context_t           |                 long                 |                  struct iocb                  |
| 247    |          io_cancel           |  0xf7   |       aio_context_t ctx_id        |          struct iocb *iocb           |            struct io_event *result            |
| 248    |          exit_group          |  0xf8   |          int error_code           |                  -                   |                       -                       |
| 249    |        lookup_dcookie        |  0xf9   |           u64 cookie64            |              char *buf               |                  size_t len                   |
| 250    |         epoll_create         |  0xfa   |             int size              |                  -                   |                       -                       |
| 251    |          epoll_ctl           |  0xfb   |             int epfd              |                int op                |                    int fd                     |
| 252    |          epoll_wait          |  0xfc   |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 253    |       remap_file_pages       |  0xfd   |        unsigned long start        |          unsigned long size          |              unsigned long prot               |
| 254    |      *not implemented*       |  0xfe   |                                   |                                      |                                               |
| 255    |      *not implemented*       |  0xff   |                                   |                                      |                                               |
| 256    |       set_tid_address        |  0x100  |            int *tidptr            |                  -                   |                       -                       |
| 257    |         timer_create         |  0x101  |       clockid_t which_clock       |  struct sigevent *timer_event_spec   |          timer_t * created_timer_id           |
| 258    |        timer_settime         |  0x102  |         timer_t timer_id          |              int flags               | const struct __kernel_itimerspec *new_setting |
| 259    |        timer_gettime         |  0x103  |         timer_t timer_id          | struct __kernel_itimerspec *setting  |                       -                       |
| 260    |       timer_getoverrun       |  0x104  |         timer_t timer_id          |                  -                   |                       -                       |
| 261    |         timer_delete         |  0x105  |         timer_t timer_id          |                  -                   |                       -                       |
| 262    |        clock_settime         |  0x106  |       clockid_t which_clock       |  const struct __kernel_timespec *tp  |                       -                       |
| 263    |        clock_gettime         |  0x107  |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 264    |         clock_getres         |  0x108  |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 265    |       clock_nanosleep        |  0x109  |       clockid_t which_clock       |              int flags               |     const struct __kernel_timespec *rqtp      |
| 266    |           statfs64           |  0x10a  |         const char *path          |              size_t sz               |             struct statfs64 *buf              |
| 267    |          fstatfs64           |  0x10b  |          unsigned int fd          |              size_t sz               |             struct statfs64 *buf              |
| 268    |            tgkill            |  0x10c  |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 269    |            utimes            |  0x10d  |          char *filename           | struct __kernel_old_timeval *utimes  |                       -                       |
| 270    |       arm_fadvise64_64       |  0x10e  |                 ?                 |                  ?                   |                       ?                       |
| 271    |       pciconfig_iobase       |  0x10f  |            long which             |          unsigned long bus           |              unsigned long devfn              |
| 272    |        pciconfig_read        |  0x110  |         unsigned long bus         |          unsigned long dfn           |               unsigned long off               |
| 273    |       pciconfig_write        |  0x111  |         unsigned long bus         |          unsigned long dfn           |               unsigned long off               |
| 274    |           mq_open            |  0x112  |         const char *name          |              int oflag               |                 umode_t mode                  |
| 275    |          mq_unlink           |  0x113  |         const char *name          |                  -                   |                       -                       |
| 276    |         mq_timedsend         |  0x114  |            mqd_t mqdes            |         const char *msg_ptr          |                size_t msg_len                 |
| 277    |       mq_timedreceive        |  0x115  |            mqd_t mqdes            |            char *msg_ptr             |                size_t msg_len                 |
| 278    |          mq_notify           |  0x116  |            mqd_t mqdes            | const struct sigevent *notification  |                       -                       |
| 279    |        mq_getsetattr         |  0x117  |            mqd_t mqdes            |     const struct mq_attr *mqstat     |            struct mq_attr *omqstat            |
| 280    |            waitid            |  0x118  |             int which             |              pid_t pid               |             struct siginfo *infop             |
| 281    |            socket            |  0x119  |                int                |                 int                  |                      int                      |
| 282    |             bind             |  0x11a  |                int                |          struct sockaddr *           |                      int                      |
| 283    |           connect            |  0x11b  |                int                |          struct sockaddr *           |                      int                      |
| 284    |            listen            |  0x11c  |                int                |                 int                  |                       -                       |
| 285    |            accept            |  0x11d  |                int                |          struct sockaddr *           |                     int *                     |
| 286    |         getsockname          |  0x11e  |                int                |          struct sockaddr *           |                     int *                     |
| 287    |         getpeername          |  0x11f  |                int                |          struct sockaddr *           |                     int *                     |
| 288    |          socketpair          |  0x120  |                int                |                 int                  |                      int                      |
| 289    |             send             |  0x121  |                int                |                void *                |                    size_t                     |
| 290    |            sendto            |  0x122  |                int                |                void *                |                    size_t                     |
| 291    |             recv             |  0x123  |                int                |                void *                |                    size_t                     |
| 292    |           recvfrom           |  0x124  |                int                |                void *                |                    size_t                     |
| 293    |           shutdown           |  0x125  |                int                |                 int                  |                       -                       |
| 294    |          setsockopt          |  0x126  |              int fd               |              int level               |                  int optname                  |
| 295    |          getsockopt          |  0x127  |              int fd               |              int level               |                  int optname                  |
| 296    |           sendmsg            |  0x128  |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 297    |           recvmsg            |  0x129  |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 298    |            semop             |  0x12a  |             int semid             |         struct sembuf *sops          |                unsigned nsops                 |
| 299    |            semget            |  0x12b  |             key_t key             |              int nsems               |                  int semflg                   |
| 300    |            semctl            |  0x12c  |             int semid             |              int semnum              |                    int cmd                    |
| 301    |            msgsnd            |  0x12d  |             int msqid             |         struct msgbuf *msgp          |                 size_t msgsz                  |
| 302    |            msgrcv            |  0x12e  |             int msqid             |         struct msgbuf *msgp          |                 size_t msgsz                  |
| 303    |            msgget            |  0x12f  |             key_t key             |              int msgflg              |                       -                       |
| 304    |            msgctl            |  0x130  |             int msqid             |               int cmd                |             struct msqid_ds *buf              |
| 305    |            shmat             |  0x131  |             int shmid             |            char *shmaddr             |                  int shmflg                   |
| 306    |            shmdt             |  0x132  |           char *shmaddr           |                  -                   |                       -                       |
| 307    |            shmget            |  0x133  |             key_t key             |             size_t size              |                   int flag                    |
| 308    |            shmctl            |  0x134  |             int shmid             |               int cmd                |             struct shmid_ds *buf              |
| 309    |           add_key            |  0x135  |         const char *_type         |       const char *_description       |             const void *_payload              |
| 310    |         request_key          |  0x136  |         const char *_type         |       const char *_description       |           const char *_callout_info           |
| 311    |            keyctl            |  0x137  |              int cmd              |          unsigned long arg2          |              unsigned long arg3               |
| 312    |          semtimedop          |  0x138  |             int semid             |         struct sembuf *sops          |                unsigned nsops                 |
| 313    |           vserver            |  0x139  |                 ?                 |                  ?                   |                       ?                       |
| 314    |          ioprio_set          |  0x13a  |             int which             |               int who                |                  int ioprio                   |
| 315    |          ioprio_get          |  0x13b  |             int which             |               int who                |                       -                       |
| 316    |         inotify_init         |  0x13c  |                 -                 |                  -                   |                       -                       |
| 317    |      inotify_add_watch       |  0x13d  |              int fd               |           const char *path           |                   u32 mask                    |
| 318    |       inotify_rm_watch       |  0x13e  |              int fd               |               __s32 wd               |                       -                       |
| 319    |            mbind             |  0x13f  |        unsigned long start        |          unsigned long len           |              unsigned long mode               |
| 320    |        get_mempolicy         |  0x140  |            int *policy            |         unsigned long *nmask         |             unsigned long maxnode             |
| 321    |        set_mempolicy         |  0x141  |             int mode              |      const unsigned long *nmask      |             unsigned long maxnode             |
| 322    |            openat            |  0x142  |              int dfd              |         const char *filename         |                   int flags                   |
| 323    |           mkdirat            |  0x143  |              int dfd              |        const char * pathname         |                 umode_t mode                  |
| 324    |           mknodat            |  0x144  |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 325    |           fchownat           |  0x145  |              int dfd              |         const char *filename         |                  uid_t user                   |
| 326    |          futimesat           |  0x146  |              int dfd              |         const char *filename         |      struct __kernel_old_timeval *utimes      |
| 327    |          fstatat64           |  0x147  |              int dfd              |         const char *filename         |            struct stat64 *statbuf             |
| 328    |           unlinkat           |  0x148  |              int dfd              |        const char * pathname         |                   int flag                    |
| 329    |           renameat           |  0x149  |            int olddfd             |         const char * oldname         |                  int newdfd                   |
| 330    |            linkat            |  0x14a  |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 331    |          symlinkat           |  0x14b  |       const char * oldname        |              int newdfd              |             const char * newname              |
| 332    |          readlinkat          |  0x14c  |              int dfd              |           const char *path           |                   char *buf                   |
| 333    |           fchmodat           |  0x14d  |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 334    |          faccessat           |  0x14e  |              int dfd              |         const char *filename         |                   int mode                    |
| 335    |           pselect6           |  0x14f  |                int                |               fd_set *               |                   fd_set *                    |
| 336    |            ppoll             |  0x150  |          struct pollfd *          |             unsigned int             |          struct __kernel_timespec *           |
| 337    |           unshare            |  0x151  |    unsigned long unshare_flags    |                  -                   |                       -                       |
| 338    |       set_robust_list        |  0x152  |   struct robust_list_head *head   |              size_t len              |                       -                       |
| 339    |       get_robust_list        |  0x153  |              int pid              |   struct robust_list_head head_ptr   |                size_t *len_ptr                |
| 340    |            splice            |  0x154  |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 341    |     arm_sync_file_range      |  0x155  |                 ?                 |                  ?                   |                       ?                       |
| 341    |       sync_file_range2       |  0x155  |              int fd               |          unsigned int flags          |                 loff_t offset                 |
| 342    |             tee              |  0x156  |             int fdin              |              int fdout               |                  size_t len                   |
| 343    |           vmsplice           |  0x157  |              int fd               |       const struct iovec *iov        |             unsigned long nr_segs             |
| 344    |          move_pages          |  0x158  |             pid_t pid             |        unsigned long nr_pages        |               const void pages                |
| 345    |            getcpu            |  0x159  |           unsigned *cpu           |            unsigned *node            |          struct getcpu_cache *cache           |
| 346    |         epoll_pwait          |  0x15a  |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 347    |          kexec_load          |  0x15b  |        unsigned long entry        |      unsigned long nr_segments       |        struct kexec_segment *segments         |
| 348    |          utimensat           |  0x15c  |              int dfd              |         const char *filename         |       struct __kernel_timespec *utimes        |
| 349    |           signalfd           |  0x15d  |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 350    |        timerfd_create        |  0x15e  |            int clockid            |              int flags               |                       -                       |
| 351    |           eventfd            |  0x15f  |        unsigned int count         |                  -                   |                       -                       |
| 352    |          fallocate           |  0x160  |              int fd               |               int mode               |                 loff_t offset                 |
| 353    |       timerfd_settime        |  0x161  |              int ufd              |              int flags               |    const struct __kernel_itimerspec *utmr     |
| 354    |       timerfd_gettime        |  0x162  |              int ufd              |   struct __kernel_itimerspec *otmr   |                       -                       |
| 355    |          signalfd4           |  0x163  |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 356    |           eventfd2           |  0x164  |        unsigned int count         |              int flags               |                       -                       |
| 357    |        epoll_create1         |  0x165  |             int flags             |                  -                   |                       -                       |
| 358    |             dup3             |  0x166  |        unsigned int oldfd         |          unsigned int newfd          |                   int flags                   |
| 359    |            pipe2             |  0x167  |            int *fildes            |              int flags               |                       -                       |
| 360    |        inotify_init1         |  0x168  |             int flags             |                  -                   |                       -                       |
| 361    |            preadv            |  0x169  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 362    |           pwritev            |  0x16a  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 363    |      rt_tgsigqueueinfo       |  0x16b  |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 364    |       perf_event_open        |  0x16c  | struct perf_event_attr *attr_uptr |              pid_t pid               |                    int cpu                    |
| 365    |           recvmmsg           |  0x16d  |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 366    |           accept4            |  0x16e  |                int                |          struct sockaddr *           |                     int *                     |
| 367    |        fanotify_init         |  0x16f  |        unsigned int flags         |      unsigned int event_f_flags      |                       -                       |
| 368    |        fanotify_mark         |  0x170  |          int fanotify_fd          |          unsigned int flags          |                   u64 mask                    |
| 369    |          prlimit64           |  0x171  |             pid_t pid             |        unsigned int resource         |        const struct rlimit64 *new_rlim        |
| 370    |      name_to_handle_at       |  0x172  |              int dfd              |           const char *name           |          struct file_handle *handle           |
| 371    |      open_by_handle_at       |  0x173  |          int mountdirfd           |      struct file_handle *handle      |                   int flags                   |
| 372    |        clock_adjtime         |  0x174  |       clockid_t which_clock       |      struct __kernel_timex *tx       |                       -                       |
| 373    |            syncfs            |  0x175  |              int fd               |                  -                   |                       -                       |
| 374    |           sendmmsg           |  0x176  |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 375    |            setns             |  0x177  |              int fd               |              int nstype              |                       -                       |
| 376    |       process_vm_readv       |  0x178  |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 377    |      process_vm_writev       |  0x179  |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 378    |             kcmp             |  0x17a  |            pid_t pid1             |              pid_t pid2              |                   int type                    |
| 379    |         finit_module         |  0x17b  |              int fd               |          const char *uargs           |                   int flags                   |
| 380    |        sched_setattr         |  0x17c  |             pid_t pid             |       struct sched_attr *attr        |              unsigned int flags               |
| 381    |        sched_getattr         |  0x17d  |             pid_t pid             |       struct sched_attr *attr        |               unsigned int size               |
| 382    |          renameat2           |  0x17e  |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 383    |           seccomp            |  0x17f  |          unsigned int op          |          unsigned int flags          |                  void *uargs                  |
| 384    |          getrandom           |  0x180  |             char *buf             |             size_t count             |              unsigned int flags               |
| 385    |         memfd_create         |  0x181  |       const char *uname_ptr       |          unsigned int flags          |                       -                       |
| 386    |             bpf              |  0x182  |              int cmd              |         union bpf_attr *attr         |               unsigned int size               |
| 387    |           execveat           |  0x183  |              int dfd              |         const char *filename         |            const char *const *argv            |
| 388    |         userfaultfd          |  0x184  |             int flags             |                  -                   |                       -                       |
| 389    |          membarrier          |  0x185  |              int cmd              |          unsigned int flags          |                  int cpu_id                   |
| 390    |            mlock2            |  0x186  |        unsigned long start        |              size_t len              |                   int flags                   |
| 391    |       copy_file_range        |  0x187  |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 392    |           preadv2            |  0x188  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 393    |           pwritev2           |  0x189  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 394    |        pkey_mprotect         |  0x18a  |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 395    |          pkey_alloc          |  0x18b  |        unsigned long flags        |        unsigned long init_val        |                       -                       |
| 396    |          pkey_free           |  0x18c  |             int pkey              |                  -                   |                       -                       |
| 397    |            statx             |  0x18d  |              int dfd              |           const char *path           |                unsigned flags                 |
| 398    |      *not implemented*       |  0x18e  |                                   |                                      |                                               |
| 399    |      *not implemented*       |  0x18f  |                                   |                                      |                                               |
| 400    |      *not implemented*       |  0x190  |                                   |                                      |                                               |
| 401    |      *not implemented*       |  0x191  |                                   |                                      |                                               |
| 402    |      *not implemented*       |  0x192  |                                   |                                      |                                               |
| 403    |       clock_gettime64        |  0x193  |                 ?                 |                  ?                   |                       ?                       |
| 404    |       clock_settime64        |  0x194  |                 ?                 |                  ?                   |                       ?                       |
| 405    |       clock_adjtime64        |  0x195  |                 ?                 |                  ?                   |                       ?                       |
| 406    |     clock_getres_time64      |  0x196  |                 ?                 |                  ?                   |                       ?                       |
| 407    |    clock_nanosleep_time64    |  0x197  |                 ?                 |                  ?                   |                       ?                       |
| 408    |       timer_gettime64        |  0x198  |                 ?                 |                  ?                   |                       ?                       |
| 409    |       timer_settime64        |  0x199  |                 ?                 |                  ?                   |                       ?                       |
| 410    |      timerfd_gettime64       |  0x19a  |                 ?                 |                  ?                   |                       ?                       |
| 411    |      timerfd_settime64       |  0x19b  |                 ?                 |                  ?                   |                       ?                       |
| 412    |       utimensat_time64       |  0x19c  |                 ?                 |                  ?                   |                       ?                       |
| 413    |       pselect6_time64        |  0x19d  |                 ?                 |                  ?                   |                       ?                       |
| 414    |         ppoll_time64         |  0x19e  |                 ?                 |                  ?                   |                       ?                       |
| 415    |      *not implemented*       |  0x19f  |                                   |                                      |                                               |
| 416    |     io_pgetevents_time64     |  0x1a0  |                 ?                 |                  ?                   |                       ?                       |
| 417    |       recvmmsg_time64        |  0x1a1  |                 ?                 |                  ?                   |                       ?                       |
| 418    |     mq_timedsend_time64      |  0x1a2  |                 ?                 |                  ?                   |                       ?                       |
| 419    |    mq_timedreceive_time64    |  0x1a3  |                 ?                 |                  ?                   |                       ?                       |
| 420    |      semtimedop_time64       |  0x1a4  |                 ?                 |                  ?                   |                       ?                       |
| 421    |    rt_sigtimedwait_time64    |  0x1a5  |                 ?                 |                  ?                   |                       ?                       |
| 422    |         futex_time64         |  0x1a6  |                 ?                 |                  ?                   |                       ?                       |
| 423    | sched_rr_get_interval_time64 |  0x1a7  |                 ?                 |                  ?                   |                       ?                       |
| 424    |      *not implemented*       |  0x1a8  |                                   |                                      |                                               |
| 425    |        io_uring_setup        |  0x1a9  |            u32 entries            |      struct io_uring_params *p       |                       -                       |
| 426    |        io_uring_enter        |  0x1aa  |          unsigned int fd          |            u32 to_submit             |               u32 min_complete                |
| 427    |      *not implemented*       |  0x1ab  |                                   |                                      |                                               |
| 428    |      *not implemented*       |  0x1ac  |                                   |                                      |                                               |
| 429    |      *not implemented*       |  0x1ad  |                                   |                                      |                                               |
| 430    |      *not implemented*       |  0x1ae  |                                   |                                      |                                               |
| 431    |      *not implemented*       |  0x1af  |                                   |                                      |                                               |
| 432    |      *not implemented*       |  0x1b0  |                                   |                                      |                                               |
| 433    |      *not implemented*       |  0x1b1  |                                   |                                      |                                               |
| 434    |      *not implemented*       |  0x1b2  |                                   |                                      |                                               |
| 435    |      *not implemented*       |  0x1b3  |                                   |                                      |                                               |
| 436    |      *not implemented*       |  0x1b4  |                                   |                                      |                                               |
| 437    |      *not implemented*       |  0x1b5  |                                   |                                      |                                               |
| 438    |      *not implemented*       |  0x1b6  |                                   |                                      |                                               |
| 439    |          faccessat2          |  0x1b7  |              int dfd              |         const char *filename         |                   int mode                    |
| 983041 |        ARM_breakpoint        | 0xf0001 |                 ?                 |                  ?                   |                       ?                       |
| 983042 |        ARM_cacheflush        | 0xf0002 |                 ?                 |                  ?                   |                       ?                       |
| 983043 |          ARM_usr26           | 0xf0003 |                 ?                 |                  ?                   |                       ?                       |
| 983044 |          ARM_usr32           | 0xf0004 |                 ?                 |                  ?                   |                       ?                       |
| 983045 |         ARM_set_tls          | 0xf0005 |                 ?                 |                  ?                   |                       ?                       |

## aarch64系统调用表

| NR   |      syscall name      |  %x8  |            arg0 (%x0)             |              arg1 (%x1)              |                  arg2 (%x2)                   |
| ---- | :--------------------: | :---: | :-------------------------------: | :----------------------------------: | :-------------------------------------------: |
| 0    |        io_setup        | 0x00  |         unsigned nr_reqs          |          aio_context_t *ctx          |                       -                       |
| 1    |       io_destroy       | 0x01  |         aio_context_t ctx         |                  -                   |                       -                       |
| 2    |       io_submit        | 0x02  |           aio_context_t           |                 long                 |                  struct iocb                  |
| 3    |       io_cancel        | 0x03  |       aio_context_t ctx_id        |          struct iocb *iocb           |            struct io_event *result            |
| 4    |      io_getevents      | 0x04  |       aio_context_t ctx_id        |             long min_nr              |                    long nr                    |
| 5    |        setxattr        | 0x05  |         const char *path          |           const char *name           |               const void *value               |
| 6    |       lsetxattr        | 0x06  |         const char *path          |           const char *name           |               const void *value               |
| 7    |       fsetxattr        | 0x07  |              int fd               |           const char *name           |               const void *value               |
| 8    |        getxattr        | 0x08  |         const char *path          |           const char *name           |                  void *value                  |
| 9    |       lgetxattr        | 0x09  |         const char *path          |           const char *name           |                  void *value                  |
| 10   |       fgetxattr        | 0x0a  |              int fd               |           const char *name           |                  void *value                  |
| 11   |       listxattr        | 0x0b  |         const char *path          |              char *list              |                  size_t size                  |
| 12   |       llistxattr       | 0x0c  |         const char *path          |              char *list              |                  size_t size                  |
| 13   |       flistxattr       | 0x0d  |              int fd               |              char *list              |                  size_t size                  |
| 14   |      removexattr       | 0x0e  |         const char *path          |           const char *name           |                       -                       |
| 15   |      lremovexattr      | 0x0f  |         const char *path          |           const char *name           |                       -                       |
| 16   |      fremovexattr      | 0x10  |              int fd               |           const char *name           |                       -                       |
| 17   |         getcwd         | 0x11  |             char *buf             |          unsigned long size          |                       -                       |
| 18   |     lookup_dcookie     | 0x12  |           u64 cookie64            |              char *buf               |                  size_t len                   |
| 19   |        eventfd2        | 0x13  |        unsigned int count         |              int flags               |                       -                       |
| 20   |     epoll_create1      | 0x14  |             int flags             |                  -                   |                       -                       |
| 21   |       epoll_ctl        | 0x15  |             int epfd              |                int op                |                    int fd                     |
| 22   |      epoll_pwait       | 0x16  |             int epfd              |      struct epoll_event *events      |                 int maxevents                 |
| 23   |          dup           | 0x17  |        unsigned int fildes        |                  -                   |                       -                       |
| 24   |          dup3          | 0x18  |        unsigned int oldfd         |          unsigned int newfd          |                   int flags                   |
| 25   |         fcntl          | 0x19  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 26   |     inotify_init1      | 0x1a  |             int flags             |                  -                   |                       -                       |
| 27   |   inotify_add_watch    | 0x1b  |              int fd               |           const char *path           |                   u32 mask                    |
| 28   |    inotify_rm_watch    | 0x1c  |              int fd               |               __s32 wd               |                       -                       |
| 29   |         ioctl          | 0x1d  |          unsigned int fd          |           unsigned int cmd           |               unsigned long arg               |
| 30   |       ioprio_set       | 0x1e  |             int which             |               int who                |                  int ioprio                   |
| 31   |       ioprio_get       | 0x1f  |             int which             |               int who                |                       -                       |
| 32   |         flock          | 0x20  |          unsigned int fd          |           unsigned int cmd           |                       -                       |
| 33   |        mknodat         | 0x21  |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 34   |        mkdirat         | 0x22  |              int dfd              |        const char * pathname         |                 umode_t mode                  |
| 35   |        unlinkat        | 0x23  |              int dfd              |        const char * pathname         |                   int flag                    |
| 36   |       symlinkat        | 0x24  |       const char * oldname        |              int newdfd              |             const char * newname              |
| 37   |         linkat         | 0x25  |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 38   |        renameat        | 0x26  |            int olddfd             |         const char * oldname         |                  int newdfd                   |
| 39   |        umount2         | 0x27  |                 ?                 |                  ?                   |                       ?                       |
| 40   |         mount          | 0x28  |          char *dev_name           |            char *dir_name            |                  char *type                   |
| 41   |       pivot_root       | 0x29  |       const char *new_root        |         const char *put_old          |                       -                       |
| 42   |       nfsservctl       | 0x2a  |                 ?                 |                  ?                   |                       ?                       |
| 43   |         statfs         | 0x2b  |         const char * path         |          struct statfs *buf          |                       -                       |
| 44   |        fstatfs         | 0x2c  |          unsigned int fd          |          struct statfs *buf          |                       -                       |
| 45   |        truncate        | 0x2d  |         const char *path          |             long length              |                       -                       |
| 46   |       ftruncate        | 0x2e  |          unsigned int fd          |         unsigned long length         |                       -                       |
| 47   |       fallocate        | 0x2f  |              int fd               |               int mode               |                 loff_t offset                 |
| 48   |       faccessat        | 0x30  |              int dfd              |         const char *filename         |                   int mode                    |
| 49   |         chdir          | 0x31  |       const char *filename        |                  -                   |                       -                       |
| 50   |         fchdir         | 0x32  |          unsigned int fd          |                  -                   |                       -                       |
| 51   |         chroot         | 0x33  |       const char *filename        |                  -                   |                       -                       |
| 52   |         fchmod         | 0x34  |          unsigned int fd          |             umode_t mode             |                       -                       |
| 53   |        fchmodat        | 0x35  |              int dfd              |        const char * filename         |                 umode_t mode                  |
| 54   |        fchownat        | 0x36  |              int dfd              |         const char *filename         |                  uid_t user                   |
| 55   |         fchown         | 0x37  |          unsigned int fd          |              uid_t user              |                  gid_t group                  |
| 56   |         openat         | 0x38  |              int dfd              |         const char *filename         |                   int flags                   |
| 57   |         close          | 0x39  |          unsigned int fd          |                  -                   |                       -                       |
| 58   |        vhangup         | 0x3a  |                 -                 |                  -                   |                       -                       |
| 59   |         pipe2          | 0x3b  |            int *fildes            |              int flags               |                       -                       |
| 60   |        quotactl        | 0x3c  |         unsigned int cmd          |         const char *special          |                   qid_t id                    |
| 61   |       getdents64       | 0x3d  |          unsigned int fd          |    struct linux_dirent64 *dirent     |              unsigned int count               |
| 62   |         lseek          | 0x3e  |          unsigned int fd          |             off_t offset             |              unsigned int whence              |
| 63   |          read          | 0x3f  |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 64   |         write          | 0x40  |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 65   |         readv          | 0x41  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 66   |         writev         | 0x42  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 67   |        pread64         | 0x43  |          unsigned int fd          |              char *buf               |                 size_t count                  |
| 68   |        pwrite64        | 0x44  |          unsigned int fd          |           const char *buf            |                 size_t count                  |
| 69   |         preadv         | 0x45  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 70   |        pwritev         | 0x46  |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 71   |        sendfile        | 0x47  |            int out_fd             |              int in_fd               |                 off_t *offset                 |
| 72   |        pselect6        | 0x48  |                int                |               fd_set *               |                   fd_set *                    |
| 73   |         ppoll          | 0x49  |          struct pollfd *          |             unsigned int             |          struct __kernel_timespec *           |
| 74   |       signalfd4        | 0x4a  |              int ufd              |         sigset_t *user_mask          |                size_t sizemask                |
| 75   |        vmsplice        | 0x4b  |              int fd               |       const struct iovec *iov        |             unsigned long nr_segs             |
| 76   |         splice         | 0x4c  |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 77   |          tee           | 0x4d  |             int fdin              |              int fdout               |                  size_t len                   |
| 78   |       readlinkat       | 0x4e  |              int dfd              |           const char *path           |                   char *buf                   |
| 79   |       newfstatat       | 0x4f  |              int dfd              |         const char *filename         |             struct stat *statbuf              |
| 80   |         fstat          | 0x50  |          unsigned int fd          |  struct __old_kernel_stat *statbuf   |                       -                       |
| 81   |          sync          | 0x51  |                 -                 |                  -                   |                       -                       |
| 82   |         fsync          | 0x52  |          unsigned int fd          |                  -                   |                       -                       |
| 83   |       fdatasync        | 0x53  |          unsigned int fd          |                  -                   |                       -                       |
| 84   |    sync_file_range     | 0x54  |              int fd               |            loff_t offset             |                 loff_t nbytes                 |
| 85   |     timerfd_create     | 0x55  |            int clockid            |              int flags               |                       -                       |
| 86   |    timerfd_settime     | 0x56  |              int ufd              |              int flags               |    const struct __kernel_itimerspec *utmr     |
| 87   |    timerfd_gettime     | 0x57  |              int ufd              |   struct __kernel_itimerspec *otmr   |                       -                       |
| 88   |       utimensat        | 0x58  |              int dfd              |         const char *filename         |       struct __kernel_timespec *utimes        |
| 89   |          acct          | 0x59  |         const char *name          |                  -                   |                       -                       |
| 90   |         capget         | 0x5a  |     cap_user_header_t header      |       cap_user_data_t dataptr        |                       -                       |
| 91   |         capset         | 0x5b  |     cap_user_header_t header      |      const cap_user_data_t data      |                       -                       |
| 92   |      personality       | 0x5c  |     unsigned int personality      |                  -                   |                       -                       |
| 93   |          exit          | 0x5d  |          int error_code           |                  -                   |                       -                       |
| 94   |       exit_group       | 0x5e  |          int error_code           |                  -                   |                       -                       |
| 95   |         waitid         | 0x5f  |             int which             |              pid_t pid               |             struct siginfo *infop             |
| 96   |    set_tid_address     | 0x60  |            int *tidptr            |                  -                   |                       -                       |
| 97   |        unshare         | 0x61  |    unsigned long unshare_flags    |                  -                   |                       -                       |
| 98   |         futex          | 0x62  |            u32 *uaddr             |                int op                |                    u32 val                    |
| 99   |    set_robust_list     | 0x63  |   struct robust_list_head *head   |              size_t len              |                       -                       |
| 100  |    get_robust_list     | 0x64  |              int pid              |   struct robust_list_head head_ptr   |                size_t *len_ptr                |
| 101  |       nanosleep        | 0x65  |  struct __kernel_timespec *rqtp   |    struct __kernel_timespec *rmtp    |                       -                       |
| 102  |       getitimer        | 0x66  |             int which             | struct __kernel_old_itimerval *value |                       -                       |
| 103  |       setitimer        | 0x67  |             int which             | struct __kernel_old_itimerval *value |     struct __kernel_old_itimerval *ovalue     |
| 104  |       kexec_load       | 0x68  |        unsigned long entry        |      unsigned long nr_segments       |        struct kexec_segment *segments         |
| 105  |      init_module       | 0x69  |            void *umod             |          unsigned long len           |               const char *uargs               |
| 106  |     delete_module      | 0x6a  |       const char *name_user       |          unsigned int flags          |                       -                       |
| 107  |      timer_create      | 0x6b  |       clockid_t which_clock       |  struct sigevent *timer_event_spec   |          timer_t * created_timer_id           |
| 108  |     timer_gettime      | 0x6c  |         timer_t timer_id          | struct __kernel_itimerspec *setting  |                       -                       |
| 109  |    timer_getoverrun    | 0x6d  |         timer_t timer_id          |                  -                   |                       -                       |
| 110  |     timer_settime      | 0x6e  |         timer_t timer_id          |              int flags               | const struct __kernel_itimerspec *new_setting |
| 111  |      timer_delete      | 0x6f  |         timer_t timer_id          |                  -                   |                       -                       |
| 112  |     clock_settime      | 0x70  |       clockid_t which_clock       |  const struct __kernel_timespec *tp  |                       -                       |
| 113  |     clock_gettime      | 0x71  |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 114  |      clock_getres      | 0x72  |       clockid_t which_clock       |     struct __kernel_timespec *tp     |                       -                       |
| 115  |    clock_nanosleep     | 0x73  |       clockid_t which_clock       |              int flags               |     const struct __kernel_timespec *rqtp      |
| 116  |         syslog         | 0x74  |             int type              |              char *buf               |                    int len                    |
| 117  |         ptrace         | 0x75  |           long request            |               long pid               |              unsigned long addr               |
| 118  |     sched_setparam     | 0x76  |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 119  |   sched_setscheduler   | 0x77  |             pid_t pid             |              int policy              |           struct sched_param *param           |
| 120  |   sched_getscheduler   | 0x78  |             pid_t pid             |                  -                   |                       -                       |
| 121  |     sched_getparam     | 0x79  |             pid_t pid             |      struct sched_param *param       |                       -                       |
| 122  |   sched_setaffinity    | 0x7a  |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 123  |   sched_getaffinity    | 0x7b  |             pid_t pid             |           unsigned int len           |         unsigned long *user_mask_ptr          |
| 124  |      sched_yield       | 0x7c  |                 -                 |                  -                   |                       -                       |
| 125  | sched_get_priority_max | 0x7d  |            int policy             |                  -                   |                       -                       |
| 126  | sched_get_priority_min | 0x7e  |            int policy             |                  -                   |                       -                       |
| 127  | sched_rr_get_interval  | 0x7f  |             pid_t pid             |  struct __kernel_timespec *interval  |                       -                       |
| 128  |    restart_syscall     | 0x80  |                 -                 |                  -                   |                       -                       |
| 129  |          kill          | 0x81  |             pid_t pid             |               int sig                |                       -                       |
| 130  |         tkill          | 0x82  |             pid_t pid             |               int sig                |                       -                       |
| 131  |         tgkill         | 0x83  |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 132  |      sigaltstack       | 0x84  |   const struct sigaltstack *uss   |       struct sigaltstack *uoss       |                       -                       |
| 133  |     rt_sigsuspend      | 0x85  |         sigset_t *unewset         |          size_t sigsetsize           |                       -                       |
| 134  |      rt_sigaction      | 0x86  |                int                |       const struct sigaction *       |              struct sigaction *               |
| 135  |     rt_sigprocmask     | 0x87  |              int how              |            sigset_t *set             |                sigset_t *oset                 |
| 136  |     rt_sigpending      | 0x88  |           sigset_t *set           |          size_t sigsetsize           |                       -                       |
| 137  |    rt_sigtimedwait     | 0x89  |      const sigset_t *uthese       |           siginfo_t *uinfo           |      const struct __kernel_timespec *uts      |
| 138  |    rt_sigqueueinfo     | 0x8a  |             pid_t pid             |               int sig                |               siginfo_t *uinfo                |
| 139  |      rt_sigreturn      | 0x8b  |                 ?                 |                  ?                   |                       ?                       |
| 140  |      setpriority       | 0x8c  |             int which             |               int who                |                  int niceval                  |
| 141  |      getpriority       | 0x8d  |             int which             |               int who                |                       -                       |
| 142  |         reboot         | 0x8e  |            int magic1             |              int magic2              |               unsigned int cmd                |
| 143  |        setregid        | 0x8f  |            gid_t rgid             |              gid_t egid              |                       -                       |
| 144  |         setgid         | 0x90  |             gid_t gid             |                  -                   |                       -                       |
| 145  |        setreuid        | 0x91  |            uid_t ruid             |              uid_t euid              |                       -                       |
| 146  |         setuid         | 0x92  |             uid_t uid             |                  -                   |                       -                       |
| 147  |       setresuid        | 0x93  |            uid_t ruid             |              uid_t euid              |                  uid_t suid                   |
| 148  |       getresuid        | 0x94  |            uid_t *ruid            |             uid_t *euid              |                  uid_t *suid                  |
| 149  |       setresgid        | 0x95  |            gid_t rgid             |              gid_t egid              |                  gid_t sgid                   |
| 150  |       getresgid        | 0x96  |            gid_t *rgid            |             gid_t *egid              |                  gid_t *sgid                  |
| 151  |        setfsuid        | 0x97  |             uid_t uid             |                  -                   |                       -                       |
| 152  |        setfsgid        | 0x98  |             gid_t gid             |                  -                   |                       -                       |
| 153  |         times          | 0x99  |         struct tms *tbuf          |                  -                   |                       -                       |
| 154  |        setpgid         | 0x9a  |             pid_t pid             |              pid_t pgid              |                       -                       |
| 155  |        getpgid         | 0x9b  |             pid_t pid             |                  -                   |                       -                       |
| 156  |         getsid         | 0x9c  |             pid_t pid             |                  -                   |                       -                       |
| 157  |         setsid         | 0x9d  |                 -                 |                  -                   |                       -                       |
| 158  |       getgroups        | 0x9e  |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 159  |       setgroups        | 0x9f  |          int gidsetsize           |           gid_t *grouplist           |                       -                       |
| 160  |         uname          | 0xa0  |       struct old_utsname *        |                  -                   |                       -                       |
| 161  |      sethostname       | 0xa1  |            char *name             |               int len                |                       -                       |
| 162  |     setdomainname      | 0xa2  |            char *name             |               int len                |                       -                       |
| 163  |       getrlimit        | 0xa3  |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 164  |       setrlimit        | 0xa4  |       unsigned int resource       |         struct rlimit *rlim          |                       -                       |
| 165  |       getrusage        | 0xa5  |              int who              |          struct rusage *ru           |                       -                       |
| 166  |         umask          | 0xa6  |             int mask              |                  -                   |                       -                       |
| 167  |         prctl          | 0xa7  |            int option             |          unsigned long arg2          |              unsigned long arg3               |
| 168  |         getcpu         | 0xa8  |           unsigned *cpu           |            unsigned *node            |          struct getcpu_cache *cache           |
| 169  |      gettimeofday      | 0xa9  |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 170  |      settimeofday      | 0xaa  |  struct __kernel_old_timeval *tv  |         struct timezone *tz          |                       -                       |
| 171  |        adjtimex        | 0xab  |   struct __kernel_timex *txc_p    |                  -                   |                       -                       |
| 172  |         getpid         | 0xac  |                 -                 |                  -                   |                       -                       |
| 173  |        getppid         | 0xad  |                 -                 |                  -                   |                       -                       |
| 174  |         getuid         | 0xae  |                 -                 |                  -                   |                       -                       |
| 175  |        geteuid         | 0xaf  |                 -                 |                  -                   |                       -                       |
| 176  |         getgid         | 0xb0  |                 -                 |                  -                   |                       -                       |
| 177  |        getegid         | 0xb1  |                 -                 |                  -                   |                       -                       |
| 178  |         gettid         | 0xb2  |                 -                 |                  -                   |                       -                       |
| 179  |        sysinfo         | 0xb3  |       struct sysinfo *info        |                  -                   |                       -                       |
| 180  |        mq_open         | 0xb4  |         const char *name          |              int oflag               |                 umode_t mode                  |
| 181  |       mq_unlink        | 0xb5  |         const char *name          |                  -                   |                       -                       |
| 182  |      mq_timedsend      | 0xb6  |            mqd_t mqdes            |         const char *msg_ptr          |                size_t msg_len                 |
| 183  |    mq_timedreceive     | 0xb7  |            mqd_t mqdes            |            char *msg_ptr             |                size_t msg_len                 |
| 184  |       mq_notify        | 0xb8  |            mqd_t mqdes            | const struct sigevent *notification  |                       -                       |
| 185  |     mq_getsetattr      | 0xb9  |            mqd_t mqdes            |     const struct mq_attr *mqstat     |            struct mq_attr *omqstat            |
| 186  |         msgget         | 0xba  |             key_t key             |              int msgflg              |                       -                       |
| 187  |         msgctl         | 0xbb  |             int msqid             |               int cmd                |             struct msqid_ds *buf              |
| 188  |         msgrcv         | 0xbc  |             int msqid             |         struct msgbuf *msgp          |                 size_t msgsz                  |
| 189  |         msgsnd         | 0xbd  |             int msqid             |         struct msgbuf *msgp          |                 size_t msgsz                  |
| 190  |         semget         | 0xbe  |             key_t key             |              int nsems               |                  int semflg                   |
| 191  |         semctl         | 0xbf  |             int semid             |              int semnum              |                    int cmd                    |
| 192  |       semtimedop       | 0xc0  |             int semid             |         struct sembuf *sops          |                unsigned nsops                 |
| 193  |         semop          | 0xc1  |             int semid             |         struct sembuf *sops          |                unsigned nsops                 |
| 194  |         shmget         | 0xc2  |             key_t key             |             size_t size              |                   int flag                    |
| 195  |         shmctl         | 0xc3  |             int shmid             |               int cmd                |             struct shmid_ds *buf              |
| 196  |         shmat          | 0xc4  |             int shmid             |            char *shmaddr             |                  int shmflg                   |
| 197  |         shmdt          | 0xc5  |           char *shmaddr           |                  -                   |                       -                       |
| 198  |         socket         | 0xc6  |                int                |                 int                  |                      int                      |
| 199  |       socketpair       | 0xc7  |                int                |                 int                  |                      int                      |
| 200  |          bind          | 0xc8  |                int                |          struct sockaddr *           |                      int                      |
| 201  |         listen         | 0xc9  |                int                |                 int                  |                       -                       |
| 202  |         accept         | 0xca  |                int                |          struct sockaddr *           |                     int *                     |
| 203  |        connect         | 0xcb  |                int                |          struct sockaddr *           |                      int                      |
| 204  |      getsockname       | 0xcc  |                int                |          struct sockaddr *           |                     int *                     |
| 205  |      getpeername       | 0xcd  |                int                |          struct sockaddr *           |                     int *                     |
| 206  |         sendto         | 0xce  |                int                |                void *                |                    size_t                     |
| 207  |        recvfrom        | 0xcf  |                int                |                void *                |                    size_t                     |
| 208  |       setsockopt       | 0xd0  |              int fd               |              int level               |                  int optname                  |
| 209  |       getsockopt       | 0xd1  |              int fd               |              int level               |                  int optname                  |
| 210  |        shutdown        | 0xd2  |                int                |                 int                  |                       -                       |
| 211  |        sendmsg         | 0xd3  |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 212  |        recvmsg         | 0xd4  |              int fd               |       struct user_msghdr *msg        |                unsigned flags                 |
| 213  |       readahead        | 0xd5  |              int fd               |            loff_t offset             |                 size_t count                  |
| 214  |          brk           | 0xd6  |         unsigned long brk         |                  -                   |                       -                       |
| 215  |         munmap         | 0xd7  |        unsigned long addr         |              size_t len              |                       -                       |
| 216  |         mremap         | 0xd8  |        unsigned long addr         |        unsigned long old_len         |             unsigned long new_len             |
| 217  |        add_key         | 0xd9  |         const char *_type         |       const char *_description       |             const void *_payload              |
| 218  |      request_key       | 0xda  |         const char *_type         |       const char *_description       |           const char *_callout_info           |
| 219  |         keyctl         | 0xdb  |              int cmd              |          unsigned long arg2          |              unsigned long arg3               |
| 220  |         clone          | 0xdc  |           unsigned long           |            unsigned long             |                     int *                     |
| 221  |         execve         | 0xdd  |       const char *filename        |       const char *const *argv        |            const char *const *envp            |
| 222  |          mmap          | 0xde  |                 ?                 |                  ?                   |                       ?                       |
| 223  |       fadvise64        | 0xdf  |              int fd               |            loff_t offset             |                  size_t len                   |
| 224  |         swapon         | 0xe0  |      const char *specialfile      |            int swap_flags            |                       -                       |
| 225  |        swapoff         | 0xe1  |      const char *specialfile      |                  -                   |                       -                       |
| 226  |        mprotect        | 0xe2  |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 227  |         msync          | 0xe3  |        unsigned long start        |              size_t len              |                   int flags                   |
| 228  |         mlock          | 0xe4  |        unsigned long start        |              size_t len              |                       -                       |
| 229  |        munlock         | 0xe5  |        unsigned long start        |              size_t len              |                       -                       |
| 230  |        mlockall        | 0xe6  |             int flags             |                  -                   |                       -                       |
| 231  |       munlockall       | 0xe7  |                 -                 |                  -                   |                       -                       |
| 232  |        mincore         | 0xe8  |        unsigned long start        |              size_t len              |              unsigned char * vec              |
| 233  |        madvise         | 0xe9  |        unsigned long start        |              size_t len              |                 int behavior                  |
| 234  |    remap_file_pages    | 0xea  |        unsigned long start        |          unsigned long size          |              unsigned long prot               |
| 235  |         mbind          | 0xeb  |        unsigned long start        |          unsigned long len           |              unsigned long mode               |
| 236  |     get_mempolicy      | 0xec  |            int *policy            |         unsigned long *nmask         |             unsigned long maxnode             |
| 237  |     set_mempolicy      | 0xed  |             int mode              |      const unsigned long *nmask      |             unsigned long maxnode             |
| 238  |     migrate_pages      | 0xee  |             pid_t pid             |        unsigned long maxnode         |           const unsigned long *from           |
| 239  |       move_pages       | 0xef  |             pid_t pid             |        unsigned long nr_pages        |               const void pages                |
| 240  |   rt_tgsigqueueinfo    | 0xf0  |            pid_t tgid             |              pid_t pid               |                    int sig                    |
| 241  |    perf_event_open     | 0xf1  | struct perf_event_attr *attr_uptr |              pid_t pid               |                    int cpu                    |
| 242  |        accept4         | 0xf2  |                int                |          struct sockaddr *           |                     int *                     |
| 243  |        recvmmsg        | 0xf3  |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 244  |   *not implemented*    | 0xf4  |                                   |                                      |                                               |
| 245  |   *not implemented*    | 0xf5  |                                   |                                      |                                               |
| 246  |   *not implemented*    | 0xf6  |                                   |                                      |                                               |
| 247  |   *not implemented*    | 0xf7  |                                   |                                      |                                               |
| 248  |   *not implemented*    | 0xf8  |                                   |                                      |                                               |
| 249  |   *not implemented*    | 0xf9  |                                   |                                      |                                               |
| 250  |   *not implemented*    | 0xfa  |                                   |                                      |                                               |
| 251  |   *not implemented*    | 0xfb  |                                   |                                      |                                               |
| 252  |   *not implemented*    | 0xfc  |                                   |                                      |                                               |
| 253  |   *not implemented*    | 0xfd  |                                   |                                      |                                               |
| 254  |   *not implemented*    | 0xfe  |                                   |                                      |                                               |
| 255  |   *not implemented*    | 0xff  |                                   |                                      |                                               |
| 256  |   *not implemented*    | 0x100 |                                   |                                      |                                               |
| 257  |   *not implemented*    | 0x101 |                                   |                                      |                                               |
| 258  |   *not implemented*    | 0x102 |                                   |                                      |                                               |
| 259  |   *not implemented*    | 0x103 |                                   |                                      |                                               |
| 260  |         wait4          | 0x104 |             pid_t pid             |            int *stat_addr            |                  int options                  |
| 261  |       prlimit64        | 0x105 |             pid_t pid             |        unsigned int resource         |        const struct rlimit64 *new_rlim        |
| 262  |     fanotify_init      | 0x106 |        unsigned int flags         |      unsigned int event_f_flags      |                       -                       |
| 263  |     fanotify_mark      | 0x107 |          int fanotify_fd          |          unsigned int flags          |                   u64 mask                    |
| 264  |   name_to_handle_at    | 0x108 |              int dfd              |           const char *name           |          struct file_handle *handle           |
| 265  |   open_by_handle_at    | 0x109 |          int mountdirfd           |      struct file_handle *handle      |                   int flags                   |
| 266  |     clock_adjtime      | 0x10a |       clockid_t which_clock       |      struct __kernel_timex *tx       |                       -                       |
| 267  |         syncfs         | 0x10b |              int fd               |                  -                   |                       -                       |
| 268  |         setns          | 0x10c |              int fd               |              int nstype              |                       -                       |
| 269  |        sendmmsg        | 0x10d |              int fd               |         struct mmsghdr *msg          |               unsigned int vlen               |
| 270  |    process_vm_readv    | 0x10e |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 271  |   process_vm_writev    | 0x10f |             pid_t pid             |       const struct iovec *lvec       |             unsigned long liovcnt             |
| 272  |          kcmp          | 0x110 |            pid_t pid1             |              pid_t pid2              |                   int type                    |
| 273  |      finit_module      | 0x111 |              int fd               |          const char *uargs           |                   int flags                   |
| 274  |     sched_setattr      | 0x112 |             pid_t pid             |       struct sched_attr *attr        |              unsigned int flags               |
| 275  |     sched_getattr      | 0x113 |             pid_t pid             |       struct sched_attr *attr        |               unsigned int size               |
| 276  |       renameat2        | 0x114 |            int olddfd             |         const char *oldname          |                  int newdfd                   |
| 277  |        seccomp         | 0x115 |          unsigned int op          |          unsigned int flags          |                  void *uargs                  |
| 278  |       getrandom        | 0x116 |             char *buf             |             size_t count             |              unsigned int flags               |
| 279  |      memfd_create      | 0x117 |       const char *uname_ptr       |          unsigned int flags          |                       -                       |
| 280  |          bpf           | 0x118 |              int cmd              |         union bpf_attr *attr         |               unsigned int size               |
| 281  |        execveat        | 0x119 |              int dfd              |         const char *filename         |            const char *const *argv            |
| 282  |      userfaultfd       | 0x11a |             int flags             |                  -                   |                       -                       |
| 283  |       membarrier       | 0x11b |              int cmd              |          unsigned int flags          |                  int cpu_id                   |
| 284  |         mlock2         | 0x11c |        unsigned long start        |              size_t len              |                   int flags                   |
| 285  |    copy_file_range     | 0x11d |             int fd_in             |            loff_t *off_in            |                  int fd_out                   |
| 286  |        preadv2         | 0x11e |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 287  |        pwritev2        | 0x11f |         unsigned long fd          |       const struct iovec *vec        |              unsigned long vlen               |
| 288  |     pkey_mprotect      | 0x120 |        unsigned long start        |              size_t len              |              unsigned long prot               |
| 289  |       pkey_alloc       | 0x121 |        unsigned long flags        |        unsigned long init_val        |                       -                       |
| 290  |       pkey_free        | 0x122 |             int pkey              |                  -                   |                       -                       |
| 291  |         statx          | 0x123 |              int dfd              |           const char *path           |                unsigned flags                 |
| 425  |     io_uring_setup     | 0x1a9 |            u32 entries            |      struct io_uring_params *p       |                       -                       |
| 426  |     io_uring_enter     | 0x1aa |          unsigned int fd          |            u32 to_submit             |               u32 min_complete                |
| 427  |   *not implemented*    | 0x1ab |                                   |                                      |                                               |
| 428  |   *not implemented*    | 0x1ac |                                   |                                      |                                               |
| 429  |   *not implemented*    | 0x1ad |                                   |                                      |                                               |
| 430  |   *not implemented*    | 0x1ae |                                   |                                      |                                               |
| 431  |   *not implemented*    | 0x1af |                                   |                                      |                                               |
| 432  |   *not implemented*    | 0x1b0 |                                   |                                      |                                               |
| 433  |   *not implemented*    | 0x1b1 |                                   |                                      |                                               |
| 434  |   *not implemented*    | 0x1b2 |                                   |                                      |                                               |
| 435  |   *not implemented*    | 0x1b3 |                                   |                                      |                                               |
| 436  |   *not implemented*    | 0x1b4 |                                   |                                      |                                               |
| 437  |   *not implemented*    | 0x1b5 |                                   |                                      |                                               |
| 438  |   *not implemented*    | 0x1b6 |                                   |                                      |                                               |
| 439  |       faccessat2       | 0x1b7 |              int dfd              |         const char *filename         |                   int mode                    |

