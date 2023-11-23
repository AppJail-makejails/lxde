# LXDE

LXDE (abbreviation for Lightweight X11 Desktop Environment) is a free desktop environment with comparatively low resource requirements. This makes it especially suitable for use on older or resource-constrained personal computers such as netbooks or system on a chip computers. 

wikipedia.org/wiki/LXDE

<img src="https://upload.wikimedia.org/wikipedia/commons/thumb/3/3e/LXDE-logo.svg/800px-LXDE-logo.svg.png" alt="lxde logo" width="40%" height="auto">

## How to use this Makejail

```
INCLUDE options/network.makejail
INCLUDE gh+AppJail-makejails/lxde

OPTION copydir=files
OPTION file=/etc/rc.conf.local
OPTION x11
```

Where `options/network.makejail` are the options that suit your environment, for example:

```
ARG network
ARG interface=lxde

OPTION virtualnet=${network}:${interface} default
OPTION nat
```

The tree structure of the `files/` directory is as follows:

```
# tree -pug files/
[drwxr-xr-x root     wheel   ]  files/
└── [drwxr-xr-x root     wheel   ]  etc
    └── [-rw-r--r-- root     wheel   ]  rc.conf.local

1 directory, 1 file
```

Where `rc.conf.local` is your custom `rc.conf(5)` file, for example:

```
clear_tmp_X="NO"
```

The `rc.conf(5)` file above sets `clear_tmp_X` to `NO` to not remove the sockets and various related files before the jail starts.

Open a shell and run `appjail makejail` and `appjail start`:

```sh
appjail makejail -j lxde -- --network development
appjail start lxde
```

After Makejail builds the jail, you can run LXDE using the `lxde_open` custom stage. `xephyr` is very useful for using these applications:

```
Xephyr -screen 900x640 -br -ac -noreset :1 &
appjail run -s lxde_open -p 'display=:1' lxde
```

### Arguments

* `lxde_tag` (default: `13.2`): see [#tags](#tags).

## How to build the Image

Make any changes you want to your image.

```
INCLUDE options/network.makejail
INCLUDE gh+AppJail-makejails/lxde --file build.makejail
```

Build the jail:

```sh
appjail makejail -j lxde
```

Remove unportable or unnecessary files and directories and export the jail:

```sh
appjail stop lxde
appjail cmd local lxde sh -c "rm -f var/log/*"
appjail cmd local lxde sh -c "rm -f var/cache/pkg/*"
appjail cmd local lxde sh -c "rm -f var/run/*"
appjail cmd local lxde vi etc/rc.conf
appjail image export lxde
```

## Tags

| Tag        | Arch    | Version           | Type   |
| ---------- | ------- | ----------------- | ------ |
| `13.2`     | `amd64` | `13.2-RELEASE` | `thin` |
