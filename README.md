<!-- LTeX: language=pl_PL -->

# Punkt startowy projektu Sieć Jako Usługa

## Co z tym zrobić

- Załóż konto na GitHub skojarzone z Twoim uczelnianym adresem e-mail.
- Wykonaj **Fork** tego repozytorium na Swoje konto.
- Sprawdź czy obraz Dockera się poprawnie buduje:
  - otwórz repozytorium w Codespaces,
  - Uruchom terminal. W terminalu wydaj komendę
    ```bash
    docker build -t sjuprojekt .
    ```
  - Jeżeli budowanie zakończył się sukcesem, możesz przystąpić do modyfikacji `Dockerfile`.
- Zacznij od instalacji rozszerzenia `Docker`
- W `Dockerfile` dodaj instalację wymaganych pakietów. Sprawdzaj czy obraz się buduje. Obrazami możesz zarządzać za pomocą zainstalowanego rozszerzenia.
- Kiedy wszystko jest gotowe, wykonaj `Commit`, co zapisze zmiany w Twoim repozytorium.
- Przejdź do `My Codespaces`. Zatrzymaj, a najlepiej skasuj uruchomioną `codespace`.

## Github Actions

## License

[GNU General Public License v3.0](LICENSE)

This license ensures that all modifications to the software remain open source, prohibiting the creation of closed-source derivatives.
