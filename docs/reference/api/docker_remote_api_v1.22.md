<!--[metadata]>
+++
title = "Remote API v1.22"
description = "API Documentation for Docker"
keywords = ["API, Docker, rcli, REST,  documentation"]
[menu.main]
parent="smn_remoteapi"
weight = 0
+++
<![end-metadata]-->

# Docker Remote API v1.22

## 1. Brief introduction

 - The Remote API has replaced `rcli`.
 - The daemon listens on `unix:///var/run/docker.sock` but you can
   [Bind Docker to another host/port or a Unix socket](../../articles/basics.md#bind-docker-to-another-hostport-or-a-unix-socket).
 - The API tends to be REST. However, for some complex commands, like `attach`
   or `pull`, the HTTP connection is hijacked to transport `stdout`,
   `stdin` and `stderr`.
 - When the client API version is newer than the daemon's, these calls return an HTTP
   `400 Bad Request` error message.

# 2. Endpoints

## 2.1 Containers

### List containers

`GET /containers/json`

List containers

**Example request**:

    GET /containers/json?all=1&before=8dfafdbc3a40&size=1 HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    [
         {
                 "Id": "8dfafdbc3a40",
                 "Names":["/boring_feynman"],
                 "Image": "ubuntu:latest",
		 "ImageID": "d74508fb6632491cea586a1fd7d748dfc5274cd6fdfedee309ecdcbc2bf5cb82",
                 "Command": "echo 1",
                 "Created": 1367854155,
                 "Status": "Exit 0",
                 "Ports": [{"PrivatePort": 2222, "PublicPort": 3333, "Type": "tcp"}],
                 "Labels": {
                         "com.example.vendor": "Acme",
                         "com.example.license": "GPL",
                         "com.example.version": "1.0"
                 },
                 "SizeRw": 12288,
                 "SizeRootFs": 0
         },
         {
                 "Id": "9cd87474be90",
                 "Names":["/coolName"],
                 "Image": "ubuntu:latest",
		 "ImageID": "d74508fb6632491cea586a1fd7d748dfc5274cd6fdfedee309ecdcbc2bf5cb82",
                 "Command": "echo 222222",
                 "Created": 1367854155,
                 "Status": "Exit 0",
                 "Ports": [],
                 "Labels": {},
                 "SizeRw": 12288,
                 "SizeRootFs": 0
         },
         {
                 "Id": "3176a2479c92",
                 "Names":["/sleepy_dog"],
                 "Image": "ubuntu:latest",
		 "ImageID": "d74508fb6632491cea586a1fd7d748dfc5274cd6fdfedee309ecdcbc2bf5cb82",
                 "Command": "echo 3333333333333333",
                 "Created": 1367854154,
                 "Status": "Exit 0",
                 "Ports":[],
                 "Labels": {},
                 "SizeRw":12288,
                 "SizeRootFs":0
         },
         {
                 "Id": "4cb07b47f9fb",
                 "Names":["/running_cat"],
                 "Image": "ubuntu:latest",
		 "ImageID": "d74508fb6632491cea586a1fd7d748dfc5274cd6fdfedee309ecdcbc2bf5cb82",
                 "Command": "echo 444444444444444444444444444444444",
                 "Created": 1367854152,
                 "Status": "Exit 0",
                 "Ports": [],
                 "Labels": {},
                 "SizeRw": 12288,
                 "SizeRootFs": 0
         }
    ]

Query Parameters:

-   **all** – 1/True/true or 0/False/false, Show all containers.
        Only running containers are shown by default (i.e., this defaults to false)
-   **limit** – Show `limit` last created
        containers, include non-running ones.
-   **since** – Show only containers created since Id, include
        non-running ones.
-   **before** – Show only containers created before Id, include
        non-running ones.
-   **size** – 1/True/true or 0/False/false, Show the containers
        sizes
-   **filters** - a JSON encoded value of the filters (a `map[string][]string`) to process on the containers list. Available filters:
  -   `exited=<int>`; -- containers with exit code of  `<int>` ;
  -   `status=`(`created`|`restarting`|`running`|`paused`|`exited`)
  -   `label=key` or `label="key=value"` of a container label

Status Codes:

-   **200** – no error
-   **400** – bad parameter
-   **500** – server error

### Create a container

`POST /containers/create`

Create a container

**Example request**:

    POST /containers/create HTTP/1.1
    Content-Type: application/json

    {
           "Hostname": "",
           "Domainname": "",
           "User": "",
           "AttachStdin": false,
           "AttachStdout": true,
           "AttachStderr": true,
           "Tty": false,
           "OpenStdin": false,
           "StdinOnce": false,
           "Env": null,
           "Cmd": [
                   "date"
           ],
           "Entrypoint": "",
           "Image": "ubuntu",
           "Labels": {
                   "com.example.vendor": "Acme",
                   "com.example.license": "GPL",
                   "com.example.version": "1.0"
           },
           "Mounts": [
             {
               "Source": "/data",
               "Destination": "/data",
               "Mode": "ro,Z",
               "RW": false
             }
           ],
           "WorkingDir": "",
           "NetworkDisabled": false,
           "MacAddress": "12:34:56:78:9a:bc",
           "ExposedPorts": {
                   "22/tcp": {}
           },
           "StopSignal": "SIGTERM",
           "HostConfig": {
             "Binds": ["/tmp:/tmp"],
             "Links": ["redis3:redis"],
             "LxcConf": {"lxc.utsname":"docker"},
             "Memory": 0,
             "MemorySwap": 0,
             "MemoryReservation": 0,
             "KernelMemory": 0,
             "CpuShares": 512,
             "CpuPeriod": 100000,
             "CpuQuota": 50000,
             "CpusetCpus": "0,1",
             "CpusetMems": "0,1",
             "BlkioWeight": 300,
             "MemorySwappiness": 60,
             "OomKillDisable": false,
             "PortBindings": { "22/tcp": [{ "HostPort": "11022" }] },
             "PublishAllPorts": false,
             "Privileged": false,
             "ReadonlyRootfs": false,
             "Dns": ["8.8.8.8"],
             "DnsOptions": [""],
             "DnsSearch": [""],
             "ExtraHosts": null,
             "VolumesFrom": ["parent", "other:ro"],
             "CapAdd": ["NET_ADMIN"],
             "CapDrop": ["MKNOD"],
             "RestartPolicy": { "Name": "", "MaximumRetryCount": 0 },
             "NetworkMode": "bridge",
             "Devices": [],
             "Ulimits": [{}],
             "LogConfig": { "Type": "json-file", "Config": {} },
             "SecurityOpt": [""],
             "CgroupParent": "",
	      "VolumeDriver": ""
          }
      }

**Example response**:

      HTTP/1.1 201 Created
      Content-Type: application/json

      {
           "Id":"e90e34656806",
           "Warnings":[]
      }

Json Parameters:

-   **Hostname** - A string value containing the hostname to use for the
      container.
-   **Domainname** - A string value containing the domain name to use
      for the container.
-   **User** - A string value specifying the user inside the container.
-   **Memory** - Memory limit in bytes.
-   **MemorySwap** - Total memory limit (memory + swap); set `-1` to disable swap
      You must use this with `memory` and make the swap value larger than `memory`.
-   **MemoryReservation** - Memory soft limit in bytes.
-   **KernelMemory** - Kernel memory limit in bytes.
-   **CpuShares** - An integer value containing the container's CPU Shares
      (ie. the relative weight vs other containers).
-   **CpuPeriod** - The length of a CPU period in microseconds.
-   **CpuQuota** - Microseconds of CPU time that the container can get in a CPU period.
-   **Cpuset** - Deprecated please don't use. Use `CpusetCpus` instead.
-   **CpusetCpus** - String value containing the `cgroups CpusetCpus` to use.
-   **CpusetMems** - Memory nodes (MEMs) in which to allow execution (0-3, 0,1). Only effective on NUMA systems.
-   **BlkioWeight** - Block IO weight (relative weight) accepts a weight value between 10 and 1000.
-   **MemorySwappiness** - Tune a container's memory swappiness behavior. Accepts an integer between 0 and 100.
-   **OomKillDisable** - Boolean value, whether to disable OOM Killer for the container or not.
-   **AttachStdin** - Boolean value, attaches to `stdin`.
-   **AttachStdout** - Boolean value, attaches to `stdout`.
-   **AttachStderr** - Boolean value, attaches to `stderr`.
-   **Tty** - Boolean value, Attach standard streams to a `tty`, including `stdin` if it is not closed.
-   **OpenStdin** - Boolean value, opens stdin,
-   **StdinOnce** - Boolean value, close `stdin` after the 1 attached client disconnects.
-   **Env** - A list of environment variables in the form of `VAR=value`
-   **Labels** - Adds a map of labels to a container. To specify a map: `{"key":"value"[,"key2":"value2"]}`
-   **Cmd** - Command to run specified as a string or an array of strings.
-   **Entrypoint** - Set the entry point for the container as a string or an array
      of strings.
-   **Image** - A string specifying the image name to use for the container.
-   **Mounts** - An array of mount points in the container.
-   **WorkingDir** - A string specifying the working directory for commands to
      run in.
-   **NetworkDisabled** - Boolean value, when true disables networking for the
      container
-   **ExposedPorts** - An object mapping ports to an empty object in the form of:
      `"ExposedPorts": { "<port>/<tcp|udp>: {}" }`
-   **StopSignal** - Signal to stop a container as a string or unsigned integer. `SIGTERM` by default.
-   **HostConfig**
    -   **Binds** – A list of volume bindings for this container. Each volume binding is a string in one of these forms:
           + `container_path` to create a new volume for the container
           + `host_path:container_path` to bind-mount a host path into the container
           + `host_path:container_path:ro` to make the bind-mount read-only inside the container.
    -   **Links** - A list of links for the container. Each link entry should be
          in the form of `container_name:alias`.
    -   **LxcConf** - LXC specific configurations. These configurations only
          work when using the `lxc` execution driver.
    -   **PortBindings** - A map of exposed container ports and the host port they
          should map to. A JSON object in the form
          `{ <port>/<protocol>: [{ "HostPort": "<port>" }] }`
          Take note that `port` is specified as a string and not an integer value.
    -   **PublishAllPorts** - Allocates a random host port for all of a container's
          exposed ports. Specified as a boolean value.
    -   **Privileged** - Gives the container full access to the host. Specified as
          a boolean value.
    -   **ReadonlyRootfs** - Mount the container's root filesystem as read only.
          Specified as a boolean value.
    -   **Dns** - A list of DNS servers for the container to use.
    -   **DnsOptions** - A list of DNS options
    -   **DnsSearch** - A list of DNS search domains
    -   **ExtraHosts** - A list of hostnames/IP mappings to add to the
        container's `/etc/hosts` file. Specified in the form `["hostname:IP"]`.
    -   **VolumesFrom** - A list of volumes to inherit from another container.
          Specified in the form `<container name>[:<ro|rw>]`
    -   **CapAdd** - A list of kernel capabilities to add to the container.
    -   **Capdrop** - A list of kernel capabilities to drop from the container.
    -   **RestartPolicy** – The behavior to apply when the container exits.  The
            value is an object with a `Name` property of either `"always"` to
            always restart, `"unless-stopped"` to restart always except when
            user has manually stopped the container or `"on-failure"` to restart only when the container
            exit code is non-zero.  If `on-failure` is used, `MaximumRetryCount`
            controls the number of times to retry before giving up.
            The default is not to restart. (optional)
            An ever increasing delay (double the previous delay, starting at 100mS)
            is added before each restart to prevent flooding the server.
    -   **NetworkMode** - Sets the networking mode for the container. Supported
          values are: `bridge`, `host`, and `container:<name|id>`
    -   **Devices** - A list of devices to add to the container specified as a JSON object in the
      form
          `{ "PathOnHost": "/dev/deviceName", "PathInContainer": "/dev/deviceName", "CgroupPermissions": "mrw"}`
    -   **Ulimits** - A list of ulimits to set in the container, specified as
          `{ "Name": <name>, "Soft": <soft limit>, "Hard": <hard limit> }`, for example:
          `Ulimits: { "Name": "nofile", "Soft": 1024, "Hard": 2048 }`
    -   **SecurityOpt**: A list of string values to customize labels for MLS
        systems, such as SELinux.
    -   **LogConfig** - Log configuration for the container, specified as a JSON object in the form
          `{ "Type": "<driver_name>", "Config": {"key1": "val1"}}`.
          Available types: `json-file`, `syslog`, `journald`, `gelf`, `awslogs`, `none`.
          `json-file` logging driver.
    -   **CgroupParent** - Path to `cgroups` under which the container's `cgroup` is created. If the path is not absolute, the path is considered to be relative to the `cgroups` path of the init process. Cgroups are created if they do not already exist.
    -   **VolumeDriver** - Driver that this container users to mount volumes.

Query Parameters:

-   **name** – Assign the specified name to the container. Must
    match `/?[a-zA-Z0-9_-]+`.

Status Codes:

-   **201** – no error
-   **404** – no such container
-   **406** – impossible to attach (container not running)
-   **500** – server error

### Inspect a container

`GET /containers/(id)/json`

Return low-level information on the container `id`


**Example request**:

      GET /containers/4fa6e0f0c678/json HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {
		"AppArmorProfile": "",
		"Args": [
			"-c",
			"exit 9"
		],
		"Config": {
			"AttachStderr": true,
			"AttachStdin": false,
			"AttachStdout": true,
			"Cmd": [
				"/bin/sh",
				"-c",
				"exit 9"
			],
			"Domainname": "",
			"Entrypoint": null,
			"Env": [
				"PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
			],
			"ExposedPorts": null,
			"Hostname": "ba033ac44011",
			"Image": "ubuntu",
			"Labels": {
				"com.example.vendor": "Acme",
				"com.example.license": "GPL",
				"com.example.version": "1.0"
			},
			"MacAddress": "",
			"NetworkDisabled": false,
			"OnBuild": null,
			"OpenStdin": false,
			"StdinOnce": false,
			"Tty": false,
			"User": "",
			"Volumes": null,
			"WorkingDir": "",
			"StopSignal": "SIGTERM"
		},
		"Created": "2015-01-06T15:47:31.485331387Z",
		"Driver": "devicemapper",
		"ExecDriver": "native-0.2",
		"ExecIDs": null,
		"HostConfig": {
			"Binds": null,
			"BlkioWeight": 0,
			"CapAdd": null,
			"CapDrop": null,
			"ContainerIDFile": "",
			"CpusetCpus": "",
			"CpusetMems": "",
			"CpuShares": 0,
			"CpuPeriod": 100000,
			"Devices": [],
			"Dns": null,
			"DnsOptions": null,
			"DnsSearch": null,
			"ExtraHosts": null,
			"IpcMode": "",
			"Links": null,
			"LxcConf": [],
			"Memory": 0,
			"MemorySwap": 0,
			"MemoryReservation": 0,
			"KernelMemory": 0,
			"OomKillDisable": false,
			"NetworkMode": "bridge",
			"PortBindings": {},
			"Privileged": false,
			"ReadonlyRootfs": false,
			"PublishAllPorts": false,
			"RestartPolicy": {
				"MaximumRetryCount": 2,
				"Name": "on-failure"
			},
			"LogConfig": {
				"Config": null,
				"Type": "json-file"
			},
			"SecurityOpt": null,
			"VolumesFrom": null,
			"Ulimits": [{}],
			"VolumeDriver": ""
		},
		"HostnamePath": "/var/lib/docker/containers/ba033ac4401106a3b513bc9d639eee123ad78ca3616b921167cd74b20e25ed39/hostname",
		"HostsPath": "/var/lib/docker/containers/ba033ac4401106a3b513bc9d639eee123ad78ca3616b921167cd74b20e25ed39/hosts",
		"LogPath": "/var/lib/docker/containers/1eb5fabf5a03807136561b3c00adcd2992b535d624d5e18b6cdc6a6844d9767b/1eb5fabf5a03807136561b3c00adcd2992b535d624d5e18b6cdc6a6844d9767b-json.log",
		"Id": "ba033ac4401106a3b513bc9d639eee123ad78ca3616b921167cd74b20e25ed39",
		"Image": "04c5d3b7b0656168630d3ba35d8889bd0e9caafcaeb3004d2bfbc47e7c5d35d2",
		"MountLabel": "",
		"Name": "/boring_euclid",
		"NetworkSettings": {
			"Bridge": "",
			"Gateway": "",
			"IPAddress": "",
			"IPPrefixLen": 0,
			"MacAddress": "",
			"Ports": null
		},
		"Path": "/bin/sh",
		"ProcessLabel": "",
		"ResolvConfPath": "/var/lib/docker/containers/ba033ac4401106a3b513bc9d639eee123ad78ca3616b921167cd74b20e25ed39/resolv.conf",
		"RestartCount": 1,
		"State": {
			"Error": "",
			"ExitCode": 9,
			"FinishedAt": "2015-01-06T15:47:32.080254511Z",
			"OOMKilled": false,
			"Paused": false,
			"Pid": 0,
			"Restarting": false,
			"Running": true,
			"StartedAt": "2015-01-06T15:47:32.072697474Z",
			"Status": "running"
		},
		"Mounts": [
			{
				"Source": "/data",
				"Destination": "/data",
				"Mode": "ro,Z",
				"RW": false
			}
		]
	}

**Example request, with size information**:

    GET /containers/4fa6e0f0c678/json?size=1 HTTP/1.1

**Example response, with size information**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {
    ....
    "SizeRw": 0,
    "SizeRootFs": 972,
    ....
    }

Query Parameters:

-   **size** – 1/True/true or 0/False/false, return container size information. Default is `false`.

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### List processes running inside a container

`GET /containers/(id)/top`

List processes running inside the container `id`

**Example request**:

    GET /containers/4fa6e0f0c678/top HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {
         "Titles": [
                 "USER",
                 "PID",
                 "%CPU",
                 "%MEM",
                 "VSZ",
                 "RSS",
                 "TTY",
                 "STAT",
                 "START",
                 "TIME",
                 "COMMAND"
                 ],
         "Processes": [
                 ["root","20147","0.0","0.1","18060","1864","pts/4","S","10:06","0:00","bash"],
                 ["root","20271","0.0","0.0","4312","352","pts/4","S+","10:07","0:00","sleep","10"]
         ]
    }

Query Parameters:

-   **ps_args** – ps arguments to use (e.g., aux)

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### Get container logs

`GET /containers/(id)/logs`

Get `stdout` and `stderr` logs from the container ``id``

> **Note**:
> This endpoint works only for containers with `json-file` logging driver.

**Example request**:

     GET /containers/4fa6e0f0c678/logs?stderr=1&stdout=1&timestamps=1&follow=1&tail=10&since=1428990821 HTTP/1.1

**Example response**:

     HTTP/1.1 101 UPGRADED
     Content-Type: application/vnd.docker.raw-stream
     Connection: Upgrade
     Upgrade: tcp

     {{ STREAM }}

Query Parameters:

-   **follow** – 1/True/true or 0/False/false, return stream. Default `false`.
-   **stdout** – 1/True/true or 0/False/false, show `stdout` log. Default `false`.
-   **stderr** – 1/True/true or 0/False/false, show `stderr` log. Default `false`.
-   **since** – UNIX timestamp (integer) to filter logs. Specifying a timestamp
    will only output log-entries since that timestamp. Default: 0 (unfiltered)
-   **timestamps** – 1/True/true or 0/False/false, print timestamps for
        every log line. Default `false`.
-   **tail** – Output specified number of lines at the end of logs: `all` or `<number>`. Default all.

Status Codes:

-   **101** – no error, hints proxy about hijacking
-   **200** – no error, no upgrade header found
-   **404** – no such container
-   **500** – server error

### Inspect changes on a container's filesystem

`GET /containers/(id)/changes`

Inspect changes on container `id`'s filesystem

**Example request**:

    GET /containers/4fa6e0f0c678/changes HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    [
         {
                 "Path": "/dev",
                 "Kind": 0
         },
         {
                 "Path": "/dev/kmsg",
                 "Kind": 1
         },
         {
                 "Path": "/test",
                 "Kind": 1
         }
    ]

Values for `Kind`:

- `0`: Modify
- `1`: Add
- `2`: Delete

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### Export a container

`GET /containers/(id)/export`

Export the contents of container `id`

**Example request**:

    GET /containers/4fa6e0f0c678/export HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/octet-stream

    {{ TAR STREAM }}

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### Get container stats based on resource usage

`GET /containers/(id)/stats`

This endpoint returns a live stream of a container's resource usage statistics.

> **Note**: this functionality currently only works when using the *libcontainer* exec-driver.

**Example request**:

    GET /containers/redis1/stats HTTP/1.1

**Example response**:

      HTTP/1.1 200 OK
      Content-Type: application/json

      {
         "read" : "2015-01-08T22:57:31.547920715Z",
         "networks": {
                 "eth0": {
                     "rx_bytes": 5338,
                     "rx_dropped": 0,
                     "rx_errors": 0,
                     "rx_packets": 36,
                     "tx_bytes": 648,
                     "tx_dropped": 0,
                     "tx_errors": 0,
                     "tx_packets": 8
                 },
                 "eth5": {
                     "rx_bytes": 4641,
                     "rx_dropped": 0,
                     "rx_errors": 0,
                     "rx_packets": 26,
                     "tx_bytes": 690,
                     "tx_dropped": 0,
                     "tx_errors": 0,
                     "tx_packets": 9
                 }
         },
         "memory_stats" : {
            "stats" : {
               "total_pgmajfault" : 0,
               "cache" : 0,
               "mapped_file" : 0,
               "total_inactive_file" : 0,
               "pgpgout" : 414,
               "rss" : 6537216,
               "total_mapped_file" : 0,
               "writeback" : 0,
               "unevictable" : 0,
               "pgpgin" : 477,
               "total_unevictable" : 0,
               "pgmajfault" : 0,
               "total_rss" : 6537216,
               "total_rss_huge" : 6291456,
               "total_writeback" : 0,
               "total_inactive_anon" : 0,
               "rss_huge" : 6291456,
               "hierarchical_memory_limit" : 67108864,
               "total_pgfault" : 964,
               "total_active_file" : 0,
               "active_anon" : 6537216,
               "total_active_anon" : 6537216,
               "total_pgpgout" : 414,
               "total_cache" : 0,
               "inactive_anon" : 0,
               "active_file" : 0,
               "pgfault" : 964,
               "inactive_file" : 0,
               "total_pgpgin" : 477
            },
            "max_usage" : 6651904,
            "usage" : 6537216,
            "failcnt" : 0,
            "limit" : 67108864
         },
         "blkio_stats" : {},
         "cpu_stats" : {
            "cpu_usage" : {
               "percpu_usage" : [
                  16970827,
                  1839451,
                  7107380,
                  10571290
               ],
               "usage_in_usermode" : 10000000,
               "total_usage" : 36488948,
               "usage_in_kernelmode" : 20000000
            },
            "system_cpu_usage" : 20091722000000000,
            "throttling_data" : {}
         }
      }

Query Parameters:

-   **stream** – 1/True/true or 0/False/false, pull stats once then disconnect. Default `true`.

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### Resize a container TTY

`POST /containers/(id)/resize`

Resize the TTY for container with  `id`. The unit is number of characters. You must restart the container for the resize to take effect.

**Example request**:

      POST /containers/4fa6e0f0c678/resize?h=40&w=80 HTTP/1.1

**Example response**:

      HTTP/1.1 200 OK
      Content-Length: 0
      Content-Type: text/plain; charset=utf-8

Query Parameters:

-   **h** – height of `tty` session
-   **w** – width

Status Codes:

-   **200** – no error
-   **404** – No such container
-   **500** – Cannot resize container

### Start a container

`POST /containers/(id)/start`

Start the container `id`

> **Note**:
> For backwards compatibility, this endpoint accepts a `HostConfig` as JSON-encoded request body.
> See [create a container](#create-a-container) for details.

**Example request**:

    POST /containers/(id)/start HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Status Codes:

-   **204** – no error
-   **304** – container already started
-   **404** – no such container
-   **500** – server error

### Stop a container

`POST /containers/(id)/stop`

Stop the container `id`

**Example request**:

    POST /containers/e90e34656806/stop?t=5 HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Query Parameters:

-   **t** – number of seconds to wait before killing the container

Status Codes:

-   **204** – no error
-   **304** – container already stopped
-   **404** – no such container
-   **500** – server error

### Restart a container

`POST /containers/(id)/restart`

Restart the container `id`

**Example request**:

    POST /containers/e90e34656806/restart?t=5 HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Query Parameters:

-   **t** – number of seconds to wait before killing the container

Status Codes:

-   **204** – no error
-   **404** – no such container
-   **500** – server error

### Kill a container

`POST /containers/(id)/kill`

Kill the container `id`

**Example request**:

    POST /containers/e90e34656806/kill HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Query Parameters

-   **signal** - Signal to send to the container: integer or string like `SIGINT`.
        When not set, `SIGKILL` is assumed and the call waits for the container to exit.

Status Codes:

-   **204** – no error
-   **404** – no such container
-   **500** – server error

### Rename a container

`POST /containers/(id)/rename`

Rename the container `id` to a `new_name`

**Example request**:

    POST /containers/e90e34656806/rename?name=new_name HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Query Parameters:

-   **name** – new name for the container

Status Codes:

-   **204** – no error
-   **404** – no such container
-   **409** - conflict name already assigned
-   **500** – server error

### Pause a container

`POST /containers/(id)/pause`

Pause the container `id`

**Example request**:

    POST /containers/e90e34656806/pause HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Status Codes:

-   **204** – no error
-   **404** – no such container
-   **500** – server error

### Unpause a container

`POST /containers/(id)/unpause`

Unpause the container `id`

**Example request**:

    POST /containers/e90e34656806/unpause HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Status Codes:

-   **204** – no error
-   **404** – no such container
-   **500** – server error

### Attach to a container

`POST /containers/(id)/attach`

Attach to the container `id`

**Example request**:

    POST /containers/16253994b7c4/attach?logs=1&stream=0&stdout=1 HTTP/1.1

**Example response**:

    HTTP/1.1 101 UPGRADED
    Content-Type: application/vnd.docker.raw-stream
    Connection: Upgrade
    Upgrade: tcp

    {{ STREAM }}

Query Parameters:

-   **logs** – 1/True/true or 0/False/false, return logs. Default `false`.
-   **stream** – 1/True/true or 0/False/false, return stream.
        Default `false`.
-   **stdin** – 1/True/true or 0/False/false, if `stream=true`, attach
        to `stdin`. Default `false`.
-   **stdout** – 1/True/true or 0/False/false, if `logs=true`, return
        `stdout` log, if `stream=true`, attach to `stdout`. Default `false`.
-   **stderr** – 1/True/true or 0/False/false, if `logs=true`, return
        `stderr` log, if `stream=true`, attach to `stderr`. Default `false`.

Status Codes:

-   **101** – no error, hints proxy about hijacking
-   **200** – no error, no upgrade header found
-   **400** – bad parameter
-   **404** – no such container
-   **500** – server error

    **Stream details**:

    When using the TTY setting is enabled in
    [`POST /containers/create`
    ](#create-a-container),
    the stream is the raw data from the process PTY and client's `stdin`.
    When the TTY is disabled, then the stream is multiplexed to separate
    `stdout` and `stderr`.

    The format is a **Header** and a **Payload** (frame).

    **HEADER**

    The header contains the information which the stream writes (`stdout` or
    `stderr`). It also contains the size of the associated frame encoded in the
    last four bytes (`uint32`).

    It is encoded on the first eight bytes like this:

        header := [8]byte{STREAM_TYPE, 0, 0, 0, SIZE1, SIZE2, SIZE3, SIZE4}

    `STREAM_TYPE` can be:

-   0: `stdin` (is written on `stdout`)
-   1: `stdout`
-   2: `stderr`

    `SIZE1, SIZE2, SIZE3, SIZE4` are the four bytes of
    the `uint32` size encoded as big endian.

    **PAYLOAD**

    The payload is the raw stream.

    **IMPLEMENTATION**

    The simplest way to implement the Attach protocol is the following:

    1.  Read eight bytes.
    2.  Choose `stdout` or `stderr` depending on the first byte.
    3.  Extract the frame size from the last four bytes.
    4.  Read the extracted size and output it on the correct output.
    5.  Goto 1.

### Attach to a container (websocket)

`GET /containers/(id)/attach/ws`

Attach to the container `id` via websocket

Implements websocket protocol handshake according to [RFC 6455](http://tools.ietf.org/html/rfc6455)

**Example request**

    GET /containers/e90e34656806/attach/ws?logs=0&stream=1&stdin=1&stdout=1&stderr=1 HTTP/1.1

**Example response**

    {{ STREAM }}

Query Parameters:

-   **logs** – 1/True/true or 0/False/false, return logs. Default `false`.
-   **stream** – 1/True/true or 0/False/false, return stream.
        Default `false`.
-   **stdin** – 1/True/true or 0/False/false, if `stream=true`, attach
        to `stdin`. Default `false`.
-   **stdout** – 1/True/true or 0/False/false, if `logs=true`, return
        `stdout` log, if `stream=true`, attach to `stdout`. Default `false`.
-   **stderr** – 1/True/true or 0/False/false, if `logs=true`, return
        `stderr` log, if `stream=true`, attach to `stderr`. Default `false`.

Status Codes:

-   **200** – no error
-   **400** – bad parameter
-   **404** – no such container
-   **500** – server error

### Wait a container

`POST /containers/(id)/wait`

Block until container `id` stops, then returns the exit code

**Example request**:

    POST /containers/16253994b7c4/wait HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {"StatusCode": 0}

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### Remove a container

`DELETE /containers/(id)`

Remove the container `id` from the filesystem

**Example request**:

    DELETE /containers/16253994b7c4?v=1 HTTP/1.1

**Example response**:

    HTTP/1.1 204 No Content

Query Parameters:

-   **v** – 1/True/true or 0/False/false, Remove the volumes
        associated to the container. Default `false`.
-   **force** - 1/True/true or 0/False/false, Kill then remove the container.
        Default `false`.

Status Codes:

-   **204** – no error
-   **400** – bad parameter
-   **404** – no such container
-   **500** – server error

### Copy files or folders from a container

`POST /containers/(id)/copy`

Copy files or folders of container `id`

**Deprecated** in favor of the `archive` endpoint below.

**Example request**:

    POST /containers/4fa6e0f0c678/copy HTTP/1.1
    Content-Type: application/json

    {
         "Resource": "test.txt"
    }

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/x-tar

    {{ TAR STREAM }}

Status Codes:

-   **200** – no error
-   **404** – no such container
-   **500** – server error

### Retrieving information about files and folders in a container

`HEAD /containers/(id)/archive`

See the description of the `X-Docker-Container-Path-Stat` header in the
following section.

### Get an archive of a filesystem resource in a container

`GET /containers/(id)/archive`

Get an tar archive of a resource in the filesystem of container `id`.

Query Parameters:

- **path** - resource in the container's filesystem to archive. Required.

    If not an absolute path, it is relative to the container's root directory.
    The resource specified by **path** must exist. To assert that the resource
    is expected to be a directory, **path** should end in `/` or  `/.`
    (assuming a path separator of `/`). If **path** ends in `/.` then this
    indicates that only the contents of the **path** directory should be
    copied. A symlink is always resolved to its target.

    **Note**: It is not possible to copy certain system files such as resources
    under `/proc`, `/sys`, `/dev`, and mounts created by the user in the
    container.

**Example request**:

        GET /containers/8cce319429b2/archive?path=/root HTTP/1.1

**Example response**:

        HTTP/1.1 200 OK
        Content-Type: application/x-tar
        X-Docker-Container-Path-Stat: eyJuYW1lIjoicm9vdCIsInNpemUiOjQwOTYsIm1vZGUiOjIxNDc0ODQwOTYsIm10aW1lIjoiMjAxNC0wMi0yN1QyMDo1MToyM1oiLCJsaW5rVGFyZ2V0IjoiIn0=

        {{ TAR STREAM }}

On success, a response header `X-Docker-Container-Path-Stat` will be set to a
base64-encoded JSON object containing some filesystem header information about
the archived resource. The above example value would decode to the following
JSON object (whitespace added for readability):

        {
            "name": "root",
            "size": 4096,
            "mode": 2147484096,
            "mtime": "2014-02-27T20:51:23Z",
            "linkTarget": ""
        }

A `HEAD` request can also be made to this endpoint if only this information is
desired.

Status Codes:

- **200** - success, returns archive of copied resource
- **400** - client error, bad parameter, details in JSON response body, one of:
    - must specify path parameter (**path** cannot be empty)
    - not a directory (**path** was asserted to be a directory but exists as a
      file)
- **404** - client error, resource not found, one of:
    – no such container (container `id` does not exist)
    - no such file or directory (**path** does not exist)
- **500** - server error

### Extract an archive of files or folders to a directory in a container

`PUT /containers/(id)/archive`

Upload a tar archive to be extracted to a path in the filesystem of container
`id`.

Query Parameters:

- **path** - path to a directory in the container
    to extract the archive's contents into. Required.

    If not an absolute path, it is relative to the container's root directory.
    The **path** resource must exist.
- **noOverwriteDirNonDir** - If "1", "true", or "True" then it will be an error
    if unpacking the given content would cause an existing directory to be
    replaced with a non-directory and vice versa.

**Example request**:

        PUT /containers/8cce319429b2/archive?path=/vol1 HTTP/1.1
        Content-Type: application/x-tar

        {{ TAR STREAM }}

**Example response**:

        HTTP/1.1 200 OK

Status Codes:

- **200** – the content was extracted successfully
- **400** - client error, bad parameter, details in JSON response body, one of:
    - must specify path parameter (**path** cannot be empty)
    - not a directory (**path** should be a directory but exists as a file)
    - unable to overwrite existing directory with non-directory
      (if **noOverwriteDirNonDir**)
    - unable to overwrite existing non-directory with directory
      (if **noOverwriteDirNonDir**)
- **403** - client error, permission denied, the volume
    or container rootfs is marked as read-only.
- **404** - client error, resource not found, one of:
    – no such container (container `id` does not exist)
    - no such file or directory (**path** resource does not exist)
- **500** – server error

## 2.2 Images

### List Images

`GET /images/json`

**Example request**:

    GET /images/json?all=0 HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    [
      {
         "RepoTags": [
           "ubuntu:12.04",
           "ubuntu:precise",
           "ubuntu:latest"
         ],
         "Id": "8dbd9e392a964056420e5d58ca5cc376ef18e2de93b5cc90e868a1bbc8318c1c",
         "Created": 1365714795,
         "Size": 131506275,
         "VirtualSize": 131506275,
         "Labels": {}
      },
      {
         "RepoTags": [
           "ubuntu:12.10",
           "ubuntu:quantal"
         ],
         "ParentId": "27cf784147099545",
         "Id": "b750fe79269d2ec9a3c593ef05b4332b1d1a02a62b4accb2c21d589ff2f5f2dc",
         "Created": 1364102658,
         "Size": 24653,
         "VirtualSize": 180116135,
         "Labels": {
            "com.example.version": "v1"
         }
      }
    ]

**Example request, with digest information**:

    GET /images/json?digests=1 HTTP/1.1

**Example response, with digest information**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    [
      {
        "Created": 1420064636,
        "Id": "4986bf8c15363d1c5d15512d5266f8777bfba4974ac56e3270e7760f6f0a8125",
        "ParentId": "ea13149945cb6b1e746bf28032f02e9b5a793523481a0a18645fc77ad53c4ea2",
        "RepoDigests": [
          "localhost:5000/test/busybox@sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf"
        ],
        "RepoTags": [
          "localhost:5000/test/busybox:latest",
          "playdate:latest"
        ],
        "Size": 0,
        "VirtualSize": 2429728,
        "Labels": {}
      }
    ]

The response shows a single image `Id` associated with two repositories
(`RepoTags`): `localhost:5000/test/busybox`: and `playdate`. A caller can use
either of the `RepoTags` values `localhost:5000/test/busybox:latest` or
`playdate:latest` to reference the image.

You can also use `RepoDigests` values to reference an image. In this response,
the array has only one reference and that is to the
`localhost:5000/test/busybox` repository; the `playdate` repository has no
digest. You can reference this digest using the value:
`localhost:5000/test/busybox@sha256:cbbf2f9a99b47fc460d...`

See the `docker run` and `docker build` commands for examples of digest and tag
references on the command line.

Query Parameters:

-   **all** – 1/True/true or 0/False/false, default false
-   **filters** – a JSON encoded value of the filters (a map[string][]string) to process on the images list. Available filters:
  -   `dangling=true`
  -   `label=key` or `label="key=value"` of an image label
-   **filter** - only return images with the specified name

### Build image from a Dockerfile

`POST /build`

Build an image from a Dockerfile

**Example request**:

    POST /build HTTP/1.1

    {{ TAR STREAM }}

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {"stream": "Step 1..."}
    {"stream": "..."}
    {"error": "Error...", "errorDetail": {"code": 123, "message": "Error..."}}

The input stream must be a `tar` archive compressed with one of the
following algorithms: `identity` (no compression), `gzip`, `bzip2`, `xz`.

The archive must include a build instructions file, typically called
`Dockerfile` at the archive's root. The `dockerfile` parameter may be
used to specify a different build instructions file. To do this, its value must be
the path to the alternate build instructions file to use.

The archive may include any number of other files,
which are accessible in the build context (See the [*ADD build
command*](../../reference/builder.md#dockerbuilder)).

The build is canceled if the client drops the connection by quitting
or being killed.

Query Parameters:

-   **dockerfile** - Path within the build context to the Dockerfile. This is
        ignored if `remote` is specified and points to an individual filename.
-   **t** – A repository name (and optionally a tag) to apply to
        the resulting image in case of success.
-   **remote** – A Git repository URI or HTTP/HTTPS URI build source. If the 
        URI specifies a filename, the file's contents are placed into a file 
		called `Dockerfile`.
-   **q** – Suppress verbose build output.
-   **nocache** – Do not use the cache when building the image.
-   **pull** - Attempt to pull the image even if an older image exists locally.
-   **rm** - Remove intermediate containers after a successful build (default behavior).
-   **forcerm** - Always remove intermediate containers (includes `rm`).
-   **memory** - Set memory limit for build.
-   **memswap** - Total memory (memory + swap), `-1` to disable swap.
-   **cpushares** - CPU shares (relative weight).
-   **cpusetcpus** - CPUs in which to allow execution (e.g., `0-3`, `0,1`).
-   **cpuperiod** - The length of a CPU period in microseconds.
-   **cpuquota** - Microseconds of CPU time that the container can get in a CPU period.
-   **buildargs** – JSON map of string pairs for build-time variables. Users pass
        these values at build-time. Docker uses the `buildargs` as the environment
        context for command(s) run via the Dockerfile's `RUN` instruction or for
        variable expansion in other Dockerfile instructions. This is not meant for
        passing secret values. [Read more about the buildargs instruction](../../reference/builder.md#arg)

    Request Headers:

-   **Content-type** – Set to `"application/tar"`.
-   **X-Registry-Config** – A base64-url-safe-encoded Registry Auth Config JSON
        object with the following structure:

            {
                "docker.example.com": {
                    "username": "janedoe",
                    "password": "hunter2"
                },
                "https://index.docker.io/v1/": {
                    "username": "mobydock",
                    "password": "conta1n3rize14"
                }
            }

        This object maps the hostname of a registry to an object containing the
        "username" and "password" for that registry. Multiple registries may
        be specified as the build may be based on an image requiring
        authentication to pull from any arbitrary registry. Only the registry
        domain name (and port if not the default "443") are required. However
        (for legacy reasons) the "official" Docker, Inc. hosted registry must
        be specified with both a "https://" prefix and a "/v1/" suffix even
        though Docker will prefer to use the v2 registry API.

Status Codes:

-   **200** – no error
-   **500** – server error

### Create an image

`POST /images/create`

Create an image either by pulling it from the registry or by importing it

**Example request**:

    POST /images/create?fromImage=ubuntu HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {"status": "Pulling..."}
    {"status": "Pulling", "progress": "1 B/ 100 B", "progressDetail": {"current": 1, "total": 100}}
    {"error": "Invalid..."}
    ...

When using this endpoint to pull an image from the registry, the
`X-Registry-Auth` header can be used to include
a base64-encoded AuthConfig object.

Query Parameters:

-   **fromImage** – Name of the image to pull.
-   **fromSrc** – Source to import.  The value may be a URL from which the image
        can be retrieved or `-` to read the image from the request body.
-   **repo** – Repository name.
-   **tag** – Tag.
-   **registry** – The registry to pull from.

    Request Headers:

-   **X-Registry-Auth** – base64-encoded AuthConfig object

Status Codes:

-   **200** – no error
-   **500** – server error



### Inspect an image

`GET /images/(name)/json`

Return low-level information on the image `name`

**Example request**:

    GET /images/example/json HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {
       "Id" : "85f05633ddc1c50679be2b16a0479ab6f7637f8884e0cfe0f4d20e1ebb3d6e7c",
       "Container" : "cb91e48a60d01f1e27028b4fc6819f4f290b3cf12496c8176ec714d0d390984a",
       "Comment" : "",
       "Os" : "linux",
       "Architecture" : "amd64",
       "Parent" : "91e54dfb11794fad694460162bf0cb0a4fa710cfa3f60979c177d920813e267c",
       "ContainerConfig" : {
          "Tty" : false,
          "Hostname" : "e611e15f9c9d",
          "Volumes" : null,
          "Domainname" : "",
          "AttachStdout" : false,
          "PublishService" : "",
          "AttachStdin" : false,
          "OpenStdin" : false,
          "StdinOnce" : false,
          "NetworkDisabled" : false,
          "OnBuild" : [],
          "Image" : "91e54dfb11794fad694460162bf0cb0a4fa710cfa3f60979c177d920813e267c",
          "User" : "",
          "WorkingDir" : "",
          "Entrypoint" : null,
          "MacAddress" : "",
          "AttachStderr" : false,
          "Labels" : {
             "com.example.license" : "GPL",
             "com.example.version" : "1.0",
             "com.example.vendor" : "Acme"
          },
          "Env" : [
             "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
          ],
          "ExposedPorts" : null,
          "Cmd" : [
             "/bin/sh",
             "-c",
             "#(nop) LABEL com.example.vendor=Acme com.example.license=GPL com.example.version=1.0"
          ]
       },
       "DockerVersion" : "1.9.0-dev",
       "VirtualSize" : 188359297,
       "Size" : 0,
       "Author" : "",
       "Created" : "2015-09-10T08:30:53.26995814Z",
       "GraphDriver" : {
          "Name" : "aufs",
          "Data" : null
       },
       "Tags" : [
          "example:1.0",
          "example:latest",
          "example:stable"
       ],
       "Config" : {
          "Image" : "91e54dfb11794fad694460162bf0cb0a4fa710cfa3f60979c177d920813e267c",
          "NetworkDisabled" : false,
          "OnBuild" : [],
          "StdinOnce" : false,
          "PublishService" : "",
          "AttachStdin" : false,
          "OpenStdin" : false,
          "Domainname" : "",
          "AttachStdout" : false,
          "Tty" : false,
          "Hostname" : "e611e15f9c9d",
          "Volumes" : null,
          "Cmd" : [
             "/bin/bash"
          ],
          "ExposedPorts" : null,
          "Env" : [
             "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
          ],
          "Labels" : {
             "com.example.vendor" : "Acme",
             "com.example.version" : "1.0",
             "com.example.license" : "GPL"
          },
          "Entrypoint" : null,
          "MacAddress" : "",
          "AttachStderr" : false,
          "WorkingDir" : "",
          "User" : ""
       }
    }

Status Codes:

-   **200** – no error
-   **404** – no such image
-   **500** – server error

### Get the history of an image

`GET /images/(name)/history`

Return the history of the image `name`

**Example request**:

    GET /images/ubuntu/history HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    [
        {
            "Id": "3db9c44f45209632d6050b35958829c3a2aa256d81b9a7be45b362ff85c54710",
            "Created": 1398108230,
            "CreatedBy": "/bin/sh -c #(nop) ADD file:eb15dbd63394e063b805a3c32ca7bf0266ef64676d5a6fab4801f2e81e2a5148 in /",
            "Tags": [
                "ubuntu:lucid",
                "ubuntu:10.04"
            ],
            "Size": 182964289,
            "Comment": ""
        },
        {
            "Id": "6cfa4d1f33fb861d4d114f43b25abd0ac737509268065cdfd69d544a59c85ab8",
            "Created": 1398108222,
            "CreatedBy": "/bin/sh -c #(nop) MAINTAINER Tianon Gravi <admwiggin@gmail.com> - mkimage-debootstrap.sh -i iproute,iputils-ping,ubuntu-minimal -t lucid.tar.xz lucid http://archive.ubuntu.com/ubuntu/",
            "Tags": null,
            "Size": 0,
            "Comment": ""
        },
        {
            "Id": "511136ea3c5a64f264b78b5433614aec563103b4d4702f3ba7d4d2698e22c158",
            "Created": 1371157430,
            "CreatedBy": "",
            "Tags": [
                "scratch12:latest",
                "scratch:latest"
            ],
            "Size": 0,
            "Comment": "Imported from -"
        }
    ]

Status Codes:

-   **200** – no error
-   **404** – no such image
-   **500** – server error

### Push an image on the registry

`POST /images/(name)/push`

Push the image `name` on the registry

**Example request**:

    POST /images/test/push HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {"status": "Pushing..."}
    {"status": "Pushing", "progress": "1/? (n/a)", "progressDetail": {"current": 1}}}
    {"error": "Invalid..."}
    ...

If you wish to push an image on to a private registry, that image must already have a tag
into a repository which references that registry `hostname` and `port`.  This repository name should
then be used in the URL. This duplicates the command line's flow.

**Example request**:

    POST /images/registry.acme.com:5000/test/push HTTP/1.1


Query Parameters:

-   **tag** – The tag to associate with the image on the registry. This is optional.

Request Headers:

-   **X-Registry-Auth** – Include a base64-encoded AuthConfig.
        object.

Status Codes:

-   **200** – no error
-   **404** – no such image
-   **500** – server error

### Tag an image into a repository

`POST /images/(name)/tag`

Tag the image `name` into a repository

**Example request**:

    POST /images/test/tag?repo=myrepo&force=0&tag=v42 HTTP/1.1

**Example response**:

    HTTP/1.1 201 OK

Query Parameters:

-   **repo** – The repository to tag in
-   **force** – 1/True/true or 0/False/false, default false
-   **tag** - The new tag name

Status Codes:

-   **201** – no error
-   **400** – bad parameter
-   **404** – no such image
-   **409** – conflict
-   **500** – server error

### Remove an image

`DELETE /images/(name)`

Remove the image `name` from the filesystem

**Example request**:

    DELETE /images/test HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-type: application/json

    [
     {"Untagged": "3e2f21a89f"},
     {"Deleted": "3e2f21a89f"},
     {"Deleted": "53b4f83ac9"}
    ]

Query Parameters:

-   **force** – 1/True/true or 0/False/false, default false
-   **noprune** – 1/True/true or 0/False/false, default false

Status Codes:

-   **200** – no error
-   **404** – no such image
-   **409** – conflict
-   **500** – server error

### Search images

`GET /images/search`

Search for an image on [Docker Hub](https://hub.docker.com).

> **Note**:
> The response keys have changed from API v1.6 to reflect the JSON
> sent by the registry server to the docker daemon's request.

**Example request**:

    GET /images/search?term=sshd HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    [
            {
                "description": "",
                "is_official": false,
                "is_automated": false,
                "name": "wma55/u1210sshd",
                "star_count": 0
            },
            {
                "description": "",
                "is_official": false,
                "is_automated": false,
                "name": "jdswinbank/sshd",
                "star_count": 0
            },
            {
                "description": "",
                "is_official": false,
                "is_automated": false,
                "name": "vgauthier/sshd",
                "star_count": 0
            }
    ...
    ]

Query Parameters:

-   **term** – term to search

Status Codes:

-   **200** – no error
-   **500** – server error

## 2.3 Misc

### Check auth configuration

`POST /auth`

Get the default username and email

**Example request**:

    POST /auth HTTP/1.1
    Content-Type: application/json

    {
         "username":" hannibal",
         "password: "xxxx",
         "email": "hannibal@a-team.com",
         "serveraddress": "https://index.docker.io/v1/"
    }

**Example response**:

    HTTP/1.1 200 OK

Status Codes:

-   **200** – no error
-   **204** – no error
-   **500** – server error

### Display system-wide information

`GET /info`

Display system-wide information

**Example request**:

    GET /info HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {
        "Containers": 11,
        "CpuCfsPeriod": true,
        "CpuCfsQuota": true,
        "Debug": false,
        "DiscoveryBackend": "etcd://localhost:2379",
        "DockerRootDir": "/var/lib/docker",
        "Driver": "btrfs",
        "DriverStatus": [[""]],
        "ExecutionDriver": "native-0.1",
        "ExperimentalBuild": false,
        "HttpProxy": "http://test:test@localhost:8080",
        "HttpsProxy": "https://test:test@localhost:8080",
        "ID": "7TRN:IPZB:QYBB:VPBQ:UMPP:KARE:6ZNR:XE6T:7EWV:PKF4:ZOJD:TPYS",
        "IPv4Forwarding": true,
        "Images": 16,
        "IndexServerAddress": "https://index.docker.io/v1/",
        "InitPath": "/usr/bin/docker",
        "InitSha1": "",
        "KernelVersion": "3.12.0-1-amd64",
        "Labels": [
            "storage=ssd"
        ],
        "MemTotal": 2099236864,
        "MemoryLimit": true,
        "NCPU": 1,
        "NEventsListener": 0,
        "NFd": 11,
        "NGoroutines": 21,
        "Name": "prod-server-42",
        "NoProxy": "9.81.1.160",
        "OomKillDisable": true,
        "OperatingSystem": "Boot2Docker",
        "RegistryConfig": {
            "IndexConfigs": {
                "docker.io": {
                    "Mirrors": null,
                    "Name": "docker.io",
                    "Official": true,
                    "Secure": true
                }
            },
            "InsecureRegistryCIDRs": [
                "127.0.0.0/8"
            ]
        },
        "SwapLimit": false,
        "SystemTime": "2015-03-10T11:11:23.730591467-07:00"
        "ServerVersion": "1.9.0"
    }

Status Codes:

-   **200** – no error
-   **500** – server error

### Show the docker version information

`GET /version`

Show the docker version information

**Example request**:

    GET /version HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {
         "Version": "1.5.0",
         "Os": "linux",
         "KernelVersion": "3.18.5-tinycore64",
         "GoVersion": "go1.4.1",
         "GitCommit": "a8a31ef",
         "Arch": "amd64",
         "ApiVersion": "1.20",
         "Experimental": false
    }

Status Codes:

-   **200** – no error
-   **500** – server error

### Ping the docker server

`GET /_ping`

Ping the docker server

**Example request**:

    GET /_ping HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: text/plain

    OK

Status Codes:

-   **200** - no error
-   **500** - server error

### Create a new image from a container's changes

`POST /commit`

Create a new image from a container's changes

**Example request**:

    POST /commit?container=44c004db4b17&comment=message&repo=myrepo HTTP/1.1
    Content-Type: application/json

    {
         "Hostname": "",
         "Domainname": "",
         "User": "",
         "AttachStdin": false,
         "AttachStdout": true,
         "AttachStderr": true,
         "Tty": false,
         "OpenStdin": false,
         "StdinOnce": false,
         "Env": null,
         "Cmd": [
                 "date"
         ],
         "Mounts": [
           {
             "Source": "/data",
             "Destination": "/data",
             "Mode": "ro,Z",
             "RW": false
           }
         ],
         "Labels": {
                 "key1": "value1",
                 "key2": "value2"
          },
         "WorkingDir": "",
         "NetworkDisabled": false,
         "ExposedPorts": {
                 "22/tcp": {}
         }
    }

**Example response**:

    HTTP/1.1 201 Created
    Content-Type: application/json

    {"Id": "596069db4bf5"}

Json Parameters:

-  **config** - the container's configuration

Query Parameters:

-   **container** – source container
-   **repo** – repository
-   **tag** – tag
-   **comment** – commit message
-   **author** – author (e.g., "John Hannibal Smith
    <[hannibal@a-team.com](mailto:hannibal%40a-team.com)>")
-   **pause** – 1/True/true or 0/False/false, whether to pause the container before committing
-   **changes** – Dockerfile instructions to apply while committing

Status Codes:

-   **201** – no error
-   **404** – no such container
-   **500** – server error

### Monitor Docker's events

`GET /events`

Get container events from docker, either in real time via streaming, or via
polling (using since).

Docker containers report the following events:

    attach, commit, copy, create, destroy, die, exec_create, exec_start, export, kill, oom, pause, rename, resize, restart, start, stop, top, unpause

and Docker images report:

    delete, import, pull, push, tag, untag

**Example request**:

    GET /events?since=1374067924

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/json

    {"status":"pull","id":"busybox:latest","time":1442421700,"timeNano":1442421700598988358}
    {"status":"create","id":"5745704abe9caa5","from":"busybox","time":1442421716,"timeNano":1442421716853979870}
    {"status":"attach","id":"5745704abe9caa5","from":"busybox","time":1442421716,"timeNano":1442421716894759198}
    {"status":"start","id":"5745704abe9caa5","from":"busybox","time":1442421716,"timeNano":1442421716983607193}

Query Parameters:

-   **since** – Timestamp used for polling
-   **until** – Timestamp used for polling
-   **filters** – A json encoded value of the filters (a map[string][]string) to process on the event list. Available filters:
  -   `container=<string>`; -- container to filter
  -   `event=<string>`; -- event to filter
  -   `image=<string>`; -- image to filter
  -   `label=<string>`; -- image and container label to filter

Status Codes:

-   **200** – no error
-   **500** – server error

### Get a tarball containing all images in a repository

`GET /images/(name)/get`

Get a tarball containing all images and metadata for the repository specified
by `name`.

If `name` is a specific name and tag (e.g. ubuntu:latest), then only that image
(and its parents) are returned. If `name` is an image ID, similarly only that
image (and its parents) are returned, but with the exclusion of the
'repositories' file in the tarball, as there were no image names referenced.

See the [image tarball format](#image-tarball-format) for more details.

**Example request**

    GET /images/ubuntu/get

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/x-tar

    Binary data stream

Status Codes:

-   **200** – no error
-   **500** – server error

### Get a tarball containing all images.

`GET /images/get`

Get a tarball containing all images and metadata for one or more repositories.

For each value of the `names` parameter: if it is a specific name and tag (e.g.
`ubuntu:latest`), then only that image (and its parents) are returned; if it is
an image ID, similarly only that image (and its parents) are returned and there
would be no names referenced in the 'repositories' file for this image ID.

See the [image tarball format](#image-tarball-format) for more details.

**Example request**

    GET /images/get?names=myname%2Fmyapp%3Alatest&names=busybox

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: application/x-tar

    Binary data stream

Status Codes:

-   **200** – no error
-   **500** – server error

### Load a tarball with a set of images and tags into docker

`POST /images/load`

Load a set of images and tags into a Docker repository.
See the [image tarball format](#image-tarball-format) for more details.

**Example request**

    POST /images/load

    Tarball in body

**Example response**:

    HTTP/1.1 200 OK

Status Codes:

-   **200** – no error
-   **500** – server error

### Image tarball format

An image tarball contains one directory per image layer (named using its long ID),
each containing these files:

- `VERSION`: currently `1.0` - the file format version
- `json`: detailed layer information, similar to `docker inspect layer_id`
- `layer.tar`: A tarfile containing the filesystem changes in this layer

The `layer.tar` file contains `aufs` style `.wh..wh.aufs` files and directories
for storing attribute changes and deletions.

If the tarball defines a repository, the tarball should also include a `repositories` file at
the root that contains a list of repository and tag names mapped to layer IDs.

```
{"hello-world":
    {"latest": "565a9d68a73f6706862bfe8409a7f659776d4d60a8d096eb4a3cbce6999cc2a1"}
}
```

### Exec Create

`POST /containers/(id)/exec`

Sets up an exec instance in a running container `id`

**Example request**:

    POST /containers/e90e34656806/exec HTTP/1.1
    Content-Type: application/json

      {
       "AttachStdin": false,
       "AttachStdout": true,
       "AttachStderr": true,
       "Tty": false,
       "Cmd": [
                     "date"
             ]
      }

**Example response**:

    HTTP/1.1 201 OK
    Content-Type: application/json

    {
         "Id": "f90e34656806",
         "Warnings":[]
    }

Json Parameters:

-   **AttachStdin** - Boolean value, attaches to `stdin` of the `exec` command.
-   **AttachStdout** - Boolean value, attaches to `stdout` of the `exec` command.
-   **AttachStderr** - Boolean value, attaches to `stderr` of the `exec` command.
-   **Tty** - Boolean value to allocate a pseudo-TTY.
-   **Cmd** - Command to run specified as a string or an array of strings.


Status Codes:

-   **201** – no error
-   **404** – no such container

### Exec Start

`POST /exec/(id)/start`

Starts a previously set up `exec` instance `id`. If `detach` is true, this API
returns after starting the `exec` command. Otherwise, this API sets up an
interactive session with the `exec` command.

**Example request**:

    POST /exec/e90e34656806/start HTTP/1.1
    Content-Type: application/json

    {
     "Detach": false,
     "Tty": false
    }

**Example response**:

    HTTP/1.1 201 OK
    Content-Type: application/json

    {{ STREAM }}

Json Parameters:

-   **Detach** - Detach from the `exec` command.
-   **Tty** - Boolean value to allocate a pseudo-TTY.

Status Codes:

-   **200** – no error
-   **404** – no such exec instance
-   **409** - container is stopped or paused

    **Stream details**:
    Similar to the stream behavior of `POST /container/(id)/attach` API

### Exec Resize

`POST /exec/(id)/resize`

Resizes the `tty` session used by the `exec` command `id`.  The unit is number of characters.
This API is valid only if `tty` was specified as part of creating and starting the `exec` command.

**Example request**:

    POST /exec/e90e34656806/resize?h=40&w=80 HTTP/1.1
    Content-Type: text/plain

**Example response**:

    HTTP/1.1 201 OK
    Content-Type: text/plain

Query Parameters:

-   **h** – height of `tty` session
-   **w** – width

Status Codes:

-   **201** – no error
-   **404** – no such exec instance

### Exec Inspect

`GET /exec/(id)/json`

Return low-level information about the `exec` command `id`.

**Example request**:

    GET /exec/11fb006128e8ceb3942e7c58d77750f24210e35f879dd204ac975c184b820b39/json HTTP/1.1

**Example response**:

    HTTP/1.1 200 OK
    Content-Type: plain/text

    {
      "ID" : "11fb006128e8ceb3942e7c58d77750f24210e35f879dd204ac975c184b820b39",
      "Running" : false,
      "ExitCode" : 2,
      "ProcessConfig" : {
        "privileged" : false,
        "user" : "",
        "tty" : false,
        "entrypoint" : "sh",
        "arguments" : [
          "-c",
          "exit 2"
        ]
      },
      "OpenStdin" : false,
      "OpenStderr" : false,
      "OpenStdout" : false,
      "Container" : {
        "State" : {
          "Status" : "running",
          "Running" : true,
          "Paused" : false,
          "Restarting" : false,
          "OOMKilled" : false,
          "Pid" : 3650,
          "ExitCode" : 0,
          "Error" : "",
          "StartedAt" : "2014-11-17T22:26:03.717657531Z",
          "FinishedAt" : "0001-01-01T00:00:00Z"
        },
        "ID" : "8f177a186b977fb451136e0fdf182abff5599a08b3c7f6ef0d36a55aaf89634c",
        "Created" : "2014-11-17T22:26:03.626304998Z",
        "Path" : "date",
        "Args" : [],
        "Config" : {
          "Hostname" : "8f177a186b97",
          "Domainname" : "",
          "User" : "",
          "AttachStdin" : false,
          "AttachStdout" : false,
          "AttachStderr" : false,
          "ExposedPorts" : null,
          "Tty" : false,
          "OpenStdin" : false,
          "StdinOnce" : false,
          "Env" : [ "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin" ],
          "Cmd" : [
            "date"
          ],
          "Image" : "ubuntu",
          "Volumes" : null,
          "WorkingDir" : "",
          "Entrypoint" : null,
          "NetworkDisabled" : false,
          "MacAddress" : "",
          "OnBuild" : null,
          "SecurityOpt" : null
        },
        "Image" : "5506de2b643be1e6febbf3b8a240760c6843244c41e12aa2f60ccbb7153d17f5",
        "NetworkSettings" : {
          "IPAddress" : "172.17.0.2",
          "IPPrefixLen" : 16,
          "MacAddress" : "02:42:ac:11:00:02",
          "Gateway" : "172.17.42.1",
          "Bridge" : "docker0",
          "Ports" : {}
        },
        "ResolvConfPath" : "/var/lib/docker/containers/8f177a186b977fb451136e0fdf182abff5599a08b3c7f6ef0d36a55aaf89634c/resolv.conf",
        "HostnamePath" : "/var/lib/docker/containers/8f177a186b977fb451136e0fdf182abff5599a08b3c7f6ef0d36a55aaf89634c/hostname",
        "HostsPath" : "/var/lib/docker/containers/8f177a186b977fb451136e0fdf182abff5599a08b3c7f6ef0d36a55aaf89634c/hosts",
        "LogPath": "/var/lib/docker/containers/1eb5fabf5a03807136561b3c00adcd2992b535d624d5e18b6cdc6a6844d9767b/1eb5fabf5a03807136561b3c00adcd2992b535d624d5e18b6cdc6a6844d9767b-json.log",
        "Name" : "/test",
        "Driver" : "aufs",
        "ExecDriver" : "native-0.2",
        "MountLabel" : "",
        "ProcessLabel" : "",
        "AppArmorProfile" : "",
        "RestartCount" : 0,
        "Mounts" : []
      }
    }

Status Codes:

-   **200** – no error
-   **404** – no such exec instance
-   **500** - server error

## 2.4 Volumes

### List volumes

`GET /volumes`

**Example request**:

  GET /volumes HTTP/1.1

**Example response**:

  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "Volumes": [
      {
        "Name": "tardis",
        "Driver": "local",
        "Mountpoint": "/var/lib/docker/volumes/tardis"
      }
    ]
  }

Query Parameters:

- **filter** - JSON encoded value of the filters (a `map[string][]string`) to process on the volumes list. There is one available filter: `dangling=true`

Status Codes:

-   **200** - no error
-   **500** - server error

### Create a volume

`POST /volumes/create`

Create a volume

**Example request**:

  POST /volumes/create HTTP/1.1
  Content-Type: application/json

  {
    "Name": "tardis"
  }

**Example response**:

  HTTP/1.1 201 Created
  Content-Type: application/json

  {
    "Name": "tardis"
    "Driver": "local",
    "Mountpoint": "/var/lib/docker/volumes/tardis"
  }

Status Codes:

- **201** - no error
- **500**  - server error

JSON Parameters:

- **Name** - The new volume's name. If not specified, Docker generates a name.
- **Driver** - Name of the volume driver to use. Defaults to `local` for the name.
- **DriverOpts** - A mapping of driver options and values. These options are
    passed directly to the driver and are driver specific.

### Inspect a volume

`GET /volumes/(name)`

Return low-level information on the volume `name`

**Example request**:

    GET /volumes/tardis

**Example response**:

  HTTP/1.1 200 OK
  Content-Type: application/json

  {
    "Name": "tardis",
    "Driver": "local",
    "Mountpoint": "/var/lib/docker/volumes/tardis"
  }

Status Codes:

-   **200** - no error
-   **404** - no such volume
-   **500** - server error

### Remove a volume

`DELETE /volumes/(name)`

Instruct the driver to remove the volume (`name`).

**Example request**:

  DELETE /volumes/local/tardis HTTP/1.1

**Example response**:

  HTTP/1.1 204 No Content

Status Codes

-   **204** - no error
-   **404** - no such volume or volume driver
-   **409** - volume is in use and cannot be removed
-   **500** - server error

## 2.5 Networks

### List networks

`GET /networks`

**Example request**:

  GET /networks HTTP/1.1

**Example response**:

  HTTP/1.1 200 OK
  Content-Type: application/json

```
  [
    {
      "name": "bridge",
      "id": "f995e41e471c833266786a64df584fbe4dc654ac99f63a4ee7495842aa093fc4",
      "driver": "bridge",
      "containers": {}
    },
    {
      "name": "none",
      "id": "21e34df9b29c74ae45ba312f8e9f83c02433c9a877cfebebcf57be78f69b77c8",
      "driver": "null",
      "containers": {}
    },
    {
      "name": "host",
      "id": "3f43a0873f00310a71cd6a71e2e60c113cf17d1812be2ec22fd519fbac68ec91",
      "driver": "host",
      "containers": {}
    }
  ]
```



Query Parameters:

- **filter** - JSON encoded value of the filters (a `map[string][]string`) to process on the volumes list. Available filters: `name=[network-names]` , `id=[network-ids]`

Status Codes:

-   **200** - no error
-   **500** - server error

### Inspect network

`GET /networks/<network-id>`

**Example request**:

  GET /networks/f995e41e471c833266786a64df584fbe4dc654ac99f63a4ee7495842aa093fc4 HTTP/1.1

**Example response**:

  HTTP/1.1 200 OK
  Content-Type: application/json

```
  {
    "name": "bridge",
    "id": "f995e41e471c833266786a64df584fbe4dc654ac99f63a4ee7495842aa093fc4",
    "driver": "bridge",
    "containers": {
      "931d29e96e63022a3691f55ca18b28600239acf53878451975f77054b05ba559": {
        "endpoint": "aa79321e2899e6d72fcd46e6a4ad7f81ab9a19c3b06e384ef4ce51fea35827f9",
        "mac_address": "02:42:ac:11:00:04",
        "ipv4_address": "172.17.0.4/16",
        "ipv6_address": ""
      },
      "961249b4ae6c764b11eed923e8463c102689111fffd933627b2e7e359c7d0f7c": {
        "endpoint": "4f62c5aea6b9a70512210be7db976bd4ec2cdba47125e4fe514d18c81b1624b1",
        "mac_address": "02:42:ac:11:00:02",
        "ipv4_address": "172.17.0.2/16",
        "ipv6_address": ""
      },
      "9f6e0fec4449f42a173ed85be96dc2253b6719edd850d8169bc31bdc45db675c": {
        "endpoint": "352b512a5bccdfc77d16c2c04d04408e718f879a16f9ce3913a4733139e4f98d",
        "mac_address": "02:42:ac:11:00:03",
        "ipv4_address": "172.17.0.3/16",
        "ipv6_address": ""
      }
    }
  }
```

Status Codes:

-   **200** - no error
-   **404** - network not found

### Create a network

`POST /networks/create`

Create a network

**Example request**:

  POST /networks/create HTTP/1.1
  Content-Type: application/json

```
  {
    "name":"isolated_nw",
    "driver":"bridge"
  }
```

**Example response**:

  HTTP/1.1 201 Created
  Content-Type: application/json

```
  {
    "id": "22be93d5babb089c5aab8dbc369042fad48ff791584ca2da2100db837a1c7c30",
    "warning": ""
  }
```

Status Codes:

- **201** - no error
- **404** - driver not found
- **500** - server error

JSON Parameters:

- **name** - The new network's name. this is a mandatory field
- **driver** - Name of the network driver to use. Defaults to `bridge` driver
- **options** - Network specific options to be used by the drivers
- **check_duplicate** - Requests daemon to check for networks with same name

### Connect a container to a network

`POST /networks/(id)/connect`

Connects a container to a network

**Example request**:

  POST /networks/22be93d5babb089c5aab8dbc369042fad48ff791584ca2da2100db837a1c7c30/connect HTTP/1.1
  Content-Type: application/json

```
  {
    "container":"3613f73ba0e4"
  }
```

**Example response**:

  HTTP/1.1 200 OK

Status Codes:

- **201** - no error
- **404** - network or container is not found

JSON Parameters:

- **container** - container-id/name to be connected to the network

### Disconnect a container from a network

`POST /networks/(id)/disconnect`

Disconnects a container from a network

**Example request**:

  POST /networks/22be93d5babb089c5aab8dbc369042fad48ff791584ca2da2100db837a1c7c30/disconnect HTTP/1.1
  Content-Type: application/json

```
  {
    "container":"3613f73ba0e4"
  }
```

**Example response**:

  HTTP/1.1 200 OK

Status Codes:

- **201** - no error
- **404** - network or container not found

JSON Parameters:

- **container** - container-id/name to be disconnected from a network

### Remove a network

`DELETE /networks/(id)`

Instruct the driver to remove the network (`id`).

**Example request**:

  DELETE /networks/22be93d5babb089c5aab8dbc369042fad48ff791584ca2da2100db837a1c7c30 HTTP/1.1

**Example response**:

  HTTP/1.1 204 No Content

Status Codes

-   **204** - no error
-   **404** - no such network
-   **500** - server error

# 3. Going further

## 3.1 Inside `docker run`

As an example, the `docker run` command line makes the following API calls:

- Create the container

- If the status code is 404, it means the image doesn't exist:
    - Try to pull it.
    - Then, retry to create the container.

- Start the container.

- If you are not in detached mode:
- Attach to the container, using `logs=1` (to have `stdout` and
      `stderr` from the container's start) and `stream=1`

- If in detached mode or only `stdin` is attached, display the container's id.

## 3.2 Hijacking

In this version of the API, `/attach`, uses hijacking to transport `stdin`,
`stdout`, and `stderr` on the same socket.

To hint potential proxies about connection hijacking, Docker client sends
connection upgrade headers similarly to websocket.

    Upgrade: tcp
    Connection: Upgrade

When Docker daemon detects the `Upgrade` header, it switches its status code
from **200 OK** to **101 UPGRADED** and resends the same headers.


## 3.3 CORS Requests

To set cross origin requests to the remote api please give values to 
`--api-cors-header` when running Docker in daemon mode. Set * (asterisk) allows all,
default or blank means CORS disabled

    $ docker daemon -H="192.168.1.9:2375" --api-cors-header="http://foo.bar"
