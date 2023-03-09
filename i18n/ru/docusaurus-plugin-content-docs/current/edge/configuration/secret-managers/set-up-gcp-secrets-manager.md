---
id: set-up-gcp-secrets-manager
title: Настройка диспетчера секретов GCP
description: "Настройте диспетчер секретов GCP для Polygon Edge."
keywords:
  - docs
  - polygon
  - edge
  - gcp
  - secrets
  - manager
---

## Обзор {#overview}

В настоящее время Polygon Edge работает над тем, чтобы сохранять два основных секрета времени выполнения:
* **Приватный ключ валидатора**, который использует нод, при условии что нод является валидатором
* **Приватный ключ для сетевого взаимодействия**, который использует libp2p, для участия и общения с другими одноранговыми узлами

Подробнее см.  [Руководство по управлению приватными ключами](/docs/edge/configuration/manage-private-keys)

Модули Polygon Edge **не должны уметь хранить секреты**. В конечном итоге модуль не должен заботиться о том, хранится ли секрет на отдаленном сервере или локально на диске нода.

Все, что должен знать модуль о хранении секрета, — **это то, как использовать этот секрет**, **какие секреты получать или сохранять**. Более тонкие детали реализации этих операций делегируются `SecretsManager`, что, конечно же, является абстракцией.

Оператор нода, который запускает Polygon Edge, может теперь указывать, какой диспетчер секретов они хотят использовать, и, как только создается правильный диспетчер секретов, модули работают с секретами через указанный интерфейс, не заботясь о том, где хранятся секреты: на диске или на сервере.

В этой статье подробно описываются необходимые шаги для того, чтобы запустить Polygon Edge  вместе с [диспетчером секретов GCP](https://cloud.google.com/secret-manager).

:::info предыдущие руководства
**Настоятельно рекомендуется** перед тем, как перейти к этой статье, ознакомиться со статьями о [**локальной настройке**](/docs/edge/get-started/set-up-ibft-locally) и [**облачной настройке**](/docs/edge/get-started/set-up-ibft-on-the-cloud).
:::


## Предварительные условия {#prerequisites}
### Аккаунт для биллинга GCP {#gcp-billing-account}
Чтобы использовать диспетчер секретов GCP, пользователь должен включить [аккаунт для биллинга](https://console.cloud.google.com/) на портале GCP. Новым аккаунтам на платформе GCP предоставляется средства для начала работы в качестве бесплатной пробной версии. Подробнее о [документации GCP](https://cloud.google.com/free)

###  API диспетчера секретов {#secrets-manager-api}
Пользователю необходимо включить API диспетчера секретов GCP, перед тем как его использовать. Это можно сделать через [портал диспетчера секретов API](https://console.cloud.google.com/apis/library/secretmanager.googleapis.com). Подробнее: [настройка диспетчера секретов](https://cloud.google.com/secret-manager/docs/configuring-secret-manager)

### Учетные данные GCP {#gcp-credentials}
Наконец, пользователь должен создать новые учетные данные, которые будут использоваться для аутентификации. Это можно сделать путем выполнения инструкций, размещенных [здесь](https://cloud.google.com/secret-manager/docs/reference/libraries).    Сгенерированный файл json, содержащий учетные данные, должен быть передан каждому ноду, которому требуется использовать диспетчер секретов GCP.

Прежде чем продолжить, ознакомьтесь со следующей информацией:
* **Идентификатор проекта** (идентификатор проекта, определенный на платформе GCP)
* **Расположение файла с учетными данными** (путь к файлу json, в котором содержатся учетные данные)

## Шаг 1 — создание конфигурации диспетчера секретов {#step-1-generate-the-secrets-manager-configuration}

Чтобы Polygon Edge мог беспрепятственно взаимодействовать с GCP SM, ему необходимо проанализировать уже созданный файл конфигурации, который содержит всю необходимую информацию для хранения секретов на GCP SM.

Запустите следующую команду, чтобы создать конфигурацию:

```bash
polygon-edge secrets generate --type gcp-ssm --dir <PATH> --name <NODE_NAME> --extra project-id=<PROJECT_ID>,gcp-ssm-cred=<GCP_CREDS_FILE>
```

Присутствующие параметры:
* `PATH`— это путь, по которому должен экспортироваться файл конфигурации. По умолчанию `./secretsManagerConfig.json`
* `NODE_NAME` — это название текущего нода, для которого создается конфигурация GCP SM. Это может быть произвольное значение. По умолчанию `polygon-edge-node`
* `PROJECT_ID`— идентификатор проекта, который пользователь определил в консоли GCP во время настройки аккаунта и активации API диспетчера секретов.
* `GCP_CREDS_FILE` — это путь к файлу json, содержащий учетные данные, который позволит читать/записывать доступ к диспетчеру секретов.

:::caution Название нодов
Будьте внимательны, когда даете названия нодам.

Polygon Edge использует указанное название нодов для отслеживания тех секретов, которые он генерирует и использует на GCP SM. Указание названия существующего нода может вызвать невозможность записи секрета на GCP SM.

Секреты хранятся на следующем базовом пути: `projects/PROJECT_ID/NODE_NAME`
:::

## Шаг 2 — инициирование секретных ключей с помощью конфигурации {#step-2-initialize-secret-keys-using-the-configuration}

Теперь, когда файл конфигурации уже присутствует, мы можем инициализировать необходимые секретные ключи с помощью файла конфигурации, созданного в шаге 1, используя `--config`:

```bash
polygon-edge secrets init --config <PATH>
```

`PATH`Param — это местоположение ранее созданного диспетчера секретов param из шага 1.

## Шаг 3 — создание генезис-файла {#step-3-generate-the-genesis-file}

Генезис-файл должен быть создан аналогично руководствам по [**локальной настройке**](/docs/edge/get-started/set-up-ibft-locally) и [**облачной настройке**](/docs/edge/get-started/set-up-ibft-on-the-cloud) с незначительными изменениями.

Поскольку GCP SM используется вместо локальной файловой системы, адреса валидаторов должны быть добавлены через флаг `--ibft-validator`:
```bash
polygon-edge genesis --ibft-validator <VALIDATOR_ADDRESS> ...
```

## Шаг 4 — запуск клиента Polygon Edge {#step-4-start-the-polygon-edge-client}

Теперь, когда ключи созданы и генезис-файл генерируется, последний шаг для запуска всего процесса — это запуск Polygon Edge с помощью команды `server`.

Команда `server` используется так же, как и в вышеупомянутых руководствах, с незначительным дополнением — флаг `--secrets-config`:
```bash
polygon-edge server --secrets-config <PATH> ...
```

`PATH`Param — это местоположение ранее созданного диспетчера секретов param из шага 1.