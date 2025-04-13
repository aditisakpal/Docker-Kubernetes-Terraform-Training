
## Web Scraping in Jupyter with Docker + SQLite

This project scrapes data from the web using Python (`requests` + `BeautifulSoup`), stores it in a local SQLite database, and runs inside a Docker container with Jupyter Notebook.

---

##  Project Structure

```
py-docker/
├── Dockerfile
├── requirements.txt
├── scraping.ipynb
└── scraped_data.db      ← Created automatically when you run the notebook
```

---

##  Getting Started

### 1. Clone or Download the Repo

```bash
git clone https://github.com/your-username/py-docker.git
cd py-docker
```

Or just download the folder directly.

---

### 2. Build the Docker Image

Make sure Docker Desktop is running.

```bash
docker build -t py-jupyter-notebook .
```

---

### 3. Run the Container and Open Jupyter Notebook

#### On Windows (CMD):

```bash
docker run -p 8888:8888 -v "%cd%":/home/jovyan/work py-jupyter-notebook
```

#### On macOS/Linux:

```bash
docker run -p 8888:8888 -v "$(pwd)":/home/jovyan/work py-jupyter-notebook
```

Copy the token link from the terminal into your browser.

---

##  What the Notebook Does

- Sends HTTP requests to a website
- Parses HTML with BeautifulSoup
- Extracts relevant data (titles, links, etc.)
- Saves the data into an SQLite database: `scraped_data.db`

---

##  Tech Stack

- Python 3
- Jupyter Notebook
- SQLite (via Python’s built-in `sqlite3`)
- Docker
- Libraries:
  - `requests`
  - `beautifulsoup4`

---

##  Database

The database is stored as `scraped_data.db` in the working directory and is created automatically by the notebook if it doesn’t exist.

You can open the `.db` file using any SQLite viewer or directly in Python.

---

##  Notes

- No need to install `sqlite3` in `requirements.txt` – it comes with Python.
- `re` is also built-in, so don’t include it in the requirements file.
- Only install what’s needed via pip (e.g. `requests`, `beautifulsoup4`).

---

##  Contact

If you have questions or suggestions, feel free to open an issue or reach out!

---



