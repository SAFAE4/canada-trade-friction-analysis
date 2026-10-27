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

The analysis is descriptive and comparative. It covers January 2022 to August 2026, with 2022 to 2024 as the pre-tariff baseline, and it does not forecast or make causal claims.

## 2. Business Questions

1. **Volatility:** Which sectors showed the most unstable export values around key trade-policy dates?
2. **Regional exposure:** How do Quebec's exports compare with Ontario's, particularly goods bound for the U.S. market?
3. **Resilience:** Which tariff-sensitive sectors (for example automotive, metals, machinery) show the weakest trade balance?

## 3. Data

| Item | Detail |
|---|---|
| Provider | Statistics Canada |
| Primary table | 12-10-0175-01, International merchandise trade by province, commodity, and Principal Trading Partners (monthly). Replaces archived table 12-10-0119-01 |
| Table coverage | Provinces and territories, 13 NAPCS product sections, 27 principal trading partners |
| Selection used (Stage 1) | Canada, Quebec, Ontario; All countries and United States; domestic exports and imports; 12 NAPCS sections plus the total of all merchandise |
| Period | January 2022 to August 2026 (56 months) |
| Flows | Imports, domestic exports, re-exports. Re-exports are only available at the Canada level, so they are excluded from the provincial analysis |
| Basis | Customs basis, not seasonally adjusted |
| Units | Thousands of Canadian dollars in the file, converted to dollars in the notebook. Values are nominal |
| Languages | English and French versions of the table are used for bilingual labels |

