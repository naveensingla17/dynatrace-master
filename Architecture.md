  ## Architecture

```text
┌──────────────────────────┐        ┌──────────────────────────┐        ┌────────────────────┐
│ EC2: petclinic host      │        │ EC2: ActiveGate host     │        │ Dynatrace SaaS     │
│                          │        │                          │        │                    │
│  Spring PetClinic :8080  │──────▶ │  Environment ActiveGate  │──────▶ │  Tenant            │
│  OneAgent (fullstack)    │        │  group: ag01             │        │                    │
└──────────────────────────┘        └──────────────────────────┘        └────────────────────┘
              └──────────── Network Zone: petclinic ────────────┘

```
  <!-- concept
  Environment active gate group - if one down other available, reliability
            AG1
        AG-A    AG-B
    Database plugin
   -->
