# Linux Administration

## Cleanining Apt Cache

Cleaning apt cache is important to [free up space and remove old package files](https://itsfoss.com/clear-apt-cache/) that are no longer needed. 

Inspecting the `/var/cache/apt/archives` directory can help you understand how much space is being used by cached packages:


```bash
root@lthlibprod:/var/cache/apt/archives# ls -1 | wc -l
178
root@lthlibprod:/var/cache/apt/archives# du -sh .
901M    .
root@lthlibprod:/var/cache/apt/archives#
```

Cleaning the apt cache can be done using the following command:

```bash
root@lthlibprod:/var/cache/apt/archives# apt clean
root@lthlibprod:/var/cache/apt/archives#
root@lthlibprod:/var/cache/apt/archives # Checking the size of the directory again
root@lthlibprod:/var/cache/apt/archives# du -sh .
32K     .
root@lthlibprod:/var/cache/apt/archives# 
```
