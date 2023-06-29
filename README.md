## keycloak, openldap 기본 구성 예제

전체적으로 돌아가는것 파악하는 용도로 구성

## Keycloak 콘솔

http://localhost:8080

**LDAP Federation 시 주요 정보 (ldap-test/ldap-config.json 참조)**

UsersDN : cn=John Doe,ou=users,dc=my-domain,dc=com

Bind DN : cn=admin,dc=my-domain,dc=com

Vender :

- ad: Active Directory (마이크로소프트 윈도우 운영체제의 디렉터리 서비스)
- tivoli: IBM Tivoli Directory Server
- edirectory: Novell eDirectory
- openldap: OpenLDAP (오픈 소스 LDAP 서버)
- rhds: Red Hat Directory Server (Red Hat 계열의 LDAP 서버)
- other: 기타 LDAP 서버

## 샘플용 LDAP 계정 추가

```
#!/usr/bin/env bash

LDAP_HOST=${1:-localhost}

# LDIF(LDAP Data Interchange Format) import
ldapadd -x -D "cn=admin,dc=mycompany,dc=com" -w admin -H ldap://$LDAP_HOST -f ldap-mycompany-com.ldif
```

## PHP Ldap admin

ldap을 GUI로 편하게 관리할 수 있음.

ID : cn=admin,dc=my-domain,dc=com
PWD : admin

https://localhost:6443/

[참조][ref]

[ref]: https://github.com/ivangfr/springboot-keycloak-openldap/blob/master/docker-compose.yml