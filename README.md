# FastAPI Book Management API

## Overview

This project is a RESTful API built with FastAPI for managing a book collection. It provides comprehensive CRUD (Create, Read, Update, Delete) operations for books with proper error handling, input validation, and documentation.

## Features

- 📚 Book management (CRUD operations)
- ✅ Input validation using Pydantic models
- 🔍 Enum-based genre classification
- 🧪 Complete test coverage
- 📝 API documentation (auto-generated by FastAPI)
- 🔒 CORS middleware enabled

## Project Structure

```
fastapi-book-project/
├── api/
│   ├── db/
│   │   ├── __init__.py
│   │   └── schemas.py      # Data models and in-memory database
│   ├── routes/
│   │   ├── __init__.py
│   │   └── books.py        # Book route handlers
│   └── router.py           # API router configuration
├── core/
│   ├── __init__.py
│   └── config.py           # Application settings
├── tests/
│   ├── __init__.py
│   └── test_books.py       # API endpoint tests
├── main.py                 # Application entry point
├── requirements.txt        # Project dependencies
└── README.md
```

## Technologies Used

- Python 3.12
- FastAPI
- Pydantic
- pytest
- uvicorn

## Installation

1. Clone the repository:

```bash
git clone https://github.com/hng12-devbotops/fastapi-book-project.git
cd fastapi-book-project
```

2. Create a virtual environment:

```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:

```bash
pip install -r requirements.txt
```

## Running the Application

1. Start the server:

```bash
uvicorn main:app
```

2. Access the API documentation:

- Swagger UI: http://localhost:8000/docs
- ReDoc: http://localhost:8000/redoc

## API Endpoints

### Books

- `GET /api/v1/books/` - Get all books
- `GET /api/v1/books/{book_id}` - Get a specific book
- `POST /api/v1/books/` - Create a new book
- `PUT /api/v1/books/{book_id}` - Update a book
- `DELETE /api/v1/books/{book_id}` - Delete a book

### Health Check

- `GET /healthcheck` - Check API status

## Book Schema

```json
{
  "id": 1,
  "title": "Book Title",
  "author": "Author Name",
  "publication_year": 2024,
  "genre": "Fantasy"
}
```

Available genres:

- Science Fiction
- Fantasy
- Horror
- Mystery
- Romance
- Thriller

## Running Tests

```bash
pytest
```

## Error Handling

The API includes proper error handling for:

- Non-existent books
- Invalid book IDs
- Invalid genre types
- Malformed requests

## Contributing

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/AmazingFeature`)
3. Commit changes (`git commit -m 'Add AmazingFeature'`)
4. Push to branch (`git push origin feature/AmazingFeature`)
5. Open a Pull Request

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Support

For support, please open an issue in the GitHub repository.

## CI/CD Pipeline and Deployment for FastAPI Project 🚀
## Overview
This guide explains how to set up a CI/CD pipeline for a FastAPI project using GitHub Actions and deploy it to an AWS EC2 instance using Nginx as a reverse proxy.

## Continuous Integration (CI) Pipeline 🔄
Objective:
Run automated tests on every pull request (PR) to the main branch.
Ensure the application is error-free before merging changes.
CI Pipeline Steps:
Trigger: Runs on every PR to the main branch.
Install dependencies (FastAPI, Uvicorn, Pytest, etc.).
Run automated tests using pytest.
Fail if tests do not pass to prevent bad code from being merged.
GitHub Actions CI Workflow (.github/workflows/ci.yml)
yaml

```bash
name: CI Pipeline

on:
  pull_request:
    branches:
      - main

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout code
        uses: actions/checkout@v3

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.10"

      - name: Install dependencies
        run: |
          python -m venv venv
          source venv/bin/activate
          pip install -r requirements.txt

      - name: Run Tests
        run: |
          source venv/bin/activate
          pytest
```

Expected Outcome:
The pipeline runs automatically when a PR is created.
If tests fail, merging is blocked until the issue is fixed.
2. Continuous Deployment (CD) Pipeline 🚀
Objective:
Deploy the FastAPI app automatically when changes are merged into main.
CD Pipeline Steps:
Trigger: Runs when a PR is merged into main.
Connect to the AWS EC2 instance via SSH.
Pull the latest code from GitHub.
Restart the FastAPI application using Uvicorn.
Restart Nginx to apply changes.
GitHub Actions CD Workflow (.github/workflows/deploy.yml)
yaml

```bash
name: CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to AWS EC2
        uses: appleboy/ssh-action@master
        with:
          host: ${{ secrets.AWS_HOST }}
          username: ubuntu
          key: ${{ secrets.AWS_PRIVATE_KEY }}
          script: |
            cd ~/fastapi-project  # Change to your project directory
            git pull origin main  # Pull latest changes
            source venv/bin/activate  # Activate virtual environment
            pip install -r requirements.txt  # Install dependencies
            sudo systemctl restart fastapi  # Restart FastAPI application
            sudo systemctl restart nginx  # Restart Nginx
```

1. Secrets Configuration (GitHub Actions)
2. Store sensitive information in GitHub Secrets:
3. AWS_HOST: Your EC2 Public IP
4. AWS_PRIVATE_KEY: Your EC2 private key (from .pem file)
