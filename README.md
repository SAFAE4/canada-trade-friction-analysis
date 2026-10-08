# canada-trade-friction-analysis
Bilingual (EN/FR) analysis of Canadian trade exposure using Python, MySQL, and Power BI.

# North American Trade Friction & Sector Exposure Analysis
# Frictions commerciales nord-américaines et exposition sectorielle

**[English](#english) · [Français](#français)**

---

<a id="english"></a>

# English

## 1. Overview

A bilingual (English/French) analytics project that studies how Canadian merchandise trade, by province and product sector, responds to shifts in international trade conditions, with a focus on **Quebec compared with Ontario and the national picture**.

The project covers the full analytics workflow: data extraction and cleaning in **Python**, storage in a **MySQL** star schema, statistical analysis in **SQL**, and a **Power BI** dashboard with an English/French language toggle.

## 2. Business Questions

1. **Volatility:** Which sectors showed the most unstable export values around key trade-policy dates?
2. **Regional exposure:** How do Quebec's exports compare with Ontario's, particularly goods bound for the U.S. market?
3. **Resilience:** Which tariff-sensitive sectors (for example automotive, metals, machinery) show the weakest trade balance?

## 3. Data

| Item | Detail |
|---|---|
| Provider | Statistics Canada |
| Primary table | 12-10-0175-01, International merchandise trade by province, commodity, and Principal Trading Partners |
| Coverage | Provinces and territories, 12 NAPCS product sections, 27 principal trading partners |
| Flows | Imports, domestic exports, re-exports |
| Basis | Customs basis, not seasonally adjusted |
| Languages | English and French versions of the table are used for bilingual labels |

Raw files are not stored in this repository. See the download links in [Sources](#8-sources).

## 4. Tech Stack

| Layer | Tools |
|---|---|
| Extraction and cleaning | Python, pandas |
| Database | MySQL, SQLAlchemy, PyMySQL |
| Analysis | SQL (window functions, CTEs), statistics |
| Reporting | Power BI Desktop, DAX |
| Environment | VS Code, Jupyter notebooks, Git/GitHub |

**Pipeline**

```
Statistics Canada CSV (EN + FR)
        |
        v
Python: clean, reshape, add bilingual labels
        |
        v
MySQL: star schema (trade_friction_db)
        |
        v
SQL analysis: volatility, regional exposure, trade balance
        |
        v
Power BI: bilingual dashboard (EN / FR toggle)
```

## 5. Repository Structure

```
canada-trade-friction-analysis/
├── data/            Raw and cleaned data (raw files are git-ignored)
├── docs/            Reports and documentation
├── notebooks/       Exploration and testing notebooks
├── scripts/         Python extraction, cleaning and loading scripts
├── sql/             Schema creation and analysis queries
├── dashboard/       Power BI file and screenshots
├── .env.example     Template for database settings
└── README.md
```

## 6. Project Status

| Stage | Description | Status |
|---|---|---|
| 0 | Environment setup and connectivity tests (Python, MySQL, Power BI) | Complete, see [docs/stage-0-setup-report.md](docs/stage-0-setup-report.md) |
| 1 | Data discovery and exploration | Next |
| 2 | Cleaning and bilingual labelling in Python | Planned |
| 3 | MySQL star schema and data loading | Planned |
| 4 | SQL analysis | Planned |
| 5 | Power BI bilingual dashboard | Planned |
| 6 | Packaging, findings and LinkedIn write-up | Planned |

## 7. How to Run (Windows, PowerShell)

1. Clone the repository and open the folder in VS Code.
2. Create and activate a virtual environment:
   ```powershell
   python -m venv venv
   .\venv\Scripts\Activate.ps1
   python -m pip install pandas sqlalchemy pymysql python-dotenv requests openpyxl jupyter ipykernel
   ```
3. Create a MySQL database named `trade_friction_db` and a user with privileges on it.
4. Copy `.env.example` to `.env` and fill in your own database user and password. The `.env` file is never committed.
5. Open the notebooks in `notebooks/` and select the `venv` kernel.

### Known Limitations

- **Province attribution differs by flow.** Exports are attributed to the province of origin, while imports are attributed to the province of clearance (where goods enter Canada), not where they are consumed. Provincial trade balances are therefore indicative, and the national balance is more reliable.
- **Re-exports** are goods that were imported and then exported again. The analysis treats domestic exports and re-exports separately.
- **Correlation, not causation.** The analysis describes how trade moved around policy dates. It does not prove the policies caused the changes.
- Suppressed or missing values in the source data are documented in the cleaning step.

## 8. Sources

**Data (Statistics Canada)**

- Table 12-10-0175-01, International merchandise trade by province, commodity, and Principal Trading Partners:
  - English page: https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1210017501
  - French page: https://www150.statcan.gc.ca/t1/tbl1/fr/tv.action?pid=1210017501
  - CSV download (English): https://www150.statcan.gc.ca/n1/tbl/csv/12100175-eng.zip
  - CSV download (French): https://www150.statcan.gc.ca/n1/tbl/csv/12100175-fra.zip
- Open Government Portal record: https://open.canada.ca/data/en/dataset/ceca9f02-b02c-49e4-b025-f3e3207d29e5
- Contact for data questions: infostats@statcan.gc.ca

**Policy dates used in the analysis**

- To be added during Stage 1, each with a dated, citable source.

**Tool documentation**

- pandas: https://pandas.pydata.org/docs/
- SQLAlchemy: https://docs.sqlalchemy.org/
- MySQL Reference Manual: https://dev.mysql.com/doc/
- Power BI documentation: https://learn.microsoft.com/power-bi/

**Licence note.** Statistics Canada data are published under the Statistics Canada Open Licence. Check the current licence terms on the Statistics Canada website before redistributing any data.

## 9. Author

**Safae Moudkar**, data and business analysis portfolio project.
LinkedIn: _add link_ · GitHub: _add link_

---

<a id="français"></a>

# Français

## 1. Aperçu

Projet d'analyse bilingue (anglais/français) qui étudie la façon dont le commerce canadien de marchandises, par province et par secteur de produits, réagit aux changements des conditions du commerce international, avec un accent sur **le Québec comparé à l'Ontario et à l'ensemble du pays**.

Le projet couvre tout le cycle d'analyse : extraction et nettoyage des données avec **Python**, stockage dans un schéma en étoile **MySQL**, analyse statistique en **SQL**, et tableau de bord **Power BI** avec un sélecteur de langue anglais/français.

## 2. Questions d'affaires

1. **Volatilité :** Quels secteurs ont connu les valeurs d'exportation les plus instables autour des dates clés de la politique commerciale?
2. **Exposition régionale :** Comment les exportations du Québec se comparent-elles à celles de l'Ontario, notamment pour les marchandises destinées au marché américain?
3. **Résilience :** Quels secteurs sensibles aux tarifs (par exemple l'automobile, les métaux, la machinerie) affichent la balance commerciale la plus faible?

## 3. Données

| Élément | Détail |
|---|---|
| Fournisseur | Statistique Canada |
| Tableau principal | 12-10-0175-01, Commerce international de marchandises par province, par produit et les principaux partenaires commerciaux |
| Couverture | Provinces et territoires, 12 sections de produits du SCPAN, 27 principaux partenaires commerciaux |
| Flux | Importations, exportations intérieures, réexportations |
| Base | Base douanière, données non désaisonnalisées |
| Langues | Les versions française et anglaise du tableau servent aux libellés bilingues |

Les fichiers bruts ne sont pas conservés dans ce dépôt. Voir les liens de téléchargement dans les [Sources](#8-sources-1).

## 4. Outils utilisés

| Couche | Outils |
|---|---|
| Extraction et nettoyage | Python, pandas |
| Base de données | MySQL, SQLAlchemy, PyMySQL |
| Analyse | SQL (fonctions de fenêtrage, CTE), statistiques |
| Rapports | Power BI Desktop, DAX |
| Environnement | VS Code, notebooks Jupyter, Git/GitHub |

**Chaîne de traitement**

```
CSV de Statistique Canada (EN + FR)
        |
        v
Python : nettoyage, restructuration, libellés bilingues
        |
        v
MySQL : schéma en étoile (trade_friction_db)
        |
        v
Analyse SQL : volatilité, exposition régionale, balance commerciale
        |
        v
Power BI : tableau de bord bilingue (sélecteur EN / FR)
```

## 5. Structure du dépôt

```
canada-trade-friction-analysis/
├── data/            Données brutes et nettoyées (fichiers bruts exclus de Git)
├── docs/            Rapports et documentation
├── notebooks/       Notebooks d'exploration et de tests
├── scripts/         Scripts Python d'extraction, de nettoyage et de chargement
├── sql/             Création du schéma et requêtes d'analyse
├── dashboard/       Fichier Power BI et captures d'écran
├── .env.example     Modèle des paramètres de la base de données
└── README.md
```

## 6. État d'avancement

| Étape | Description | Statut |
|---|---|---|
| 0 | Configuration de l'environnement et tests de connexion (Python, MySQL, Power BI) | Terminée, voir [docs/stage-0-setup-report.md](docs/stage-0-setup-report.md) (en anglais) |
| 1 | Découverte et exploration des données | Prochaine étape |
| 2 | Nettoyage et libellés bilingues avec Python | Prévue |
| 3 | Schéma en étoile MySQL et chargement des données | Prévue |
| 4 | Analyse SQL | Prévue |
| 5 | Tableau de bord Power BI bilingue | Prévue |
| 6 | Mise en forme, constats et publication LinkedIn | Prévue |

## 7. Mode d'emploi (Windows, PowerShell)

1. Clonez le dépôt et ouvrez le dossier dans VS Code.
2. Créez et activez un environnement virtuel :
   ```powershell
   python -m venv venv
   .\venv\Scripts\Activate.ps1
   python -m pip install pandas sqlalchemy pymysql python-dotenv requests openpyxl jupyter ipykernel
   ```
3. Créez une base de données MySQL nommée `trade_friction_db` et un utilisateur disposant des privilèges sur celle-ci.
4. Copiez `.env.example` vers `.env` et saisissez votre propre utilisateur et mot de passe. Le fichier `.env` n'est jamais versionné.
5. Ouvrez les notebooks du dossier `notebooks/` et sélectionnez le noyau `venv`.

### Limites connues

- **L'attribution provinciale varie selon le flux.** Les exportations sont attribuées à la province d'origine, tandis que les importations sont attribuées à la province de dédouanement (là où les marchandises entrent au Canada), et non là où elles sont consommées. Les balances commerciales provinciales sont donc indicatives, et la balance nationale est plus fiable.
- **Les réexportations** sont des marchandises importées puis exportées de nouveau. L'analyse distingue les exportations intérieures des réexportations.
- **Corrélation, et non causalité.** L'analyse décrit l'évolution des échanges autour des dates de politique commerciale. Elle ne prouve pas que ces politiques ont causé les changements.
- Les valeurs supprimées ou manquantes des données sources sont documentées à l'étape de nettoyage.

## 8. Sources <a id="8-sources-1"></a>

**Données (Statistique Canada)**

- Tableau 12-10-0175-01, Commerce international de marchandises par province, par produit et les principaux partenaires commerciaux :
  - Page en français : https://www150.statcan.gc.ca/t1/tbl1/fr/tv.action?pid=1210017501
  - Page en anglais : https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1210017501
  - Téléchargement CSV (français) : https://www150.statcan.gc.ca/n1/tbl/csv/12100175-fra.zip
  - Téléchargement CSV (anglais) : https://www150.statcan.gc.ca/n1/tbl/csv/12100175-eng.zip
- Fiche du Portail du gouvernement ouvert : https://open.canada.ca/data/en/dataset/ceca9f02-b02c-49e4-b025-f3e3207d29e5
- Contact pour les questions sur les données : infostats@statcan.gc.ca

**Dates de politique commerciale utilisées dans l'analyse**

- À ajouter à l'étape 1, chacune avec une source datée et citable.

**Documentation des outils**

- pandas : https://pandas.pydata.org/docs/
- SQLAlchemy : https://docs.sqlalchemy.org/
- Manuel de référence MySQL : https://dev.mysql.com/doc/
- Documentation Power BI : https://learn.microsoft.com/power-bi/

**Note sur la licence.** Les données de Statistique Canada sont publiées sous la Licence d'utilisation ouverte de Statistique Canada. Vérifiez les conditions de licence en vigueur sur le site de Statistique Canada avant de redistribuer des données.

## 9. Autrice

**Safae Moudkar**, projet de portfolio en analyse de données et analyse d'affaires.
LinkedIn : _ajouter le lien_ · GitHub : _ajouter le lien_