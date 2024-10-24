I can access via root with: `ssh root@185.193.126.243`

Because I've configured the DNS for my domain, I can also just do
`ssh root@arquipelago.org`.

User `aventura` was created.
`useradd aventura -g aventura`

You can pretend to by a user using `sudo su - aventura`. This makes sures that
folders created are attributed to the correct user and group.

Otherwise, you have to `chown aventura:aventura /home/aventura`

Created a folder for them at `/home/aventura` with `/home/aventura/www` as the
place serving from Caddy.

/home
├── aventura
│   ├── .ssh
│   │   └── authorized_keys
│   └── www
│       └── index.html
└── root
    └── www
        └── index.html

For `aventura` to be able to SSH, I've:
(1) Added them to the `AllowUsers` in `/etc/ssh/sshd_config`
(2) I've added my own public key to `/home/aventura/.ssh/authorized_keys`

Note, this probably only works because the OS has some default mapping between
users and home folders that I'm depending on. Hence, 
`ssh aventura@arquipelago.org` knows where to look for the matching keys.

The server is using Caddy to serve file_servers for each `/home/$USER/www`.
For some reason, HTTPS configuration was failing on these when I tried to use a
wildcard DNS record, e.g. `*.arquipelago.org`.

The Caddyfile is at `/root` and the server can be started or stopped based
on that Caddyfile (if you're in that directory!).
