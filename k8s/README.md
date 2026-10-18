# Lab 04 — Kubernetes (Kind)

Archivos del laboratorio de Kubernetes del curso Introducción a DevOps (semana del 21 de octubre).

1. `kind.yml` — configuración del clúster (usar tal cual): `kind create cluster --name clu-equiponn --config kind.yml`
2. `deployment.yml` — completar namespace e imagen de su sitio (Lab 03), luego `kubectl apply -f deployment.yml`
3. `service.yml` — completar selector; aplicar con `kubectl apply -f service.yml`

No olviden: abrir el puerto **TCP 30001** en el security group de la EC2 y probar `http://IP-PUBLICA:30001`.
Al terminar: `kind delete cluster --name clu-equiponn` y **Stop instance**.
