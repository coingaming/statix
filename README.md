# Statix

[![CI Status](https://github.com/lexmag/statix/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/lexmag/statix/actions/workflows/ci.yml)
[![Hex Version](https://img.shields.io/hexpm/v/statix.svg "Hex Version")](https://hex.pm/packages/statix)

Statix is an Elixir client for StatsD-compatible servers.
It is focused on speed without sacrificing simplicity, completeness, or correctness.

What makes Statix the fastest library around:

  * direct sending to socket <sup>[[1](#direct-sending)]</sup>
  * caching of the UDP packet header
  * connection pooling to distribute the metric sending
  * diligent usage of [IO lists](http://jlouisramblings.blogspot.se/2013/07/problematic-traits-in-erlang.html)

<sup><a name="direct-sending"></a>[1]</sup> In contrast with process-based clients, Statix has lower memory consumption and higher throughput – Statix v1.0.0 does about __876640__ counter increments per flush:

![Statix](https://www.dropbox.com/s/uijh5i8qgzmd11a/statix-v1.0.0.png?raw=1)

<details>
  <summary>It is possible to measure that yourself.</summary>

  ```elixir
  for _ <- 1..10_000 do
    Task.start(fn ->
      for _ <- 1..10_000 do
        StatixSample.increment("sample", 1)
      end
    end)
  end
  ```

  Make sure you have StatsD server running to get more realistic results.

</details>

See [the documentation](https://hexdocs.pm/statix) for detailed usage information.

## Why Forked Repo?

**Statix Compatibility**: [Statix](https://github.com/lexmag/statix), does not yet support OTP-26. It is using `Port.command` instead of `:gen_udp.send`, as the former was considered more performant for OTP 25 and older versions. However, due to OTP updates occurring at the networking layer, this approach no longer works. Now, `gen_udp.send` has been optimized, rendering the previous workaround obsolete.

### References
- **Open PR**: There is an open pull request in the Statix repository addressing this issue: [Pull Request #72](https://github.com/lexmag/statix/pull/72). This PR aims to update the codebase to support OTP-26 and utilize the optimized `gen_udp.send` function.

- **Erlang Discussion**: For further insights, you can refer to the Erlang discussion about the breaking change in `Port.command` for OTP-26: [Issue #7130](https://github.com/erlang/otp/issues/7130).

By utilizing the forked repository of [Statix](https://github.com/lexmag/statix), we can maintain compatibility with OTP-26. As Statix evolves and potentially adds support for our current version, we aim to transition back to the main Statix repository. Alternatively, we'll explore alternatives should the need arise in the future.

## Installation

Add Statix as a dependency to your `mix.exs` file:

```elixir
defp deps() do
  [{:statix, ">= 0.0.0"}]
end
```

Then run `mix deps.get` in your shell to fetch the dependencies.

## License

This software is licensed under [the ISC license](LICENSE).
