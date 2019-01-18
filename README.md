# Pre-requisite


```
# Install autoconf (Automatic configure script builder) and associated dependencies

debian:~$ sudo apt-get install autoconf automake autotools-dev libsigsegv2 m4
```

# Install Cmake and associated dependencies
```
debian:~$ sudo apt-get install cmake cmake-data libjsoncpp1
```

# Install Default JDK and associated dependencies
```
debian:~$ sudo apt-get install ca-certificates-java default-jdk default-jdk-headless default-jre default-jre-headless fonts-dejavu-extra java-common libbonobo2-0 libbonobo2-common libgif7 libgnome-2-0 libgnome2-common libgnomevfs2-0 libgnomevfs2-common liborbit-2-0 openjdk-8-jdk openjdk-8-jdk-headless openjdk-8-jre openjdk-8-jre-headless
```
# Install PAM (pluggable authentication module) development module
```
debian:~$ sudo apt-get install libpam0g-dev
```

# Install Curl OpenSSL Development Libraries
```
debian:~$ sudo apt-get install libcurl4-openssl-dev
```
# Install development files for the GNOME XML Libraries
```
debian:~$ sudo apt-get install icu-devtools libicu-dev libxml2-dev
```
# Install SWIG (Simplified Wrapper & Interface Generator) for wrapping C/C++ functions for use with a script generator
```
debian:~$ sudo apt-get install swig3.0
```
# Install sblim-sfcc (Small Footprint CIM Client) libraries
```
debian:~$ sudo apt-get install libcimcclient0 libcimcclient0-dev
```
# Install python development libraries
```
debian:~$ sudo apt-get install libpython-dev
```

Download the latest committed release openwsman-2.6.3.tar.gz source tar ball from the github url and extract it:
```
debian:~$ cd /tmp

debian:/tmp$ wget https://github.com/Openwsman/openwsman/archive/v2.6.3.tar.gz -O /tmp/openwsman-2.6.3.tar.gz

debian:/tmp$ tar –zxvf openwsman-2.6.3.tar.gz

```
Let’s disable the Ruby bindings so that you don’t run into Ruby installations errors. In order to do so, please navigate to openwsman-2.6.3 directory and edit the top-level CMakeLists.txt file and set the BUILD_RUBY option to NO.
```
debian:/tmp$ cd openwsman-2.6.3

debian:/tmp/openwsman-2.6.3$ perl –p –i –e "s/(\\$\{CURL_LIBRARIES\})/\1 ssl crypto/g" src/lib/CMakeLists.txt

debian:/tmp/openwsman-2.6.3$ vi CMakeLists.txt
```
#Find and edit the BUILD_RUBY target option to NO:
``
OPTION( BUILD_RUBY “Build Ruby Bindings” YES)

                           To

