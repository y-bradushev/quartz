
##### Definition:
$\quad$ gRPC (acronym for Google Remote Procedure Calls) is a cross-platform high-performance remote procedure call (RPC) framework. gRPC was initially created by Google, but is open source and is used in many organizations. Use cases range from microservices to the "last mile" of computing (mobile, web, and Internet of Things). gRPC uses HTTP/2 for transport, Protocol Buffers as the interface description language, and provides features such as authentication, bidirectional streaming and flow control, blocking or nonblocking bindings, and cancellation and timeouts. It generates cross-platform client and server bindings for many languages. Most common usage scenarios include connecting services in a microservices style architecture, or connecting mobile device clients to backend services.

##### Layman's explanation and some details:
$\quad$ Sort of like http that uses http2 to make super fast requests without the overheads of using JSON. JSON encoding and decoding is pretty inefficient so gRPC uses ProtoBuffers which are binary and type safe. JSON is text. An int is four bytes, and (as an example) `{ "answer": 42 }` is 16 chars so 16 bytes in UTF-8, and it takes time to turn back into an int (that's "decoding"). gRPC transmits binary, so instead of `{ "answer": 42 }` (16 bytes) it would transmit something like `0x2A` in binary (1 byte) and both sides would understand that the first byte would be 'answer', thanks to the extra step of precompiling for your language from the '.protobuf' files that gRPC asks you to create. This is the _encoding efficiency_ that gRPC offers. Compare [BSON](https://en.wikipedia.org/wiki/BSON) or [MessagePack](https://msgpack.org/index.html), which are both binary encodings of JSON-like documents.

Assume here that with gRPC:
- The client and server have to know the schema beforehand, and  
- gRPC would send the 4-btyte int as 4 bytes, no conversion to and from text required as it is with JSON.
So it's better suited to when: 
- The contract is not changing, you can tightly couple the sender and receiver, and
- performance is very important.
If you don't have those 2 conditions, the use of gRPC is probably not worthwhile.