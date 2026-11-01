DSpace
======

# Docs

Documentation:

* Docker: [https://wiki.lyrasis.org/spaces/DSPACE/pages/425331100/Try+out+DSpace+10](https://wiki.lyrasis.org/spaces/DSPACE/pages/425331100/Try+out+DSpace+10)
Start `lazydocker` to monitor the messages during start up.

Start DSpace:

```
sudo docker compose -p d10 -f docker/docker-compose-dist.yml -f docker/docker-compose-rest.yml up -d
```

And monitor the output in `lazydocker`.

Next steps:

1. Add test content.
1. Add an initial administrator account.
```
```
