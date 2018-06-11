# configure配置

nginx的编译过程，第一步是configure。我们使用 --help可以看到configure的很多配置。

在编译安装Nginx时，我们需要使用configure命令做大量"幕后“工作，包括检测操作系统内核和已经安装的软件，参数解析，中间目录生成以及根据各种参数生成的.c文件、Makefile文件等。

默认我们configure运行之后，nginx里面多了一个文件Makefile，也多了一个文件夹objs

objs文件夹里面有这几个文件：

![](http://tuchuang.funaio.cn/18-6-7/71206260.jpg)

configure的命令说明可以看

https://github.com/its-tech/nginx-1.14.0-research/blob/master/configure

configure的输出：
```
checking for OS
 + Linux 3.10.0-514.16.1.el7.x86_64 x86_64
checking for C compiler ... found
 + using GNU C compiler
 + gcc version: 4.4.7 20120313 (Red Hat 4.4.7-18) (GCC)
checking for gcc -pipe switch ... found
checking for -Wl,-E switch ... found
checking for gcc builtin atomic operations ... found
checking for C99 variadic macros ... found
checking for gcc variadic macros ... found
checking for gcc builtin 64 bit byteswap ... found
checking for unistd.h ... found
checking for inttypes.h ... found
checking for limits.h ... found
checking for sys/filio.h ... not found
checking for sys/param.h ... found
checking for sys/mount.h ... found
checking for sys/statvfs.h ... found
checking for crypt.h ... found
checking for Linux specific features
checking for epoll ... found
checking for EPOLLRDHUP ... found
checking for EPOLLEXCLUSIVE ... not found
checking for O_PATH ... not found
checking for sendfile() ... found
checking for sendfile64() ... found
checking for sys/prctl.h ... found
checking for prctl(PR_SET_DUMPABLE) ... found
checking for prctl(PR_SET_KEEPCAPS) ... found
checking for capabilities ... found
checking for crypt_r() ... found
checking for sys/vfs.h ... found
checking for nobody group ... found
checking for poll() ... found
checking for /dev/poll ... not found
checking for kqueue ... not found
checking for crypt() ... not found
checking for crypt() in libcrypt ... found
checking for F_READAHEAD ... not found
checking for posix_fadvise() ... found
checking for O_DIRECT ... found
checking for F_NOCACHE ... not found
checking for directio() ... not found
checking for statfs() ... found
checking for statvfs() ... found
checking for dlopen() ... not found
checking for dlopen() in libdl ... found
checking for sched_yield() ... found
checking for sched_setaffinity() ... found
checking for SO_SETFIB ... not found
checking for SO_REUSEPORT ... found
checking for SO_ACCEPTFILTER ... not found
checking for SO_BINDANY ... not found
checking for IP_TRANSPARENT ... found
checking for IP_BINDANY ... not found
checking for IP_BIND_ADDRESS_NO_PORT ... not found
checking for IP_RECVDSTADDR ... not found
checking for IP_SENDSRCADDR ... not found
checking for IP_PKTINFO ... found
checking for IPV6_RECVPKTINFO ... found
checking for TCP_DEFER_ACCEPT ... found
checking for TCP_KEEPIDLE ... found
checking for TCP_FASTOPEN ... not found
checking for TCP_INFO ... found
checking for accept4() ... found
checking for eventfd() ... found
checking for int size ... 4 bytes
checking for long size ... 8 bytes
checking for long long size ... 8 bytes
checking for void * size ... 8 bytes
checking for uint32_t ... found
checking for uint64_t ... found
checking for sig_atomic_t ... found
checking for sig_atomic_t size ... 4 bytes
checking for socklen_t ... found
checking for in_addr_t ... found
checking for in_port_t ... found
checking for rlim_t ... found
checking for uintptr_t ... uintptr_t found
checking for system byte ordering ... little endian
checking for size_t size ... 8 bytes
checking for off_t size ... 8 bytes
checking for time_t size ... 8 bytes
checking for AF_INET6 ... found
checking for setproctitle() ... not found
checking for pread() ... found
checking for pwrite() ... found
checking for pwritev() ... found
checking for sys_nerr ... found
checking for localtime_r() ... found
checking for clock_gettime(CLOCK_MONOTONIC) ... not found
checking for clock_gettime(CLOCK_MONOTONIC) in librt ... found
checking for posix_memalign() ... found
checking for memalign() ... found
checking for mmap(MAP_ANON|MAP_SHARED) ... found
checking for mmap("/dev/zero", MAP_SHARED) ... found
checking for System V shared memory ... found
checking for POSIX semaphores ... not found
checking for POSIX semaphores in libpthread ... found
checking for struct msghdr.msg_control ... found
checking for ioctl(FIONBIO) ... found
checking for struct tm.tm_gmtoff ... found
checking for struct dirent.d_namlen ... not found
checking for struct dirent.d_type ... found
checking for sysconf(_SC_NPROCESSORS_ONLN) ... found
checking for sysconf(_SC_LEVEL1_DCACHE_LINESIZE) ... found
checking for openat(), fstatat() ... found
checking for getaddrinfo() ... found
checking for zlib library ... found
creating objs/Makefile

Configuration summary
  + PCRE library is not used
  + OpenSSL library is not used
  + using system zlib library

  nginx path prefix: "/usr/local/nginx"
  nginx binary file: "/usr/local/nginx/sbin/nginx"
  nginx modules path: "/usr/local/nginx/modules"
  nginx configuration prefix: "/usr/local/nginx/conf"
  nginx configuration file: "/usr/local/nginx/conf/nginx.conf"
  nginx pid file: "/usr/local/nginx/logs/nginx.pid"
  nginx error log file: "/usr/local/nginx/logs/error.log"
  nginx http access log file: "/usr/local/nginx/logs/access.log"
  nginx http client request body temporary files: "client_body_temp"
  nginx http proxy temporary files: "proxy_temp"
  nginx http fastcgi temporary files: "fastcgi_temp"
  nginx http uwsgi temporary files: "uwsgi_temp"
  nginx http scgi temporary files: "scgi_temp
```

# objs是干什么的

## ngx_modules.c

我们可以在这个文件里面看到我们启动的nginx加载了哪些模块。

其中的一些模块是很重要的，但是我们也可以自主选择。

## autoconf.err

这个文件记录检查的结果。

## Makefile

nginx/Makefile 只是一个命令的封装，实际上本质的Makefile操作是在objs/Makefile里面的。

我们可以看一下，它使用的编译器是cc， cc编译器在linux上就是gcc。 gcc是一个各种不同语言的编译器。gcc代表 the GNU Compiler Collection。 比如你的代码后缀是.c， 它会调用c的编译器还有linker去链接c的库。

# configure的语法

## have 语法：
```
have=NGX_SBIN_PATH value="\"$NGX_SBIN_PATH\"" . auto/define
```

autodefine的定义：

```
# Copyright (C) Igor Sysoev
# Copyright (C) Nginx, Inc.


cat << END >> $NGX_AUTO_CONFIG_H

#ifndef $have
#define $have  $value
#endif

END
```




# 参考文章
https://blog.csdn.net/fzy0201/article/details/17683883
