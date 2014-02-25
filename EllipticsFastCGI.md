#Elliptics FastCGI

Elliptics FastCGI is a proxy, which receives http-requests and using elliptics-client component to write them. This is a multi-threaded proxy. Can specify multiple pools-streams and set what handlers on which pools will be hanging.
Elliptics FastCGI proxy consists of the following sections that allow you to specify different configurations:
  - pools - sets the name of thread pool which will serve;
```xml
<pools>
  <pool name="read" threads="4" queue="16"/><!--- It consists of 4 threads, which have all 16 queries -->
</pools>
```
  - handlers - define what requests in what pools will be processed;
```xml
<handlers>
  <handler pool="read" port="84" url="/(download-info|get).*"><!--- It means that the pool reads at 84 port and describes requests specified by regex. -->
        <component name="elliptics-proxy"/><!--- It serviced Elliptics proxy component. -->
  </handler>
</handlers>
```
  - [components](#components) - section that describes the components of Elliptics FastCGI proxy: [elliptics-proxy](#elliptics-proxy) and [daemon-logger](#daemon-log) components;
  - modules - path to components;
  - [daemon](#daemon) - define settings for the logger to use for elliptics HTTP proxy.

##Components
###<a name="elliptics-proxy"></a>The elliptics-proxy component
The elliptics-proxy component contains all sittings for proxy. It consists of the following sections: 
  - `logger` section is the logger to use for elliptics HTTP proxy;
  - `dnet` section contains the main settings of proxy. 

The `dnet` section consists of the following sections:

| Parameter | Description |
|-----------|-------------|
| die-limit | Sets how many live connections between Elliptics FastCGI proxy and Elliptics that to assume that the system is operable. But it is impossible make a record if the the system contains fewer connections because it works in read-only mode. |
| base-port | The value for Dnet base port. |
| eblob_style_path |  If the value is 1 that's eblob, else if the value 0. |
| write_chunk_size | Chunk size of the file for writing. If size is not specified, the file is written all at once. |
| read_chunk_size | Chunk size of the file for reading. If size is not specified, the file is read all at once. |
| log | It is the settings for file logger to elliptics. |
| remote | Used to add connection with server by the host, the port and the family. |
| allow | Allow list: what file extentions allowed to download. |
| deny | Deny list: what file extentions denied to download. |
| typemap | Typemap: the file extension determines the type of content. |
| groups| Dnet groups. They are determined by a colon. |
| replication-count | The number of copies that you want to record. |
|	copies-num | The number of writes that are considered successful. |

The log section includes the following parameters to create a logger:

| Parameter | Description |
|-----------|-------------|
| path | Is a file path to logging. |
| mask | Is a log-level. |
| wait_timeout |	The time to wait for the operation complete. |
|	reconnect_timeout | 	The timeout for pinging node. |

###<a name="daemon-log"></a>The daemon-logger component
Defualts:
```xml
<component name="daemon-logger" type="logger:logger">
       <level>DEBUG</level>
       <control-uri>/log</control-uri>
        <ident>fastcgi-proxy</ident>
</component>
```
This component is used to set the logger for Elliptics FastCGI proxy. It contains the following settings:
  - `level` is a level of verbosity;
  - `ident` is a word identifier that transforms all the entries in the log. This word can be used when working with the grep command.

##Daemon
Defualts:
```xml
<daemon>
    <logger component="daemon-logger"/>
    <endpoint>
        <backlog>128</backlog>
        <socket>/tmp/fastcgi/elliptics-proxy.sock</socket>
        <threads>1</threads>
    </endpoint>
    <pidfile>/tmp/pid/elliptics-proxy.pid</pidfile>
    <monitor_port>20084</monitor_port>
</daemon>
```
This section sets out the basic parameters of the demon-logger:
  - `socket` path to the Elliptics FastCGI socket;
  - `backlog` queue on this socket;
  - `threads` number of threads.
