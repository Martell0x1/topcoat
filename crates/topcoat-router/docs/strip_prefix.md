Rewriting the request path by removing a leading prefix.

A mounted service that looks up files or nested routes from the request URI often expects paths relative to its own root. [`StripPrefix`] is a [`Layer`](crate::Layer) that removes a leading URI prefix before inner layers and the route run, so a service mounted at `/res/{*path}` can see `/hello.txt` instead of `/res/hello.txt`.

Register it with [`RouterBuilder::layer`](crate::RouterBuilder::layer). The layer wraps the matched routes under the same prefix; scope it to a different path with [`at`](StripPrefix::at).

```rust
use topcoat::{Cx, router::{Router, StripPrefix, request::uri, route}};

#[route(GET "/api/{*path}")]
async fn path(cx: &Cx) -> String {
    uri(cx).path().to_owned()
}

let router = Router::builder()
    .route(path)
    .layer(StripPrefix::new("/api"))
    .build();
```

The original URI stays on the outer context for logging and error rendering. Query strings are kept. A request whose path does not start with the prefix is left unchanged.
