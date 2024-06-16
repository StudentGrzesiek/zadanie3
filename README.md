import subprocess
import json

def test_api(endpoint, expected_status, expected_keys):
    response = subprocess.run(['curl', '-s', '-o', 'response.json', '-w', '%{http_code}', endpoint],
                              capture_output=True, text=True)
    status_code = response.stdout.strip()
    if status_code != expected_status:
        print(f"Test {endpoint}: FAILED (Status code {status_code})")
        return

    with open('response.json') as f:
        data = json.load(f)

    for key in expected_keys:
        if key not in data:
            print(f"Test {endpoint}: FAILED (Missing key {key})")
            return

    print(f"Test {endpoint}: PASSED")

# Testowanie trzech endpointów
test_api("https://jsonplaceholder.typicode.com/posts/1", "200", ["userId", "id", "title", "body"])
test_api("https://jsonplaceholder.typicode.com/comments/1", "200", ["postId", "id", "name", "email", "body"])
test_api("https://jsonplaceholder.typicode.com/users/1", "200", ["id", "name", "username", "email"])

# Dokumentacja w README.md
with open('README.md', 'w') as f:
    f.write("""# API Testing Script
This script tests JSONPlaceholder API endpoints using curl.

## Endpoints Tested
- https://jsonplaceholder.typicode.com/posts/1
- https://jsonplaceholder.typicode.com/comments/1
- https://jsonplaceholder.typicode.com/users/1

## Test Results
- Each test checks for status code 200.
- Each test checks for the presence of specific keys in the JSON response.

## Usage
Run the script with Python 3.x to perform the tests and see the results.

    python3 test_api.py
""")
