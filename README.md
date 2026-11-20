🚀 CI/CD Demo App (Python)
![CI](https://github.com/ThisIsMikeyS/cicd-demo-app/actions/workflows/python-app.yml/badge.svg)
![Coverage](https://img.shields.io/badge/coverage-100%25-brightgreen.svg)
![Lint](https://img.shields.io/badge/lint-passing-brightgreen.svg)
![Last Commit](https://img.shields.io/github/last-commit/ThisIsMikeyS/cicd-demo-app)
![codecov](https://codecov.io/gh/ThisIsMikeyS/cicd-demo-app/branch/main/graph/badge.svg)

A minimal Python project to demonstrate continuous integration (CI) and code quality enforcement using GitHub Actions, `unittest`, `coverage.py`, and `flake8`.
---
✨ Features
✅ Basic Python calculator module (`add`, `subtract`, `multiply`, `divide`)
🧪 Unit-tested with `unittest`
📊 Coverage reporting via `coverage.py`
🧼 Linting enforced using `flake8` for code quality
🔁 CI pipeline with GitHub Actions
🛠 Compatible with Visual Studio 2022 on Windows 11
---
📁 Project Structure
```
CICDDemoApp/
├── src/
│   └── calculator.py            # Application logic
├── tests/
│   └── test_calculator.py       # Unit tests
├── .github/
│   └── workflows/
│       └── python-app.yml       # CI/CD pipeline config
├── .coveragerc                  # Coverage config
├── .flake8                      # Flake8 linting config
├── requirements.txt             # Project dependencies
├── README.md                    # Project documentation
├── LICENSE                      # MIT License
```
---
🧪 Running the Tests Locally
```bash
# Run unit tests
python -m unittest discover -s tests

# Run tests with coverage
coverage run -m unittest discover -s tests
coverage report
```
---
🧼 Lint Check (flake8)
```bash
flake8 src tests
```
---
⚙️ CI/CD Pipeline (GitHub Actions)
This repo includes a GitHub Actions workflow that:
Runs on `push` and `pull_request` to `main`
Installs Python 3.10
Installs dependencies from `requirements.txt`
Runs `flake8` for code linting
Runs `unittest` with coverage tracking
Fails the build if linting or tests fail
Workflow location:
```
.github/workflows/python-app.yml
```
---
🚀 Getting Started (Development)
Prerequisites
Python 3.10+
GitHub account
(Optional) Visual Studio 2022 with Python development tools
Clone and Run
```bash
git clone https://github.com/ThisIsMikeyS/cicd-demo-app.git
cd cicd-demo-app
python src/calculator.py
```
---
🔧 Jenkins Pipeline (Практическая работа № 2)
В корне репозитория находится `Jenkinsfile` (Declarative Pipeline). Он получает код из Git, проверяет структуру проекта, запускает unit-тесты, собирает архив `app.tar.gz` и сохраняет его как артефакт сборки Jenkins. Тот же скрипт нужно вставить в Jenkins Job типа Pipeline (вариант Pipeline script), чтобы ссылка на репозиторий и ветка действительно менялись при запуске.
Параметры запуска
Параметр	Тип	Значение по умолчанию	Назначение
`REPO_URL`	String	`https://github.com/Mahm-lgtm/cicd-demo-app`	HTTPS-ссылка на репозиторий (Fork), который нужно собрать
`GIT_BRANCH`	String	`master`	Имя существующей ветки, которую нужно собрать
`RUN_TESTS`	Boolean	`true`	`true` — выполнять unit-тесты, `false` — пропустить стадию Test
Стадии Pipeline
Checkout — получает код по `REPO_URL` и `GIT_BRANCH`, выводит в Console Output имя ветки и полный SHA коммита.
Validate — проверяет наличие `README.md`, `requirements.txt`, `src/` и `tests/`; при отсутствии любого из них сборка завершается ошибкой.
Test — выполняет `python3 -m unittest discover -s tests -v`. При `RUN_TESTS=false` стадия пропускается, остальные стадии выполняются. Если тест падает, сборка получает статус FAILURE, а Build и Archive не выполняются.
Build — создаёт `build-info.txt` (номер сборки Jenkins, SHA коммита, ветка) и архив `app.tar.gz` с `src/`, `tests/`, `README.md`, `requirements.txt` и `build-info.txt`.
Archive — проверяет содержимое `app.tar.gz` и сохраняет `app.tar.gz` (и отдельно `build-info.txt`) в Jenkins Artifacts.
Блок `post` пишет в лог сообщение об успешном или неуспешном завершении и очищает рабочую область (`cleanup`).
Запуск тестов локально
```bash
python3 -m unittest discover -s tests -v
```
Как воспроизвести сценарии
Ветка `broken-tests` создана от `master` и отличается одним изменением: в `tests/test_calculator.py` в тесте `test_multiply` ожидаемое значение изменено с `21` на `22` (`3 * 7` равно `21`), поэтому тест намеренно падает. Исходный код в `src/` не менялся. Локально:
```bash
git checkout broken-tests
python3 -m unittest discover -s tests -v   # FAILED (failures=1)
git checkout master
python3 -m unittest discover -s tests -v   # OK
```
Сценарии в Jenkins («Build with Parameters»):
№	`GIT_BRANCH`	`RUN_TESTS`	Ожидаемый результат
1	`master`	`true`	SUCCESS, все стадии выполнены
2	`master`	`false`	SUCCESS, стадия Test пропущена, архив создан
3	`broken-tests`	`true`	FAILURE на стадии Test, Build и Archive пропущены
4	`master`	`true`	SUCCESS (восстановление после сбоя)
В каждом случае `REPO_URL` — ссылка на этот Fork. Для Jenkins требуется Linux-агент с установленными Git и Python 3.
---
🧑‍💻 Author
Michael Saunders  
Freelance Software & Systems Developer | Technical Writer  
LinkedIn · GitHub
---
📚 License
This project is licensed under the MIT License – see the `LICENSE` file for details.
