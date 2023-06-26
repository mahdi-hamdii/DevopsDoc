# Sticky Sessions and Socket IO
- Without sticky session, you will experience HTTP 400 error 

- **important note**:  if you are in a CORS situation (the front domain is different from the server domain) and session affinity is achieved with a cookie, you need to allow credentials.

server side
```Python
const io = require("socket.io")(httpServer, {
  cors: {
    origin: "https://front-domain.com",
    methods: ["GET", "POST"],
    credentials: true
  }
});
```
Client Side
```Python
const io = require("socket.io-client");
const socket = io("https://server-domain.com", {
  withCredentials: true
});

```
- Without it the cookie will not be sent by the browser and you will experience HTTP 400 "session ID unknown" responses
- In order to use sticky sessions, the client must support cookies. ALB support both duration-based cookies and application-based cookies. Sticky sessions are enabled at the target group level. 

- Determine how long your loadbalancer should consistently route the user's request to the same target. if your application has its own session cookie, then you can use your application based stickiness and the load balancer session cookie follows the duration specified by the app session cookie.

- **IMPORTANT**: If your application does not have its own session cookie, then you can use duration-based stickiness to generate a load balancer session cookie with a duration that you specify.

- Sticky sessions are not supported if cross zone load balancing is disabled.

-  For application-based cookies, cookie names have to be specified individually for each target group. however for duration based cookies, `AWSALB` is the only name used across all target groups.

## Duration Based Cookies
- With duration based cookies, you can enable sticky sessions only on one layer, because `AWSALB` is the only valid name available.

- Duration based cookie has its own expiry of 7 days which is non-configurable.

- With Cross-origin resource sharing CORS requests, some browsers require `sameSite=None; Secure` to enable stickiness. In this case, the load balancer generates a second stickiness cookie, `AWSALBCORS`, which inclues the same information as the original stickiness cookie plus the `sameSite` attribute. Clients receive both cookies.

# Application based stickiness:

- Gives you the flexbility to set your own criteria for client-target stickiness. When it is enabled, the load balancer routes the first request to a target within the target group based on the chosen algorithm.  The target is 