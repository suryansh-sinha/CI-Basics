# CI-Basics
This project demonstrates an end-to-end implementation of Continuous Integration

# Continuous Integration
## Why - What - How
Let's say we have a machine learning application and have uploaded it to a repository. The ML app has many components that run through a pipeline. We also have a Flask/FastAPI-based backend that we've created for the user to interact with the model.

We will always have changes that we want to make in our code to improve it. We may try to make our code more efficient by reducing the complexity. We could also be adding or removing some methods/features that may not be needed. So, there are many such changes that we may want to make to our code. 

Let's say we work for Rapido and are on the team that handles the microservice that predicts the time our driver will arrive. When we make changes to that microservice as developers, we want to make sure that it's integrated properly into the entire Rapido app. This is why the continuous integration technique is required.

We must have a testing protocol that automatically executes all the tests that we've designed for the application before pushing the changes to production. To solve this, we have a server, which provides us with some kind of service that runs all the tests in a pipeline. The server has an OS on which the application was built, it has all the python dependencies that are required for the project to run, then it pulls the code from our repository, runs all the tests, and then gives us an evaluation report on all the tests, giving all the failures and passes.

What if we have an automated service which is smart enough to understand when to run the CI flow? We can do this using GitHub Actions. It's a service that creates a server to execute our CI pipeline. We can create .yaml files to create tasks that are to be executed by GitHub Actions.

## Practical

Create a basic Streamlit app - 
```python
import streamlit as st

# Streamlit UI
st.title("Power Calculator")
st.write("Enter a number to calculate it's square, cube, and fifth power")  

# Get user input
n = st.number_input('Enter an integer', value=1, step=1)  

# Calculate the results
square = n**2
cube = n**3
fifth = n**5  

# Display the results
st.write(f'The square of {n} is: {square}')
st.write(f'The cube of {n} is: {cube}')
st.write(f'The fifth power of {n} is: {fifth}')
```
Upload the app to the GitHub repository. Then in the local repo, create a folder structure `{shell}.github -> workflows` and in that, create a file called`ci.yaml`.

```yaml
name: CI Workflow

on:
	push:
		branches:  # Specify which branches to test here
		- main
	pull_request:  # Can exclude this if you don't want to run tests when pushing.
		branches:
		- main

jobs:
	test:  # can also name my_test or something else
		runs-on: ubuntu-latest

	steps:
	- name: Checkout code  # Can put anything else instead of 'Checkout code'
		uses: actions/checkout@v2 # Tell how to checkout code so syntax matters.

	- name: Setup Python
		uses: actions/setup-python@v2
		with:
			python-version: '3.9'

	- name: Install Dependencies
		run: |
			python -m pip install --upgrade pip
			pip install pytest streamlit

	- name: Run Tests
		run: |
			pytest _test.py
```

This `_test.py` file needs to be created by us as follows -
```python
import pytest

# Function to test square
def square(n):
	return n ** 2  

def cube(n):
	return n ** 3  

def fifth(n):
	return n ** 5  

# Testing the square function

def test_square():
	assert square(2) == 4, "Test Failed: Square of 2 should be 4"
	assert square(3) == 9, "Test Failed: Square of 3 should be 9"

def test_cube():
	assert cube(2) == 8, "Test Failed: Cube of 2 should be 8"
	assert cube(3) == 27, "Test Failed: Cube of 3 should be 27"

def test_fifth():
	assert fifth(2) == 32, "Test Failed: Fifth power of 2 should be 32"
	assert fifth(3) == 243, "Test Failed: Fifth power of 3 should be 243"

def test_invalid_input():
	with pytest.raises(TypeError):
		square("string")
```

Now, whenever we push our code, the CI pipeline gets executed and the tests in the above file are executed on an Ubuntu server by GitHub.

## Theory
CI is a software development practice where developers regularly merge their code changes into a shared repository, usually multiple times a day and each merge/push/pull triggers an automated process to build and test the code, ensuring that new changes integrate smoothly with the existing codebase.

Why - 
- Early Detection of Errors
- Automation of Testing
- Faster Development Cycles
- Reduced Integration Problems

How CI Works -
- Version Control System: Devs commit their code to a shared repository.
- CI Server: A CI Server monitors the repo. Any time it detects a change, the server automatically triggers a series of tasks.
- Build Automation: The code is built, which includes compiling the code and generating artifacts.
- Automated Testing:
  * Unit Tests: These tests check individual functions or components to ensure they are working as expected
  * Integration Tests: These tests verify that the different parts of the application work together as intended.
  * End-To-End Tests: These tests simulate real user scenarios to ensure the system as a whole behaves correctly.
- Feedback: The results of the build and tests are reported back to the developers, usually in the form of success or failure notifications.

CI Workflow with GitHub Actions:
- Triggering CI: CI can be triggered by various events such as code push, pull request, or even on a schedule.
- Configuration Files: CI is configured using YAML files that define the tasks to be performed.
- Steps in CI Pipeline:
  - Checkout Code: The first step is to checkout code from the repository.
  - Setup Environment: Install dependencies, set up the runtime environment
  - Run Tests: Execute the tests defined for the project
  - Build Artifacts: If the tests pass, build the necessary artifacts for deployment
  - Deploy: Optionally, deploy the code to a staging or production environment.
