1. Your AWS CodeBuild project fails because an external dependency (e.g., an NPM package) is not found. How can you fix it?

- Check the buildspec.yml file to ensure dependencies are installed before the build

Why:

buildspec.yml defines the build phases in CodeBuild.

Dependencies should usually be installed in the install or pre_build phase.

---
