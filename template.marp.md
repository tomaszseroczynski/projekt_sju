---
marp: true
math: mathjax
title: Raport z projektu SJU
size: 16:9
paginate: true
transition: fade
backgroundImage: url("page-background.png")
footer: "**Raport SJU**"
style: |
  @import url('https://unpkg.com/tailwindcss@^2/dist/utilities.min.css');
---
# 
<div class="flex flex-col items-center justify-center h-full text-center">

<img src="https://upload.wikimedia.org/wikipedia/commons/3/34/Logo_PolSl.svg" class="w-60" />

#### Raport z projektu

###### Tomasz Seroczyński 

</div>

---
# 1. Przygotowanie repozytorium

- Wykonałem fork repozytorium na własne konto GitHub.
- Otworzyłem repozytorium w Codespaces.
- Zbudowałem obraz Docker:
  ```
  docker build -t sjuprojekt .
  ```
- Uruchomiłem kontener i zweryfikowałem widoczność plików projektu.

---

# 2. Modyfikacja Dockerfile

- Zainstalowałem rozszerzenie Docker w Codespaces.
- Dodałem do Dockerfile polecenie instalujące wymagane pakiety:
  ```
  RUN pip install --no-cache-dir qiskit matplotlib pillow pycryptodomex cryptography
  ```
---

# 2. Modyfikacja Dockerfile cd.

- Konfiguracja Dockerfile:
  ```
  ARG VARIANT=3.12-bullseye
  FROM mcr.microsoft.com/vscode/devcontainers/python:${VARIANT}
  RUN pip3 install --disable-pip-version-check --no-cache-dir ipykernel jupyter
  RUN pip install --no-cache-dir qiskit matplotlib pillow pycryptodomex cryptography

  COPY test.py /home/vscode/workspace/test.py

  USER vscode

  RUN mkdir -p /home/vscode/workspace
  ```
- Ponownie zbudowałem obraz i sprawdziłem, czy proces przebiega bez błędów.

---

# 3. Konfiguracja devcontainer

- Utworzyłem plik `.devcontainer/devcontainer.json` z poniższą konfiguracją:
  ```
  {
    "workspaceMount": "source=${localWorkspaceFolder},target=/home/vscode/workspace,type=bind,consistency=cached",
    "workspaceFolder": "/home/vscode/workspace",
    "name": "Projekt-SJU",
    "image": "sjuprojekt",
    "customizations": {
      "vscode": {
        "extensions": ["ms-python.python", "ms-toolsai.jupyter"]
      }
    },
    "postCreateCommand": "pip install --no-cache-dir -r requirements.txt && uname -a && python --version && pip --version",
    "remoteUser": "vscode"
  }
  ```
- Przebudowałem i uruchomiłem kontener developerski.

---

# 4. GitHub Actions: budowa i publikacja obrazu

- Utworzyłem workflow `.github/workflows/docker-publish.yml`:
  - Konfiguracja workflow:
    ```
    name: Build and Publish Docker Image

    on:
      push:
        tags:
          - "*"
      workflow_dispatch:

    env:
      REGISTRY: ghcr.io
      IMAGE_NAME: ${{ github.repository }}

---
# 4. GitHub Actions: budowa i publikacja obrazu cd.
    jobs:
      build-and-push:
        runs-on: ubuntu-latest
        
        permissions:
          contents: read
          packages: write

        steps:
          - name: Checkout repository
            uses: actions/checkout@v4

          - name: Set up Docker Buildx
            uses: docker/setup-buildx-action@v3

          - name: Build Docker image
            run: docker build -t sjuprojekt .

          - name: Run test script inside container
            run: |
              docker run --rm sjuprojekt python /home/vscode/workspace/test.py

          - name: Log in to GitHub Container Registry
            uses: docker/login-action@v3
            with:
              registry: ${{ env.REGISTRY }}
              username: ${{ github.actor }}
              password: ${{ secrets.GITHUB_TOKEN }}

          - name: Tag Docker image
            run: |
              docker tag sjuprojekt ${{ env.REGISTRY }}/${{ github.repository }}:${{ github.ref_name }}
              docker tag sjuprojekt ${{ env.REGISTRY }}/${{ github.repository }}:latest

          - name: Push Docker image
            run: |
              docker push ${{ env.REGISTRY }}/${{ github.repository }}:${{ github.ref_name }}
              docker push ${{ env.REGISTRY }}/${{ github.repository }}:latest

    ```
---
# 4. GitHub Actions: budowa i publikacja obrazu cd.

- Workflow automatyzuje budowę, testowanie i publikację obrazu do rejestru ghcr.io.
  
- W pliku `.devcontainer/devcontainer.json` zaktulizowałem wpis:
  `"image": "ghcr.io/tomaszseroczynski/projekt_sju:latest",`

---

# 5. Testowanie obrazu

- Dodałem plik `test.py` w katalogu głównym repozytorium.

- Test wykonywany był w workflow przed publikacją obrazu.

---

# 6. Praca z notatnikiem Jupyter

- Utworzyłem katalog `sample` i dodałem notatnik `.ipynb`.
- Otworzyłem notatnik w VS Code.
- Wybrałem jądro Python i uruchomiłem komórki.
- Zweryfikowałem poprawność działania kodu.

---


# Podsumowanie i refleksje

- Wszystkie etapy projektu przebiegły poprawnie i zgodnie z założeniami.
- Środowisko developerskie, automatyzacja CI/CD oraz testowanie obrazu działały bez zarzutu.
- Projekt pozwolił mi w praktyce poznać nowoczesne narzędzia chmurowe i zautomatyzowane procesy CI/CD, które znacząco usprawniają pracę zespołową i rozwój oprogramowania.