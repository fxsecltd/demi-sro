# DeMI-SRO Consortium
### Decentralized Mutual Insurance Self-Regulated Organization
Custom Infrastructure Protocol for Alternative Payment Providers (APPs) in South and Southeast Asia.

[Official Website](https://demi-sro.org) | [IETF Draft](https://demi-sro.org)

---

## ENGLISH VERSION (EN)

### Overview
DeMI-SRO is an international, open-source fintech consortium co-founded by **Sole Proprietor Shubralov E.A.** and **"AI Cybersecurity" LLC**. The alliance establishes an extraterritorial, decentralized self-regulated framework (Blockchain-SRO) to eliminate time-delayed fraud vectors and liquidity gaps within alternative payment processing markets (India, Pakistan, Bangladesh, Vietnam, Cambodia).

The technical core runs on **Ethereum Layer 1**, establishing a Decentralized Compensation Fund governed strictly by smart contracts ("Code is Law").

### Git-Based Corporate Governance (Zero Bureaucracy)
In accordance with `GOVERNANCE_EN.md`, this consortium waives all paper document requirements. Membership management is fully handled via cryptographically signed internal branches and **Pull Requests (PR)** within this master repository. 

*To preserve the integrity of the Linear Audit Trail, the use of external forks is strictly prohibited. All actions must happen inside this single ledger.*

* **Joining:** Submitting a PR adding your entity to `PARTICIPANTS.json` constitutes an explicit expression of intent to accept the Adhesion Agreement. Membership becomes active once the Founders merge the PR.
* **Exiting:** Submitting a PR removing your entity or changing status to `Withdrawn` constitutes a unilateral termination of membership.

### Quick Start: How to Join via Git

Every commit modifying `PARTICIPANTS.json` **MUST** be cryptographically signed using a PGP or SSH key to be legally binding.

#### 1. Configure Your Cryptographic Signature
Ensure your local Git client is linked to your PGP key:
```bash
# Set your signing key ID
git config --global user.signingkey 3AA5C34371567BD2

# Enable mandatory signing for all commits
git config --global commit.gpgsign true
```

#### 2. Checkout and Edit the Ledger
Do not fork. Clone the master repository directly, create a local branch inside it, and append your organization's metadata block into the `associated_participants` array within `PARTICIPANTS.json`.
```bash
# Clone the master consortium repository directly
git clone https://github.com
cd consortium-core

# Create a dedicated local branch for your organization
git checkout -b joinder-[your-org-name]
```

#### 3. Commit and Push Directly
Commit the changes using the `-S` flag to enforce cryptographic verification and push the branch straight back to the master repository:
```bash
git add PARTICIPANTS.json
git commit -S -m "feat: request joinder for [Your Organization Name]"
git push origin joinder-[your-org-name]
```
Open a **Pull Request** targeting our `main` branch. Once our automated CI/CD pipelines validate the cryptographic signature and the Founders approve the merger, your membership in the SRO becomes active.

---

## РУССКАЯ ВЕРСИЯ (RU)

### Обзор проекта
DeMI-SRO — это международный финтех-консорциум с открытым исходным кодом, созданный совместно **Индивидуальным предпринимателем Шубраловым Е. А.** и **ООО «ИИ Кибербезопасность»**. Альянс формирует экстерриториальный децентрализованный контур саморегулирования (Блокчейн-СРО) для ликвидации рисков отложенного фрода и кассовых разрывов на рынках альтернативных платежных систем (Индия, Пакистан, Бангладеш, Вьетнам, Камбоджа).

Техническое ядро развернуто в сети **Ethereum L1** в виде смарт-контракта Децентрализованного компенсационного фонда, функционирующего по принципу «Код есть Закон».

### Управление членством через Git (Ноль бюрократии)
В соответствии с регламентом `GOVERNANCE_RU.md`, консорциум полностью отказывается от бумажного документооборота. Управление составом участников осуществляется исключительно через создание внутренних веток и механизмы **Pull Requests (PR)** внутри данного репозитория.

*В целях сохранения целостности линейного аудиторского следа (Linear Audit Trail) использование внешних форков строго запрещено. Все действия должны происходить внутри единого репозитория-реестра.*

* **Вступление:** Направление PR с добавлением вашей организации в файл `PARTICIPANTS.json` является прямым и безоговорочным выражением воли на акцепт договора присоединения. Членство активируется в момент слияния (Merge) вашего PR Учредителями.
* **Выход:** Направление PR, удаляющего ваши реквизиты, является официальным уведомлением об одностороннем расторжении договора и выходе из СРО.

### Инструкция: Как вступить в консорциум через консоль

Каждый коммит, изменяющий список участников, **ОБЯЗАН** быть подписан PGP- или SSH-ключом уполномоченного лица организации.

#### 1. Настройка криптографической подписи в Git
Свяжите локальный Git-клиент с вашим PGP-ключом:
```bash
# Укажите ID вашего ключа подписи
git config --global user.signingkey 3AA5C34371567BD2

# Включите обязательное подписание всех коммитов
git config --global commit.gpgsign true
```

#### 2. Чекаут оригинального репозитория
Не делайте форк. Клонируйте оригинальный репозиторий напрямую, создайте локальную ветку внутри него и добавьте блок с реквизитами вашей организации в массив `associated_participants` внутри `PARTICIPANTS.json`.
```bash
# Клонируйте оригинальный репозиторий консорциума напрямую
git clone https://github.com
cd consortium-core

# Создайте целевую локальную ветку для вашей организации
git checkout -b joinder-[название-организации]
```

#### 3. Фиксация изменений и прямая отправка веток
Закоммитьте изменения с флагом `-S`, который отвечает за генерацию криптографической подписи, и отправьте ветку напрямую в репозиторий консорциума:
```bash
git add PARTICIPANTS.json
git commit -S -m "feat: запрос на вступление от [Наименование Организации]"
git push origin joinder-[название-организации]
```
Перейдите в веб-интерфейс основного репозитория консорциума на GitHub и создайте **Pull Request** из вашей отправленной ветки в ветку `main`. После того как автоматические тесты CI/CD верифицируют подпись коммита, а Учредители одобрят слияние, ваша организация становится участником Блокчейн-СРО.

---

## Supporting DeMI-SRO / Спонсорство и поддержка проекта

Following the best practices of global open-source and financial ecosystems, DeMI-SRO provides tiered visibility for organizations driving alternative payment security forward. 

В соответствии с лучшими международными практиками глобальных open-source и финансовых экосистем, консорциум DeMI-SRO предлагает систему спонсорских статусов для организаций, активно развивающих безопасность альтернативных платежей.

### Sponsorship Tiers / Уровни поддержки:
* 👑 **Core Infrastructure Partners** — Entities hosting local National Embassy Nodes and backing network consensus.
* 🥇 **Platinum Sponsors** — Organizations providing substantial liquidity to the Decentralized Compensation Pool.
* 🥈 **Gold Sponsors** — Academic, legal, and cybersec institutions driving IETF standard iterations.

[Become a Sponsor / Стать спонсором](https://demi-sro.org)

---

## Consortium Contributors / Участники и Контрибьюторы
All entities that successfully complete the Git joinder process will be permanently displayed in the repository's official **Contributors** section. This serves as public, cryptographically verifiable proof of your organization's alignment with international alternative payment security standards.

Все организации, успешно прошедшие процедуру присоединения через Git, навсегда фиксируются в официальном разделе **Contributors** (Контрибьюторы) данного репозитория. Это является публичным, криптографически верифицируемым доказательством соответствия вашей организации международным стандартам безопасности альтернативных платежей.

---

## Repository Structure / Структура репозитория
* `draft-shubralov-demi-sro-payment-security.md` — Исходный код IETF Internet-Draft (синтаксис kramdown-rfc2629).
* `openapi.yaml` — Спецификация интерфейсов Embassy Node в формате OpenAPI 3.0 (Swagger) с поддержкой mTLS и кодов ошибок ERC-20 Allowance.
* `PARTICIPANTS.json` — Immutable ledger of SRO members / Неизменяемый реестр и иерархия участников консорциума.
* `GOVERNANCE_RU.md` — Регламент цифрового членства и правила Git-управления (Русская версия).
* `GOVERNANCE_EN.md` — Digital joinder, exit rules, and Git-governance policies (English version).
* `index.html` — Двуязычная главная страница веб-сайта проекта на GitHub Pages.
