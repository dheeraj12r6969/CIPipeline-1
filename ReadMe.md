Step 1: Create the Flask App
Create the Project Directory:

Open Command Prompt or PowerShell and create a new project folder:
bash
Copy code
mkdir flask-app
cd flask-app
Create the Flask Application:

Inside the flask-app directory, create a Python file named app.py with the following content:
python
Copy code
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return "Hello, World!"

if __name__ == '__main__':
    app.run(debug=True, host='0.0.0.0')
Set Up the Virtual Environment:

In PowerShell or Command Prompt, run the following commands to set up a virtual environment:
bash
Copy code
python -m venv venv
.\venv\Scripts\activate  # For PowerShell
.\venv\Scripts\activate.bat  # For Command Prompt
Install Flask:

Run the following command to install Flask in your virtual environment:
bash
Copy code
pip install flask
Create requirements.txt:

To list the dependencies, run:
bash
Copy code
pip freeze > requirements.txt
Step 2: Dockerize the Flask App
Create the Dockerfile:

In the project directory, create a file named Dockerfile with the following content:
dockerfile
Copy code
# Use an official Python runtime as a parent image
FROM python:3.9-slim

# Set the working directory in the container
WORKDIR /app

# Copy the current directory contents into the container at /app
COPY . /app

# Install any needed packages specified in requirements.txt
RUN pip install --no-cache-dir -r requirements.txt

# Make port 5000 available to the world outside this container
EXPOSE 5000

# Define environment variable
ENV FLASK_APP=app.py

# Run app.py when the container launches
CMD ["flask", "run", "--host=0.0.0.0"]
Create .dockerignore:

In the project directory, create a .dockerignore file to exclude unnecessary files from the Docker build context:
text
Copy code
__pycache__
venv/
.git
.gitlab-ci.yml
Build the Docker Image:

In PowerShell or Command Prompt, run:
bash
Copy code
docker build -t flask-app .
Run the Docker Container:

Start the container:

bash
Copy code
docker run -p 5000:5000 flask-app
Visit http://localhost:5000 to verify that your Flask app is running inside Docker.

Step 3: Set Up GitHub Actions CI/CD
Create the .github/workflows Directory:

In the project directory, create the following folder structure:
bash
Copy code
mkdir -p .github/workflows
Create the GitHub Actions Workflow File:

Inside .github/workflows/, create a file named ci-cd.yml and add the following content:
yaml
Copy code
name: Flask App CI/CD Pipeline

on:
  push:
    branches:
      - main
  pull_request:
    branches:
      - main

jobs:
  # Job to build Docker image
  build:
    runs-on: ubuntu-latest
    steps:
      # Checkout code from the repository
      - name: Checkout code
        uses: actions/checkout@v2

      # Set up Docker
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      # Build Docker image
      - name: Build Docker image
        run: |
          docker build -t flask-app .

  # Job to run tests
  test:
    runs-on: ubuntu-latest
    needs: build  # Ensures this job runs after the build job
    steps:
      # Checkout code from the repository
      - name: Checkout code
        uses: actions/checkout@v2

      # Set up Docker
      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      # Run the tests (you can replace this with more complex tests)
      - name: Run unit tests
        run: |
          docker run flask-app python -m unittest discover

  # Job to deploy the app (You can customize this for cloud deployment)
  deploy:
    runs-on: ubuntu-latest
    needs: test  # Ensures this job runs after the test job
    steps:
      # Checkout code from the repository
      - name: Checkout code
        uses: actions/checkout@v2

      # Deploy the application (this is just a placeholder)
      - name: Deploy Flask app
        run: |
          echo "Deploying Flask app..."
          # Add deployment steps here (e.g., deploy to Heroku, AWS, etc.)
Step 4: Commit and Push to GitHub
Initialize Git and Push the Project:

Initialize a Git repository in your project directory:
bash
Copy code
git init
git remote add origin https://github.com/your-username/your-repository.git
Commit the Changes:

Add all files and commit the changes:
bash
Copy code
git add .
git commit -m "Initial commit with Flask app, Docker, and GitHub Actions"
Push the Code to GitHub:

Push the code to your GitHub repository:
bash
Copy code
git push -u origin main
Step 5: Verify the GitHub Actions Pipeline
Go to the GitHub Repository:

Open your GitHub repository.
Go to the Actions tab to see your pipeline running.
Check Pipeline Status:

After pushing your code to GitHub, the GitHub Actions pipeline will automatically trigger. You’ll see the build, test, and deploy steps being executed.
Step 6: Customize Deployment (Optional)
To deploy your Flask app, you need to customize the deploy job. Here’s an example of how you can deploy to Heroku:

Create a Heroku App:

Follow Heroku's guide to create a Heroku app.
Set Up Heroku Deployment in GitHub Actions:

Add the following to the deploy section of the GitHub Actions workflow:
yaml
Copy code
- name: Deploy to Heroku
  uses: akshaybabloo/heroku-deploy-action@v0.0.5
  with:
    heroku_api_key: ${{ secrets.HEROKU_API_KEY }}
    heroku_app_name: your-heroku-app-name
    heroku_email: your-heroku-email@example.com
Add Heroku API Key as Secret:

Go to your GitHub repository's Settings > Secrets.
Add HEROKU_API_KEY as a secret with your Heroku API key.
Next Steps
Testing: You can expand the testing section by adding more detailed tests for your Flask application using pytest or other testing frameworks.
Deployment: Customize the deployment steps for your preferred platform, like AWS, Google Cloud, or Azure.
Monitoring: You might want to add monitoring or logging steps after deployment to track the app’s performance.