# 事前設定

## Project
- AppTrustで使用するプロジェクトを作成

> JFrog Platform > Administration > All Projects Overview > + Create New

## Repositories
- このデモで使用する途中成果物の入れ物を準備

> JFrog Platform > (Project を選択) > Administration > Repositories > Create a repository
  - (your-project)-mvn-dev-local
  - (your-project)-mvn-remote
  - (your-project)-mvn-virtual (上記 mvnリポジトリを紐付け)
  - (your-project)-docker-dev-local
  - (your-project)-helm-dev-local
  - (your-project)-generic-dev-local


## Application 
- AppTrustで使用するApplication(箱)を作成

> JFrog Platform > (Project を選択) > AppTrust > Applications > + Create Application

or

```bash
jf apptrust app-create --project <project> <application>
```

## OIDC

> JFrog Platform > Administration > General Management > Manage Integration > New Integration > OpenID Connect

  - Provider Name: (your-oidc)
  - Provider Type: GitHub
  - Description: (any)
  - Provider URL: https://token.actions.githubusercontent.com
  - Audience: (github側で指定したい場合)
  - Organization: (your github organization)

### Identity mapping

> JFrog Platform > Administration > General Management > Manage Integration > New Integration > OpenID Connect > (作成した OpenID Connect) > Add Identity Mapping

  - Name: (any)
  - Priority: (any)
  - Description: (any)
  - Claim JSON:
```json
{
  "repository": "<your-organization>/<your-git-repo-name>"
}
```

注意）　OIDC接続のセキュリティ担保のため、かならずIdentity Mappingを実施してください。

## キーのセットアップとGitHub変数への追加

### key作成
```bash
openssl genrsa --out private.pem 2048
openssl rsa -in private.pem -pubout -out public.pem
```

### JFrogにKeyの登録
> JFrog Platform > Administration > Security > Keys Management > + Add keys > RSA keys

### GitHub変数
> GitHub > (this repo) > Settings > Secrets and variables > Actions > Repository secrets > New repository Secret

JFROG_CLI_SIGNING_KEY に public.pemの中身を登録

## main.yml更新

```yaml
env:
  JOB_NAME: "<any>"
  JFrog_Project: "<project>"
  JF_URL: "<yourhost>.jfrog.io"
  Application_Key: "<application>"
  OIDC-PROVIDER-NAME: "<your-oidc>"
```

# デモ手順

## (Optional)ルール作成

## ポリシーの作成と割り当て
> JFrog Platform > (your project) > AppTrust > Policies > Create New Policy

  - Scope: (今回のデモアプリ / プロジェクト全体)
  - Operation Type: (任意の場所：今回は PROD|Release gate)
  - Rule: (Critical CVE with CVSS score between 9.0 and 10.0 (skip if not applicable) など)
  - Action: Fail / Manual Approval
  - Policy Name: (any)

NOTE: 今回のデモでは、slsaやsbom(cyclonedx)などがEvidenceとなっているので、それをGate ruleにしても良い。

## (Optional) CRAなどのポリシーテンプレート追加(原則Releaseポリシーとなる)
> JFrog Platform > (your project) > AppTrust > Compliance

(CRA / NIST SSDF などのテンプレート選択) > (割り当てるアプリをScopeに指定) > 各規約のON/OFF - Warning/Fail を指定

## AppTrustページのGUIをベースにPromotionを試す
