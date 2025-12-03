# 🚦 DEVS Traffic Light Simulator

**Modélisation et simulation d'un feu tricolore utilisant le formalisme DEVS (Discrete Event System Specification) avec MATLAB/Simulink**

![DEVS](https://img.shields.io/badge/DEVS-Formal%20Specification-blue)
![MATLAB](https://img.shields.io/badge/MATLAB-R2025a+-orange)
![Simulink](https://img.shields.io/badge/Simulink-Stateflow-red)

---

## 📋 Description

Ce projet implémente un simulateur de feu tricolore basé sur le formalisme DEVS de Bernard P. Zeigler, entièrement développé en **MATLAB/Simulink avec Stateflow**. Il comprend deux modèles pédagogiques :

1. **Feu tricolore simple** : Automate autonome cyclique (Vert → Jaune → Rouge → Vert...)
2. **Feu tricolore avec passage piéton** : Système réactif avec bouton-poussoir permettant aux piétons de demander le passage

Le projet illustre les concepts fondamentaux du formalisme DEVS :
- ✅ Modèles atomiques avec transitions internes et externes
- ✅ Fonction d'avance temporelle (time advance `ta`)
- ✅ Gestion des événements discrets (internes et externes)
- ✅ Représentation vectorielle des états
- ✅ Implémentation via Stateflow

---

## 🎯 Objectifs pédagogiques

- Comprendre le formalisme DEVS : $M = \langle X, S, Y, \delta_{int}, \delta_{ext}, \lambda, ta \rangle$
- Modéliser des systèmes à événements discrets
- Implémenter des automates temporisés avec Stateflow
- Simuler le comportement de systèmes réels dans Simulink
- Maîtriser la correspondance DEVS ↔ Stateflow

---

## 📦 Structure du projet

```
DEVS-Traffic-Light/
├── README.md                              # Ce fichier
├── docs/
│   ├── Présentation_DEVS_Traffic_Light.pdf                   # Présentation Beamer du projet
│   └── DEVS_Formalism_Slides.html        # Slides de cours complets (33 slides)
├── models/
│   ├── traffic_light_simple.slx          # Modèle Simulink feu simple
│   └── traffic_light_with_pedestrian.slx      # Modèle Simulink feu + piéton
└── references/        #Ressources
```

---

## 🔧 Modèles implémentés

### 1️⃣ Feu Tricolore Simple

**Spécification formelle DEVS :**

$$M_1 = \langle S, Y, \delta_{int}, \lambda, ta \rangle$$

| Composant DEVS | Spécification | Implémentation Stateflow |
|----------------|---------------|--------------------------|
| **États S** | $\{V, J, R\}$ | 3 états Stateflow |
| **Entrées X** | $\varnothing$ | Aucun port d'entrée |
| **Sorties Y** | $\{signal\_vert, signal\_jaune, signal\_rouge\}$ | 3 signaux booléens |
| **ta(V)** | 5 secondes | `[after(5, sec)]` |
| **ta(J)** | 2 secondes | `[after(2, sec)]` |
| **ta(R)** | 3 secondes | `[after(3, sec)]` |
| **δ_int** | V→J, J→R, R→V | Transitions temporisées |
| **λ(s)** | Sorties par état | Actions `entry` |

**Diagramme Stateflow :**
```
┌─────────┐  after(5,sec)  ┌─────────┐  after(2,sec)  ┌─────────┐
│  VERT   │ ─────────────> │  JAUNE  │ ─────────────> │  ROUGE  │
│  ta=5s  │                │  ta=2s  │                │  ta=3s  │
└─────────┘                └─────────┘                └────┬────┘
     ↑                                                     │
     └─────────────────── after(3,sec) ────────────────────┘
```

### 2️⃣ Feu Tricolore avec Passage Piéton

**Spécification formelle DEVS :**

$$M_2 = \langle X, S, Y, \delta_{int}, \delta_{ext}, \lambda, ta \rangle$$

| Composant DEVS | Spécification | Implémentation Stateflow |
|----------------|---------------|--------------------------|
| **États S** | $\{S_0, S_1, S_2\}$ | 3 états avec vecteur binaire |
| **Entrées X** | $\{p\}$ (pushbutton) | Signal d'entrée `p` |
| **Sorties Y** | 5 signaux (G, Y, R, Walk, Stop) | 5 signaux booléens |
| **δ_ext(p=1)** | Transition vers S1 | `[p == 1]` |
| **δ_int(S1)** | S1 → S2 | `[after(3, sec)]` |
| **δ_int(S2)** | S2 → S0 | `[after(6, sec)]` |

**États détaillés :**

| État | G | Y | R | Walk | Stop | Description |
|------|---|---|---|------|------|-------------|
| S0 | 1 | 0 | 0 | 0 | 1 | Vert + Stop (attente) |
| S1 | 0 | 1 | 0 | 1 | 0 | Jaune + Walk (transition) |
| S2 | 0 | 0 | 1 | 1 | 0 | Rouge + Walk (passage) |

---

## 🚀 Prérequis et Installation

### Prérequis

- **MATLAB R2025a** ou version ultérieure
- **Simulink** (inclus avec MATLAB)
- **Stateflow** (toolbox requise)

### Installation

```bash
# Cloner le repository
git clone https://github.com/[username]/DEVS-Traffic-Light.git
cd DEVS-Traffic-Light
```

### Vérifier les toolboxes MATLAB

```matlab
% Vérifier que Stateflow est installé
ver stateflow
```

---

## ▶️ Exécution des simulations

### Méthode 1 : Interface graphique

1. Ouvrir MATLAB
2. Naviguer vers le dossier du projet
3. Double-cliquer sur `models/traffic_light_simple.slx` ou `models/traffic_light_with_pedestrian.slx`
4. Appuyer sur le bouton **Run** (▶️)

---

## 🏗️ Création des modèles Stateflow

### Étape 1 : Créer le modèle Simulink

1. Ouvrir MATLAB
2. `Simulink` → `New Model`
3. Ajouter un bloc **Stateflow Chart** depuis la bibliothèque

### Étape 2 : Configurer les états (Feu simple)

Double-cliquer sur le bloc Stateflow et créer :

```matlab
% État VERT
state Vert {
    entry:
        signal_vert  = 1;
        signal_jaune = 0;
        signal_rouge = 0;
}

% État JAUNE
state Jaune {
    entry:
        signal_vert  = 0;
        signal_jaune = 1;
        signal_rouge = 0;
}

% État ROUGE
state Rouge {
    entry:
        signal_vert  = 0;
        signal_jaune = 0;
        signal_rouge = 1;
}
```

### Étape 3 : Ajouter les transitions temporisées

- Transition Vert → Jaune : `[after(5, sec)]`
- Transition Jaune → Rouge : `[after(2, sec)]`
- Transition Rouge → Vert : `[after(3, sec)]`

### Étape 4 : Définir les signaux de sortie

Dans le **Model Explorer** :
1. Ajouter 3 **Data** de type Output : `signal_vert`, `signal_jaune`, `signal_rouge`
2. Type : `boolean` ou `double`
3. Scope : Output to Simulink

---

## 📊 Correspondance DEVS ↔ Stateflow

| Concept DEVS | Notation formelle | Implémentation Stateflow |
|--------------|-------------------|--------------------------|
| Ensemble d'états | $S$ | États dans le diagramme |
| Fonction d'avance temporelle | $ta : S \to [0, +\infty]$ | `[after(duration, sec)]` |
| Transition interne | $\delta_{int} : S \to S$ | Transition temporisée |
| Transition externe | $\delta_{ext} : Q \times X \to S$ | Transition conditionnée `[input == value]` |
| Fonction de sortie | $\lambda : S \to Y$ | Actions `entry` des états |
| État total | $Q = \{(s, e)\}$ | Historique implicite Stateflow |
| Temps écoulé | $e$ | Géré automatiquement par `after()` |
| Temps restant | $\sigma = ta(s) - e$ | Calculé par Stateflow |

---

## 📈 Visualisation des résultats

### Ajouter des scopes dans Simulink

1. Ajouter un bloc **Scope** depuis la bibliothèque Simulink
2. Connecter les sorties du bloc Stateflow au Scope
3. Exécuter la simulation et observer les chronogrammes

---

## 📚 Références bibliographiques

1. **Zeigler, B. P.** (1976). *Theory of Modelling and Simulation*. Wiley.

2. **Vangheluwe, H.** (2001). *The Discrete Event System Specification (DEVS) formalism*. Technical Report, McGill University.

3. **Vangheluwe, H.** (2018). *Discrete Event System Specification Modeling and Simulation*. Winter Simulation Conference (WSC).

4. **Chebbi, E.** (2019). *De la modélisation formelle à la simulation à événements discrets*. Thèse de doctorat, Université du Littoral Côte d'Opale.

5. **MathWorks** (2024). *Stateflow User's Guide*. Documentation MATLAB.

---

## 🤝 Contribution

Les contributions sont bienvenues ! Pour contribuer :

1. **Fork** le projet
2. Créer une **branche** (`git checkout -b feature/NewFeature`)
3. **Committer** (`git commit -m 'Add NewFeature'`)
4. **Push** (`git push origin feature/NewFeature`)
5. Créer une **Pull Request**

---

## 👤 Auteur

**Issam GHERIB**  
M2 Automatique & Commande de Systèmes (ACS)  
Université d'Aix-Marseille / UFC Sciences  
Décembre 2025

---

## ❓ FAQ

**Q: Pourquoi utiliser Stateflow pour DEVS ?**  
A: Stateflow offre une implémentation naturelle des automates temporisés. Les transitions `[after(..., sec)]` correspondent directement à la fonction $ta(s)$ du DEVS.

**Q: Puis-je simuler des modèles couplés ?**  
A: Oui, utilisez plusieurs blocs Stateflow interconnectés dans Simulink pour créer des modèles DEVS couplés.

**Q: Comment exporter les résultats ?**  
A: Utilisez le bloc **To Workspace** dans Simulink pour enregistrer les signaux dans le workspace MATLAB.

---

**Last updated:** December 3, 2025  
**Project Status:** ✅ Stable (v1.0)
