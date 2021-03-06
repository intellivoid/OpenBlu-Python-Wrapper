# OpenBlu-Python-Wrapper

Official Python wrapper around OpenBlu API


# Overview

This wrapper can be used to fetch servers from the OpenBlu API and retrieve their OpenVPN configuration.

To make things easy, the library abstracts the JSON responses from the API inside three objects:

- `Server` -> Contains more detailed information from the server as described in [OpenBlu's Documentation](https://docs.intellivoid.net/openblu/v1/get_server)
- `ServerListing` -> Contains metadata from a server such as ping, country and location
- `OpenVPN` -> Contained inside a `Server` object, stores the OpenVPN configuratiom for the server


The `ServerListing` and `Server` objects can be printed to get an overview of the server's information.


## Installation

You can obtain this package via PyPi

```sh
pip install openblu
```

## Manual Installation

To install this wrapper, simply clone this repo with `git clone`, then run `python3 setup.py install`


## Usage

### Fetch multiple servers

To fetch multiple servers, without their OpenVPN configuration, you can use the `list_servers()` method of the `openblu.OpenBluAPI` class like shown below

```python

from openblu import OpenBluAPI

api = OpenBluAPI('access_key')  # Get your access key at openblu.intellivoid.net

servers = api.list_servers(filter_by=("germany", "country"))
```

Below the full documentation for the `list_servers` method in Sphinx format

```
Fetches OpenVPN servers from the OpenBlu API

:param filter_by: If not ``None``, filter the results by the given parameter. It must be a tuple containing a country name and either one of these strings (In this order): "country", "country_short". If "country_short" is the second element of the tuple, the country name must be the short form of its name (e.g. 'de' for germany or 'jp' for Japan) otherwise, the full extended form is required. Defaults to ``None``
:type filter_by: tuple, None, optional
:param order_by: If not ``None``, order the results by this order. It can either be ``'ascending'`` or ``'descending'``, defaults to ``None``.
:type order_by: str, None, optional
:param sort_by: Sorts the list by the given condition, defaults to ``None`` (no sorting). Possible choices are "score", "ping", "sessions", "total_sessions", "last_updated" and "created"
:type sort_by: str, None, optional
:param verbose: If ``True``, make the output verbose, default to ``False``
:type verbose: bool, optional
:returns servers_list: A list of class:ServerListing objects
:rtype servers_list: list
:raises OpenBluError: An proper subclass of OpenBluError is raised if something goes wrong. If the error cannot be determined, a generic OpenBluError is raised
```


### Get a single server

As described in OpenBlu's documentation, servers can be identified by a unique ID. That ID can be used to fetch a more detailed server object containing the OpenVPN server configuration from the OpenBlu API as shown below.
This server object also contains the server's IP address

```python

from openblu import OpenBluAPI

api = OpenBluAPI('access_key')  # Get your access key at openblu.intellivoid.net

servers = api.list_servers(filter_by=("germany", "country"))

server = servers[0]    # Take the first entry

srv = api.get_server(server.id)   # Retrieve the server's configuration
```

Below the full documentation for the `get_server` method in Sphinx format

```

Fetches a single OpenVPN server from the OpenBlu API, given its unique ID

:param uuid: The unique ID of the desired server
:type uuid: string
:param verbose: If ``True``, make output verbose, default to ``False``
:type verbose: bool, optional
:returns: A class:Server object
:rtype: class: Server
:raises OpenBluError: An proper subclass of OpenBluError is raised if something goes wrong. If the error cannot be determined, a generic OpenBluError is raised
```

For more examples and a deeper description check the `examples.py` file in this repo

## Objects' overview

Here are some example objects to get you started straight away

__Note__: All objects, when printed out, show all the information inside the object itself (including `None` values and such)


### ServerListing

When printed out, a `ServerListing` object should look like the following, although the corresponding parameters may differ.

```
ServerListing(
id=dbd24ff8e4802f22,
host_name=vpn774575625,
country=Germany,
country_short=DE,
score=366198,
ping=28,
sessions=66,
total_sessions=30926,
last_updated=1576539451,
created=1576529629)
```

### Server

The server object has a similar output to the `ServerListing`, but (of course) it contains different and more descriptive information about a single server.

```
Server(
id=dbd24ff8e4802f22,
host_name=vpn774575625,
country=Germany,
country_short=DE,
score=366198,
ping=28,
sessions=66,
total_sessions=30926,
ip_address=78.54.226.91,
openvpn=<OpenVPN object at 0x7b2b706f10>,
last_updated=1576539451,
created=1576529629)
```

### OpenVPN objects

OpenVPN objects' output is much less verbose, because printing all of the object's parameters would literally need pages of output, their attributes are still accessible both via dot notation and via dict-like accessing.

For the sake of completeness, all the object's parameters are listed here; to get detailed info about their meaning refer to [OpenBlu's official docs](https://docs.intellivoid.net/openblu/v1)

```
- parameters
- certificate_authority
- certificate_authority_b64
- certificate
- certificate_b64
- key
- key_b64
- ovpn_configuration
```

## Exceptions

The wrapper implements 3 exceptions:
- `OpenBluError` -> Generic parent class for all exceptions, also raised when an error other than 401 and 404 is returned by the API
- `ServerNotFound` -> When an invalid server ID is provided to the API
- `UnauthorizedAccess` -> When an invalid access key is provided to the API

Other errors, such as JSON decoding errors or HTTP failures, are not catched and must be handled by the end user itself.

### Last, but not least

All objects objects support dict-like accessing as well as dot notation, which means that doing `server["id"]` or `server.id` yields the same result.


