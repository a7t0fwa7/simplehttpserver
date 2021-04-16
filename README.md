<h1 align="center">
  <img src="static/simplehttpserver-logo.png" alt="simplehttpserver" width="200px"></a>
  <br>
</h1>

[![License](https://img.shields.io/badge/license-MIT-_red.svg)](https://opensource.org/licenses/MIT)
[![Go Report Card](https://goreportcard.com/badge/github.com/projectdiscovery/simplehttpserver)](https://goreportcard.com/report/github.com/projectdiscovery/simplehttpserver)
[![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/projectdiscovery/simplehttpserver/issues)
[![GitHub Release](https://img.shields.io/github/release/projectdiscovery/simplehttpserver)](https://github.com/projectdiscovery/simplehttpserver/releases)
[![Follow on Twitter](https://img.shields.io/twitter/follow/pdiscoveryio.svg?logo=twitter)](https://twitter.com/pdiscoveryio)
[![Docker Images](https://img.shields.io/docker/pulls/projectdiscovery/simplehttpserver.svg)](https://hub.docker.com/r/projectdiscovery/simplehttpserver)
[![Chat on Discord](https://img.shields.io/discord/695645237418131507.svg?logo=discord)](https://discord.gg/KECAGdH)

simplehttpserver is a go enhanced version of the well known python simplehttpserver with in addition a fully customizable TCP server, both supporting TLS.

# Resources

- [Features](#features)
- [Usage](#usage)
- [Installation Instructions](#installation-instructions)
- [Running simplehttpserver](#running-simplehttpserver-in-the-current-folder)
- [Thanks](#thanks)

# Features

<h1 align="left">
  <img src="static/simplehttpserver-run.png" alt="simplehttpserver" width="700px"></a>
  <br>
</h1>

- HTTPS support
- File server in arbitrary directory
- Full request/response dump
- Configurable ip address and listening port
- Configurable HTTP/TCP server with customizable response via YAML template


# Installation Instructions


### From Binary

The installation is easy. You can download the pre-built binaries for your platform from the [Releases](https://github.com/projectdiscovery/simplehttpserver/releases/) page. Extract them using tar, move it to your `$PATH`and you're ready to go.

```sh
Download latest binary from https://github.com/projectdiscovery/simplehttpserver/releases

▶ tar -xvf simplehttpserver-linux-amd64.tar
▶ mv simplehttpserver-linux-amd64 /usr/local/bin/simplehttpserver
▶ simplehttpserver -h
```

### From Source

simplehttpserver requires **go1.14+** to install successfully. Run the following command to get the repo - 

```sh
▶ GO111MODULE=on go get -v github.com/projectdiscovery/simplehttpserver
```

### From Github

```sh
▶ git clone https://github.com/projectdiscovery/simplehttpserver.git; cd simplehttpserver; go build; mv simplehttpserver /usr/local/bin/; simplehttpserver -h
```

# Usage

```sh
simplehttpserver -h
```

This will display help for the tool. Here are all the switches it supports.

| Flag        | Description                                                          | Example                                           |
| ----------- | -------------------------------------------------------------------- | ------------------------------------------------- |
| listen      | Configure listening ip:port (default 127.0.0.1:8000)                 | simplehttpserver -listen 127.0.0.1:8000           |
| path        | Fileserver folder (default current directory)                        | simplehttpserver -path /var/docs                  |
| verbose     | Verbose (dump request/response, default false)                       | simplehttpserver -v                               |
| tcp         | TCP server (default 127.0.0.1:8000)                                  | simplehttpserver -tcp 127.0.0.1:8000              |
| tls         | Enable TLS for TCP server                                            | simplehttpserver -tls                             |
| rules       | File containing yaml rules                                           | simplehttpserver -rules rule.yaml                 |              |
| upload      | Enable file upload in case of http server                            | simplehttpserver -upload                          |
| https       | Enable HTTPS in case of http server                                  | simplehttpserver -https                           |
| cert        | HTTPS/TLS certificate (self generated if not specified)              | simplehttpserver -cert cert.pem                   |
| key         | HTTPS/TLS certificate private key (self generated if not specified)  | simplehttpserver -key cert.key                    |
| domain      | Domain name to use for the self-generated certificate                | simplehttpserver -domain projectdiscovery.io      |
| basic-auth  | Basic auth (username:password)                                       | simplehttpserver -basic-auth user:password        |
| realm       | Basic auth message                                                   | simplehttpserver -realm "insert the credentials"  |
| version     | Show version                                                         | simplehttpserver -version                         |
| silent      | Show only results                                                    | simplehttpserver -silent                          |

### Running simplehttpserver in the current folder  

This will run the tool exposing the current directory on port 8000 

```sh
▶ simplehttpserver 
2021/01/11 21:40:48 Serving . on http://0.0.0.0:8000/...
2021/01/11 21:41:15 [::1]:50181 "GET / HTTP/1.1" 200 383
2021/01/11 21:41:15 [::1]:50181 "GET /favicon.ico HTTP/1.1" 404 19
```

### Running simplehttpserver in the current folder with HTTPS

This will run the tool exposing the current directory on port 8000 over HTTPS with user provided certificate:

```sh
▶ simplehttpserver -https -cert cert.pen -key cert.key
2021/01/11 21:40:48 Serving . on http://0.0.0.0:8000/...
2021/01/11 21:41:15 [::1]:50181 "GET / HTTP/1.1" 200 383
2021/01/11 21:41:15 [::1]:50181 "GET /favicon.ico HTTP/1.1" 404 19
```

Instead, to run with self-signed certificate and specific domain name:
```sh
▶ simplehttpserver -https -domain localhost
2021/01/11 21:40:48 Serving . on http://0.0.0.0:8000/...
2021/01/11 21:41:15 [::1]:50181 "GET / HTTP/1.1" 200 383
2021/01/11 21:41:15 [::1]:50181 "GET /favicon.ico HTTP/1.1" 404 19
```

### Running simplehttpserver with basic auth and file upload

This will run the tool and will request the user to enter username and password before authorizing file uploads

```sh
▶ simplehttpserver -basic-auth root:root -upload
2021/01/11 21:40:48 Serving . on http://0.0.0.0:8000/...
```

To upload files use the following curl request with basic auth header:
```sh
▶ curl -v --user 'root:root' --upload-file file.txt http://localhost:8000/file.txt
```

### Running TCP server with custom responses

This will run the tool as TLS TCP server and enable custom responses based on YAML templates:

```sh
▶ simplehttpserver -rule rules.yaml -tcp -tls -domain localhost
```

The rules are written as follows:
```yaml
rules:
  - match: regex
    response: response data
```

For example to handle two different paths simulating an HTTP server or SMTP commands:
```yaml
rules:
  # HTTP Requests
  - match: GET /path1
    response: |
              HTTP/1.0 200 OK
              Server: httpd/2.0
              x-frame-options: SAMEORIGIN
              x-xss-protection: 1; mode=block
              Date: Fri, 16 Apr 2021 14:30:32 GMT
              Content-Type: text/html
              Connection: close

              <HTML><HEAD><script>top.location.href='/Main_Login.asp';</script>
              </HEAD></HTML>
  - match: GET /path2
    response: |
              HTTP/1.0 404 OK
              Server: httpd/2.0
            
              <HTML><HEAD></HEAD><BODY>Not found</BODY></HTML>
  # SMTP Commands
  - match: "EHLO example.com"
    response: |
              250-localhost Nice to meet you, [127.0.0.1]
              250-PIPELINING
              250-8BITMIME
              250-SMTPUTF8
              250-AUTH LOGIN PLAIN
              250 STARTTLS
  - match: "MAIL FROM: <noreply@example.com>"
    response: 250 Accepted
  - match: "RCPT TO: <test@example.com>"
    response: 250 Accepted
```

# Thanks

simplehttpserver is made with 🖤 by the [projectdiscovery](https://projectdiscovery.io) team. Community contributions have made the project what it is. See the **[Thanks.md](https://github.com/projectdiscovery/simplehttpserver/blob/master/THANKS.md)** file for more details.
