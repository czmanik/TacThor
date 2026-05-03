# TacThor

**TacThor** je offline-first velitelský systém pro airsoft / LARP operace, navržený pro prostředí se slabým nebo žádným signálem.

TacThor = Tactical + Thor  
(neoficiálně: „Traktor“ 😄)

## Hlavní cíl

Zlepšit velení v terénu:

- lepší přehled o jednotkách
- rychlejší rozhodování
- offline fungování
- méně chaosu v komunikaci

## Klíčové vlastnosti

- offline-first architektura
- event-based sync
- ultra low bandwidth režim
- GPS tracking s úsporou baterie
- Big Picture (automatická interpretace situace)
- QR systém pro herní logiku
- práce s nepřítelem (intel, směry, odhady)

## Architektura

Mobile (Flutter)  
→ API (Node/NestJS)  
→ PostgreSQL + PostGIS  
→ Web dashboard (Command / GM)

## Stav

🚧 MVP ve vývoji  
🎯 první nasazení: Event Zero

## Filosofie

TacThor není mapa.  
Je to nástroj pro rozhodování. 

--

# TacThor

**TacThor** is an open-source tactical coordination tool designed for airsoft, milsim, and LARP operations.

Built by players, for players — with a focus on reliability, clarity, and real-world usability in the field.

---

## 🎯 Purpose

TacThor aims to replace fragmented communication tools like:
- WhatsApp
- Radios (partially)
- Paper maps
- Disconnected tracking apps

with a **semi-offline, tactical coordination system** that works even in limited connectivity environments.

---

## ⚙️ Key Features (planned / in progress)

- 📍 **Real-time / delayed position tracking**
  - Adaptive update intervals (battery-friendly)
  - Offline-first behavior

- 🗺️ **Tactical map interface**
  - NATO APP-6 based symbology (internally)
  - Simplified visual mode for accessibility

- 🧩 **Unit & role system**
  - Infantry, recon, AT, sniper, spec ops
  - Roles: commander, medic, radio operator, etc.

- 🔄 **Offline / semi-offline support**
  - Data sync when connection is available
  - Minimal dependency on constant network

- 📡 **Low battery impact design**
  - Configurable update intervals
  - Smart location polling

- 🔗 **(Planned) Device-to-device communication**
  - Bluetooth / mesh concepts for local coordination

---

## 🧠 Design Philosophy

TacThor is built on three core principles:

### 1. **Clarity over complexity**
Information must be understandable in under 1 second.

### 2. **Realism where it matters**
Uses NATO APP-6 symbology internally, but allows simplified display for non-trained users.

### 3. **Field reliability**
Must work in:
- poor signal conditions
- long-duration events (24h+)
- high-stress environments

---

## 🪖 Target Use Cases

- Airsoft events
- Milsim operations
- LARP scenarios
- Tactical training (non-military)

---

## 🎨 Branding & Identity

TacThor combines:
- **Tactical mindset (Tac)**
- **Force & impact (Thor)**
- **Symbolism of resilience and endurance**

The project also uses a custom visual system alongside NATO standards to improve usability in-game.

---

## ⚠️ Disclaimer

TacThor is an open-source community project.

This project:
- is **not affiliated with any military organization**
- is **not intended for real combat use**
- is **not affiliated with ThorTac or any commercial brand**

The name is used in good faith within the community context.

---

## 🤝 Community & Contribution

This project is built for the community.

Contributions are welcome:
- ideas
- testing feedback
- UI/UX improvements
- code contributions

---

## 🚧 Project Status

Early development / concept phase.

Core areas currently in progress:
- application architecture
- map & symbol system
- data model
- UI/UX concepts

---

## 🛠️ Tech Stack (planned)

- Flutter (cross-platform mobile app)
- Local-first data handling
- Map integration (TBD)
- SVG-based tactical icon system

---

## 📦 Project Structure (planned)

---

---

## 🔮 Vision

TacThor aims to become:

> A standard tool for tactical coordination in the airsoft and milsim community.

Not just an app — but a **shared platform and language** for players in the field.

---

## 📬 Contact

If you're part of the community and want to collaborate, contribute, or test:

→ open an issue or discussion on GitHub

---

## 🧾 License

TBD (recommended: MIT or Apache 2.0)


