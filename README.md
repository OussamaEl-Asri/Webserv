*This project has been created as part of the 42 curriculum by saharchi, oel-asri, relamine.*

---

# Webserv

> This is when you finally understand why a URL starts with HTTP.

---

## Table of Contents

- [Webserv](#webserv)
  - [Table of Contents](#table-of-contents)
  - [Description](#description)
  - [Team \& Responsibilities](#team--responsibilities)
  - [Features](#features)
    - [Mandatory](#mandatory)
    - [Bonus](#bonus)
  - [Configuration File](#configuration-file)
    - [Supported Directives](#supported-directives)
  - [Instructions](#instructions)
    - [Requirements](#requirements)
    - [Compilation](#compilation)
    - [Execution](#execution)
  - [Usage Examples](#usage-examples)
    - [Serving a Static Page](#serving-a-static-page)
    - [Uploading a File (POST)](#uploading-a-file-post)
    - [Deleting a File](#deleting-a-file)
    - [Running a Python CGI Script](#running-a-python-cgi-script)
    - [Running a PHP CGI Script](#running-a-php-cgi-script)
  - [Resources](#resources)
    - [HTTP \& Web Server References](#http--web-server-references)
    - [AI Usage](#ai-usage)
  - [License](#license)

---

## Description

**Webserv** is a fully functional HTTP/1.1-compliant web server written in C++98, developed as part of the 42 school curriculum. It is capable of serving static websites, handling file uploads, executing CGI scripts, and managing multiple simultaneous client connections through a non-blocking I/O architecture driven by a single `poll()` call.

The server is configured via a custom configuration file inspired by NGINX's `server` block syntax, allowing full control over ports, routes, error pages, upload directories, and CGI execution.

---

## Team & Responsibilities

| Login | Responsibility |
|---|---|
| **saharchi** | HTTP request parsing — reading raw data, tokenizing and interpreting HTTP request lines, headers, and body |
| **oel-asri** | HTTP response generation (GET, POST, DELETE methods) and Python CGI script |
| **relamine** | Core server logic — event loop, `poll()`-based I/O multiplexing, connection lifecycle, and bonus features (cookies, session management, PHP CGI) |

---

## Features

### Mandatory
- Non-blocking architecture using a single `poll()` for all I/O operations (sockets + listen)
- Support for **GET**, **POST**, and **DELETE** HTTP methods
- Accurate HTTP response status codes with default error pages
- Serving fully static websites
- Client file uploads
- CGI execution based on file extension (`.py` — Python)
- Multiple server blocks listening on different ports
- Per-route configuration: allowed methods, redirections, root directory, directory listing, default index file, upload directory
- Compatible with standard web browsers

### Bonus
- Cookie support and session management
- Multiple CGI types: **Python** (`.py`) and **PHP** (`.php`)

---

## Configuration File

The configuration file is inspired by NGINX's `server` block syntax. It is passed as a command-line argument (or loaded from a default path if none is provided).

### Supported Directives

```nginx
server {
    listen       8080;
    server_name  localhost;
    error_page   404 /errors/404.html;
    client_max_body_size 10M;

    location / {
        root        /var/www/html;
        index       index.html;
        methods     GET POST DELETE;
        autoindex   off;
    }

    location /upload {
        methods     POST;
        upload_dir  /var/www/uploads;
    }

    location /cgi-bin {
        methods     GET POST;
        cgi         .py  /usr/bin/python3;
        cgi         .php /usr/bin/php-cgi;
    }

    location /old-path {
        return      301 /new-path;
    }
}
```

Multiple `server` blocks can be defined to serve different content on different ports.

---

## Instructions

### Requirements

- A Unix-based OS (Linux or macOS)
- `g++` with C++98 support (`-std=c++98`)
- `make`
- Python 3 (for `.py` CGI)
- `php-cgi` (for `.php` CGI bonus)

### Compilation

Clone the repository and compile using the provided Makefile:

```bash
git clone https://github.com/OussamaEl-Asri/Webserv.git
cd webserv
make
```

Available Makefile rules: `all`, `clean`, `fclean`, `re`

### Execution

```bash
./webserv [configuration file]
```

If no configuration file is provided, the server will attempt to load from the default path.

**Example:**

```bash
./webserv config/default.conf
```

The server will start listening on the port(s) defined in the configuration file. Open your browser and navigate to:

```
http://localhost:<port>
```

---

## Usage Examples

### Serving a Static Page

Configure a `location /` block pointing to your HTML root directory. Navigate to `http://localhost:<port>/` in your browser.

### Uploading a File (POST)

```bash
curl -X POST http://localhost:8080/upload \
     -F "file=@/path/to/your/file.txt"
```

The uploaded file will be stored in the directory specified by `upload_dir`.

### Deleting a File

```bash
curl -X DELETE http://localhost:8080/upload/file.txt
```

### Running a Python CGI Script

Place a `.py` script in your configured `cgi-bin` directory and make it executable:

```bash
chmod +x /var/www/cgi-bin/hello.py
```

Then access it via:

```
http://localhost:8080/cgi-bin/hello.py
```

### Running a PHP CGI Script

Same as above with a `.php` file, provided `php-cgi` is installed and configured in the `cgi` directive.

---

## Resources

### HTTP & Web Server References

- [RFC 7230 — HTTP/1.1 Message Syntax and Routing](https://datatracker.ietf.org/doc/html/rfc7230)
- [RFC 7231 — HTTP/1.1 Semantics and Content](https://datatracker.ietf.org/doc/html/rfc7231)
- [RFC 3875 — The CGI/1.1 Specification](https://datatracker.ietf.org/doc/html/rfc3875)
- [MDN Web Docs — HTTP Overview](https://developer.mozilla.org/en-US/docs/Web/HTTP/Overview)
- [NGINX Documentation](https://nginx.org/en/docs/)
- [Beej's Guide to Network Programming](https://beej.us/guide/bgnet/)

### AI Usage

AI tools (primarily Claude by Anthropic) were used during the development of this project for the following tasks:

- **Debugging assistance** — Helping interpret error messages and suggesting areas to investigate when encountering unexpected behaviour in socket handling and CGI output parsing.
- **Conceptual explanations** — Clarifying parts of the HTTP RFC (chunked transfer encoding, CGI environment variables) to support implementation decisions.
- **Code review support** — Reviewing logic flow in the response generation module and CGI execution to spot potential edge cases.

All AI-generated suggestions were reviewed, tested, and validated by the team before integration. No code was blindly copied — every implementation was understood and owned by the relevant team member.

---

## License

This project is licensed under the MIT License.

```
MIT License

Copyright (c) 2025 saharchi, oel-asri, relamine

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```