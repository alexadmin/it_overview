Скачать файлы из artifacts
```
- 'curl -L --output artifacts.zip --header "PRIVATE-TOKEN: ${GITLAB_TOKEN}" "https://host.ru/api/v4/projects/867/jobs/artifacts/DEVOPS-60-artifacts/download?job=build_src"'
- unzip artifacts.zip
```
