# ◈ nullclaw-neural-interface

> Interface graphique bleu néon futuriste pour [nullclaw](https://github.com/nullclaw/nullclaw) — un assistant IA autonome ultra-léger tourné 100% en local sur Linux.

![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)
![Platform: Linux](https://img.shields.io/badge/platform-Linux%20Debian%2FDevuan-informational)
![Python: 3](https://img.shields.io/badge/python-3-green.svg)

---

## 📸 Aperçu

![Nullclaw Neural Interface](screenshot.png)
![Nullclaw Neural Interface](André.png)


Interface terminal cyberpunk bleu néon avec :
- Séquence de boot animée
- Zone de chat avec historique
- Barre de saisie avec glow effect
- Bouton `+` pour envoyer des fichiers dans `drops/`
- Démarrage automatique d'Ollama et nullclaw

---

## 🧩 Prérequis

- Linux Debian / Devuan / Peppermint OS (x86_64)
- Python 3 + python3-tk + python3-pexpect
- [Ollama](https://ollama.com) installé
- nullclaw compilé (voir installation ci-dessous)
- Au moins **2 Go d'espace libre** et **4 Go de RAM**

---

## ⚙️ Installation complète — de A à Z

### Étape 1 — Installer Ollama

```bash
curl -fsSL https://ollama.com/install.sh | sh
```

Vérifie :

```bash
ollama --version
```

---

### Étape 2 — Télécharger un modèle IA

Ce projet utilise **kimi-k2.5:cloud** par défaut — c'est une passerelle gratuite vers un modèle cloud puissant via Ollama, sans clé API :

```bash
ollama pull kimi-k2.5:cloud
```

#### Autres modèles compatibles

Tu peux utiliser n'importe quel modèle Ollama. Voici quelques suggestions selon ta RAM :

| RAM disponible | Modèle recommandé | Commande |
|----------------|-------------------|----------|
| < 2 Go | smollm:1.7b (local, rapide) | `ollama pull smollm:1.7b` |
| 2-4 Go | gemma2:2b (local, qualité) | `ollama pull gemma2:2b` |
| 2-4 Go | granite3.3:2b (local, raisonnement) | `ollama pull granite3.3:2b` |
| 4+ Go | kimi-k2.5:cloud (cloud gratuit, puissant) | `ollama pull kimi-k2.5:cloud` |
| 4+ Go | kimi-k2-thinking:cloud (cloud, réfléchi) | `ollama pull kimi-k2-thinking:cloud` |

> ⚠️ Les modèles `:cloud` nécessitent une connexion internet. Les modèles locaux fonctionnent 100% hors-ligne.

---

### Étape 3 — Installer Zig 0.15.2

nullclaw est écrit en Zig. La façon la plus simple d'installer Zig sur Debian/Devuan est via pip :

```bash
# Crée un environnement virtuel Python
python3 -m venv ~/venv-zig
source ~/venv-zig/bin/activate

# Installe Zig
pip install ziglang==0.15.2

# Vérifie
python -m ziglang version
# Doit afficher : 0.15.2
```

> ⚠️ Zig 0.15.2 n'est pas disponible en téléchargement direct sur ziglang.org — la méthode pip est la seule qui fonctionne actuellement.

---

### Étape 4 — Cloner et compiler nullclaw

```bash
# Clone nullclaw (choisis ton dossier)
git clone https://github.com/nullclaw/nullclaw.git ~/nullclaw/app
cd ~/nullclaw/app

# Active l'environnement Zig si pas déjà fait
source ~/venv-zig/bin/activate

# Compile (2-5 minutes)
python -m ziglang build -Doptimize=ReleaseSmall

# Vérifie
./zig-out/bin/nullclaw --version
```

---

### Étape 5 — Configurer nullclaw (onboard)

```bash
cd ~/nullclaw/app
./zig-out/bin/nullclaw onboard --interactive
```

Réponses recommandées dans le wizard :

| Étape | Choix |
|-------|-------|
| Provider | `ollama` |
| API Key | Entrée (vide — pas besoin avec Ollama) |
| Modèle | `kimi-k2.5:cloud` ou ton modèle choisi |
| Memory backend | `1` — SQLite (recommandé) |
| Tunnel | `1` — none |
| Autonomy level | `1` — supervised |
| Configure channels | `n` |
| Workspace path | `/home/TON_USER/nullclaw/workspace` |

---

### Étape 6 — Personnaliser l'identité (optionnel)

```bash
nano ~/nullclaw/workspace/IDENTITY.md
```

Exemple :

```markdown
# Identité

Tu es mon assistant personnel IA.
Tu parles français par défaut.
Tu es direct, efficace et honnête.
Tu m'aides avec le code, les tâches quotidiennes et les questions techniques.
Tu te souviens de mes préférences au fil du temps.
```

---

### Étape 7 — Installer nullclaw-neural-interface

Télécharge le `.deb` depuis les [Releases](../../releases) puis :

```bash
sudo dpkg -i nullclaw-ui_1.3.0_all.deb
```

Si des dépendances manquent :

```bash
sudo apt install python3 python3-tk python3-pexpect
sudo dpkg -i nullclaw-ui_1.3.0_all.deb
```

---

### Étape 8 — Adapter les chemins

Par défaut, l'interface cherche nullclaw dans :

```
/media/pep00/Back Up/nullclaw/app/zig-out/bin/nullclaw
```

Si ton installation est ailleurs, modifie le fichier :

```bash
sudo nano /usr/share/nullclaw/nullclaw_app.py
```

Trouve et modifie ces deux lignes :

```python
NULLCLAW_DIR = "/ton/chemin/vers/nullclaw/app"
NULLCLAW_BIN = "/ton/chemin/vers/nullclaw/app/zig-out/bin/nullclaw"
DROPS_DIR    = "/ton/chemin/vers/nullclaw/workspace/drops"
```

---

## 🚀 Lancement

```bash
nullclaw-ui
```

Ou depuis le menu Applications de ton bureau.

---

## 📁 Dossier drops

L'interface inclut un bouton `+` pour envoyer des fichiers à nullclaw. Les fichiers sont copiés dans :

```
~/nullclaw/workspace/drops/
```

nullclaw peut ensuite les lire, les analyser ou les exécuter (selon ta config de sécurité).

---

## 🔒 Sécurité

La config de sécurité se trouve dans `~/.nullclaw/config.json`. Par défaut :

- **supervised** — nullclaw demande confirmation avant d'agir
- **workspace_only** — accès limité au dossier workspace
- **allowed_commands** — seulement `python3`, `ls`, `cat`, `pwd`

Pour autoriser l'exécution de scripts Python dans `drops/` :

```json
"allowed_commands": ["python3", "ls", "cat", "pwd"],
"allowed_paths": ["/ton/chemin/nullclaw/workspace"],
"require_approval_for_medium_risk": false
```

---

## 🖥️ Lanceur bureau (optionnel)

Pour lancer nullclaw-ui d'un double-clic :

```bash
nano ~/Desktop/nullclaw.desktop
```

```ini
[Desktop Entry]
Name=Nullclaw
Comment=Assistant IA local
Exec=nullclaw-ui
Icon=utilities-terminal
Terminal=false
Type=Application
Categories=Development;Utility;
```

```bash
chmod +x ~/Desktop/nullclaw.desktop
```

---

## 📦 Structure du projet

```
nullclaw-neural-interface/
├── nullclaw-ui_1.3.0_all.deb   # Package installable
├── LICENSE                      # MIT
└── README.md                    # Ce fichier
```

---

## 🤝 Crédits

- [nullclaw](https://github.com/nullclaw/nullclaw) — le moteur IA (MIT)
- [Ollama](https://ollama.com) — le runtime de modèles locaux
- Interface graphique par **Richer Rail**

---

## 📄 Licence

MIT — voir [LICENSE](LICENSE)

---

> **nullclaw-neural-interface** — Null overhead. Null compromise. Deploy anywhere.
