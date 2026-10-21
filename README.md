# Détection d'anomalies de trafic réseau par régression linéaire (v1)

Baseline de trafic réseau construite avec une régression linéaire (scikit-learn). L'écart entre le trafic observé et le trafic attendu (le résidu) sert de score d'anomalie, selon une logique SOC / UEBA.

> **Version 1 : dataset fictif (synthétique).** Elle valide la méthode. Les performances obtenues ne sont pas représentatives d'un réseau réel.

## Objectif

- Modéliser le trafic attendu (Mbps) en fonction du nombre d'utilisateurs connectés
- Séparer entraînement et test (80/20, `random_state=42`)
- Interpréter les paramètres β0 et β1
- Évaluer le modèle avec MAE, MSE et R²
- Calculer les résidus et en faire un indicateur d'investigation

## Contenu

| Fichier | Rôle |
|---|---|
| `regression_lineaire_trafic_reseau_fictif.ipynb` | Notebook Google Colab complet, avec sorties |
| `requirements.txt` | Dépendances Python |

## Résultats (jeu de test de 4 observations)

| Élément | Valeur |
|---|---|
| Équation apprise | ŷ = 4,67 + 1,51x |
| β1 | 1,51 Mbps par utilisateur supplémentaire |
| MAE | 0,36 Mbps |
| MSE | 0,22 |
| R² | 0,99994 |
| Trafic attendu pour 110 utilisateurs | 170,81 Mbps |
| Résidu pour 220 Mbps observés | +49,19 Mbps |
| Plus grand résidu du test | +0,85 Mbps (95 utilisateurs) |

## Lecture SOC

- Un résidu positif signale un excès de trafic (exfiltration, DDoS, téléchargement massif). Un résidu négatif signale un trafic manquant (panne, lien coupé, collecte de logs interrompue).
- Seuil de démonstration de 40 Mbps comparé à un seuil statistique de 3σ (σ = 0,652, soit 1,96 Mbps) : le choix du seuil arbitre faux positifs (fatigue d'alerte) et faux négatifs.
- Un écart important ne prouve pas une cyberattaque. Il faut le corréler avec les logs pare-feu et NetFlow, les adresses IP et ports, les authentifications, l'horodatage et les événements planifiés (maintenance, sauvegardes, déploiements).

## Limites

- Dataset synthétique : le R² quasi parfait n'est pas réaliste.
- Seulement 4 observations de test : métriques peu robustes (validation croisée à prévoir).
- Une seule variable explicative ; extrapolation hors plage (au-delà de 105 utilisateurs) risquée.
- Un attaquant discret qui reste dans la baseline n'est pas détecté.

## Feuille de route

1. **v1 (ce dépôt)** : méthode validée sur données synthétiques.
2. **v2** : dataset public réaliste (CIC-IDS2017 ou jeux NetFlow, agrégation par minute, étiquettes d'attaque pour vérifier que les résidus montent pendant les attaques).
3. **v3** : données de mon lab GOAD-Mini (pfSense, Suricata, journaux Windows collectés par Splunk Universal Forwarder). Les attaques lancées depuis Kali fournissent des étiquettes fiables, mappées sur MITRE ATT&CK. Modèles envisagés : variables supplémentaires (heure, connexions), Isolation Forest, séries temporelles, intégration des alertes au SIEM.

## Exécution

1. Ouvrir le notebook dans Google Colab (les bibliothèques sont préinstallées).
2. Ou en local : `pip install -r requirements.txt`, puis ouvrir le notebook avec Jupyter.
