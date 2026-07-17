# 🔀 Lab 7 — Configuring PPP Encapsulation

![Cisco](https://img.shields.io/badge/Cisco-CCNA-blue?style=for-the-badge&logo=cisco&logoColor=white)
![PacketTracer](https://img.shields.io/badge/Packet%20Tracer-8.x-orange?style=for-the-badge&logo=cisco)
![Status](https://img.shields.io/badge/Status-✅%20Completed-brightgreen?style=for-the-badge)
![Lab](https://img.shields.io/badge/101%20Labs-Lab%207-purple?style=for-the-badge)
![PPP](https://img.shields.io/badge/PPP-Encapsulation-red?style=for-the-badge)

---

## 📋 Description

Septième lab du livre **101 Labs Cisco CCNA (200-301)**.
L'objectif est de configurer et vérifier l'encapsulation
**PPP** sur des interfaces Serial Cisco, et d'observer
les différentes phases de négociation PPP via le debug.

### Objectifs
- ✅ Configurer les **hostnames** sur R1 et R2
- ✅ Activer les interfaces **Serial Se0/0/0**
- ✅ Configurer le **clock rate 512 Kbps** sur R2
- ✅ Configurer l'encapsulation **PPP**
- ✅ Observer les phases de négociation PPP via **debug**
- ✅ Tester la **connectivité** entre R1 et R2

---

## 📖 Théorie PPP

### C'est quoi PPP ?
```
→ Point-to-Point Protocol
→ Protocole couche 2 WAN standard
→ Compatible toutes marques
→ Remplace HDLC (propriétaire Cisco)
→ Supporte : PAP, CHAP, compression
→ Négociation en phases : LCP puis NCP
```

### Phases de négociation PPP
```
Phase 1 : ESTABLISHING
→ LCP (Link Control Protocol) négocie
→ Paramètres du lien établis

Phase 2 : AUTHENTICATING (si configuré)
→ PAP ou CHAP authentification

Phase 3 : UP
→ Lien PPP opérationnel ✅
```

---

## 🖥️ Équipements

| Équipement | Modèle | Nom | Rôle |
|-----------|--------|-----|------|
| 🔀 Routeur | Cisco 2911 | R1 | DTE |
| 🔀 Routeur | Cisco 2911 | R2 | DCE |

---

## 🗺️ Topologie

```
[R1]──Se0/0/0──────────────Se0/0/0──[R2]
(DTE) 10.0.254.1        10.0.254.2  (DCE)
              10.0.254.0/28
              Encapsulation : PPP
              Clock rate : 512 Kbps
```

<img width="1920" height="1080" alt="lab7Topo" src="https://github.com/user-attachments/assets/628e58f9-2783-4a3b-89c6-be1264583fb6" />


---

## 📊 Plan d'adressage

| Interface | IP | Réseau | Rôle | Routeur |
|-----------|-----|--------|------|---------|
| Se0/0/0 | 10.0.254.1 | 10.0.254.0/28 | DTE | R1 |
| Se0/0/0 | 10.0.254.2 | 10.0.254.0/28 | DCE | R2 |

---

## ⚙️ Configuration complète

### 🔧 Task 1 — Hostnames

```cisco
enable
configure terminal
hostname R1
end
```

```cisco
enable
configure terminal
hostname R2
end
```

---

### 🔧 Task 2 — Serial + Clock Rate

```cisco
! R1 (DTE)
enable
configure terminal
interface serial 0/0/0
no shutdown
exit
end
```

```cisco
! R2 (DCE)
enable
configure terminal
interface serial 0/0/0
clock rate 512000
no shutdown
exit
end
```

```cisco
! Vérifier DCE/DTE
R2# show controllers serial 0/0/0
R1# show controllers serial 0/0/0
```

---

### 🔧 Task 3 — PPP + IP

```cisco
! R1
enable
configure terminal
interface serial 0/0/0
encapsulation ppp
ip address 10.0.254.1 255.255.255.240
no shutdown
exit
end
write
```

```cisco
! R2
enable
configure terminal
interface serial 0/0/0
encapsulation ppp
ip address 10.0.254.2 255.255.255.240
clock rate 512000
no shutdown
exit
end
write
```

```cisco
! Vérifier encapsulation PPP
R1# show interfaces serial 0/0/0
```

**Résultat attendu :**
```
Serial0/0/0 is up, line protocol is up
  Encapsulation PPP ✅
```

---

### 🔧 Task 4 — Debug PPP

```cisco
R1# debug ppp negotiation
```

```cisco
! Faire tomber et relever l'interface
R1(config)# interface serial 0/0/0
R1(config-if)# shutdown
R1(config-if)# no shutdown
```

**Phases visibles :**
```
PPP: Phase is ESTABLISHING ← LCP négocie
PPP: Phase is UP           ← PPP établi ✅
```

```cisco
! Désactiver debug
R1# no debug all
```

---

## 🧪 Tests

```cisco
R1# ping 10.0.254.2  ✅
R2# ping 10.0.254.1  ✅
```

---

## 💡 Points clés

| 🔑 Commande | 📖 Rôle |
|-------------|---------|
| `encapsulation ppp` | Active PPP sur l'interface |
| `show interfaces serial 0/0/0` | Voir encapsulation PPP |
| `debug ppp negotiation` | Voir phases PPP |
| `no debug all` | Désactiver tous les debugs |
| `clock rate 512000` | 512 Kbps sur DCE |

---

## 📊 Comparatif HDLC vs PPP

| | HDLC (Lab 6) | PPP (Lab 7) |
|---|---|---|
| **Type** | Propriétaire Cisco | Standard ouvert |
| **Par défaut** | ✅ Oui | ❌ Non |
| **Commande** | (rien) | `encapsulation ppp` |
| **Auth** | ❌ | ✅ PAP/CHAP |
| **Debug** | `debug serial interface` | `debug ppp negotiation` |
| **Clock rate** | 256 Kbps | 512 Kbps |

---

## 👨‍💻 Auteur

**Urbain Sedami Landjidé**
🎓 Étudiant en 2ème année — Licence Professionnelle
📡 Réseaux Informatique Mobilité Sécurité (RMS)
🏫 Cisco Networking Academy
📍 Cotonou, Bénin 🇧🇯

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connecter-blue?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/urbain-sedami-landjide-9b49043a8/)

---

## 📄 Licence

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
