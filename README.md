# ☁️ DevOps 10 – Cloud Deployment mit Azure & GitHub Actions

## 👥 Zusammenarbeit

### 📄 Person und Repository

|                          |                                                                                              |
|--------------------------|----------------------------------------------------------------------------------------------|
| Bearbeiter               | Alessio Pugliese                                                                             |
| Repository               | [DevOps-10-Cloud-Deployment](https://github.com/pugliale/DevOps-10-Cloud-Deployment)        |

---

## 💻 Verwendete Systeme & Tools

| Tool / Plattform        | Beschreibung                                                                                  |
|-------------------------|-----------------------------------------------------------------------------------------------|
| **Microsoft Azure**     | Cloud-Plattform für Web-Apps, Container, Storage, Datenbanken etc.                           |
| **GitHub Actions**      | CI/CD-Plattform für automatisierte Workflows aus GitHub heraus                               |
| **Docker Hub**          | Container Registry für die Veröffentlichung von Images                                        |
| **Node.js / Express**   | Beispielanwendung zur Demonstration von Deployment-Vorgängen                                 |
| **Azure CLI**           | Kommandozeilenwerkzeug zur Steuerung von Azure-Diensten                                      |

---

## 📖 Theoretischer Hintergrund

### ☁️ Was ist Deployment?

Deployment bezeichnet den Prozess der **Bereitstellung einer Anwendung** auf Servern oder Cloud-Plattformen. Ziel ist es, dass Software nach dem Build automatisch produktionsbereit ausgeliefert wird.

#### Deployment-Typen:

- **Lokal**: Installation auf einzelnen Rechnern
- **Server-/Cloud-basiert**: Zentrale Bereitstellung über Webserver oder Dienste wie Azure
- **Automatisiert**: Durchführung über CI/CD-Plattformen (z. B. GitHub Actions, Jenkins)

---

### 🔄 Continuous Delivery

Continuous Delivery ist eine Weiterentwicklung von Continuous Integration – dabei wird die Software nach jedem erfolgreichen Build so vorbereitet, dass sie **jederzeit produktiv einsetzbar** ist.

- Änderungen werden automatisiert und wiederholt bereitgestellt
- Das System ist stets einsatzfähig
- Ideal im Zusammenspiel mit Cloud-Plattformen wie Azure oder AWS

---

## ☁️ Azure als Cloud-Plattform

**Microsoft Azure** bietet Cloud-Dienste für:

- Computing (z. B. App Services, VMs)
- Datenbanken & Speicher
- Container & Kubernetes
- DevOps-Funktionen wie automatische Deployments

Azure unterstützt mehrere Programmiersprachen:  
.NET, Java, Node.js, Python, PHP, u. a.

### 💡 PaaS vs. IaaS vs. SaaS

- **IaaS (Infrastructure as a Service)**: Bereitstellung von Infrastruktur (VMs, Netzwerke)
- **PaaS (Platform as a Service)**: Bereitstellung von Laufzeitumgebung für Apps
- **SaaS (Software as a Service)**: Nutzung fertiger Software über Web

Für unser Beispiel wurde **PaaS (Azure App Service)** verwendet.

---

## 🔧 Azure Deployment Methoden

### 📦 Zip-Deployment mit Azure CLI

1. Login: `az login`
2. Ressourcengruppe anlegen:
   ```bash
   az group create --name devops-rg --location switzerlandnorth
   ```
3. Web-App bereitstellen:
   ```bash
   az webapp up --name devops-alessio --plan devops-plan \
     --resource-group devops-rg --runtime NODE:18-lts --sku F1
   ```

🔹 Wichtig:
- `PORT`-Variable aus `process.env.PORT` verwenden
- Vorher bestehende Apps ggf. löschen
- `node_modules` vor Deployment entfernen

---

### 🐳 Docker Deployment auf Azure

1. Container lokal bauen & testen
2. Image auf Docker Hub pushen
3. Azure App Service Container erstellen:

```bash
az appservice plan create --name devops-container-plan \
  --resource-group devops-container --sku F1 --is-linux --location switzerlandnorth

az webapp create --resource-group devops-container \
  --plan devops-container-plan --name devops-container \
  --deployment-container-image-name mosazhaw/node-web-app:latest
```

---

## ⚙️ Automatisierung mit GitHub Actions

### 🔁 Alternative zu Jenkins

GitHub Actions ermöglichen, **Build-, Test- und Deploymentprozesse direkt aus dem GitHub-Repo** zu starten.  

Ein Workflow besteht aus:
- **Events** (z. B. Push auf `release`)
- **Jobs**
- **Steps**
- **Secrets** (Token, Passwörter)

### 🧱 Struktur

```yaml
name: Build and Deploy

on:
  push:
    branches:
      - release

jobs:
  docker:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - name: Build Docker Image
        run: docker build -t my-image:${{ github.sha }} .
```

### 🔐 GitHub Secrets (Beispiele)

| Name                      | Verwendet für                         |
|---------------------------|---------------------------------------|
| `DOCKERHUB_USERNAME`      | Login auf Docker Hub                  |
| `DOCKERHUB_TOKEN`         | Zugriffstoken für Push                |
| `AZURE_WEBAPP_NAME`       | Ziel-Name der App                     |
| `AZURE_WEBAPP_PUBLISH_PROFILE` | Deployment auf Azure über GitHub |

---

### 🧠 Vorteile GitHub Actions

- Komplett integriert in GitHub
- Keine externe CI/CD-Plattform nötig
- Gute Automatisierung
- Sauberes Workflow-Tracking

---

## 🔧 Praktische Umsetzung 
### ☁️ Setup in Azure & Zip Deployment

---

### 📁 Azure Projekt & CLI-Login

Zuerst wurde ein neues Projekt angelegt und die Azure CLI installiert. Nach erfolgreichem Login über `az login` wurde die eigene Subscription geprüft.

<img src="Images/Bild1.png" width="600">
<img src="Images/Bild2.png" width="600">

---

### ⚠️ Probleme mit Subscription

Beim ersten Versuch mit „Azure for Students Starters“ schlug das Anlegen einer Ressourcengruppe fehl. Nach intensiver Recherche stellte sich heraus, dass diese Version nicht ausreicht.

Die Subscription wurde auf „Azure for Students“ aktualisiert, und danach funktionierte auch das Anlegen der Gruppe via CLI:

```bash
az group create --name devops-rg --location switzerlandnorth
```

<img src="Images/Bild3.png" width="600">
<img src="Images/Bild4.png" width="600">

---

### 🟢 WebApp erfolgreich deployed (Zip-Methode)

Die einfache Node.js-App („Hello World“) wurde erfolgreich gepusht und auf Azure deployed. Die Anwendung war nach wenigen Sekunden über die bereitgestellte URL abrufbar.

```bash
az webapp up --name devops-alessio \
  --resource-group devops-rg --runtime NODE:18-lts --sku F1
```

<img src="Images/Bild5.png" width="600">
<img src="Images/Bild6.png" width="600">

---

### 🔍 Sichtbarkeit im Azure-Portal

Im Azure-Portal war die App unter „App Services“ sichtbar und korrekt verlinkt.

<img src="Images/Bild7.png" width="600">

---

## 🔧 Praktische Umsetzung – Teil 2/3  
### 🐳 Azure Deployment via Docker Image

---

### 🐳 Dockerfile & Image erstellen

Im zweiten Schritt wurde die bestehende App containerisiert.  
Ein eigenes `Dockerfile` wurde erstellt und getestet.

```dockerfile
FROM node:18
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["npm", "start"]
```

Anschliessend wurde das Image gebaut:

```bash
docker build -t devopscloudapp .
```

<img src="Images/Bild8.png" width="600">
<img src="Images/Bild9.png" width="600">

---

### ✅ Container lokal getestet

Die Anwendung wurde lokal im Container gestartet und über `localhost:3000` getestet.

```bash
docker run -p 3000:3000 devopscloudapp
```

<img src="Images/Bild10.png" width="600">
<img src="Images/Bild11.png" width="600">

---

### 🔐 Login bei Docker Hub & Push vorbereiten

Es wurde ein Access-Token in Docker Hub generiert und in der CLI verwendet:

```bash
docker login -u <username> -p <access_token>
```

Anschliessend wurde das Image getaggt und gepusht:

```bash
docker tag devopscloudapp pugliale/devopscloudapp
docker push pugliale/devopscloudapp
```

<img src="Images/Bild12.png" width="600">
<img src="Images/Bild13.png" width="600">
<img src="Images/Bild14.png" width="600">
<img src="Images/Bild15.png" width="600">

---

### ☁️ Azure Web App for Container

#### Schritt 1 – Plan erstellen:

```bash
az appservice plan create --name devops-container-plan \
  --resource-group devops-container --sku F1 --is-linux --location switzerlandnorth
```

#### Schritt 2 – Container-Web-App erstellen:

```bash
az webapp create --resource-group devops-container \
  --plan devops-container-plan \
  --name devopscontaineralessio \
  --deployment-container-image-name pugliale/devopscloudapp
```

<img src="Images/Bild16.png" width="600">
<img src="Images/Bild17.png" width="600">

---

### 🔄 Container Deployment & Kontrolle

Die App wurde über Docker Container auf Azure ausgerollt.  
Einige Minuten nach Bereitstellung war die Seite aktiv und unter der Azure-URL erreichbar.

<img src="Images/Bild18.png" width="600">
<img src="Images/Bild19.png" width="600">

---

### 🔧 Diagnose und Überprüfung

- Deployment Center kontrolliert
- Logs analysiert
- Umgebungsvariablen geprüft

<img src="Images/Bild20.png" width="600">
<img src="Images/Bild21.png" width="600">
<img src="Images/Bild22.png" width="600">

---

### 🧪 Test durch Entfernung des Ports

Als Fehlertest wurde der `EXPOSE 3000`-Eintrag entfernt → das Deployment schlug daraufhin fehl. Dies bestätigte die Notwendigkeit der Portangabe.

<img src="Images/Bild23.png" width="600">
<img src="Images/Bild24.png" width="600">

---

### 🔁 Update durch neuen Push

Nach Korrektur und neuem Docker Push wurde das Image in Azure automatisch neu gezogen und die App war wieder erreichbar.

<img src="Images/Bild25.png" width="600">
<img src="Images/Bild26.png" width="600">
<img src="Images/Bild27.png" width="600">
<img src="Images/Bild28.png" width="600">
<img src="Images/Bild29.png" width="600">

### 🔁 GitHub Actions für automatisiertes Deployment

---

### ⚙️ Ziel: Deployment nach Push auf den `release`-Branch

Die Anwendung sollte nach jedem Commit auf `release` automatisch in Azure deployed werden. Dazu wurde ein **GitHub Actions Workflow** in `.github/workflows/deploy.yml` erstellt.

---

### 🧾 Azure Publish-Profil generieren

Im Azure-Portal wurde das **Publish-Profil** der Web-App heruntergeladen.  
Es enthält alle notwendigen Zugangsdaten zum Deployment.

<img src="Images/Bild30.png" width="600">

---

### 🔐 GitHub Secret erstellen

Das Publish-Profil wurde in GitHub unter **Settings → Secrets → Actions** als neues Secret `AZURE_WEBAPP_PUBLISH_PROFILE` gespeichert.

<img src="Images/Bild31.png" width="600">
<img src="Images/Bild32.png" width="600">

---

### 📝 GitHub Actions Workflow

Der folgende Workflow wurde konfiguriert:

```yaml
name: Deploy to Azure

on:
  push:
    branches:
      - release

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout Code
        uses: actions/checkout@v2

      - name: Setup Node.js
        uses: actions/setup-node@v2
        with:
          node-version: '18'

      - name: Install Dependencies
        run: npm install

      - name: Build
        run: npm run build --if-present

      - name: Deploy to Azure WebApp
        uses: azure/webapps-deploy@v2
        with:
          app-name: 'devopscloudactions'
          publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
          package: .
```

<img src="Images/Bild33.png" width="600">
<img src="Images/Bild34.png" width="600">

---

### 🚀 Push auf `release` → Automatisches Deployment

Nach dem Push wurde automatisch ein Deployment ausgelöst.  
Der Status war live im Reiter „Actions“ sichtbar.

<img src="Images/Bild35.png" width="600">
<img src="Images/Bild36.png" width="600">

---

### 🌐 Azure App über GitHub Actions live geschaltet

Die Anwendung wurde erfolgreich über den GitHub-Workflow deployed.  
Ein manuelles Eingreifen war nicht mehr nötig.

<img src="Images/Bild37.png" width="600">

## 🧠 Erkenntnisse & Fazit

### 📌 Fachlich gelernt

- Ich habe eine vollständige **Cloud Deployment Pipeline mit Azure** aufgebaut – sowohl via CLI (Zip-Methode) als auch über **Docker Container**.
- Das Einrichten und Verwalten von Ressourcen (App Service, Resource Groups, Container Plans) über die **Azure CLI** war ein zentrales Lernziel.
- Durch das Pushen von Docker Images auf Docker Hub und die Nutzung in Azure konnte ich einen vollständigen Container Lifecycle durchlaufen.
- Das Setup von **GitHub Actions** als CI/CD-Alternative zu Jenkins zeigte, wie tief automatisierte Prozesse direkt in GitHub integriert werden können.
- Die Arbeit mit **Secrets und Publish-Profilen** in GitHub und Azure war für mich neu, aber sehr praxisnah und relevant für den professionellen Betrieb.

---

### 💬 Persönliche Reflexion

Diese letzte Aufgabe war ein Highlight: Ich konnte das bisher Gelernte kombinieren und auf eine reale Cloud-Plattform anwenden. Besonders der Moment, als das Deployment über **GitHub Actions ohne mein Zutun funktionierte**, war ein Aha-Erlebnis.

Ich verstehe nun die **Komplexität und den Nutzen automatisierter Cloud Deployments**, vor allem wenn verschiedene Systeme wie Azure, Docker, GitHub und lokale Tools zusammenwirken.

> ☁️ *„DevOps endet nicht beim Build – es wird erst durch Deployment in die Cloud vollständig.“*

---

📅 *Frühjahrssemester 2025 – ZHAW School of Management and Law*


