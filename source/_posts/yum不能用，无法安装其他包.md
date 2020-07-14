---

title: yum不能用，无法安装其他包，也无法update，报错“One of the configured repositories failed (Unknown),”

date: 2020-04-11 14:17:56

tags: 
 - Linux

---

### 问题： 

我刚刚买一台服务器出现，这种情况！

```shell
已加载插件：fastestmirror
base                                                                                                                                                                              | 3.6 kB  00:00:00     

 One of the configured repositories failed (未知),
 and yum doesn't have enough cached data to continue. At this point the only
 safe thing yum can do is fail. There are a few ways to work "fix" this:

     1. Contact the upstream for the repository and get them to fix the problem.

     2. Reconfigure the baseurl/etc. for the repository, to point to a working
        upstream. This is most often useful if you are using a newer
        distribution release than is supported by the repository (and the
        packages for the previous distribution release still work).

     3. Disable the repository, so yum won't use it by default. Yum will then
        just ignore the repository until you permanently enable it again or use
        --enablerepo for temporary usage:

            yum-config-manager --disable <repoid>

     4. Configure the failing repository to be skipped, if it is unavailable.
        Note that yum will try to contact the repo. when it runs most commands,
        so will have to try and fail each time (and thus. yum will be be much
        slower). If it is a very temporary problem though, this is often a nice
        compromise:

            yum-config-manager --save --setopt=<repoid>.skip_if_unavailable=true


```

到处找原因，发现原因其实就是某一个repository不能用。
最后发现可能的原因是运行了`yum install epel-release`, 其实问题就出在这

### 解决方案:

```
yum remove epel-release
yum update
yum install epel-release
```

