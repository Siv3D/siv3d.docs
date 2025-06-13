# Managing Projects on GitHub

This explains the steps to manage Siv3D projects using GitHub for version control. Note that Siv3D doesn't automatically place .gitignore files, so downloading and placing them is necessary as shown in this procedure.

## 1. Prerequisites
- Create a GitHub account
- Install [GitHub Desktop :material-open-in-new:](https://github.com/apps/desktop){:target="_blank"}


## 2. Create Siv3D Project
- Create a Siv3D project
	- When creating a new project in Visual Studio, we recommend checking "Place solution and project in the same directory." This places the project directory at the top level, making the repository structure simpler (not required)


## 3. Download .gitignore File

!!!info "Role of .gitignore File"
	- The `.gitignore` file specifies files and directories that Git should not track (include in version control)
	- Projects contain many files that don't need to be managed on GitHub or shouldn't be managed, such as files generated every time you build and IDE temporary files
	- The `.gitignore` file is used to exclude these files from Git management

- Download the [official Siv3D `.gitignore` file :material-open-in-new:](https://github.com/Siv3D/gitignore/blob/main/.gitignore){:target="_blank"}
- During download, the filename might get a `.txt` extension like `gitignore.txt`. In that case, correct the filename to exactly `.gitignore` (filename starting with a dot)


## 4. Place .gitignore File
- Place the downloaded `.gitignore` file in the top-level directory (root directory) of your created Siv3D project
	- For Visual Studio, this is usually the directory containing the `.sln` file


## 5. Create Local Repository
!!!info "About Repository Names"
	- Here we explain using the example where the local existing project folder is `C:\Users\YourName\Desktop\projects\MyGame`
	- Replace the `MyGame` part with your actual project name

- Launch GitHub Desktop
- Select **New repository...** from the **File** menu

| Item | Description |
| ---- | ---- |
| **Name** | Enter the project folder name (`MyGame`) |
| **Description** | Enter project description (optional) |
| **Local path** | Specify the **parent directory** of the project folder (`C:\Users\YourName\Desktop\projects`) |
| **Initialize this repository with a README** | Check this (README file will be auto-generated) |
| **Git ignore** | Don't select here since we'll use the already placed `.gitignore` file (leave as `None`) |
| **License** | Select a license if needed. Leaving as "None" is fine. Licenses can be added/changed later |

- Click **Create repository**
- This initializes the existing `MyGame` project folder as a local Git repository
- When you check the commit history of `MyGame` in GitHub Desktop, you'll see that all existing project files have been committed as `Initial commit`
	- If not committed, follow the steps in **5. Update Project** to manually commit

## 6. Create and Publish Remote Repository
- In GitHub Desktop, confirm that the created `MyGame` repository is selected
- Click the **Publish repository** button at the top of the main area
- The **Publish repository** dialog will appear

| Item | Description |
| ---- | ---- |
| **Name** | Enter the repository name on GitHub (`MyGame`) |
| **Description** | Enter repository description (can be changed later on GitHub, optional) |
| **Keep this code private** | Check to make it a private repository. Uncheck to make it public. For learning purposes or personal projects, it's safer to start with private |
| **organization** | Select if you want the repository to belong to a specific GitHub Organization. Leave as default None for personal account management |

!!!info "About Repository Sharing and Settings Changes"
	- Even private repositories can be shared for collaboration by inviting other GitHub users from repository Settings > Collaborators on GitHub
	- Public/private repository settings can be changed later in Settings on GitHub

- Click **Publish repository**
- This creates a remote repository named `MyGame` on GitHub and pushes (uploads) the current content (commit history) of the local repository


## 7. Update Project
- When you make changes to project files, open GitHub Desktop
- If there are changed files, they'll be listed in the **Changes** pane on the left
- Review the changes and select files to include in the commit (usually all changed files)
- Enter a commit message briefly describing the changes in the **Summary** field at the bottom (e.g., "Add player movement feature")
- Click the **Commit to** main (or current branch name) button to commit changes to the local repository
- Then click the **Push origin** button at the top to reflect local repository commits to the remote repository on GitHub