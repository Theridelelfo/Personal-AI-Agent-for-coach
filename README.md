# Personal-AI-Agent-for-coach
Progetto per Agent AI usando Gemma su colab
[![Open In Colab](https://colab.research.google.com/assets/colab-badge.svg)](https://colab.research.google.com/github/Theridelelfo/Personal-AI-Agent-for-coach/blob/main/launcher.ipynb)


## 🎯 Obiettivo

Costruire un **Agent AI modulare** e **riproducibile** che:

# La parte sottostante è da verificare

- esegue **inferenza** e **fine‑tuning leggero (LoRA/PEFT)** su modelli open (es. Gemma),
- funziona **subito** in Google **Colab** (senza setup complesso),
- separa le responsabilità in **moduli** (es. `inference`, `finetune`) e **micromoduli** riusabili (`drive`, `db_sqlite`, `utils`),
- supporta **configurazioni YAML** per cambiare modello, prompt, dataset e iper‑parametri senza toccare il codice,
- salva **output, checkpoint e log** in una directory **persistente** (Google Drive) per non perdere i risultati tra una sessione e l’altra.

L’obiettivo è offrirti un’ossatura pulita per **sperimentare rapidamente** (PoC) e poi **scalare**: aggiungi moduli (`evaluate`, `rag`, `serve`) mantenendo la stessa interfaccia e la stessa orchestrazione.

---

## 🧭 Panoramica

Questo repository espone:
- **`colab_bootstrap.py`** – inizializza l’ambiente Colab: monta Google Drive, clona/aggiorna il repo, installa le dipendenze, imposta variabili e `PYTHONPATH`.
- **`orchestrator.py`** – punto d’ingresso unico che indirizza l’esecuzione verso i moduli registrati:
  - `--task inference --config configs/inference.yaml`
  - `--task finetune  --config configs/finetune.yaml`
- **`modules/`** – implementazioni delle funzionalità principali:
  - `inference.py`: inferenza con Hugging Face Transformers (streaming, salvataggio output, log run).
  - `finetune.py`: fine‑tuning **LoRA/PEFT** su dataset testuali, pensato per GPU Colab.
- **`micromodules/`** – servizi trasversali e riusabili:
  - `drive.py`: gestione della directory **persistente** su Drive via variabile `AI_AGENT_DATA`.
  - `db_sqlite.py`: **logging dei run** (task, modello, timestamp, note) in un database SQLite.
  - `utils.py`: utilità (logging, loader YAML, check GPU).
- **`configs/`** – file **YAML** per parametrizzare sperimenti:
  - `inference.yaml`: modello, prompt, limiti token.
  - `finetune.yaml`: modello, dataset, batch, LR, epoche, accumulo gradiente.

### Perché questa architettura
- **Modularità** – ogni capability vive in un modulo con firma uniforme `run(cfg, data_dir)`.
- **Orchestrazione semplice** – un solo comando lancia compiti diversi; puoi schedulare step in notebook o script esterni.
- **Portabilità** – funziona in Colab ma non è legato a Colab: il codice è standard Python.
- **Tracciabilità** – ogni esecuzione lascia tracce (file di output + riga in SQLite) utili per auditing e confronto tra esperimenti.
- **Estendibilità** – aggiungere `evaluate.py`, `rag.py`, `serve.py` o sostituire `db_sqlite` con PocketBase/Postgres richiede minime modifiche.

> **Non‑obiettivi (per ora)**: addestramento full‑fine‑tuning su LLM di grandi dimensioni; orchestrazioni distribuite multi‑nodo; UI grafica completa. Questi aspetti possono essere introdotti in step successivi.

