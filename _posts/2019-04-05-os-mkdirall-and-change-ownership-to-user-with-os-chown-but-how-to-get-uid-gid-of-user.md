---
layout: post
title: "os.mkdirall and change ownership to user with os.chown but how to get uid, gid of user"
date: 2019-04-05 18:41:32 +0700
tags: golang change-owner
---
To mkdir on linux as root, so I run may app elevated with `sudo`. But the app making use of this directory runs as a different user, `postgres`, so it errors out with a permission denied error message.

```go
dir: = "/srv/tablepspaces/psql96/client"
err := os.MkdirAll(dir, os.FileMode(0775))
```

(for some reason the above results in 755 when i stat the dir on linux)

so I would like to chown the dir:

I need to use `os.Chown(name string, uid, gid int) error`

How do I get the uid and gid of a user without shelling out and getting the return and parsing of `id`

```go
err := os.MkdirAll(dir, os.FileMode(0775))
if err != nil {
    return fmt.Errorf("error creating tabelspace folders: %v ", err.Error())
}
if runtime.GOOS != "windows" {
    group, err := user.Lookup("postgres")
    if err != nil {
        return fmt.Errorf("error looking up postgres user user info")
    }
    uid, _ := strconv.Atoi(group.Uid)
    gid, _ := strconv.Atoi(group.Gid)

    err = syscall.Chown(dir, uid, gid)
}
```

Link follow: [Stackoverflow](https://stackoverflow.com/a/51864387)