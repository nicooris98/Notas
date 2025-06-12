# ¿Qué es?
Es una herramienta que sirve para automatizar procesos

# Correr
```bash
docker volume create n8n_data

docker run -it --rm --name n8n -p 5678:5678 -v n8n_data:/home/node/.n8n -e WEBHOOK_URL=https://a489-2803-9800-b402-808e-9553-802f-371c-54c2.ngrok-free.app docker.n8n.io/n8nio/n8n
```