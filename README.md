<h1 align="center">Deploying a Python project on a Linux server in an isolated network segment.</h1>
I would like to note in advance that the possible solutions to the problem include the following options:

1. Configuring a PyPI repository proxy in an isolated network segment.
2. Building the entire Python project into a Docker image.
3. Building the entire Python project into a single binary package.

> The widely known Nexus, in its free version, can act as a PyPI proxy repository. It can be utilized to build a solution that controls the security of installed Python modules.

For building binary packages, I recommend reading the article on Python Compilation.

However, if the aforementioned options do not suit your needs, this article might be helpful.

The article is simple and aimed at beginners; I kindly ask veterans not to be offended.

## Task:
The goal is to deploy a Python project along with all its dependencies from a local Git (Bitbucket) onto a server in an isolated network segment.

The issue arises from the fact that on the server, it's not possible to pull dependencies with a simple "pip install" command due to the lack of access to package directories like PyPI. The solution to this problem is described in this article.

**Server Information:**
- No access to the global internet.
- No access to the Python Package Index and other indices.
- Access to a local RHEL repository.
- Access to a local Git.
- Python and Git are already installed on the server.

**Available Resources:**
- A VM on Linux or Windows in an open network segment with internet access.
- The actual server in an isolated segment.
- Data transmission between the server and VM is possible.
## Requirements.txt file:
If your Python project doesn't have a requirements file, you need to create one.

The requirements.txt file is a text file used in Python projects to specify a list of dependencies and their versions. Each line in this file represents a dependency and follows the format `package_name==version`, where `package_name` is the name of the Python library or package, and `version` is the desired version of that dependency.

This file is often used with package management tools like pip for the automatic installation and management of project dependencies.

Example requirements.txt file in the demonstration project:
```
netrnd-parser==1.0.428
pydantic[dotenv]
typer
requests
typing_extensions==4.5.0
urllib3==1.26.16
wheel==0.40.0
```
## Clone the project to an offline server
Disable SSL Certificate validation in git (optional):
```
git config --global http.sslVerify false
```
Let's clone the project's git repository to the server's home directory:
```
git clone https://company.ru/scm/nec/prefix_check.git
Cloning into 'prefix_check'...
Username: ********
Password: ********
```
After successful cloning, a directory with the project should appear in your home directory:
```
ls -l
drwxrwxr-x. 5 smirnov-nk smirnov-nk 181 Jul 25 17:06 prefix_check
```
## Preparing an archive with wheel packages according to the requirements on the online VM
### Linux VM option
It is important to have the same version of python installed on the vm and server, otherwise you will have to download all packages with their dependencies manually.

Let's clone the repository:
```
git config --global http.sslVerify false
git clone https://company.ru/scm/nec/prefix_check.git
```
Create a folder and use pip to download all the dependencies of the project according to the requirements file:
```
mkdir dwl
cd dwl
pip3.10 download -r ~/prefix_check/requirements.txt
```
<details>
  <summary>option description</summary>
-r, --requirement <file>: Install from the given requirements file. This option can be used multiple times.
</details>
Let's get the archive together:
```
tar -czvf dwl.tar.gz ./*
```
