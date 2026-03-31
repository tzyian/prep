Compare REST, gRPC, and GraphQL 

- REST: resource‑oriented, simple, cache‑friendly via HTTP; verbose for chatty calls.
	- Use OpenAPI / Swagger to generate the contract!!
- GraphQL: client‑driven queries reduce over/under‑fetch; server complexity and caching harder.
	- moving complexity from frontend to backend

- gRPC: contract‑first, HTTP/2 + protobuf, bi‑di streaming;
	- useful between backend services
	- web does not support full gRPC spec, hence not used for BE-FE comms


| Criteria        | REST                      | GraphQL                     | gRPC                                           |
| --------------- | ------------------------- | --------------------------- | ---------------------------------------------- |
| **Data Format** | JSON / XML                | JSON (via GraphQL query)    | Binary (Protocol Buffers)                      |
| **Transport**   | HTTP/1.1 or 2             | HTTP                        | HTTP/2<br><br>there's grpc over http/3 plans   |
| **Performance** | Moderate (~250ms latency) | Good (~180ms latency)       | Excellent (~25ms latency)                      |
| **Throughput**  | ~20,000 req/sec           | ~15,000 req/sec             | ~50,000 req/sec                                |
| **Flexibility** | Fixed endpoints           | Highly flexible queries     | Fixed service contracts defined in proto files |
| **Caching**     | Native HTTP caching       | Complex, needs custom setup | Limited native caching                         |

