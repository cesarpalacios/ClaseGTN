# 🐳 Lab 04 — Docker Swarm · Gestión de Tecnologías en la Nube

Lab en **4 partes** sobre la misma instancia EC2 y la carpeta `ClaseGTN`.
Guía basada en las diapositivas de la sesión (Orquestación de Contenedores).

> ⚠️ **Importante:** el `Dockerfile` ahora incluye `COPY BASICA/ /var/www/html/`.
> En Swarm **no hay volume mounts**: el sitio va horneado dentro de la imagen que construyen.

---

## 🐙 Parte 1 · Iniciar el clúster Swarm (25 min)

Enciendan la instancia y conéctense con la nueva IP.

```bash
cd ~/ClaseGTN
docker compose down            # bajar el Compose de la semana 07 (libera el puerto)
docker build -t clasegtn:1.0 . # la imagen lleva el sitio dentro
docker swarm init
docker service create --name sitio \
  --replicas 3 -p PUERTO:80 clasegtn:1.0   # PUERTO = el mismo de la semana 07
docker service ls
docker service ps sitio
```

Recarguen el navegador: el sitio del taller debe aparecer servido por las 3 réplicas.

---

## 🔥 Parte 2 · Escalar y romper a propósito (25 min)

```bash
# escalar de 3 a 5 réplicas
docker service scale sitio=5
docker ps

# borrar un contenedor a mano
docker rm -f $(docker ps -q -f name=sitio | head -1)

# esperar 10 segundos y revisar
docker service ps sitio
```

📸 **Captura 1:** la salida de `docker service ps sitio` con la réplica reemplazada.

> El estado deseado era 5; al borrar uno, el actual bajó a 4 — Swarm crea una réplica
> nueva sin que nadie se lo pida. Recarguen el navegador: el sitio nunca se cayó.

---

## 🔄 Parte 3 · Actualizar y deshacer (15 min)

```bash
nano index.html                # cambien un título visible y guarden
docker build -t clasegtn:1.1 .
docker service update --image clasegtn:1.1 \
  --update-parallelism 1 --update-delay 10s sitio
docker service ps sitio        # recarga: unas responden 1.0 y otras 1.1
docker service rollback sitio  # rollback ordenado a la versión anterior
```

💡 Esto, automatizado desde GitHub, es lo que hará Jenkins.

---

## ☁️ Parte 4 · El mismo contenedor en Fargate (25 min · consola AWS)

1. **ECS → Clusters → Create**, con infraestructura Fargate.
2. **Task definitions → Create**: imagen `public.ecr.aws/nginx/nginx:alpine`, puerto 80, 0.25 vCPU y 0.5 GB, rol LabRole.
3. **Run task** en el clúster, con IP pública activada y grupo de seguridad con el puerto 80 abierto.
4. Abran la IP pública de la tarea en el navegador.

🧹 **Al terminar:** detengan la tarea y borren el clúster de ECS. `Stop` a la instancia.
