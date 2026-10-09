# Manoj-Mimport os
from github import Github, GithubException


def upload_file_to_github(
    token: str,
    repo_name: str,
    local_file_path: str,
    target_path_in_repo: str = None,
    commit_message: str = "Add file via Python script",
    is_private: bool = False,
):
    """Creates a repository (if it doesn't exist) and uploads a file to GitHub."""
    # 1. Authenticate with GitHub
    g = Github(token)
    user = g.get_user()
    print(f"Authenticated as: {user.login}")

    # 2. Get or create the GitHub repository
    try:
        repo = user.get_repo(repo_name)
        print(f"Repository '{repo_name}' found.")
    except GithubException:
        print(f"Repository '{repo_name}' not found. Creating a new repository...")
        repo = user.create_repo(repo_name, private=is_private)
        print(f"Repository '{repo_name}' created successfully.")

    # 3. Read content from local file
    if not os.path.exists(local_file_path):
        raise FileNotFoundError(f"Local file not found at: {local_file_path}")

    with open(local_file_path, "r", encoding="utf-8") as file:
        content = file.read()

    # Default repo destination path to local filename if not specified
    if target_path_in_repo is None:
        target_path_in_repo = os.path.basename(local_file_path)

    # 4. Upload or update the file in the repository
    try:
        # Check if file already exists in repo to update it
        existing_file = repo.get_contents(target_path_in_repo)
        repo.update_file(
            path=target_path_in_repo,
            message=f"Update {target_path_in_repo}",
            content=content,
            sha=existing_file.sha,
        )
        print(f"Updated '{target_path_in_repo}' in repository.")
    except GithubException:
        # Create new file if it doesn't exist
        repo.create_file(
            path=target_path_in_repo,
            message=commit_message,
            content=content,
        )
        print(f"Uploaded '{target_path_in_repo}' to repository.")


if __name__ == "__main__":
    # Replace with your GitHub Personal Access Token
    GITHUB_TOKEN = "ghp_YourPersonalAccessTokenHere"

    # Repository name and local file details
    REPO_NAME = "my-python-project"
    LOCAL_FILE = "script.py"  # Path to the file on your local machine

    upload_file_to_github(
        token=GITHUB_TOKEN,
        repo_name=REPO_NAME,
        local_file_path=LOCAL_FILE,
        commit_message="Initial upload via Python script",
        is_private=False,
    )
