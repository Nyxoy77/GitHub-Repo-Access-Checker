# GitHub Repo Access Checker

This is a Bash script that interacts with the GitHub API to list users with read (pull) access to a specific repository. The script requires a GitHub username and personal access token for authentication and uses `jq` to parse the JSON response from the API.

## Features

- Fetches a list of collaborators for a GitHub repository.
- Filters and displays users who have **read access** (pull permission).
- Works with both public and private repositories (with proper token permissions).
- Lightweight and easy to run from the command line.

## Prerequisites

Before running the script, ensure you have the following:

1. **GitHub Personal Access Token (PAT):**
   - You need a PAT with the `repo` scope if you want to check private repositories.
   - You can create one by following [GitHub's guide on creating a token](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token).

2. **`jq` (for parsing JSON):**
   - The script uses `jq` to filter and parse the API response.
   - To install `jq`, run the following command:
     ```bash
     sudo apt-get install jq
     ```

3. **`curl` (for sending API requests):**
   - `curl` is used for interacting with the GitHub API.
   - Most Linux distributions have `curl` pre-installed. If not, you can install it with:
     ```bash
     sudo apt-get install curl
     ```

## How to Use

### 1. Clone the repository

```bash
git clone https://github.com/yourusername/repo-access-checker.git
cd repo-access-checker
```

### 2. Set up your GitHub credentials

Before running the script, you need to set your **GitHub username** and **Personal Access Token** as environment variables:

```bash
export username="your_github_username"
export token="your_github_token"
```

### 3. Run the Script

You can run the script with two arguments: the **repository owner** and the **repository name**.

```bash
./script.sh REPO_OWNER REPO_NAME
```

For example, to check users with read access to the `Hello-World` repository owned by `hellocat`, run:

```bash
./script.sh hellocat Hello-World
```

### 4. Output

The script will print the list of users with read access (pull permission) to the specified repository.

Example:

```bash
Listing users with read access to hellocat/Hello-World...
Users with read access to hellocat/Hello-World:
user1
user2
user3
```

If no users are found with read access, it will print:

```bash
No users with read access found for hellocat/Hello-World.
```

## Script Breakdown

### 1. **github_api_get** function
This function sends a **GET request** to the GitHub API, using the provided username and token for authentication.

```bash
function github_api_get {
    local endpoint="$1"
    local url="${API_URL}/${endpoint}"
    curl -s -u "${USERNAME}:${TOKEN}" "$url"
}
```

### 2. **list_users_with_read_access** function
This function fetches the list of collaborators from the GitHub API and filters out the users with read access using `jq`.

```bash
function list_users_with_read_access {
    local endpoint="repos/${REPO_OWNER}/${REPO_NAME}/collaborators"
    collaborators="$(github_api_get "$endpoint" | jq -r '.[] | select(.permissions.pull == true) | .login')"
    ...
}
```

## Example

```bash
./script.sh hellocat Hello-World
```

This will list all users with **read access** to the `Hello-World` repository owned by `hellocat`.

## Notes

- Ensure that your **Personal Access Token** has sufficient permissions, especially when working with private repositories.
- API rate limits may apply when making too many requests. Be mindful of the [GitHub rate limits](https://docs.github.com/en/rest/overview/resources-in-the-rest-api#rate-limiting).

## Contributing

Feel free to open issues or submit pull requests to improve this script.
