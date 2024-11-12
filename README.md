# HTTP Proxy Server

This project implements an HTTP Proxy Server capable of handling HTTP requests, HTTP CONNECT tunneling, and basic HTTP header transformations. The proxy server intercepts HTTP traffic between a browser and web servers, allowing inspection and redirection of data through the proxy.

## Features
- **HTTP Request Forwarding**: Intercepts HTTP requests from a browser, processes them, and forwards them to the target web server.
- **CONNECT Tunneling**: Supports HTTP CONNECT requests, establishing TCP connections between the client and server for protocols like HTTPS.
- **Header Modifications**:
  - Transforms `Connection: keep-alive` headers to `Connection: close`.
  - Modifies HTTP version to HTTP/1.0 to discourage persistent connections.
- **Traffic Logging**: Logs HTTP request methods and URIs received from the client, helping with debugging and traffic inspection.

## Requirements
- **Language**: Implemented using TCP sockets directly, avoiding high-level HTTP libraries.
- **Concurrency**: Supports concurrent client requests without one request delaying others, using threads or event-loop-based I/O.

## Configuration
To use the proxy, configure your browser to route all traffic through it. For example, in Firefox:
1. Go to **Settings > Network Settings > Manual Proxy Configuration**.
2. Set **HTTP Proxy** to the server's host and port.
3. Check **Use this proxy server for all protocols** (for testing all requests through the proxy).

## Usage

### Starting the Proxy Server
To start the proxy server:
```bash
./proxy_server <port_number>
```
- `<port_number>`: The port the proxy will listen on for client requests.

### Connecting a Client (Browser) to the Proxy
Once the proxy is running, configure your browser to connect to the proxy server on the specified port.

### Output
- The proxy logs each intercepted HTTP request with the following format:
  ```
  >>> <HTTP_METHOD> <URI>
  ```
  For example:
  ```
  >>> GET http://www.example.com/
  ```

## Implementation Details
1. **HTTP Request Handling**:
   - Intercepts browser requests, extracts the `Host` field, and forwards the request to the appropriate server.
   - Logs only the request’s HTTP method and URI.

2. **Header Modification**:
   - Replaces `Connection: keep-alive` headers with `Connection: close` to prevent persistent connections.
   - Downgrades HTTP version to 1.0 for simplicity.

3. **CONNECT Method for Tunneling**:
   - Handles HTTPS by establishing a TCP tunnel when a `CONNECT` request is received.
   - Forwards all data in both directions without modifying the content.

## Sample Output
Example output when browsing two URLs:

```plaintext
>>> GET http://www.cnn.com/
>>> GET http://www.google.com/
```

## Testing
- **Basic Functionality**: Point a browser to the proxy and observe HTTP requests and responses.
- **HTTPS Support**: Access an HTTPS site and verify the CONNECT method handling.
- **Concurrency**: Test multiple concurrent connections by accessing multiple sites or using multiple clients.

## Known Limitations
- The proxy does not cache content.
- Only HTTP and HTTPS (CONNECT) methods are supported.
