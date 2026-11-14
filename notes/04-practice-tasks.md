# Dynatrace Practice Tasks

## Tasks

- [x] Set up Environment ActiveGate and Network Zone — [Lab 1](../lab01-petclinic-ec2-apm-rum/01-install-activegate.md)
- [x] Set up the PetClinic application for monitoring — [Lab 1](../lab01-petclinic-ec2-apm-rum/02-install-petclinic.md)
- [x] Set up OneAgent for application and OS monitoring — [Lab 1](../lab01-petclinic-ec2-apm-rum/03-install-oneagent.md)
- [ ] Create a host group after OneAgent installation
- [ ] Modify the Network Zone of OneAgent after installation
- [ ] By default log monitoring is enabled, practice to disable while oneagent installation and observe
- [ ] Create custom service if not auto detect
- [ ] Create alert from distributed traces
- [ ] Set conversion goals
## Answer :  go to custom service --> create service --> restart application --> restart agent

## Later
- [ ] Workflows


## Notes

> **Whenever you modify the OneAgent configuration, the application must be restarted.**

## References

- [Spring PetClinic](https://github.com/spring-projects/spring-petclinic)
- [Dynatrace ActiveGate documentation](https://docs.dynatrace.com/docs/ingest-from/dynatrace-activegate)
- [Dynatrace OneAgent documentation](https://docs.dynatrace.com/docs/ingest-from/dynatrace-oneagent)

- [Easy trade](https://github.com/Dynatrace/easytrade)