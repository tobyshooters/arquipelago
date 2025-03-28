I can access via root with: `ssh root@185.193.126.243`

Because I've configured the DNS for my domain, I can also just do
`ssh root@arquipelago.org`.

Some utils were installed: `apt install vim rsync tree`

Group and user `aventura` was created.
Note that `-m` also create the home directory.

```
sudo groupadd aventura
useradd -m aventura -g aventura
sudo chmod 744 /home/aventura
sudo chown -R aventura:aventura /home/aventura
sudo chsh -s /bin/bash username
```

You can pretend to by a user using `sudo su - aventura`. This makes sures that
subsequent folders created are attributed to the correct user and group.

Otherwise, you have to `chown aventura:aventura $DIR`

It's also nice to make sure that other users can read, but not edit each
other's home directory, via `chmod 744 /home/aventura`. This is in the spirit
of knowledge sharing, but avoiding accidents or abuse.

TODO: I think I need to actually set permissions for sub-folders, not just 744
on the parent. I'm told something like `echo "umask 077" | sudo tee -a
/home/newuser/.bashrc` is the way to go.

```
/home/...

0 drwxr-xr-x 1 root      root       42 Oct 24 13:16 .
0 drwxr-xr-x 1 root      root      154 Oct 14 16:15 ..
0 drwxr--r-- 1 aventura  aventura   14 Oct 14 16:47 aventura
0 drwxr--r-- 1 cristobal cristobal  14 Oct 24 13:20 cristobal
0 drwxr--r-- 1 root      root        6 Oct 14 16:39 root

```

Created a folder for them at `/home/aventura` with `/home/aventura/www` as the
place serving from Caddy.

```
/home
├── aventura
│   ├── .ssh
│   │   └── authorized_keys
│   └── www
│       └── index.html
└── root
    └── www
        └── index.html
```

For `aventura` to be able to SSH, I've:

1. Added them to the `AllowUsers` in `/etc/ssh/sshd_config`
2. I've added my own public key to `/home/aventura/.ssh/authorized_keys`
3. Restarting the SSH daemon `systemctl restart ssh`

The public key can usually be found via: `cat ~/.ssh/id_ed25519.pub `

Note, this probably only works because the OS has some default mapping between
users and home folders that I'm depending on. Hence, 
`ssh aventura@arquipelago.org` knows where to look for the matching keys.


To copy files over from a git repo, prefer to leave the git repo local:

```
cd ~/dev/tato
git pull
rsync -vrz --exclude .git . root@arquipelago.org:/home/tato/www
```


The server is using Caddy to serve file_servers for each `/home/$USER/www`.
For some reason, HTTPS configuration was failing on these when I tried to use a
wildcard DNS record, e.g. `*.arquipelago.org`.

The Caddyfile is at `/root` and the server can be started or stopped based
on that Caddyfile (if you're in that directory!). `caddy reload`


TODO:

- [ ] Figure out how to do back-ups? Perhaps the source of truth is each user's
      computer, and not the server.
- [ ] Figure out DNS records for subdomains without a record for each user.
- [ ] Figure out bandwidth of images and videos.
