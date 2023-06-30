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

## 축약어

|name|desc|
|---|------------------|
|DN|Distinguished Name (식별 이름)|
|RDN|Relative Distinguished Name (상대식별 이름)|
|OU|Organizational Unit (조직 단위)|
|DC|Domain Component (도메인 구성요소)|
|CN|Common Name (일반 이름)|
|SN|Surname (성)|
|UID|User ID (사용자 식별자)|
|GID|Group ID (그룹 식별자)|
|LDAP|Lightweight Directory Access Protocol (경량 디렉터리 액세스 프로토콜)|
|LDIF|LDAP Data Interchange Format (LDAP 데이터 교환 형식)|

## openldap 명령

**목록 확인**

```bash
# 그룹 목록 확인
ldapsearch -x -LLL -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] -b "ou=groups,dc=mycompany,dc=com" "(objectClass=posixGroup)"

# 유저 목록 확인
ldapsearch -x -LLL -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] -b "ou=users,dc=mycompany,dc=com" "(objectClass=posixAccount)"
```

**그룹 수정**

```bash
# 그룹 추가
ldapadd -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: cn=new-group,ou=groups,dc=mycompany,dc=com
objectClass: posixGroup
cn: new-group
gidNumber: 10000
EOF

# 그룹 속성 수정
ldapmodify -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: cn=group-id,ou=groups,dc=mycompany,dc=com
changetype: modify
replace: description
description: updated description
EOF

# 그룹에 유저 추가
ldapmodify -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: cn=group-id,ou=groups,dc=mycompany,dc=com
changetype: modify
add: memberUid
memberUid: user-id
EOF

# 그룹에서 유저 제거
ldapmodify -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: cn=group-id,ou=groups,dc=mycompany,dc=com
changetype: modify
delete: memberUid
memberUid: user-id
EOF
```

**유저 수정**

```bash
# 유저 추가
ldapadd -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: uid=new-user,ou=users,dc=mycompany,dc=com
objectClass: posixAccount
uid: new-user
cn: New User
sn: User
uidNumber: 10000
gidNumber: 10000
homeDirectory: /home/new-user
userPassword: {CLEARTEXT}password
EOF

# 유저 속성 수정
ldapmodify -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: uid=user-id,ou=users,dc=mycompany,dc=com
changetype: modify
replace: email
email: updated-email@example.com
EOF

# 유저를 그룹에 추가
ldapmodify -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: cn=group-id,ou=groups,dc=mycompany,dc=com
changetype: modify
add: memberUid
memberUid: user-id
EOF

# 유저를 그룹에서 제거
ldapmodify -x -H ldap://localhost:389 -D "cn=admin,dc=mycompany,dc=com" -w [[adminpwd]] << EOF
dn: cn=group-id,ou=groups,dc=mycompany,dc=com
changetype: modify
delete: memberUid
memberUid: user-id
EOF
```


[참조][ref]

[ref]: https://github.com/ivangfr/springboot-keycloak-openldap/blob/master/docker-compose.yml
