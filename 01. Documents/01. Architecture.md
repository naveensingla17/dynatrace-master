# Architecture

## Lab setup

```text
┌──────────────────────────┐        ┌──────────────────────────┐        ┌────────────────────┐
│ EC2: petclinic host      │        │ EC2: ActiveGate host     │        │ Dynatrace SaaS     │
│                          │        │                          │        │                    │
│  Spring PetClinic :8080  │──────▶ │  Environment ActiveGate  │──────▶ │  Tenant            │
│  OneAgent (fullstack)    │        │  group: ag01             │        │                    │
└──────────────────────────┘        └──────────────────────────┘        └────────────────────┘
              └──────────── Network Zone: petclinic ────────────┘
```

## Concept: Environment ActiveGate group

An ActiveGate group gives **reliability**: if one ActiveGate goes down, another one in the group is still available.

```text
               ┌──────────────────────────────┐
               │   ActiveGate Group: AG1      │
               │                              │
               │   ┌────────┐    ┌────────┐   │
               │   │  AG-A  │    │  AG-B  │   │
               │   └────────┘    └────────┘   │
               └──────────────┬───────────────┘
                              │
                              ▼
                      Database plugin
              (runs on any available AG in the group)
```
