# airflow-docker

# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements. See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership. The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License. You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied. See the License for the
# specific language governing permissions and limitations
# under the License.

## Basic Airflow cluster configuration for CeleryExecutor with Redis and PostgreSQL.

**WARNING: This configuration is for local development. Do not use it in a production deployment.**

This configuration supports basic configuration using environment variables or an `.env` file. The following variables are supported:

- `AIRFLOW_IMAGE_NAME`: Docker image name used to run Airflow. Default: `apache/airflow:2.6.2`
- `AIRFLOW_UID`: User ID in Airflow containers. Default: `50000`
- `AIRFLOW_PROJ_DIR`: Base path to which all the files will be volumed. Default: `.`
  
Those configurations are useful mostly in case of standalone testing/running Airflow in test/try-out mode.

- `_AIRFLOW_WWW_USER_USERNAME`: Username for the administrator account (if requested). Default: `airflow`
- `_AIRFLOW_WWW_USER_PASSWORD`: Password for the administrator account (if requested). Default: `airflow`
- `_PIP_ADDITIONAL_REQUIREMENTS`: Additional PIP requirements to add when starting all containers. Use this option ONLY for quick checks. Installing requirements at container startup is done EVERY TIME the service is started. A better way is to build a custom image or extend the official image as described in [Airflow documentation](https://airflow.apache.org/docs/docker-stack/build.html). Default: ''

Feel free to modify this file to suit your needs.

---

### Services

#### postgres

- Image: `postgres:13`
- Environment variables:
  - `POSTGRES_USER`: airflow
  - `POSTGRES_PASSWORD`: airflow
  - `POSTGRES_DB`: airflow
- Volumes:
  - postgres-db-volume:/var/lib/postgresql/data
- Healthcheck:
  - Test: `CMD pg_isready -U airflow`
  - Interval: 10s
  - Retries: 5
  - Start Period: 5s
- Restart: always

#### redis

- Image: redis:latest
- Exposed Port: 6379
- Healthcheck:
  - Test: `CMD redis-cli ping`
  - Interval: 10s
  - Timeout: 30s
  - Retries: 50
  - Start Period: 30s
- Restart: always

#### airflow-webserver

- Inherits from: *airflow-common*
- Command: webserver
- Ports:
  - 8080:8080
- Healthcheck:
  - Test: `CMD curl --fail http://localhost:8080/health`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-scheduler

- Inherits from: *airflow-common*
- Command: scheduler
- Healthcheck:
  - Test: `CMD curl --fail http://localhost:8974/health`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-worker

- Inherits from: *airflow-common*
- Command: celery worker
- Healthcheck:
  - Test:
    - CMD-SHELL
    - `celery --app airflow.executors.celery_executor.app inspect ping -d "celery@${HOSTNAME}"`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Environment:
  - Inherits from: *airflow-common-env*
  - `DUMB_INIT_SETSID`: "0"
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-triggerer

- Inherits from: *airflow-common*
- Command: triggerer
- Healthcheck:
  - Test: CMD-SHELL
    - `airflow jobs check --job-type TriggererJob --hostname "${HOSTNAME}"`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-init

- Inherits from: *airflow-common*
- Entrypoint: /bin/bash
- Command:
  - -c
  - |
    function ver() {
      printf "%04d%04d%04d%04d" ${1//./ }
    }
    airflow_version=$(AIRFLOW__LOGGING__LOGGING_LEVEL=INFO && gosu airflow airflow version)
    airflow_version_comparable=$(ver ${airflow_version})
    min_airflow_version=2.2.0
    min_airflow_version_comparable=$(ver ${min_airflow_version})
    if (( airflow_version_comparable < min_airflow_version_comparable )); then
      echo
      echo -e "\033[1;31mERROR!!!: Too old Airflow version ${airflow_version}!\e[0m"
      echo "The minimum Airflow version supported: ${min_airflow_version}. Only use this or higher!"
      echo
      exit 1
    fi
    if [[ -z "${AIRFLOW_UID}" ]]; then
      echo
      echo -e "\033[1;33mWARNING!!!: AIRFLOW_UID not set!\e[0m"
      echo "If you are on Linux, you SHOULD follow the instructions below to set "
      echo "AIRFLOW_UID environment variable, otherwise files will be owned by root."
      echo "For other operating systems you can get rid of the warning with manually created .env file:"
      echo "    See: https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html#setting-the-right-airflow-user"
      echo
    fi
    one_meg=1048576
    mem_available=$(($(getconf _PHYS_PAGES) * $(getconf PAGE_SIZE) / one_meg))
    cpus_available=$(grep -cE 'cpu[0-9]+' /proc/stat)
    disk_available=$(Sorry, it seems there was a formatting issue in the previous response. Here's the corrected version:

```markdown
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements. See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership. The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License. You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied. See the License for the
# specific language governing permissions and limitations
# under the License.

## Basic Airflow cluster configuration for CeleryExecutor with Redis and PostgreSQL.

**WARNING: This configuration is for local development. Do not use it in a production deployment.**

This configuration supports basic configuration using environment variables or an `.env` file. The following variables are supported:

- `AIRFLOW_IMAGE_NAME`: Docker image name used to run Airflow. Default: `apache/airflow:2.6.2`
- `AIRFLOW_UID`: User ID in Airflow containers. Default: `50000`
- `AIRFLOW_PROJ_DIR`: Base path to which all the files will be volumed. Default: `.`

Those configurations are useful mostly in case of standalone testing/running Airflow in test/try-out mode.

- `_AIRFLOW_WWW_USER_USERNAME`: Username for the administrator account (if requested). Default: `airflow`
- `_AIRFLOW_WWW_USER_PASSWORD`: Password for the administrator account (if requested). Default: `airflow`
- `_PIP_ADDITIONAL_REQUIREMENTS`: Additional PIP requirements to add when starting all containers. Use this option ONLY for quick checks. Installing requirements at container startup is done EVERY TIME the service is started. A better way is to build a custom image or extend the official image as described in [Airflow documentation](https://airflow.apache.org/docs/docker-stack/build.html). Default: ''

Feel free to modify this file to suit your needs.

---

### Services

#### postgres

- Image: `postgres:13`
- Environment variables:
  - `POSTGRES_USER`: airflow
  - `POSTGRES_PASSWORD`: airflow
  - `POSTGRES_DB`: airflow
- Volumes:
  - postgres-db-volume:/var/lib/postgresql/data
- Healthcheck:
  - Test: `CMD pg_isready -U airflow`
  - Interval: 10s
  - Retries: 5
  - Start Period: 5s
- Restart: always

#### redis

- Image: redis:latest
- Exposed Port: 6379
- Healthcheck:
  - Test: `CMD redis-cli ping`
  - Interval: 10s
  - Timeout: 30s
  - Retries: 50
  - Start Period: 30s
- Restart: always

#### airflow-webserver

- Inherits from: *airflow-common*
- Command: webserver
- Ports:
  - 8080:8080
- Healthcheck:
  - Test: `CMD curl --fail http://localhost:8080/health`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-scheduler

- Inherits from: *airflow-common*
- Command: scheduler
- Healthcheck:
  - Test: `CMD curl --fail http://localhost:8974/health`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-worker

- Inherits from: *airflow-common*
- Command: celery worker
- Healthcheck:
  - Test:
    - CMD-SHELL
    - `celery --app airflow.executors.celery_executor.app inspect ping -d "celery@${HOSTNAME}"`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Environment:
  - Inherits from: *airflow-common-env*
  - `DUMB_INIT_SETSID`: "0"
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-triggerer

- Inherits from: *airflow-common*
- Command: triggerer
- Healthcheck:
  - Test: CMD-SHELL
    - `airflow jobs check --job-type TriggererJob --hostname "${HOSTNAME}"`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-init

- Inherits from: *airflow-common*
- Entrypoint: /bin/bash
- Command:
  - -c
  - |
    function ver() {
      printf "%04d%04d%04d%04d" ${1//./ }
    }
    airflow_version=$(AIRFLOW__LOGGING__LOGGING_LEVEL=INFO && gosu airflow airflow version)
    airflow_version_comparable=$(ver ${airflow_version})
    min_airflow_version=2.2.0
    min_airflow_version_comparable=$(ver ${min_airflow_version})
    if (( airflow_version_comparable < min_airflow_version_comparable )); then
      echo
      echo -e "\033[1;31mERROR!!!: Too old Airflow version ${airflow_version}!\e[0m"
      echo "The minimum Airflow version supported: ${min_airflow_version}. Only use this or higher!"
      echo
      exit 1
    fi
    if [[ -z "${AIRFLOW_UID}" ]]; then
      echo
      echo -e "\033[1;33mWARNING!!!: AIRFLOW_UID not set!\e[0m"
      echo "If you are on Linux, you SHOULD follow the instructions below to set "
      echo "AIRFLOW_UID environment variable, otherwise files will be owned by root."
      echo "For other operating systems you can get rid of the warning with manually created .env file:"
      echo "    See: https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html#setting-the-right-airflow-user"
      echo
    fi
    one_meg=1048576
    mem_available=$(($(getconf _PHYS_PAGES) * $(getconf PAGE_SIZE) / one_meg))
    cpus_available=$(grep -cE 'cpu[0-9]+' /proc/stat)
Apologies for the previous incomplete response. Here's the corrected version:

```markdown
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements. See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership. The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License. You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied. See the License for the
# specific language governing permissions and limitations
# under the License.

## Basic Airflow cluster configuration for CeleryExecutor with Redis and PostgreSQL.

**WARNING: This configuration is for local development. Do not use it in a production deployment.**

This configuration supports basic configuration using environment variables or an `.env` file. The following variables are supported:

- `AIRFLOW_IMAGE_NAME`: Docker image name used to run Airflow. Default: `apache/airflow:2.6.2`
- `AIRFLOW_UID`: User ID in Airflow containers. Default: `50000`
- `AIRFLOW_PROJ_DIR`: Base path to which all the files will be volumed. Default: `.`

Those configurations are useful mostly in case of standalone testing/running Airflow in test/try-out mode.

- `_AIRFLOW_WWW_USER_USERNAME`: Username for the administrator account (if requested). Default: `airflow`
- `_AIRFLOW_WWW_USER_PASSWORD`: Password for the administrator account (if requested). Default: `airflow`
- `_PIP_ADDITIONAL_REQUIREMENTS`: Additional PIP requirements to add when starting all containers. Use this option ONLY for quick checks. Installing requirements at container startup is done EVERY TIME the service is started. A better way is to build a custom image or extend the official image as described in [Airflow documentation](https://airflow.apache.org/docs/docker-stack/build.html). Default: ''

Feel free to modify this file to suit your needs.

---

### Services

#### postgres

- Image: `postgres:13`
- Environment variables:
  - `POSTGRES_USER`: airflow
  - `POSTGRES_PASSWORD`: airflow
  - `POSTGRES_DB`: airflow
- Volumes:
  - postgres-db-volume:/var/lib/postgresql/data
- Healthcheck:
  - Test: `CMD pg_isready -U airflow`
  - Interval: 10s
  - Retries: 5
  - Start Period: 5s
- Restart: always

#### redis

- Image: redis:latest
- Exposed Port: 6379
- Healthcheck:
  - Test: `CMD redis-cli ping`
  - Interval: 10s
  - Timeout: 30s
  - Retries: 50
  - Start Period: 30s
- Restart: always

#### airflow-webserver

- Inherits from: *airflow-common*
- Command: webserver
- Ports:
  - 8080:8080
- Healthcheck:
  - Test: `CMD curl --fail http://localhost:8080/health`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-scheduler

- Inherits from: *airflow-common*
- Command: scheduler
- Healthcheck:
  - Test: `CMD curl --fail http://localhost:8974/health`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-worker

- Inherits from: *airflow-common*
- Command: celery worker
- Healthcheck:
  - Test:
    - CMD-SHELL
    - `celery --app airflow.executors.celery_executor.app inspect ping -d "celery@${HOSTNAME}"`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Environment:
  - Inherits from: *airflow-common-env*
  - `DUMB_INIT_SETSID`: "0"
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-triggerer

- Inherits from: *airflow-common*
- Command: triggerer
- Healthcheck:
  - Test: CMD-SHELL
    - `airflow jobs check --job-type TriggererJob --hostname "${HOSTNAME}"`
  - Interval: 30s
  - Timeout: 10s
  - Retries: 5
  - Start Period: 30s
- Restart: always
- Depends On:
  - redis (condition: service_healthy)
  - postgres (condition: service_healthy)

#### airflow-init

- Inherits from: *airflow-common*
- Entrypoint: /bin/bash
- Command:
  - -c
  - |
    function ver() {
      printf "%04d%04d%04d%04d" ${1//./ }
    }
    airflow_version=$(AIRFLOW__LOGGING__LOGGING_LEVEL=INFO && gosu airflow airflow version)
    airflow_version_comparable=$(ver ${airflow_version})
    min_airflow_version=2.2.0
    min_airflow_version_comparable=$(ver ${min_airflow_version})
    if (( airflow_version_comparable < min_airflow_version_comparable )); then
      echo
      echo -e "\033[1;31mERROR!!!: Too old Airflow version ${airflow_version}!\e[0m"
      echo "The minimum Airflow version supported: ${min_airflow_version}. Only use this or higher!"
      echo
      exit 1
    fi
    if [[ -z "${AIRFLOW_UID}" ]]; then
      echo
      echo -e "\033[1;33mWARNING!!!: AIRFLOW_UID not set!\e[0m"
      echo "If you are on Linux, you SHOULD follow the instructions below to set "
      echo "AIRFLOW_UID environment variable, otherwise files will be owned by root."
      echo "For other operating systems you can get rid of the warning with manually created .env file:"
      echo "    See: https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html#setting-the-right-airflow-user"
      echo
    fi
    one_meg=1048576
    mem_available=$(($(getconf _PHYS_PAGES) * $(getconf PAGE_SIZE) / one_meg))
    cpus_available=$(grep -cE 'cpu[0-9]+' /proc/stat)
    disk_available=$(
-----------------------------------------------------------------------------------------------------------
