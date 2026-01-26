<<<<<<< HEAD
<<<<<<< HEAD
# LLM Answer Watcher – UI Nadogradnja  

##  Uvod i predstavljanje problema

U današnje vrijeme, sve više organizacija želi pratiti kako se njihovi brendovi, proizvodi ili usluge pojavljuju u odgovorima velikih jezičnih modela (LLM-ova).  
Na primjer: kada korisnik postavi pitanje poput *"Koji su najbolji alati za XYZ?"*, model može spomenuti određeni brend – ili ga uopće ne spomenuti – što ima veliki značaj za vidljivost, konkurentnost i reputaciju.

Projekt **[LLM Answer Watcher](https://github.com/nibzard/llm-answer-watcher)** rješava taj problem kroz **CLI alat** koji:
- izvršava upite prema LLM-ovima i analizira pojavu brendova (spomen, pozicija, kontekst);
- sprema rezultate u **SQLite bazu** za praćenje kroz vrijeme;
- generira **HTML izvještaje** i omogućuje upravljanje putem komandne linije.

Iako CLI pristup omogućuje moćnu automatizaciju, on nije uvijek najpraktičniji za krajnje korisnike koji žele **vizualno** pratiti rezultate, filtrirati podatke, i brzo donositi zaključke.  
Zato je nastao ovaj projekt – **UI nadogradnja**.

---

##  Hipoteza i cilj projekta

> **Hipoteza:**  
> Ako se LLM Answer Watcher proširi intuitivnim grafičkim korisničkim sučeljem (UI), korisnici će lakše, brže i učinkovitije moći pratiti rezultate i donositi odluke.

**Ciljevi projekta:**
- Dodati **grafički sloj (frontend)** koji omogućuje pregled rezultata kroz web ili desktop sučelje.  
- Vizualizirati rezultate pomoću **grafova, tablica i filtera**.  
- Zadržati postojeću funkcionalnost originalnog CLI alata.  
- Osigurati **laganu integraciju**: UI komunicira s postojećim modulima, bez potrebe za ponovnim pisanjem jezgre alata.

---

##  Opseg nadogradnje

###  Frontend
Implementacija web aplikacije (npr. React, Vue ili Svelte) koja omogućuje korisniku da:
- učita konfiguracijski fajl (`watcher.config.yaml`);
- pokrene praćenje (ili učita postojeće rezultate);
- pregleda rezultate po **upitima, modelima, brendovima i datumima**;
- filtrira i sortira rezultate;
- vizualizira trendove (npr. promjene pozicije brenda kroz vrijeme);
- preuzme izvještaje u **HTML, PDF ili CSV** formatu.

###  Backend / Integracija
- povezuje UI s postojećim CLI funkcijama i SQLite bazom;
- omogućuje REST API sloj ili direktan pristup lokalnim fajlovima;
- (opcionalno) podržava pokretanje novih analiza iz UI-ja.

###  UX / UI dizajn
- jednostavan i pregledan raspored (dashboard s glavnim metrikama);
- jasna navigacija: *Pregled*, *Trendovi*, *Upiti*, *Brendovi*;
- naglasak na razumljivim vizualnim prikazima i intuitivnom korištenju.

---

##  Zašto ova nadogradnja?

- **Pristupačnost:** omogućuje korištenje alata i osobama bez CLI iskustva (npr. analitičari, marketinški timovi).  
- **Učinkovitost:** vizualni prikaz trendova omogućuje brže prepoznavanje promjena.
- **Vrijednost:** povećava upotrebljivost i dugoročni utjecaj originalnog projekta.

---


##  Arhitektura sustava

Mermaid dijagram:

flowchart TD
    User["User"]
    Config["watcher.config.yaml<br/>(models, brands, intents, budget)"]
    CLI["llm-answer-watcher CLI"]
    WebApp["User Interface<br/>"]
    Providers["LLM Providers"]
    Responses["LLM Responses"]
    Extraction["Brand<br/>Mention &amp; Rank Extraction"]
    SQLite["SQLite Database"]
    OutputDir["Output Directory"]
    HTMLReport["HTML Report"]
    Analysis["Historical &amp;<br/>Trend Analysis"]

    User -->|Configures| Config
    User -->|Browses/Triggers| WebApp
    Config -->|Runs CLI or triggers| CLI
    CLI -->|Sends queries| Providers
    Providers -->|Returns| Responses
    Responses --> Extraction
    Extraction --> SQLite
    Extraction --> OutputDir
    OutputDir --> HTMLReport
    SQLite --> Analysis
    HTMLReport --> WebApp
    Analysis --> WebApp
    SQLite --> WebApp
    WebApp -->|Displays| User

=======
# LLM Answer Watcher

LLM Answer Watcher je alat za praćenje spominjanja brendova u odgovorima velikih jezičnih modela (LLM). Omogućuje vam da vidite kako se vaši brendovi, proizvodi i usluge pozicioniraju u odnosu na konkurenciju u odgovorima na upite korisnika.

Ovaj projekt nadograđuje postojeći CLI (Command-Line Interface) alat s grafičkim korisničkim sučeljem (UI) za lakšu vizualizaciju i analizu podataka.

## Instalacija

### Opcija 1: Docker (preporučeno)

Najjednostavniji način pokretanja je korištenjem Dockera. Ova opcija automatski pokreće API server i web sučelje.

```bash
# Pokretanje s docker-compose
docker-compose up --build

# Ili u pozadini
docker-compose up -d --build
```

Nakon pokretanja:
- **Web UI**: http://localhost:3000
- **API**: http://localhost:8000

Za zaustavljanje:
```bash
docker-compose down
```

### Opcija 2: Ručna instalacija

Za pokretanje alata, potrebno je instalirati sve ovisnosti definirane u `requirements.txt` datoteci.

```bash
pip install -r requirements.txt
```

## Konfiguracija

Prije pokretanja, potrebno je izraditi konfiguracijsku datoteku. Možete kopirati priloženi primjer:

```bash
cp examples/default.config.yaml moja-konfiguracija.yaml
```

Zatim, uredite `moja-konfiguracija.yaml` i prilagodite je svojim potrebama. Ključni dijelovi za konfiguraciju su:

*   **`run_settings`**:
    *   `output_dir`: Direktorij u koji će se spremati rezultati.
    *   `sqlite_db_path`: Putanja do SQLite baze podataka za pohranu povijesnih podataka.
    *   `models`: Lista LLM modela koje želite koristiti. Potrebno je navesti `provider` (npr. `google`), `model_name` (npr. `gemini-1.5-flash`) i `env_api_key` (naziv varijable okruženja koja sadrži vaš API ključ).

*   **`brands`**:
    *   `mine`: Lista vaših brendova i njihovih aliasa.
    *   `competitors`: Lista konkurentskih brendova.

*   **`intents`**: Lista upita koje želite pratiti. Svaki upit ima `id` i `prompt` (tekst upita).

Primjer `moja-konfiguracija.yaml`:

```yaml
run_settings:
  output_dir: "./output"
  sqlite_db_path: "./output/watcher.db"
  max_concurrent_requests: 10
  models:
    - provider: "google"
      model_name: "gemini-1.5-flash"
      env_api_key: "GEMINI_API_KEY"
      system_prompt: "google/default"

brands:
  mine:
    - "MojBrend"
    - "MojProizvod"
  competitors:
    - "KonkurentA"
    - "KonkurentB"

intents:
  - id: "usporedba-proizvoda"
    prompt: "Koji su najbolji alati za [kategorija]?"
  - id: "odluka-o-kupnji"
    prompt: "Koji [vrsta proizvoda] da odaberem?"
```

Prije pokretanja, ne zaboravite postaviti varijablu okruženja s vašim API ključem (npr. za Gemini):

```bash
export GEMINI_API_KEY=AIza...
```

## Pokretanje

Nakon što ste konfigurirali `moja-konfiguracija.yaml`, možete pokrenuti alat pomoću sljedeće naredbe:

```bash
llm-answer-watcher run --config moja-konfiguracija.yaml
```

Alat će izvršiti upite prema definiranim LLM modelima, analizirati odgovore i spremiti rezultate.

## Pregled Rezultata

Rezultati se spremaju u direktorij definiran u `output_dir` (npr. `./output`). Unutar tog direktorija, pronaći ćete:

*   **`watcher.db`**: SQLite baza podataka sa svim prikupljenim podacima.
*   **HTML izvještaje**: Za svaki pokrenuti `run`, generira se HTML datoteka s detaljnim izvještajem.
*   **Web-UI**: Pokretanjem `llm-answer-watcher ui` (funkcionalnost u razvoju), moći ćete pregledavati i analizirati rezultate kroz interaktivno web sučelje.
>>>>>>> 04342a810ea5441727877f814c8d4fc9141799b1
=======
# LLM Answer Watcher

## Overview

LLM Answer Watcher is a powerful tool designed to monitor and analyze brand mentions within the responses generated by Large Language Models (LLMs). It provides insights into how your brands, products, and services are positioned relative to competitors in LLM outputs, helping you understand market perception and identify opportunities.

This project enhances an existing Command-Line Interface (CLI) tool with a user-friendly Graphical User Interface (GUI) for easier data visualization, analysis, and interactive exploration of LLM responses.

## Features

*   **Brand Monitoring:** Track mentions of your brands and competitors in LLM generated text.
*   **Customizable Intents:** Define specific queries or prompts to evaluate LLM responses.
*   **Multi-Model Support:** Integrate with various LLM providers (e.g., Google Gemini).
*   **Data Visualization:** Interactive Web UI for analyzing trends and patterns in brand mentions.
*   **Historical Data:** Stores results in a SQLite database for long-term tracking and analysis.
*   **Reporting:** Generates detailed HTML reports for each analysis run.

## Technologies Used

*   **Backend:** Python 3.9+ (FastAPI, Pydantic, SQLAlchemy)
*   **Frontend:** React, TypeScript, Vite, TailwindCSS
*   **Database:** SQLite
*   **Containerization:** Docker, Docker Compose
*   **LLM Providers:** Google Gemini, Groq (extensible to others)

## Prerequisites

Before you begin, ensure you have the following installed:

*   **Git:** For cloning the repository.
*   **Docker & Docker Compose (Recommended):** For the easiest setup and deployment.
*   **Python 3.9+ (Optional, for manual setup):**
    *   `pip` (Python package installer)
    *   `venv` (Python virtual environment)
*   **Node.js & npm (Optional, for manual Web UI setup):**
    *   Node.js 18+
    *   npm 9+

## Getting Started

### Option 1: Docker (Recommended)

The simplest way to get started is by using Docker Compose, which automatically sets up both the API server and the web interface.

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/LLM-Answer-Watcher.git
    cd LLM-Answer-Watcher
    ```

2.  **Create an `.env` file:**
    Copy the example environment file and fill in your LLM API keys.
    ```bash
    cp .env.example .env
    ```
    Edit `.env` and replace `YOUR_GEMINI_API_KEY` with your actual API key.

3.  **Start the application:**
    ```bash
    docker-compose up --build
    ```
    To run in the background:
    ```bash
    docker-compose up -d --build
    ```

4.  **Access the application:**
    *   **Web UI:** `http://localhost:3000`
    *   **API:** `http://localhost:8000`

5.  **Stop the application:**
    ```bash
    docker-compose down
    ```

### Option 2: Manual Installation (Backend & Frontend)

This option provides more control but requires separate setup for the backend and frontend.

#### 1. Backend Setup

1.  **Clone the repository:**
    ```bash
    git clone https://github.com/your-username/LLM-Answer-Watcher.git
    cd LLM-Answer-Watcher
    ```

2.  **Create and activate a Python virtual environment:**
    ```bash
    python -m venv .venv
    # On Windows:
    .venv\Scripts\activate
    # On macOS/Linux:
    source .venv/bin/activate
    ```

3.  **Install Python dependencies:**
    ```bash
    pip install -r requirements.txt
    ```

4.  **Set up environment variables:**
    Copy `.env.example` to `.env` and populate your LLM API keys.
    ```bash
    cp .env.example .env
    ```
    Load these variables into your environment (e.g., `export GEMINI_API_KEY=AIza...` or use a tool like `python-dotenv`).

5.  **Run the backend API:**
    ```bash
    uvicorn llm_answer_watcher.api:app --host 0.0.0.0 --port 8000
    ```

#### 2. Frontend Setup

1.  **Navigate to the `web-ui` directory:**
    ```bash
    cd web-ui
    ```

2.  **Install Node.js dependencies:**
    ```bash
    npm install
    ```

3.  **Start the development server:**
    ```bash
    npm run dev
    ```
    The Web UI will typically be available at `http://localhost:5173` (or another port indicated by Vite).

## Configuration

Before running the watcher, you need to create and configure an analysis file.

1.  **Copy the example configuration:**
    ```bash
    cp examples/default.config.yaml my-config.yaml
    ```

2.  **Edit `my-config.yaml`:**
    Customize this file according to your needs. Key sections to configure include:

    *   **`run_settings`**:
        *   `output_dir`: Directory where results will be stored (e.g., `./output`).
        *   `sqlite_db_path`: Path to the SQLite database for historical data (e.g., `./output/watcher.db`).
        *   `models`: A list of LLM models to use. Each model requires a `provider` (e.g., `google`), `model_name` (e.g., `gemini-1.5-flash`), and `env_api_key` (the name of the environment variable containing your API key).

    *   **`brands`**:
        *   `mine`: A list of your brands and their aliases.
        *   `competitors`: A list of competitor brands.

    *   **`intents`**: A list of queries you want to monitor. Each intent has an `id` and a `prompt` (the query text).

    Example `my-config.yaml`:
    ```yaml
    run_settings:
      output_dir: "./output"
      sqlite_db_path: "./output/watcher.db"
      max_concurrent_requests: 10
      models:
        - provider: "google"
          model_name: "gemini-1.5-flash"
          env_api_key: "GEMINI_API_KEY"
          system_prompt: "google/default"

    brands:
      mine:
        - "MyBrand"
        - "MyProduct"
      competitors:
        - "CompetitorA"
        - "CompetitorB"

    intents:
      - id: "product-comparison"
        prompt: "What are the best tools for [category]?"
      - id: "purchase-decision"
        prompt: "Which [product type] should I choose?"
    ```

    Ensure your API keys are set as environment variables before running (e.g., `export GEMINI_API_KEY=AIza...`).

## Usage

After configuring `my-config.yaml`, you can run the LLM Answer Watcher:

```bash
llm-answer-watcher run --config my-config.yaml
```

The tool will execute queries against the defined LLM models, analyze their responses for brand mentions, and store the results.

### Exploring Results

Results are saved in the directory specified by `output_dir` (e.g., `./output`). Within this directory, you will find:

*   **`watcher.db`**: An SQLite database containing all collected data.
*   **HTML Reports**: An HTML file with a detailed report for each `run`.
*   **Web UI**: If you are running the Docker setup or have manually started the frontend, you can explore and analyze results through the interactive web interface.

## Project Structure

```
.
├── .github/                 # GitHub Actions workflows
├── .claude/                 # Claude agent configurations (if applicable)
├── config/                  # Configuration files
├── docs/                    # Project documentation (MkDocs)
├── examples/                # Example configuration files
├── llm_answer_watcher/      # Python backend application
│   ├── api.py               # FastAPI application entry point
│   ├── cli.py               # Command-Line Interface definitions
│   ├── config/              # Backend configuration logic
│   ├── llm_runner/          # LLM client implementations
│   ├── report/              # Report generation logic
│   ├── storage/             # Database interaction (SQLite)
│   └── ...
├── web-ui/                  # React/TypeScript frontend application
│   ├── src/                 # Frontend source code
│   ├── public/              # Static assets
│   ├── package.json         # Node.js dependencies
│   └── ...
├── tests/                   # Unit and integration tests
├── .env.example             # Example environment variables
├── Dockerfile               # Docker image definition
├── docker-compose.yml       # Docker Compose setup
├── requirements.txt         # Python dependencies
├── pyproject.toml           # Python project configuration (e.g., Ruff)
└── README.md                # This file
```

## Contributing

We welcome contributions to the LLM Answer Watcher! Please see our [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines on how to contribute, report bugs, and suggest features.

## License

This project is licensed under the [LICENSE](LICENSE) file.
>>>>>>> e6cc3f803a0dc91ef9f04c0504d746fd2dc57576
