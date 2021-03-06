# resources-api

## Build & Run

docker-compose
1. `docker-compose up -d --build`
2. go to `http://localhost:8000/swagger/` to see the list of available endpoints 

## Running tests

1. create virtual env 
   ```
   virtualenv --python=python3 .venv
   ```
2. install deps
   ```
   set -o pipefail \
   && curl -sSL https://raw.githubusercontent.com/sdispater/poetry/${POETRY_VERSION}/get-poetry.py > get-poetry.py \
   && python get-poetry.py --version 0.12.16 -y \
   && . ${HOME}/.poetry/env \
   && python -m venv /venv \
   && . /venv/bin/activate \
   && poetry install
   ```
 3. run tests
    ```
    ./src/manage.py test ./src
    ``` 

OR

Enter shell of running container with application and run `cd /app/src && ./manage.py test`

## Authorization

For authorization purposes JWT is used. 
New user can be registered via `/api/v1/auth/register` endpoint with email and password. `/api/v1/auth/register` and `/api/v1/auth/login` endpoints response with `access` token that is used to access non-public endpoints. One have to provide Authorization header: `Authorization: Bearer ...`.

#### Examples

Registration
```bash
 curl -X POST http://localhost:8000/api/v1/auth/register -H "Content-type:application/json" -d '{"email": "zas@zas.com", "password":"zaszaszas"}'
 
{"refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTU3MTk2MTg0MywianRpIjoiN2JlNjUyODgwNTMxNGM1MzgzNTg1NGJmMjhhYzE1ZjkiLCJ1c2VyX2lkIjoxfQ.2_qYToV63jTEDv5fdg6hwSEr1IqKV8xRfOijXn6yz1g", "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTcxODc1NzQzLCJqdGkiOiJhYzAxZDM0YjEwOWE0NDI2ODRhMTRhNmMxNDMxNjU5OCIsInVzZXJfaWQiOjF9.hMQmol_yEDVfMme4wo0JWOgPDEnBI5WR_pB7NaAXti0"}%
```  

Login
```bash
 curl -X POST http://localhost:8000/api/v1/auth/login -H "Content-type:application/json" -d '{"email": "zas@zas.com", "password": "zaszaszas"}'

{"refresh": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoicmVmcmVzaCIsImV4cCI6MTU3MTk2MjI0OCwianRpIjoiYzQ0MTFmZGJiZjNmNDY0NTgxNGFlMzE0NjkwNDI1YzgiLCJ1c2VyX2lkIjoxfQ.DWJ3PUM92wmAnHZmLekiQHN-xzIqeK6Xa-qMbrTRQv0", "access": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTcxODc2MTQ4LCJqdGkiOiI4MjM3ZDUwNTdkZjU0MjM3YWFjN2ZhMmJjMTQ4MGQ0OCIsInVzZXJfaWQiOjF9.nk66FHrcDBRW6qJaBD38sic_45uZAZXZroKJOOqN8w8"}%
```

Geting non-public info
```bash
 curl -X GET http://localhost:8000/api/v1/users/me -H "Content-type:application/json" -H "Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ0b2tlbl90eXBlIjoiYWNjZXNzIiwiZXhwIjoxNTcxODc2MTQ4LCJqdGkiOiI4MjM3ZDUwNTdkZjU0MjM3YWFjN2ZhMmJjMTQ4MGQ0OCIsInVzZXJfaWQiOjF9.nk66FHrcDBRW6qJaBD38sic_45uZAZXZroKJOOqN8w8" -d '{"first_name": "zas"}'

{"pk":1,"email":"zas@zas.com","first_name":"zas","last_name":""}%
```

## Swagger

Autogenerated swagger-ui can be accessed via `/swagger/` url. Json/yaml formatted swagger schemas can be found via `/swagger.(yaml|json)`.

## Admin permissions

Some endpoint can be accessed only by admin (`is_staff=True`) user. Now it can be created via `manage.py create_superuser` cmd inside docker container or by updating existing user in the corresponding table (`users_usermodel`).

Endpoints that have additional functionality with admin permissions:
* api/v1/users (CRUD) - users management
* api/v1/users/<id>/options - is used to set maximum number of user's resources (`qouta`)
* api/v1/resources - admin user can access all resources of any user, ordinary users can access only their resources