OPTION( BUILD_RUBY “Build Ruby Bindings” NO)
```




# How to compile and run openwsman from GIT ? 

After checking out the project from git, create a build directory
with

    mkdir build

cd into it

    cd build
    cmake ..
    make

See `package/openwsman.spec.in` for running cmake in 'release' mode

Most likely you will need to install some of the packages, depending
on the distribution you are running.

Pre-build (RPM) packages for many distributions are available at
[the openSUSE build service](https://build.opensuse.org/project/show?project=Openwsman)


## Packages and other software needed:

* libxml2
* sblim-sfcc (from the sblim project)
* swig and python for python binding support
* other development packages like cmake, gcc, etc.

After all packages are installed, compile everything and install. The
server can run as a daemon, which would require root access; But it
can be run in the foreground with debugging messages printed to stdout
as well. This the help output when you run:

    /usr/sbin/openwsmand --help
    Usage:
    openwsmand [OPTION...] WS-Management Server

    Help Options:
     -?, --help                   Show help options

    Application Options:
     -n, --no-plugins             Do not load any plugins
     -d, --debug                  Start daemon in foreground and turn on debugging
     -s, --syslog=0-6             Set the verbosity of syslog output.
     -c, --config-file=<file>     Alternate configuration file

Starting from version 0.1.1 a configuration file is needed. you can
find an example in the `./etc` directory. The configuration file has the
following syntax:

    [server]
    port = 5985
    #ssl_port = 5986
    ssl_cert_file = /etc/openwsman/servercert.pem
    ssl_key_file = /etc/openwsman/serverkey.pem
    #digest_password_file = /etc/openwsman/digest_auth.passwd
    basic_password_file = /etc/openwsman/simple_auth.passwd
    enum_idle_timeout = 5000

    max_threads = 1

    #use_digest is OBSOLETED, see below.

    #
    # Authentication backend for BASIC authentication. Default is to read a configuration file defined with 'basic_password_file'
    #

    #basic_authenticator = libwsman_pam_auth.so
    #basic_authenticator_arg = openwsman


    [client]
    port = 5988
    agent = openwsman 2.2.4

    [cim]
    default_cim_namespace = root/cimv2

    # The following are in part fake namespaces for some publicly available CIM implementations.
    vendor_namespaces = OpenWBEM=http://schema.openwbem.org/wbem/wscim/1/cim-schema/2,Linux=http://sblim.sf.net/wbem/wscim/1/cim-schema/2,OMC=http://schema.omc-project.org/wbem/wscim/1/cim-schema/2,Reef=http://reef.sblim.sf.net/wbem/wscim/1/cim-schema/2,CWS=http://cws.sblim.sf.net/wbem/wscim/1/cim-schema/2


To run in SSL mode you need to enable the SSL port and create certificates and adapt the configuration
with the correct path to the files.

To start the server in the foreground, run:

    /usr/sbin/openwsmand  -d

You can also specify the configuration file to be used on the command line using the -c option.

On the client side, which has the following options

    /usr/bin/wsman --help-all

    Usage:
      wsman [Option...] <action> <Resource Uri>

    Help Options
     -?, --help
     --help-all                                      Show help options
     --help-enumeration                              Enumeration Options
     --help-tests                                    Test Cases
     --help-cim                                      CIM Options
     --help-flags                                    Request Flags
     --help-event                                    Subscription Options

    Enumeration
     -m, --max-elements=<max number of elements>     Max Elements Per Pull/Optimized Enumeration
     -o, --optimize                                  Optimize enumeration results
     -E, --estimate-count                            Return estimation of total items
     -M, --enum-mode=epr|objepr                      Enumeration Mode
     -U, --enum-context=<enum context>               Enumeration Context (For use with Pull and Release)

    Tests
     -f, --from-file=<file name>                     Send request from file
     -R, --print-request                             print request on stdout
     -Q, --request                                   Only output reqest. Not send it.
     -S, --step                                      Do not perform multiple operations (do not pull data when enumerating)

    CIM
     -N, --namespace=<namespace>                     CIM Namespace (default is root/cimv2)
     -B, --binding-enum-mode=none|include|exclude    CIM binding Enumeration Mode
     -T, --cim-extensions                            Show CIM Extensions
     -W, --references                                CIM References
     -w, --associators                               CIM Associators

    Flags
     -x, --filter=<filter>                           Filter
     -D, --dialect=<dialect>                         Filter Dialect
     -t, --operation-timeout=<time in sec>           Operation timeout in seconds
     -e, --max-envelope-size=<size>                  maximal envelope size
     -F, --fragment=<fragment>                       Fragment (Supported Dialects: XPATH)

    Event subscription
     -G, --delivery-mode=<mode>                      Four delivery modes available: push/pushwithack/events/pull
     -Z, --notification-uri=<uri>                    Where notifications are sent
     -r, --subscription-expiry-time=<seconds>        subscription expiry time in seconds
     -H, --heartbeat=<seconds>                       Send hearbeat in an interval
     -l, --bookmark                                  Send bookmark
     -i, --subscription-identifier=<uuid:XXX>        Used to specify which subscription
     -L, --notify-reference-properties=<xs:anyURI>   Notify Reference Properties

    Application Options
     -d, --debug=1-6                                 Set the verbosity of debugging output.
     -c, --cacert=<filename>                         Certificate file to verify the peer
     -A, --cert=<filename>                           Certificate file. The certificate must be in PEM format.
     -K, --sslkey=<key>                              SSL Key.
     -u, --username=<username>                       User name
     -g, --path=<path>                               Path
     -J, --input=<filename>                          File with resource for Create and Put operations in XML, can be a SOAP envelope
     -p, --password=<password>                       Password
     -h, --hostname=<hostname>                       Host name
     -b, --endpoint=<url>                            End point
     -P, --port=<port>                               Server Port
     -X, --proxy=<proxy>                             Proxy name
     -Y, --proxyauth=<proxyauth>                     Proxy user:pwd
     -y, --auth=<basic|digest|gss>                   Authentication Method
     -a, --method=<custom method>                    Method (Works only with 'invoke')
     -k, --prop=<key=val>                            Properties with key value pairs (For 'put', 'invoke' and 'create')
     -C, --config-file=<file>                        Alternate configuration file
     -O, --out-file=<file>                           Write output to file
     -V, --noverifypeer                              Not to verify peer certificate
     -v, --noverifyhost                              Not to verify hostname
     -I, --transport-timeout=<time in sec>           Transport timeout in seconds

To create a password file, use the `htpasswd` and `htdigest` utilities
from the [Apache](http://www.apache.org) distribution.

You can connect to the server with the following command, which is part of the DMTF WS-Management specification

    wsman identify -h <hostname> --port 5988 -u wsman --password secret


The above command should give the following result:


    <?xml version="1.0" encoding="UTF-8"?>
    <s:Envelope xmlns:s="http://www.w3.org/2003/05/soap-envelope" xmlns:wsmid="http://schemas.dmtf.org/wbem/wsman/identity/1/wsmanidentity.xsd">
     <s:Header/>
     <s:Body>
      <wsmid:IdentifyResponse>
       <wsmid:ProtocolVersion>http://schemas.dmtf.org/wbem/wsman/1/wsman.xsd</wsmid:ProtocolVersion>
       <wsmid:ProductVendor>openwsman</wsmid:ProductVendor>
       <wsmid:ProductVersion>2.2.4</wsmid:ProductVersion>
      </wsmid:IdentifyResponse>
     </s:Body>
    </s:Envelope>
