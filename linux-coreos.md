# Linux - CoreOS setup

- [Documentation](https://coreos.com/os/docs/latest/)
- [ISO Download](https://coreos.com/os/docs/latest/booting-with-iso.html)
- [Install on disk guide](https://coreos.com/os/docs/latest/installing-to-disk.html)
- [Install video](https://www.youtube.com/watch?v=_I7ErvhwYeY)


...after booted from ISO

>`sudo openssl passwd -1 > cloud-config-file`  
>`vi cloud-config-file`  

##### Write this:
```yaml
#cloud-config 

 users:
  - name: "<username>"
    passwd: "<generatedhash from sudossl passwd -1 command>"
    groups:
      - sudo
      - docker
 hostname: <hostaname>
```
>`sudo coreos-install -d /dev/sda -C stable -c cloud-config-file` 


