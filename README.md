### Artillery Performance & API Tests

This project contains Artillery scenarios for API testing, including basic CRUD examples and credentialed login flows fed by a CSV dataset.

### Prerequisites

- **Node.js** 16+ (recommend 18+)
- **npm** 8+

### Install

```bash
npm install
```

### Environment configuration

Artillery config reads the target and headers from environment variables via `config/common.yml`.

Create a `.env` file in the project root (`Artillery/.env`) with at least:

```dotenv
BASE_URL=https://reqres.in/api
API_KEY=replace-with-api-key-if-required
```

Notes:

- `BASE_URL` is required by `config/common.yml` for `config.target`.
- `API_KEY` is added as `x-api-key` header for all requests. If your target doesn’t require it, you can omit the variable or leave it blank; adjust `config/common.yml` if needed.

### Project layout

- `config/common.yml` — shared target, phases, and default headers
- `login-test.yml` — logs in users from `datasets/users.csv` and expects a `token`
- `create-user-test.yml` — creates a user with random data and expects an `id`
- `basic/tests/*.yml` — simple CRUD flows using `/users` endpoints
- `datasets/users.csv` — CSV with columns `email,password` used by `login-test.yml`

### How to run

You can run scenarios either through the npm script (which loads `.env`) or directly with `npx`.

Using npm script (preferred):

```bash
# Append the scenario file after -- to pass it to artillery
npm run test -- login-test.yml
npm run test -- create-user-test.yml
npm run test -- basic/tests/get-users.yml
npm run test -- basic/tests/create-user.yml
npm run test -- basic/tests/update-user.yml
npm run test -- basic/tests/delete-user.yml
```

Directly with npx (equivalent):

```bash
npx dotenv -e .env -- artillery run --config config/common.yml login-test.yml
```

### Dataset format

`login-test.yml` expects `datasets/users.csv` with the following header row and fields:

```csv
email,password
user1@example.com,Secret123!
user2@example.com,AnotherSecret!
```

### Useful options

- Save report output to a file:

```bash
npm run test -- login-test.yml --output results.json
```

- Increase duration or arrival rate: edit `phases` in `config/common.yml`.
- Change default headers: edit `defaults.headers` in `config/common.yml`.

### Endpoints used by scenarios

- `login-test.yml`: `POST /api/login`
- `create-user-test.yml`: `POST /api/users`
- `basic/tests/get-users.yml`: `GET /users?page=2`
- `basic/tests/create-user.yml`: `POST /users`
- `basic/tests/update-user.yml`: `PUT /users/2`
- `basic/tests/delete-user.yml`: `DELETE /users/2`

Adjust paths to match your API if they differ.

### Troubleshooting

- Missing `BASE_URL` or wrong host → requests fail immediately. Set `BASE_URL` in `.env`.
- 401/403 errors → ensure `API_KEY` (or remove the header from `config/common.yml`).
- CSV errors → verify `datasets/users.csv` exists and columns are `email,password`.