Raw files are not stored in this repository. See the download links in [Sources](#10-sources).

**Checks done in Stage 1:** the selected data has 8,736 rows, which equals 3 geographies × 2 flows × 2 partners × 13 products × 56 months, so the grid is complete. The 12 product sections add up to the total of all merchandise in every month, geography, flow and partner (gap of about 0.00008%, rounding only). The download contains no suppressed or flagged values. Product section C24 was not selected, and C23 (special transactions trade) is excluded from sector comparisons because it is an accounting category.

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
| 1 | Data discovery and exploration | In progress, see `notebooks/01_explore.ipynb`. Remaining: policy texts not yet verified, notebook cleanup |
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
   python -m pip install pandas sqlalchemy pymysql python-dotenv requests openpyxl jupyter ipykernel matplotlib
   ```
3. Create a MySQL database named `trade_friction_db` and a user with privileges on it.
4. Copy `.env.example` to `.env` and fill in your own database user and password. The `.env` file is never committed.
5. Download table 12-10-0175-01 with the selection above into `data/raw/`.
6. Open the notebooks in `notebooks/`, select the `venv` kernel and run them from top to bottom.

### Known Limitations

- **Province attribution.** Imports are attributed to the province of clearance (where goods cleared customs), which may not be where they are consumed (table metadata, note 4). The table metadata does not state how domestic exports are attributed to provinces, so this project does not assume it. Provincial trade balances are therefore indicative, and the national balance is more reliable.
- **Re-exports** are goods that were imported and then exported again. The table provides them at the Canada level only, so the provincial analysis uses domestic exports.
- **Correlation, not causation.** The analysis describes how trade moved around policy dates. It does not prove the policies caused the changes. Front-loading ahead of tariffs, commodity prices, exchange rates and seasonality can produce the same patterns.
- **Nominal values.** Prices and exchange rates are mixed into the dollar figures, so a change in value is not necessarily a change in volume. This matters most for energy and metals.
- **Broad product sections.** NAPCS sections are coarse. For example, the metals section mixes aluminum with other metals and swings sharply from month to month in Ontario.
- **Baseline sensitivity.** 2023 and 2024 were unusually high for some sections (for example Quebec automotive), so percentage changes depend on the baseline chosen.
- **Revisions and estimates.** The latest months are preliminary and get revised. Statistics Canada also added estimates to some import figures during the CBSA CARM transition.
- **Sector labels are a judgment** tied to which policies covered each section, not to how trade moved. Effective tariffs varied by product because of USMCA content rules.
- **Partial year.** 2026 covers January to August only, so comparisons use the same months in every year.
- No suppressed or missing values were found in the Stage 1 download.

## 8. Policy Dates

Dates, rates and scope below were read in primary documents (proclamations, executive orders, Federal Register).

| Event | Signed | Effective | Scope (Canada) | Source |
|---|---|---|---|---|
| IEEPA tariffs on Canada | EO 14193, Feb 1, 2025 (paused by EO 14197, Feb 3) | Mar 4, 2025 | 25%; 10% on energy and critical minerals. USMCA-qualifying goods exempted from Mar 6, 2025 (EO 14231) | [CBP notice](https://www.govinfo.gov/content/pkg/FR-2025-03-06/html/2025-03664.htm), 90 FR 11423 |
| IEEPA increase | EO 14325, Jul 31, 2025 | Aug 1, 2025 | 25% rate raised to 35%; USMCA exemption kept | [EO 14325](https://www.govinfo.gov/content/pkg/FR-2025-08-06/html/2025-14999.htm), 90 FR 37957 |
| Steel and aluminum (Section 232) | Proclamations 10896 and 10895, Feb 10, 2025 | Mar 12, 2025 | 25% on all countries, including Canada | [Proclamation 10896](https://www.federalregister.gov/documents/full_text/html/2025/02/18/2025-02833.html), 90 FR 9817 |
| Steel and aluminum increase | Proclamation 10947, Jun 3, 2025 | Jun 4, 2025 | 25% raised to 50% | [Proclamation 10947](https://www.govinfo.gov/content/pkg/FR-2025-06-09/html/2025-10524.htm), 90 FR 24199 |
| Autos and parts (Section 232) | Proclamation 10908, Mar 26, 2025 | Vehicles Apr 3, 2025; parts by May 3, 2025 | 25%; USMCA-qualifying vehicles taxed on non-U.S. content only; USMCA-qualifying parts not tariffed until a Commerce process exists | [Proclamation 10908](https://www.govinfo.gov/content/pkg/DCPD-202500403/html/DCPD-202500403.htm), 90 FR 14705 |
| Section 122 surcharge | Proclamation 11012, Feb 20, 2026 | Feb 24 to Jul 24, 2026 | 10%; exempts USMCA goods of Canada, Section 232 articles, passenger vehicles and certain parts, energy and critical minerals | [Proclamation 11012](https://www.govinfo.gov/content/pkg/FR-2026-02-25/pdf/2026-03824.pdf), 91 FR 9339 |

**Not yet verified in primary text** (provisional until confirmed):

- Timber and lumber, Section 232: Proclamation 10976 (Sep 29, 2025), effective Oct 14, 2025, 10% on softwood lumber, amended by Proclamation 11000 (Dec 31, 2025).
- Section 301 forced-labor tariffs replacing Section 122 on Jul 24, 2026 (10% or 12.5%, about 60 economies). Whether Canada's USMCA-qualifying goods are exempt is unconfirmed. Only July and August 2026 in the data fall after this change.
- The Supreme Court ruling of Feb 20, 2026 and the order ending the IEEPA duties.
- Canada's counter-measures (Canada.ca and Finance Canada).

Softwood lumber also faces separate anti-dumping and countervailing duties, which this analysis does not cover.

## 9. Preliminary Findings (Stage 1)

These are descriptive. They show what moved and when, not why.

**Method.** Totals are compared over January to August in every year, because the data is not seasonally adjusted. Monthly values are compared with the same month's 2023 to 2024 average. Product sections are grouped by policy coverage, not by outcome:

| Group | Sections |
|---|---|
| Targeted: Section 232 steel and aluminum | C14 |
| Targeted: Section 232 autos | C19 |
| Targeted: Section 232 lumber (from Oct 2025) | C16 |
| Partly covered: steel and aluminum derivatives (hypothesis) | C17, C18 |
| Watch: aerospace | C21 |
| Indirect: food and agri | C11 |
| Comparison: energy | C12 |
| Other | C13, C15, C22 |
| Excluded | C23 |

**Domestic exports to the U.S., January to August 2026 against the 2023 to 2024 average:**

| | Targeted sections (C14, C16, C19) | Other sections (excl. C23) |
|---|---|---|
| Ontario | about −16% (72.1 to 60.7 B CAD) | about +9% (58.6 to 63.9 B CAD) |
| Quebec | about −12% (24.7 to 21.7 B CAD) | about −1% (31.6 to 31.4 B CAD) |

- The targeted sections fell by a similar share in both provinces. Ontario's other sections grew, offsetting part of the loss, while Quebec's did not, so the decline shows in Quebec's total.
- The mix differs. Autos are about 63% of Ontario's targeted base but about 15% of Quebec's, where lumber and metals contribute more dollars to the decline. Quebec autos have the steepest percentage fall (about −40%) and stayed 27% to 51% below baseline in every month of 2026.
- Monthly exports of autos, metals and forestry turn negative against baseline in April 2025 in both provinces, after unusually high January and February 2025 values. That is consistent with front-loading, which these data cannot confirm.
- **Diversion.** The U.S. share of domestic exports (January to August) fell from 76.3% in 2024 to 67.9% in 2026 for Canada, from 76.9% to 64.1% for Ontario and from 75.0% to 69.5% for Quebec. About 96% of Ontario's growth in non-U.S. exports is in metals (C14), so it is not a clear redirection of tariff-hit goods. Quebec's U.S. loss of about 4.6 B CAD was offset by about 4.0 B CAD of growth in other markets.
- **Imports.** Non-U.S. imports rose about 18% to 19% from 2024 to 2026 in Canada, Ontario and Quebec, while imports from the U.S. were roughly flat.

## 10. Sources

**Data (Statistics Canada)**

- Table 12-10-0175-01, International merchandise trade by province, commodity, and Principal Trading Partners:
  - English page: https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1210017501
  - French page: https://www150.statcan.gc.ca/t1/tbl1/fr/tv.action?pid=1210017501
  - CSV download (English): https://www150.statcan.gc.ca/n1/tbl/csv/12100175-eng.zip
  - CSV download (French): https://www150.statcan.gc.ca/n1/tbl/csv/12100175-fra.zip
- Open Government Portal record: https://open.canada.ca/data/en/dataset/ceca9f02-b02c-49e4-b025-f3e3207d29e5
- Contact for data questions: infostats@statcan.gc.ca
- Download date: `[fill in]`. Table's last release: `[fill in]`.

**Policy dates used in the analysis**

- See the links in [Policy Dates](#8-policy-dates). Items marked as not yet verified are to be confirmed against primary texts.

**Tool documentation**

- pandas: https://pandas.pydata.org/docs/
- SQLAlchemy: https://docs.sqlalchemy.org/
- MySQL Reference Manual: https://dev.mysql.com/doc/
- Power BI documentation: https://learn.microsoft.com/power-bi/

**Licence note.** Statistics Canada data are published under the Statistics Canada Open Licence. Check the current licence terms on the Statistics Canada website before redistributing any data.

## 11. Author

**Safae Moudkar**, data and business analysis portfolio project.
LinkedIn: linkedin.com/in/safae-moudkar-70812b307

---

<a id="français"></a>

# Français

## 1. Aperçu

Projet d'analyse bilingue (anglais/français) qui étudie la façon dont le commerce canadien de marchandises, par province et par secteur de produits, réagit aux changements des conditions du commerce international, avec un accent sur **le Québec comparé à l'Ontario et à l'ensemble du pays**.

Le projet couvre tout le cycle d'analyse : extraction et nettoyage des données avec **Python**, stockage dans un schéma en étoile **MySQL**, analyse statistique en **SQL**, et tableau de bord **Power BI** avec un sélecteur de langue anglais/français.

L'analyse est descriptive et comparative. Elle couvre la période de janvier 2022 à août 2026, avec 2022 à 2024 comme référence avant les tarifs, et elle ne fait ni prévision ni affirmation causale.

## 2. Questions d'affaires

1. **Volatilité :** Quels secteurs ont connu les valeurs d'exportation les plus instables autour des dates clés de la politique commerciale?
2. **Exposition régionale :** Comment les exportations du Québec se comparent-elles à celles de l'Ontario, notamment pour les marchandises destinées au marché américain?
3. **Résilience :** Quels secteurs sensibles aux tarifs (par exemple l'automobile, les métaux, la machinerie) affichent la balance commerciale la plus faible?

## 3. Données

| Élément | Détail |
|---|---|
| Fournisseur | Statistique Canada |
| Tableau principal | 12-10-0175-01, Commerce international de marchandises par province, par produit et les principaux partenaires commerciaux (mensuel). Remplace le tableau archivé 12-10-0119-01 |
| Couverture du tableau | Provinces et territoires, 13 sections de produits du SCPAN, 27 principaux partenaires commerciaux |
| Sélection utilisée (étape 1) | Canada, Québec, Ontario; Tous les pays et États-Unis; exportations intérieures et importations; 12 sections du SCPAN plus le total de toutes les marchandises |
| Période | Janvier 2022 à août 2026 (56 mois) |
| Flux | Importations, exportations intérieures, réexportations. Les réexportations ne sont disponibles qu'à l'échelle du Canada, et sont donc exclues de l'analyse provinciale |
| Base | Base douanière, données non désaisonnalisées |
| Unités | Milliers de dollars canadiens dans le fichier, convertis en dollars dans le notebook. Les valeurs sont nominales |
| Langues | Les versions française et anglaise du tableau servent aux libellés bilingues |

Les fichiers bruts ne sont pas conservés dans ce dépôt. Voir les liens de téléchargement dans les [Sources](#10-sources-1).

**Vérifications effectuées à l'étape 1 :** les données sélectionnées comptent 8 736 lignes, soit 3 géographies × 2 flux × 2 partenaires × 13 produits × 56 mois, ce qui montre que la grille est complète. Les 12 sections de produits s'additionnent au total de toutes les marchandises pour chaque mois, géographie, flux et partenaire (écart d'environ 0,00008 %, simple arrondi). Le téléchargement ne contient aucune valeur supprimée ou signalée. La section C24 n'a pas été sélectionnée, et la section C23 (commerce de transactions spéciales) est exclue des comparaisons sectorielles parce qu'il s'agit d'une catégorie comptable.

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
| 1 | Découverte et exploration des données | En cours, voir `notebooks/01_explore.ipynb`. Reste à faire : textes de politiques non encore vérifiés, nettoyage du notebook |
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
   python -m pip install pandas sqlalchemy pymysql python-dotenv requests openpyxl jupyter ipykernel matplotlib
   ```
3. Créez une base de données MySQL nommée `trade_friction_db` et un utilisateur disposant des privilèges sur celle-ci.
4. Copiez `.env.example` vers `.env` et saisissez votre propre utilisateur et mot de passe. Le fichier `.env` n'est jamais versionné.
5. Téléchargez le tableau 12-10-0175-01 avec la sélection décrite plus haut dans `data/raw/`.
6. Ouvrez les notebooks du dossier `notebooks/`, sélectionnez le noyau `venv` et exécutez-les du début à la fin.

### Limites connues

- **Attribution provinciale.** Les importations sont attribuées à la province de dédouanement (là où les marchandises ont été dédouanées), qui n'est pas forcément celle où elles sont consommées (métadonnées du tableau, note 4). Les métadonnées du tableau n'indiquent pas comment les exportations intérieures sont attribuées aux provinces, et ce projet ne le suppose donc pas. Les balances commerciales provinciales sont donc indicatives, et la balance nationale est plus fiable.
- **Les réexportations** sont des marchandises importées puis exportées de nouveau. Le tableau ne les fournit qu'à l'échelle du Canada, et l'analyse provinciale utilise donc les exportations intérieures.
- **Corrélation, et non causalité.** L'analyse décrit l'évolution des échanges autour des dates de politique commerciale. Elle ne prouve pas que ces politiques ont causé les changements. L'anticipation des tarifs par les exportateurs, les prix des produits de base, les taux de change et la saisonnalité peuvent produire les mêmes tendances.
- **Valeurs nominales.** Les prix et les taux de change sont inclus dans les montants en dollars, et une variation de valeur n'est donc pas forcément une variation de volume. Cela compte surtout pour l'énergie et les métaux.
- **Sections de produits larges.** Les sections du SCPAN sont grossières. Par exemple, la section des métaux regroupe l'aluminium et d'autres métaux, et elle fluctue fortement d'un mois à l'autre en Ontario.
- **Sensibilité à la période de référence.** Les années 2023 et 2024 ont été exceptionnellement élevées pour certaines sections (par exemple l'automobile au Québec), de sorte que les variations en pourcentage dépendent de la référence choisie.
- **Révisions et estimations.** Les derniers mois sont préliminaires et sont révisés. Statistique Canada a aussi ajouté des estimations à certaines données d'importation pendant la transition vers le programme CARM de l'ASFC.
- **Les groupes sectoriels relèvent d'un jugement** fondé sur les politiques qui visaient chaque section, et non sur l'évolution des échanges. Les tarifs effectifs variaient selon le produit en raison des règles de contenu de l'ACEUM.
- **Année partielle.** L'année 2026 ne couvre que janvier à août, et les comparaisons portent donc sur les mêmes mois chaque année.
- Aucune valeur supprimée ou manquante n'a été relevée dans le téléchargement de l'étape 1.

## 8. Dates des politiques commerciales

Les dates, taux et portées ci-dessous ont été lus dans des documents officiels (proclamations, décrets, Federal Register).

| Événement | Signature | Entrée en vigueur | Portée (Canada) | Source |
|---|---|---|---|---|
| Droits IEEPA visant le Canada | Décret 14193, 1er févr. 2025 (suspendu par le décret 14197, 3 févr.) | 4 mars 2025 | 25 %; 10 % sur l'énergie et les minéraux critiques. Marchandises admissibles à l'ACEUM exemptées dès le 6 mars 2025 (décret 14231) | [Avis de la CBP](https://www.govinfo.gov/content/pkg/FR-2025-03-06/html/2025-03664.htm), 90 FR 11423 |
| Hausse des droits IEEPA | Décret 14325, 31 juill. 2025 | 1er août 2025 | Taux de 25 % porté à 35 %; exemption ACEUM maintenue | [Décret 14325](https://www.govinfo.gov/content/pkg/FR-2025-08-06/html/2025-14999.htm), 90 FR 37957 |
| Acier et aluminium (article 232) | Proclamations 10896 et 10895, 10 févr. 2025 | 12 mars 2025 | 25 % pour tous les pays, y compris le Canada | [Proclamation 10896](https://www.federalregister.gov/documents/full_text/html/2025/02/18/2025-02833.html), 90 FR 9817 |
| Hausse sur l'acier et l'aluminium | Proclamation 10947, 3 juin 2025 | 4 juin 2025 | Taux de 25 % porté à 50 % | [Proclamation 10947](https://www.govinfo.gov/content/pkg/FR-2025-06-09/html/2025-10524.htm), 90 FR 24199 |
| Automobiles et pièces (article 232) | Proclamation 10908, 26 mars 2025 | Véhicules : 3 avr. 2025; pièces : au plus tard le 3 mai 2025 | 25 %; véhicules admissibles à l'ACEUM taxés sur le contenu non américain seulement; pièces admissibles à l'ACEUM non taxées tant qu'un processus du département du Commerce n'existe pas | [Proclamation 10908](https://www.govinfo.gov/content/pkg/DCPD-202500403/html/DCPD-202500403.htm), 90 FR 14705 |
| Surtaxe de l'article 122 | Proclamation 11012, 20 févr. 2026 | Du 24 févr. au 24 juill. 2026 | 10 %; exempte les marchandises canadiennes admissibles à l'ACEUM, les articles visés par l'article 232, les véhicules de tourisme et certaines pièces, l'énergie et les minéraux critiques | [Proclamation 11012](https://www.govinfo.gov/content/pkg/FR-2026-02-25/pdf/2026-03824.pdf), 91 FR 9339 |

**Pas encore vérifiés dans le texte officiel** (provisoires jusqu'à confirmation) :

- Bois d'œuvre et produits du bois, article 232 : proclamation 10976 (29 sept. 2025), en vigueur le 14 oct. 2025, 10 % sur le bois d'œuvre résineux, modifiée par la proclamation 11000 (31 déc. 2025).
- Droits de l'article 301 (travail forcé) remplaçant l'article 122 le 24 juill. 2026 (10 % ou 12,5 %, environ 60 économies). On ne sait pas encore si les marchandises canadiennes admissibles à l'ACEUM sont exemptées. Seuls juillet et août 2026 dans les données suivent ce changement.
- La décision de la Cour suprême du 20 févr. 2026 et le décret mettant fin aux droits IEEPA.
- Les contre-mesures du Canada (Canada.ca et Finances Canada).

Le bois d'œuvre résineux fait aussi l'objet de droits antidumping et compensateurs distincts, que cette analyse ne couvre pas.

## 9. Constats préliminaires (étape 1)

Ces constats sont descriptifs. Ils montrent ce qui a changé et à quel moment, et non pourquoi.

**Méthode.** Les totaux sont comparés de janvier à août pour chaque année, car les données ne sont pas désaisonnalisées. Les valeurs mensuelles sont comparées à la moyenne du même mois en 2023 et 2024. Les sections de produits sont regroupées selon les politiques qui les visaient, et non selon les résultats :

| Groupe | Sections |
|---|---|
| Ciblé : article 232, acier et aluminium | C14 |
| Ciblé : article 232, automobiles | C19 |
| Ciblé : article 232, bois d'œuvre (dès oct. 2025) | C16 |
| Partiellement visé : produits dérivés de l'acier et de l'aluminium (hypothèse) | C17, C18 |
| À surveiller : aérospatiale | C21 |
| Indirect : alimentation et agroalimentaire | C11 |
| Groupe de comparaison : énergie | C12 |
| Autres | C13, C15, C22 |
| Exclu | C23 |

**Exportations intérieures vers les États-Unis, janvier à août 2026 par rapport à la moyenne de 2023 et 2024 :**

| | Sections ciblées (C14, C16, C19) | Autres sections (sans C23) |
|---|---|---|
| Ontario | environ −16 % (de 72,1 à 60,7 G$ CA) | environ +9 % (de 58,6 à 63,9 G$ CA) |
| Québec | environ −12 % (de 24,7 à 21,7 G$ CA) | environ −1 % (de 31,6 à 31,4 G$ CA) |

- Les sections ciblées ont reculé dans une proportion semblable dans les deux provinces. Les autres sections de l'Ontario ont progressé et ont compensé une partie de la perte, alors que celles du Québec n'ont pas progressé, ce qui fait que le recul se voit dans le total du Québec.
- La composition diffère. L'automobile représente environ 63 % de la base ciblée de l'Ontario, mais environ 15 % de celle du Québec, où le bois d'œuvre et les métaux contribuent davantage au recul en dollars. L'automobile québécoise affiche la baisse la plus marquée en pourcentage (environ −40 %) et est restée de 27 % à 51 % sous la référence chaque mois de 2026.
- Les exportations mensuelles d'automobiles, de métaux et de produits forestiers passent sous la référence en avril 2025 dans les deux provinces, après des valeurs exceptionnellement élevées en janvier et février 2025. Cela concorde avec une anticipation des tarifs par les exportateurs, que ces données ne permettent pas de confirmer.
- **Diversification des marchés.** La part des États-Unis dans les exportations intérieures (janvier à août) est passée de 76,3 % en 2024 à 67,9 % en 2026 pour le Canada, de 76,9 % à 64,1 % pour l'Ontario et de 75,0 % à 69,5 % pour le Québec. Environ 96 % de la hausse des exportations ontariennes vers les autres pays se trouve dans les métaux (C14), ce qui n'est pas une réorientation évidente de produits touchés par les tarifs. La perte du Québec aux États-Unis, d'environ 4,6 G$ CA, a été compensée par environ 4,0 G$ CA de hausse sur d'autres marchés.
- **Importations.** Les importations hors États-Unis ont augmenté d'environ 18 % à 19 % de 2024 à 2026 au Canada, en Ontario et au Québec, alors que les importations en provenance des États-Unis sont restées à peu près stables.

## 10. Sources <a id="10-sources-1"></a>

**Données (Statistique Canada)**

- Tableau 12-10-0175-01, Commerce international de marchandises par province, par produit et les principaux partenaires commerciaux :
  - Page en français : https://www150.statcan.gc.ca/t1/tbl1/fr/tv.action?pid=1210017501
  - Page en anglais : https://www150.statcan.gc.ca/t1/tbl1/en/tv.action?pid=1210017501
  - Téléchargement CSV (français) : https://www150.statcan.gc.ca/n1/tbl/csv/12100175-fra.zip
  - Téléchargement CSV (anglais) : https://www150.statcan.gc.ca/n1/tbl/csv/12100175-eng.zip
- Fiche du Portail du gouvernement ouvert : https://open.canada.ca/data/en/dataset/ceca9f02-b02c-49e4-b025-f3e3207d29e5
- Contact pour les questions sur les données : infostats@statcan.gc.ca
- Date de téléchargement : `[à compléter]`. Dernière diffusion du tableau : `[à compléter]`.

**Dates de politique commerciale utilisées dans l'analyse**

- Voir les liens de la section [Dates des politiques commerciales](#8-dates-des-politiques-commerciales). Les éléments indiqués comme non vérifiés restent à confirmer dans les textes officiels.

**Documentation des outils**

- pandas : https://pandas.pydata.org/docs/
- SQLAlchemy : https://docs.sqlalchemy.org/
- Manuel de référence MySQL : https://dev.mysql.com/doc/
- Documentation Power BI : https://learn.microsoft.com/power-bi/

**Note sur la licence.** Les données de Statistique Canada sont publiées sous la Licence d'utilisation ouverte de Statistique Canada. Vérifiez les conditions de licence en vigueur sur le site de Statistique Canada avant de redistribuer des données.

## 11. Autrice

**Safae Moudkar**, projet de portfolio en analyse de données et analyse d'affaires.
LinkedIn : linkedin.com/in/safae-moudkar-70812b307
