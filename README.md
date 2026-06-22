# gen-python

Python client + server generator for [webrpc](https://github.com/webrpc/webrpc).

Emits stdlib `@dataclass` models (no runtime dependency for the types), an
[`httpx`](https://www.python-httpx.org/) client, and a framework-agnostic WSGI server.

## Usage

```
webrpc-gen -schema=service.ridl -target=python -client -server -out=./api.py
```

Or pin this generator directly:

```
webrpc-gen -schema=service.ridl -target=github.com/webrpc/gen-python@VERSION -client -server -out=./api.py
```

## Options

| Option | Default | Description |
|---|---|---|
| `-client` | off | generate the `httpx` client |
| `-server` | off | generate the WSGI server + service `Protocol` |
| `-schemaHash=false` | on | omit the schema hash + version constants from the header |
| `-webrpcHeader=false` | on | omit the `Webrpc` version header from client requests |

## Output

- **Types** — `@dataclass` with generated `from_dict` / `to_dict`. Enums become `enum.Enum`; aliases become type assignments.
- **Errors** — a `WebRPCError` subclass per webrpc and schema error, plus a code registry for client-side reconstruction.
- **Client** — one `<Service>Client`; methods are `snake_case`, return typed values, and raise the matching error on a non-2xx response.
- **Server** — a `<Service>` `typing.Protocol` to implement, plus a `<Service>Server` WSGI app that routes, deserializes inputs, and serializes outputs.

Requires Python 3.9+. The client requires `httpx`.

## Limitations (draft)

- Enums serialize as their member-name string (matches gen-dart). Integer-valued enum wire interop with other generators is untested.
- Alias-typed struct fields deserialize via the alias name; only scalar aliases are exercised in `_examples`.
- No streaming support.
