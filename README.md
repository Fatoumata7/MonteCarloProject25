# Monte Carlo Project : Drone Delivery

## Description
Simulation et optimisation de livraisons par drone sur une zone de 10 km × 10 km avec contraintes d’autonomie, de capacité et de fenêtres horaires. L’objectif est de maximiser le nombre de colis livrés **dans la fenêtre** impartie.

## Problème
- **Zone** : carré 10 km × 10 km (coordonnées en cm : [0, 1 000 000])  
- **Drone** : 60 km/h (1 km/min), autonomie 40 km, recharge 1 h, capacité 20 kg  
- **Dépôt** : (0,0), départ batterie pleine à 9h00  
- **Colis** : 80 colis, masse 0.05–2 kg, positions aléatoires  
- **Fenêtres horaires** : 10 colis par tranche de 2h de 9h à 18h  
- **Dernier décollage** : avant 18h00  

## Scoring
- Livraison **dans la fenêtre** : 0 point  
- Livraison **hors fenêtre** : -50 points  
- **Non livré** : -100 points  
- Objectif : **maximiser le score** en livrant le plus de colis possible dans la fenêtre  

## Hypothèses supplémentaires
- Recharge nécessaire si autonomie insuffisante pour trajet complet (dépôt → tous colis du lot → dépôt)  
- Temps de prise/pose colis : 0 min  
- Distance calculée en ligne droite  

## Approches testées

### Greedy (glouton)
- **Principe** : à chaque étape, choisir le prochain colis selon une heuristique simple (ex. le plus proche ou respectant la fenêtre).  
- **Avantages** : très rapide, facile à implémenter  
- **Limites** : peut se bloquer dans des choix locaux → sous-optimal globalement  

### NRPA (Nested Rollout Policy Adaptation)
- **Principe** :
  - Effectue des rollouts (simulations aléatoires pondérées)  
  - Sélectionne la meilleure séquence trouvée  
  - Adapte la politique en augmentant la probabilité des actions de cette séquence  
  - Fonctionne par niveaux (nesting) : plus le niveau est haut, plus la recherche est guidée  
- **Avantages** : apprend progressivement une politique efficace  
- **Limites** : sensible au nombre d’itérations, purement statistique (pas de connaissance métier)  

### PBR (Proximity Biased Rollouts)
- **Principe** : variante de rollout avec **biais vers les clients proches**  
- **Avantages** : exploite une heuristique naturelle → moins de temps perdu sur des choix aberrants  
- **Limites** : reste aléatoire, qualité dépend du nombre de rollouts et du réglage du biais  

### GNRPA (Generalized NRPA)
- **Principe** : extension de NRPA avec :
  - **Température** : contrôle le degré d’exploration (T < 1 → déterministe, T > 1 → exploratoire)  
  - **Biais métier** (distance) : intègre directement une connaissance du problème dans la politique  
- **Avantages** : combine apprentissage adaptatif **+ heuristiques structurées** → plus robuste  
- **Limites** : tuning plus complexe (T, alpha, n_iter, level)

## Auteur

Fatoumata Wadiou

Pénélope Millet

---