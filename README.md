**nuke absolutely everything including the default Docker networks (`bridge`, `host`, `none`)** — it’s worth noting:

👉 **Default Docker networks cannot be deleted.**
They’re part of Docker’s core runtime and will automatically regenerate if removed (though Docker won’t let you remove them via `docker network rm`).

But — to attempt deleting **all networks**, including anything possible, we can still run a command for all network IDs including those defaults (they’ll just fail silently if not allowed).

---

## 📝 Full Docker Reset Script (including network removal attempts)

```bash
#!/bin/bash

echo "🛑 Stopping all running containers..."
docker stop $(docker ps -aq) 2>/dev/null

echo "🗑️ Removing all containers..."
docker rm $(docker ps -aq) 2>/dev/null

echo "🗑️ Removing all images..."
docker rmi -f $(docker images -aq) 2>/dev/null

echo "🗑️ Removing all volumes..."
docker volume rm $(docker volume ls -q) 2>/dev/null

echo "🔗 Removing all Docker networks (including default ones — will ignore errors)..."
docker network rm $(docker network ls -q) 2>/dev/null

echo "🧹 Pruning build cache..."
docker builder prune -af

echo "✅ Docker environment fully cleaned."
echo "📦 Remaining volumes:"
docker volume ls

echo "🔗 Remaining networks:"
docker network ls

echo "🐳 Docker reset complete."
```

---

## 📌 Usage:

```bash
chmod +x docker-full-reset.sh
./docker-full-reset.sh
```

---

## 📌 Expected Behavior:

* Stops all containers
* Removes all containers
* Removes all images
* Removes all volumes
* **Attempts to remove all networks (including defaults)** — errors ignored
* Prunes build cache
* Displays remaining volumes and networks

---

## 🔍 Important:

**Even if you try removing `bridge`, `host`, and `none` — Docker will either:**

* Regenerate them instantly
* Or refuse removal, which is okay because the `2>/dev/null` ensures no errors interrupt the script
