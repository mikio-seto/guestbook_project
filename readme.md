# 事前設定
## Project
- AppTrustで使用するプロジェクトを作成
JFrog Platform > Administration > All Projects Overview > + Create New

## Repositories
- このデモで使用する途中成果物の入れ物を準備
JFrog Platform > (Project を選択) > Administration > Repositories > Create a repository
- <project>-mvn-dev-local
- <project>-mvn-remote
- <project>-mvn-virtual
- <project>-docker-dev-local
- <project>-helm-dev-local
- <project>-generic-dev-local
 
## Application 
- AppTrustで使用するApplication(箱)を作成

JFrog Platform > (Project を選択) > AppTrust > Applications > + Create Application

or

```bash
jf apptrust app-create --project <project> <application>
```

## main.yml更新

```yaml
env:
  JOB_NAME: "<any>"
  JFrog_Project: "<project>"
  JF_URL: "<yourhost>.jfrog.io"
  Application_Key: "<application>"
```

