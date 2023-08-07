## DevOps for Kong - Experiment

This repo contains my experiment to do devops workflows to sync Kong config.

> In the examples, all expression path are **EXACT** matches except the catch all route.

I have rewrite routes to [expression](https://docs.konghq.com/gateway/latest/reference/router-expressions-language/#main) and use GHA [Setup Deck](https://github.com/marketplace/actions/setup-deck) and Nix to run deck.

I know it might be over kill to use nix to run decK since it is a very simple process. However, the reason to include nix is to showcase the possibility as nix provides so many packages on the fly. Users can use other packages like jq, yq, curl etc. when they need to.

Next step, I might upload a flake in this repo so you can build and use any version of decK you like. Currently `nix run` use the latest version on nixpkgs unstable channel.

```text
                        ┌───────────┐
                        │    CA     │
                        └─────┬─────┘
                            Cert                            ┌──────┐
                        ┌─────▼─────┐    ┌────────────┐  ┌──► SVC1 │
                        │           │    │            ├──┘  └──────┘
                        │           │    │ ┌────────┐ │     ┌──────┐
┌──────────┐            │           │    │ │ proxy  │ ├─────► SVC2 │
│ API      │            │           │    │ └────────┘ │     └──────┘
│ Consumer ├──Requests──►    ALB    ├────►            │     ┌──────┐
└──────────┘            │           │    │ ┌────────┐ ├─────► SVC3 │
                        │           │    │ │  Admin │ │     └──────┘
                        │           │    │ │   API  │ │     ┌──────┐
                        │           │    │ └────────┘ ├─────► SVC4 │
                        │           │    │       ┌────┤     └──────┘
                        │           │    │       │Kong├──┐  ┌──────┐
                        └─────▲─────┘    └───────┴────┘  └──► SVC5 │
                    ▲─────────┴──────────────◄──────────┐   └──────┘
┌───────────────────┼───────────────────────────────────┼──────────┐
│ ┌──────────┐ ┌────┴─────┐ ┌──────────┐ ┌──────────┐ ┌─┴────────┐ │
│ │ Kong     │ │   Sync   │ │   diff   │ │ validate │ │   Ping   │ │
│ │ Configs  │ │          ◄─┤          ◄─┤          ◄─┤          │ │
│ └─────┬────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘ └────┬─────┘ │
│       │           │            │            │     ┌──────┘       │
│       │   ┌───────┴────────────┴────────────┴───┐ │              │
│       └───►                decK                 ├─┘  ┌───────────┤
│           └─────────────────────────────────────┘    │  GitHub   │
│                                                      │  Actions  │
└──────────────────────────────────────────────────────┴───────────┘
```