# IoT Systems Design Course / Curso de Diseño de Sistemas IoT

**ESP32-C6 • OpenThread • ISO/IEC 30141:2024**

---

## 🇬🇧 English

### [📚 Start the Course →](en/)

**8 hands-on labs** building a Thread mesh IoT system, aligned with **ISO/IEC 30141:2024** standard.

**What you'll build:**
- Thread mesh sensor network
- CoAP application protocol
- Secure border router gateway
- Complete dashboard integration

**Technologies:** ESP32-C6, OpenThread, CoAP, CBOR, DTLS

---

### Quick Navigation (English)

| Document | Description |
|----------|-------------|
| [📖 Course Overview](en/README.md) | Start here - complete course introduction |
| [⚙️ Setup Guide](en/0_0_setup.md) | Install ESP-IDF and configure environment |
| [🌐 Minimal IoT: HTTP](en/0_2_Minimal_IoT_Implementation_http.md) | Lab 0 - Build a minimal IoT system with HTTP |
| [📡 Minimal IoT: MQTT](en/0_3_Minimal_IoT_Implementation_mqtt.md) | Lab 1 - Rebuild with MQTT publish/subscribe |
| [🎯 Project Scenario](en/1_project_scenario.md) | GreenField Technologies - your role as IoT engineer |
| [🏗️ ISO Architecture](en/2_iso_architecture.md) | ISO/IEC 30141:2024 reference architecture guide |
| [📝 Templates](en/3_deliverables_template.md) | DDR and ADR templates for deliverables |
| [🔍 Quick References](en/references.md) | CoAP, Thread, ESP-IDF cheat sheets |
| [🧪 Labs 1-8](en/labs/) | Weekly lab guides with role-based context |
| [📐 Detailed Guides](en/labs/sops/) | Step-by-step implementation references (SOPs) |

---

## 🇪🇸 Español

> 🚧 **Próximamente.** El contenido del curso está completo en inglés (`en/`). La traducción al español (`es/`) está planeada y se publicará más adelante.

**El curso cubrirá** — 8 laboratorios prácticos construyendo un sistema IoT con redes mesh Thread, alineado con **ISO/IEC 30141:2024**: red de sensores mesh, protocolo CoAP, gateway border router seguro e integración con dashboard.

**Tecnologías:** ESP32-C6, OpenThread, CoAP, CBOR, DTLS

---

## 📊 Course Structure / Estructura del Curso

```
Week/Semana 1-2:  Physical/Link Layer       →  SCD Domain
Week/Semana 3-4:  Mesh Network & CoAP       →  SCD + ASD Domains
Week/Semana 5-6:  Border Router & Security  →  SCD + OMD + RAID
Week/Semana 7-8:  Dashboard & Integration   →  All 6 ISO Domains
```

### ISO/IEC 30141:2024 Six Domains / Seis Dominios

| Domain / Dominio | Abbr. | Focus / Enfoque |
|------------------|-------|-----------------|
| Physical Entity | PED | Sensed/controlled objects / Objetos sensados/controlados |
| Sensing & Controlling | SCD | Sensors, actuators, gateways / Sensores, actuadores, gateways |
| Application & Service | ASD | Core functions, services / Funciones core, servicios |
| Operation & Management | OMD | Device lifecycle, monitoring / Ciclo de vida, monitoreo |
| User | UD | Human/digital users, HMI / Usuarios humanos/digitales, HMI |
| Resource Access & Interchange | RAID | Authentication, API / Autenticación, API |

---

## 🛠️ Tools / Herramientas

**Common tools for both languages / Herramientas comunes para ambos idiomas:**

| Tool | Description |
|------|-------------|
| [tools/coap_client.py](tools/coap_client.py) | CoAP client for testing |
| [tools/dashboard_http.py](tools/dashboard_http.py) | HTTP dashboard server (Lab 0) |
| [tools/dashboard_mqtt.py](tools/dashboard_mqtt.py) | MQTT dashboard server (Lab 1) |
| [tools/ota_server.py](tools/ota_server.py) | OTA update server |
| [tools/test_e2e.py](tools/test_e2e.py) | End-to-end system tests |

---

## 📖 Reference / Referencia

**ISO/IEC 30141:2024** — Internet of Things (IoT) Reference Architecture. The course is
aligned to this standard. It is copyrighted and is **not** redistributed in this repo;
obtain it from ISO: [iso.org → ISO/IEC 30141](https://www.iso.org/search.html?q=ISO%2FIEC%2030141).

---

## 🎓 What You'll Learn / Lo Que Aprenderás

✅ Design ISO-compliant IoT systems / Diseñar sistemas IoT compatibles con ISO
✅ Implement Thread mesh networks / Implementar redes mesh Thread
✅ Use CoAP protocol for constrained devices / Usar protocolo CoAP para dispositivos restringidos
✅ Secure IoT communications / Asegurar comunicaciones IoT
✅ Build complete sensor-to-dashboard systems / Construir sistemas completos sensor-a-dashboard
✅ Document architectural decisions / Documentar decisiones arquitectónicas

---

## 🚀 Getting Started / Comenzar

### English Course
1. Go to [en/](en/)
2. Read [en/README.md](en/README.md)
3. Follow [en/0_0_setup.md](en/0_0_setup.md)
4. Start [en/labs/lab1.md](en/labs/lab1.md)

### Curso en Español
🚧 Próximamente — la versión en español está planeada. Por ahora, sigue el curso en inglés ([en/](en/)).

---

## 📂 Repository Structure / Estructura del Repositorio

```
4201327-IoT_Systems_Design_Labs/
│
├── README.md                    (This file / Este archivo)
│                                (ISO/IEC 30141:2024 — external, see References)
│
├── en/                          🇬🇧 English Course
│   ├── README.md                   Course overview
│   ├── 0_setup.md                  Environment setup
│   ├── 0_1_networking_recap.md     Networking fundamentals
│   ├── 0_2_Minimal_IoT_...http.md  Lab 0: Minimal IoT (HTTP)
│   ├── 0_3_Minimal_IoT_...mqtt.md  Lab 1: Minimal IoT (MQTT)
│   ├── 1_project_scenario.md       GreenField project
│   ├── 2_iso_architecture.md       ISO/IEC 30141 guide
│   ├── 3_deliverables_template.md  DDR & ADR templates
│   ├── references.md               Quick references
│   └── labs/                       8 lab guides
│       ├── lab1.md - lab8.md
│       └── sops/                   Detailed guides
│
├── http_simple/                 ESP-IDF project for Lab 0 (HTTP) — planned
├── mqtt_simple/                 ESP-IDF project for Lab 1 (MQTT) — planned
│
├── es/                          🇪🇸 Curso en Español (planned)
│   └── ...                          Spanish translation — coming soon
│
└── tools/                       🛠️ Utilities (language-agnostic)
    ├── coap_client.py
    ├── dashboard_http.py         HTTP dashboard (Lab 0)
    ├── dashboard_mqtt.py         MQTT dashboard (Lab 1)
    ├── ota_server.py
    └── test_e2e.py
```

---

**Ready to build professional IoT systems?**
**¿Listo para construir sistemas IoT profesionales?**

→ [English Course](en/) | Curso en Español (próximamente)
