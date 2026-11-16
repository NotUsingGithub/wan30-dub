# wan30

D client for a [wan30.video](https://wan30.video/) account. It speaks the same
Streamable HTTP MCP endpoint as `@wan30/cli` 0.1.0: `POST {origin}/api/mcp/`.

Purchased wan30.video credits are required for generation. Sign-up, check-in
and gift credits alone do not qualify. `estimate` does not spend credits.
`generate` does not call the service unless `confirmSpend` is `true`, and that
flag is not sent in the request. Pass the same `requestId` on a retry.

This is not an official Alibaba, DashScope, or Tongyi client. It does not
depend on the Python package, which is not on PyPI yet.

```d
import std.json : parseJSON;
import wan30;

auto client = Wan30Client.fromEnvironment();
auto input = parseJSON(`{
    "modelId": "wan30-text-to-video",
    "prompt": "A paper boat on a rainy street"
}`);
auto quote = client.estimate(input);
auto task = client.generate(input, "paper-boat-request01", true);
```

`fromEnvironment` reads `WAN30_ACCESS_TOKEN` and optional `WAN30_ORIGIN` from
the process environment. It does not read `.env` files or a credential store.
Sign in with the existing Wan30 CLI, then export the token into the process.
You can also construct `Wan30Client` with a token and an injected HTTP
delegate. The default delegate uses `std.net.curl` and does not follow
redirects. `disconnect` posts to `{origin}/api/mcp/disconnect/` with only the
bearer token and an empty body. HTTP 401 from that call is
`REVOCATION_UNCONFIRMED`, not a fresh sign-in error. `close` only drops the
local session.

Models are `wan30-reference-to-video`, `wan30-text-to-video`, and
`wan30-image-to-video`. Omitted optional fields are not filled in by the client.

## DUB registry

This repository root is the DUB library. `dub.json` names the package `wan30`.
It has no version field and no dependencies. Version `0.1.0` comes from the
git tag `v0.1.0`. `dub build` and `dub test` run from this directory.

code.dlang.org registers a GitHub, GitLab, or Bitbucket project and reads
`dub.json` from the root of semver tags named `vX.Y.Z`. A branch name is not
the published version. The registered Wan27 package uses the dedicated
repository `NotUsingGithub/wan27-d`.

This import does not register the package. https://code.dlang.org/packages/wan30
is not a listing created here.

To register later:

1. Sign in to https://code.dlang.org with the GitHub account that owns
   `NotUsingGithub/wan30-dub`. Do not create a new account for this package.
2. Register owner `NotUsingGithub` and project `wan30-dub`. The package name
   read from `dub.json` is `wan30`.
3. The registry looks for tag `v0.1.0` about twice an hour.

Do not commit an access token.
