# Documentation Prototype Digisys — Workflows DS-01 & DS-02

Ce prototype met en œuvre les deux premiers blocs fondamentaux de la structure **Digisys** :
1. **DS-01 — Capture & Validation d'un prospect** : Nettoyage, normalisation des indicatifs (+225 Côte d'Ivoire) et validation des données d'entrée.
2. **DS-02 — Qualification & Brouillon d'approche** : Scoring explicable et adaptable à toute niche, rédaction sécurisée du brouillon d'accroche et envoi d'une alerte d'approbation sur **Telegram**.

> ⚠️ **Sécurité & Human-in-the-Loop :**
> Ces workflows ne contiennent aucun envoi automatisé vers les prospects, aucun scrap, aucun secret en clair. Tout contact sortant reste soumis à la validation humaine manuelle.

---

## 1. Fichiers Disponibles

* [`DS-01_Capture_Validation_Prospect.json`](file:///c:/Users/salou/Downloads/N8N/DS-01_Capture_Validation_Prospect.json) : Export JSON du workflow d'entrée.
* [`DS-02_Qualification_Brouillon_IA.json`](file:///c:/Users/salou/Downloads/N8N/DS-02_Qualification_Brouillon_IA.json) : Export JSON du workflow de qualification & brouillon.
* [`donnees_test_fictives.json`](file:///c:/Users/salou/Downloads/N8N/donnees_test_fictives.json) : Scénarios de tests complets avec cas nominaux, cas incomplets et cas d'exclusion.

---

## 2. Procédure d'Importation dans votre n8n Auto-hébergé

1. Ouvrez votre interface **n8n**.
2. Cliquez sur **Workflows** $\rightarrow$ **Add workflow** $\rightarrow$ Menu trois points en haut à droite $\rightarrow$ **Import from File**.
3. Sélectionnez le fichier `DS-01_Capture_Validation_Prospect.json`.
4. Répétez l'opération pour `DS-02_Qualification_Brouillon_IA.json`.

---

## 3. Configuration des Identifiants & Paramètres (Credentials)

### A. Bot Telegram (Pour recevoir les notifications d'approbation dans DS-02)
1. Créez un bot Telegram avec `@BotFather` pour obtenir votre **Bot Token**.
2. Récupérez votre **Chat ID** personnel ou de groupe (via `@userinfobot` ou en envoyant un message au bot puis en consultant l'URL getUpdates).
3. Dans n8n, créez une entrée de credential de type **Telegram API** et collez votre token (ne collez jamais ce token dans un prompt ou un fichier partagé).
4. Dans le nœud `Notification Telegram (Approbation)` du workflow DS-02 :
   * Associez votre Credential Telegram.
   * Remplacez `VOTRE_CHAT_ID_TELEGRAM` par votre véritable ID numérique.

### B. Stockage des Données (n8n Data Tables ou Google Sheets)
* **Option 1 (n8n Data Tables intégrées) :** Vous pouvez insérer un nœud *n8n Data Tables* après la validation pour enregistrer les leads.
* **Option 2 (Google Sheets / Tableur) :** Vous pouvez connecter un nœud *Google Sheets* avec une feuille contenant les colonnes : `lead_id`, `nom_contact`, `nom_entreprise`, `secteur_activite`, `ville`, `telephone`, `email`, `score_qualification`, `statut_crm`, `ne_plus_contacter`, `date_creation`.

---

## 4. Protocole de Test avec Données Fictives

Vous pouvez tester l'endpoint de DS-01 via un outil comme cURL ou Postman, ou directement en cliquant sur **"Test step"** dans n8n :

### Test 1 : Prospect Valide (Boutique Mode Abidjan)
```json
{
  "nom_contact": "Awa Kouassi",
  "nom_entreprise": "Élégance d'Abidjan",
  "secteur_activite": "Mode et Accessoires",
  "ville": "Abidjan - Cocody",
  "canal_source": "formulaire_site",
  "telephone": "0708091011",
  "email": "contact@elegance-abidjan.ci",
  "besoin_exprime": "Gérer les nombreuses commandes WhatsApp et éviter les oublis de livraison"
}
```
* **Résultat attendu :** 
  * DS-01 répond HTTP 200 avec `lead_id` et statut `nouveau`. Le téléphone est formaté en `+2250708091011`.
  * DS-02 calcule un score de 100/100, génère le brouillon d'accroche et vous envoie l'alerte sur Telegram.

### Test 2 : Prospect Refusé / Ne plus contacter
```json
{
  "nom_contact": "Jean Kouamé",
  "nom_entreprise": "Boutique Auto CI",
  "telephone": "+2250506070809",
  "ne_plus_contacter": true
}
```
* **Résultat attendu :** DS-02 s'oriente vers la branche d'arrêt et n'envoie aucune notification d'approche commerciale.

---

## 5. Limites & Étapes Suivantes
* Les workflows sont en mode **brouillon/test**.
* Une fois ces deux workflows validés sur votre instance, nous pourrons enchaîner avec **DS-03 (Traitement des réponses)** et **DS-04 (Souscription et validation du paiement)**.
