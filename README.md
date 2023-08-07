## DevOps for Kong experiment

This repo contains my experiment to do devops workflows to sync Kong config.

> In the examples, all expression path are **EXACT** matches except the catch all route.

My current plan is to rewrite routes in [expression](https://docs.konghq.com/gateway/latest/reference/router-expressions-language/#main) and use GHA [Setup Deck](https://github.com/marketplace/actions/setup-deck) or Nix to run deck.

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