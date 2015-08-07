# exim-greylist
EXIM patches to implement greylisting

       Idea: Lena(at)lena.kiev.ua, http://wiki.exim.org/DbLessGreyListingC
       Written by: vc(at)vc.org.ua, 2008-06-10

**This patchset is intended to be used with FreeBSD port of EXIM (ports/mail/exim)**

####INSTALLATION
`/etc/make.conf`
```Makefile
.if !empty(.CURDIR:M/*/ports/mail/exim)
EXTRA_PATCHES+=/path/to/greylist.patch
.endif
```
```shell
#> portinstall (or portupgrade) -W mail/exim && make -C /usr/ports/mail/exim/ clean
```
####USAGE
`/usr/local/etc/exim/configure`
```
acl_greylist:
  defer log_message         = greylisted
        !host               = 127.0.0.1 : ^(relay|mail|mx|smtp) : *.mail.ru : *.google.com : *.yahoo.com
        # parameters -- key, defer-timeout, allow-timeout (in seconds)
        condition           = ${dlfunc{/usr/local/libexec/greylist.so}{greylist} \
                            {$sender_host_address,$sender_address}{240}{604800}}
        message             = Greylisting in progress, please try again later

acl_check_rcpt:
   accept  local_parts      = firstuser : anotheruser
           acl              = acl_greylist
```
