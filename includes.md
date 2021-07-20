```
#include <dirent.h> /* opendir, readdir */
#include <errno.h> /* errno */
#include <fcntl.h> /* open, O_CREAT */
#include <grp.h> /* getgrent */
#include <netdb.h> /* gethostbyname, hstrerror, h_errno */
#include <netinet/in.h> /* sockaddr_in */
#include <pthread.h> /* pthread_create, pthread_join */
#include <pwd.h> /* getpwent, getpwnam, getpwuid */
#include <semaphore.h> /* sem_init, sem_wait, sem_post */
#include <stdio.h> /* fputs, fprintf, perror, printf, puts */
#include <stdlib.h> /* exit, getenv */
#include <string.h> /* strcpy, strlen */
#include <sys/socket.h> /* socket, connect, socketpair */
#include <sys/stat.h> /* chmod, fchmod, mkdir */
#include <sys/types.h> /* alsomkdir */
#include <sys/un.h> /* sockaddr_un */
#include <sys/wait.h> /* waitpid */
#include <unistd.h> /* close */
#include <unistd.h> /* dup, dup2, read, write, exec1, execvp, fork, pipe, getpid, getuid, getgid,  getgroups, link, symlink, readlink, rmdir, chdir, sleep, usleep, unlink */
```
