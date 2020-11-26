## Table of Contents

1. [문서 개요](#1)  
  1.1. [목적](#1-1)  
  1.2. [범위](#1-2)  
  1.3. [시스템 구성도](#1-3)  
  1.4. [참고자료](#1-4)  

2. [PaaS-TA Portal 설치](#2)  
  2.1. [PaaS-TA Portal API Release 설치](#2-1)   
  2.2. [소스코드 다운로드](#2-2)    
  2.3. [PaaS-TA Portal CF 배포](#2-3)   
  2.3.1. [Portal Web Admin 배포](#2-3-1)   
  2.3.2. [Portal Web User 배포](#2-3-2)   
  2.3.3. [Portal Registration 배포](#2-3-3)   
  2.3.4. [Portal Gateway 배포](#2-3-4)   
  2.3.5. [Portal Api 배포](#2-3-5)   
  2.3.6. [Portal Storage Api 배포](#2-3-6)   
  2.3.7. [Portal Common Api 배포](#2-3-7)   
  2.3.8. [Portal Log Api 배포](#2-3-8)   
  2.3.9. [Portal SSH 배포](#2-3-9)   
  2.4. [rule](#2-4)  

3. [PaaS-TA Portal 운영](#3)  
  3.1. [사용자의 조직 생성 Flag 활성화](#3-1)  
  3.2. [사용자포탈 UAA 페이지 오류](#3-2)  
  3.3. [운영자포탈 유저 페이지 조회 오류](#3-3)  
  3.4. [카탈로그 적용](#3-4)  
  3.5. [모니터링 및 오토스케일링 적용](#3-5)  


## <div id="1"/> 1. 문서 개요
### <div id="1-1"/> 1.1. 목적

본 문서(PaaS-TA Portal Container 설치 가이드)는 전자정부표준프레임워크 기반의 PaaS-TA에서 제공되는 PaaS-TA Portal 을 CF를 이용하여 배포 하는 방법을 기술하였다.<br />
기존 Release 를 사용한 VM 배포에서 CF Container 로 배포하면서 VM 수 감소로 인한 PaaS-TA 경량화에 목적이 있다.

### <div id="1-2"/> 1.2. 범위
설치 범위는 PaaS-TA Portal API Release 설치 및 API 6개 서비스, UI 3개 설치를 기준으로 작성하였다.

### <div id="1-3"/> 1.3. 시스템 구성도
본 문서의 설치된 시스템 구성도이다. Binary Storage, Mariadb, Proxy, Gateway Api, Registration Api, Portal Api, Common Api, Log Api, Storage Api, Webadmin, Webuser로 최소사항을 구성하였다.

본 문서의 설치된 시스템 구성도 이다.<br />
API Release : Binary Storage, Mariadb<br />
WEB : Portal Web User, Portal Web Admin, PaaS-TA SSH<br />
API : Registration API, Gateway API,  Common API, Portal API, Storage API, Log API<br />

![시스템구성도][paas-ta-portal-01-b]<br/>

* Paas-TA Portal각 Instance의 Resource Pool과 스펙

| 구분 | 스펙 |
|--------|-------|
| paas-ta-portal-webadmin | 1GB RAM / 4GB Disk |
| paas-ta-portal-webuser | 512MB RAM / 4GB Disk|
| paasta-ssh | 512MB RAM / 4GB Disk|
| paas-ta-portal-registration | 1GB RAM / 4GB Disk|
| paas-ta-portal-gateway | 1GB RAM / 4GB Disk|
| paas-ta-portal-api | 2GB RAM / 4GB Disk|
| paas-ta-portal-common-api | 1GB RAM / 4GB Disk|
| paas-ta-portal-storage-api | 1GB RAM / 4GB Disk|
| paas-ta-portal-log-api | 1GB RAM / 4GB Disk|

* API Release 는 API Release 가이드 참조.<br />
  [**https://github.com/PaaS-TA/PAAS-TA-PORTAL-API-RELEASE**](https://github.com/PaaS-TA/PAAS-TA-PORTAL-API-RELEASE)

### <div id="1-4"/> 1.4. 참고자료
[**http://bosh.io/docs**](http://bosh.io/docs)  
[**http://docs.cloudfoundry.org/**](http://docs.cloudfoundry.org/)

## <div id="2"/> 2. PaaS-TA Portal 설치

### <div id="2-1"/> 2.1. PaaS-TA Portal API Release 설치
API Release 가이드 참조.<br />
[**https://github.com/PaaS-TA/PAAS-TA-PORTAL-API-RELEASE**](https://github.com/PaaS-TA/PAAS-TA-PORTAL-API-RELEASE)


### <div id="2-2"/> 2.2. 소스코드 다운로드
서비스 설치에 필요한 소스 코드를 Git Repository에서 받아 서비스 설치 작업 경로로 위치시킨다.   

- Portal Web User Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-WEBADMIN
- Portal Web Admin Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-WEBUSER
- Portal Registration API Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-REGISTRATION
- Portal Gateway API Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-GATEWAY
- Portal API Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-API
- Portal Storate API Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-STORAGE-API
- Portal Common API Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-COMMON-API
- Portal Log API Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-LOG-API
- Portal SSH  Git Repository URL : https://github.com/PaaS-TA/PAAS-TA-PORTAL-SSH

```
# 소스 코드 다운로드 파일 위치 경로 생성 및 설치 경로 이동
$ mkdir -p ~/workspace/paasta/portal
$ cd ~/workspace/paasta/portal

# Deployment 파일 다운로드
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-WEBADMIN.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-WEBUSER.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-REGISTRATION.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-GATEWAY.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-API.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-STORAGE-API.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-COMMON-API.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-LOG-API.git -b v2.1.0
$ git clone https://github.com/PaaS-TA/PAAS-TA-PORTAL-SSH.git
```

### <div id="2-3"/> 2.3. PaaS-TA Portal CF 배포
본 설치 가이드는 Linux 환경에서 설치하는 것을 기준으로 하였다.
Portal 배포를 위해서는 먼저 CF CLI 가 설치 되어 있어야 하고 CF 로그인 및 배포할 Target 가 지정 되어 있어야 한다.<br>

- CF CLI 사용자 가이드  
  - [CF CLI 사용자 가이드](https://docs.cloudfoundry.org/cf-cli/index.html)<br>

#### <div id="2-3-1"/> 2.3.1. Portal Web Admin 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
  - name: portal-web-admin                                                            # 어플리케이션 이름
    memory: 1G                                                                        # 어플리케이션에 할당할 메모리
    instances: 1                                                                      # 어플리케이션 인스턴스 수
    buildpacks:
    - java_buildpack
    path: build/libs/paas-ta-portal-webadmin.war
    env:
      server_port: 8090

      spring_application_name: PortalWebAdmin

      datasource_cfg_url: jdbc:mysql://<PORTAL_DB_IP>:<PORTAL_DB_PORT>/webconfig      # API Release 의 mariadb ip 및 port
      datasource_cfg_username: <PORTAL_DB_USER>                                       # API Release 의 mariadb 계정
      datasource_cfg_password: <PORTAL_DB_USER_PASSWORD>                              # API Release 의 mariadb 비밀번호
      datasource_cfg_ddl-auto: none
      datasource_cfg_naming_strategy: org.hibernate.dialect.MySQL5Dialect

      paasta_portal_api_authorization_base64: Basic YWRtaW46b3BlbnBhYXN0YQ==
      paasta_portal_api_zuulUrl_cfapi: http://portal-gateway.<DOMAIN>/portalapi       # System Domain
      paasta_portal_api_zuulUrl_commonapi: http://portal-gateway.<DOMAIN>/commonapi   # System Domain
      paasta_portal_api_zuulUrl_storageapi: http://portal-gateway.<DOMAIN>/storageapi # System Domain
      paasta_portal_storageapi_type: swift

      logging_level_org.openpaas.paasta.portal.web.admin.exception.GlobalControllerExceptionHandler: DEBUG
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-web-admin --recent

#### <div id="2-3-2"/> 2.3.2. Portal Web User 배포
해당 소스를 빌드한다.
> $ npm run build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
- name: portal-web-user   # 어플리케이션 이름
  memory: 1G              # 어플리케이션에 할당할 메모리
  instances: 1            # 어플리케이션 인스턴스 수
  buildpacks:
  - staticfile_buildpack
  path: ./dist/paas-ta-portal-webuser
```

환경 설정 파일을 수정 한다.<br />
경로 : /src/assets/resources/evn/config.json
```
{
  ...
  "webadminUri": "http://<portal-web-admin uri>/", # Portal Web Admin uri 등록
  "sshUri": "http://paasta-ssh.<DOMAIN>/",         # System Domain
  "userAppSizeMb": "0"                             # 사용자 앱 배포시 파일 사이즈 제한(MB), 0 은 제한 안함.
}
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-web-user --recent

#### <div id="2-3-3"/> 2.3.3. Portal Registration 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
  - name: portal-registration               # 어플리케이션 이름
    memory: 1G                              # 어플리케이션에 할당할 메모리
    instances: 1                            # 어플리케이션 인스턴스 수
    buildpacks:
    - java_buildpack
    routes:
    - route: portal-registration.<DOMAIN>   # System Domain
    path: build/libs/paas-ta-portal-registration.jar
    env:
      server_port: 80

      spring_application_name: PortalRegistration

      eureka_server_enableSelfPreservation: true
      eureka_instance_hostname: ${vcap.application.uris[0]}
      eureka_instance_nonSecurePort: 80
      eureka_client_registerWithEureka: false
      eureka_client_fetchRegistry: false
      eureka_server_maxThreadsForPeerReplication: 0
      eureka_client_server_waitTimeInMsWhenSyncEmpty: 0
      eureka_client_serviceUrl_defaultZone: http://${vcap.application.uris[0]}/eureka/
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-registration --recent

#### <div id="2-3-4"/> 2.3.4. Portal Gateway 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
  - name: portal-gateway                                                                    # 어플리케이션 이름
    memory: 1G                                                                              # 어플리케이션에 할당할 메모리
    instances: 1                                                                            # 어플리케이션 인스턴스 수
    buildpacks:
    - java_buildpack
    routes:
    - route: portal-gateway.<DOMAIN>                                                        # System Domain
    path: build/libs/paas-ta-portal-gateway.jar
    env:
      server_port: 80

      spring_application_name: PortalGateWay

      eureka_client_serviceUrl_defaultZone: http://portal-registration.<DOMAIN>/eureka/     # System Domain
      eureka_instance_hostname: ${vcap.application.uris[0]}
      eureka_instance_nonSecurePort: 80
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-gateway --recent

#### <div id="2-3-5"/> 2.3.5. Portal Api 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
  - name: portal-api                                                                   # 어플리케이션 이름
    memory: 2G                                                                         # 어플리케이션에 할당할 메모리
    disk: 2G                                                                           # 어플리케이션에 할당할 디스크
    instances: 1                                                                       # 어플리케이션 인스턴스 수
    buildpacks:
    - java_buildpack
    path: build/libs/paas-ta-portal-api.jar
    env:
      server_port: 80

      JBP_CONFIG_CONTAINER_SECURITY_PROVIDER: '{key_manager_enabled: false, trust_manager_enabled: false}'
      spring_application_name: PortalApi

      spring_security_username: admin
      spring_security_password: openpaasta

      tailsocket_port: 5555

      management_security_enabled: false
      management_health_ldap_enabled: false

      ### paasta info
      cloudfoundry_cc_api_url: https://api.<DOMAIN>
      cloudfoundry_cc_api_uaaUrl: https://uaa.<DOMAIN>
      cloudfoundry_cc_api_sslSkipValidation: true
      cloudfoundry_user_admin_username: <CF_USER_ADMIN_USERNAME>                        # (e.g. admin)
      cloudfoundry_user_admin_password: <CF_USER_ADMIN_PASSWORD>                        # (e.g. admin)
      cloudfoundry_user_uaaClient_clientId: <UAA_CLIENT_ID>                             # (e.g. admin)
      cloudfoundry_user_uaaClient_clientSecret: <UAA_CLIENT_SECRET>                     # (e.g. admin-secret)
      cloudfoundry_user_uaaClient_adminClientId: <UAA_ADMIN_CLIENT_ID>                  # (e.g. admin)
      cloudfoundry_user_uaaClient_adminClientSecret: <UAA_ADMIN_CLIENT_SECRET>          # (e.g. admin-secret)
      cloudfoundry_user_uaaClient_loginClientId: <UAA_LOGIN_CLIENT_ID>                  # (e.g. admin)
      cloudfoundry_user_uaaClient_loginClientSecret: <UAA_LOGIN_CLIENT_SECRET>          # (e.g. admin-secret)
      cloudfoundry_user_uaaClient_skipSSLValidation: true
      cloudfoundry_authorization: cf-Authorization

      paasta_portal_api_authorization_base64: Basic YWRtaW46b3BlbnBhYXN0YQ==
      paasta_portal_api_zuulUrl_cfapi: http://portal-gateway.<DOMAIN>/portalapi         # System Domain
      paasta_portal_api_zuulUrl_commonapi: http://portal-gateway.<DOMAIN>/commonapi     # System Domain
      paasta_portal_api_zuulUrl_storageapi: http://portal-gateway.<DOMAIN>/storageapi   # System Domain  
      paasta_portal_storageapi_type: swift

      eureka.client.serviceUrl.defaultZone: http://portal-registration.<DOMAIN>/eureka/ # System Domain
      eureka.instance.hostname: ${vcap.application.uris[0]}
      eureka.instance.nonSecurePort: 80

      ### abacus
      abacus_url: http://<ABACUS_URL>

      ## monitoring
      monitoring_api_url: http://<MONITORING_API_URL>
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-api --recent

#### <div id="2-3-6"/> 2.3.6. Portal Storage Api 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
- name: portal-storage-api                                                                     # 어플리케이션 이름
  memory: 1G                                                                                   # 어플리케이션에 할당할 메모리
  instances: 1                                                                                 # 어플리케이션 인스턴스 수
  buildpacks:
  - java_buildpack
  path: build/libs/paas-ta-portal-storage-api.jar
  env:
    server_port: 80

    spring_application_name: PortalStorageApi

    spring_security_username: admin
    spring_security_password: openpaasta

    eureka_client_serviceUrl_defaultZone: http://portal-registration.<DOMAIN>/eureka/           # System Domain
    eureka_instance_hostname: ${vcap.application.uris[0]}
    eureka_instance_nonSecurePort: 80

    objectStorage_swift_tenantName: <OBJECTSTORAGE_TENANTNAME>                                  # API Release Object Storage 의 tenantName
    objectStorage_swift_username: <OBJECTSTORAGE_USERNAME>                                      # API Release Object Storage 의 username
    objectStorage_swift_password: <OBJECTSTORAGE_PASSWORD>                                      # API Release Object Storage 의 password
    objectStorage_swift_authUrl: http://<OBJECTSTORAGE_IP>:<OBJECTSTORAGE_PORT>/v2.0/tokens     # API Release Object Storage 의 IP 및 PORT
    objectStorage_swift_authMethod: <OBJECTSTORAGE_AUTHMETHOD>                                  # API Release Object Storage 의 authMethod
    objectStorage_swift_preferredRegion: <OBJECTSTORAGE_PREFERREDREGION>                        # API Release Object Storage 의 preferredRegion
    objectStorage_swift_container: <OBJECTSTORAGE_CONTAINER>                                    # API Release Object Storage 의 container
    objectStorage_swift_user_app_size_mb: 0                                                     # user app upload size ( 0 : unlimited )
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-storage-api --recent

#### <div id="2-3-7"/> 2.3.7. Portal Common Api 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
- name: portal-common-api                                                                        # 어플리케이션 이름
  memory: 1G                                                                                     # 어플리케이션에 할당할 메모리
  instances: 1                                                                                   # 어플리케이션 인스턴스 수
  buildpacks:
  - java_buildpack
  path: build/libs/paas-ta-portal-common-api.jar
  env:
    server_port: 80

    spring_application_name: PortalCommonApi

    spring_security_username: admin
    spring_security_password: openpaasta

    datasource_cc_driver-class-name: <PAAS-TA_DB_DRIVER>                                         # (e.g. org.postgresql.Driver OR com.mysql.jdbc.Driver)
    datasource_cc_url: jdbc:<PAAS-TA_DATABASE>://<PAAS-TA_DB_IP>:<PAAS-TA_DB_PORT>/<CC_DB_NAME>  # (e.g. PAAS-TA_DATABASE :: postgresql OR mysql)
    datasource_cc_username: <CC_DB_USER_NAME>
    datasource_cc_password: <CC_DB_USER_PASSWORD>
    datasource_portal_driver-class-name: com.mysql.jdbc.Driver
    datasource_portal_url: jdbc:mysql://<PORTAL_DB_IP>:<PORTAL_DB_PORT>/portaldb                   # API Release 의 mariadb ip, port
    datasource_portal_username: <PORTAL_DB_USER>                                                   # API Release 의 mariadb user
    datasource_portal_password: <PORTAL_DB_USER_PASSWORD>                                          # API Release 의 mariadb password
    datasource_uaa_driver-class-name: <PAAS-TA_DB_DRIVER>                                          # (e.g. org.postgresql.Driver OR com.mysql.jdbc.Driver)
    datasource_uaa_url: jdbc:<PAAS-TA_DATABASE>://<PAAS-TA_DB_IP>:<PAAS-TA_DB_PORT>/<UAA_DB_NAME>  # (e.g. PAAS-TA_DATABASE :: postgresql OR mysql)
    datasource_uaa_username: <UAA_DB_USER_NAME>
    datasource_uaa_password: <UAA_DB_USER_PASSWORD>

    jpa_show-sql: true
    jpa_hibernate_ddl-auto: none
    jpa_hibernate_naming_strategy: org.hibernate.dialect.MySQL5Dialect
    jpa_generate-ddl: false

    mail_smtp_host: <MAIL_SMTP_HOST>
    mail_smtp_port: <MAIL_SMTP_PORT>
    mail_smtp_username: <MAIL_SMTP_USERNAME>
    mail_smtp_password: <MAIL_SMTP_PASSWORD>
    mail_smtp_useremail: <MAIL_SMTP_USEREMAIL>
    mail_smtp_properties_auth: true
    mail_smtp_properties_starttls_enable: true
    mail_smtp_properties_starttls_required: true
    mail_smtp_properties_maximumTotalQps: 90
    mail_smtp_properties_authUrl: http://<MAIL_SMTP_PROPERTIES_AUTHURL>
    mail_smtp_properties_charset: UTF-8
    mail_smtp_properties_subject: "PaaS-TA User Potal"
    mail_smtp_properties_createUrl: authcreate
    mail_smtp_properties_expiredUrl: authreset
    mail_smtp_properties_inviteUrl: inviteorg

    paasta_portal_api_authorization_base64: Basic YWRtaW46b3BlbnBhYXN0YQ==
    paasta_portal_api_zuulUrl_cfapi: http://portal-gateway.<DOMAIN>/portalapi                    # System Domain
    paasta_portal_api_zuulUrl_commonapi: http://portal-gateway.<DOMAIN>/commonapi                # System Domain
    paasta_portal_api_zuulUrl_storageapi: http://portal-gateway.<DOMAIN>/storageapi              # System Domain
    paasta_portal_storageapi_type: swift

    eureka_client_serviceUrl_defaultZone: http://portal-registration.<DOMAIN>/eureka/            # System Domain
    eureka_instance_hostname: ${vcap.application.uris[0]}
    eureka_instance_nonSecurePort: 80
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-common-api --recent

#### <div id="2-3-8"/> 2.3.8. Portal Log Api 배포
해당 소스를 빌드한다.
> $ gradlew clean build

배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
applications:
- name: portal-log-api                                                        # 어플리케이션 이름
  memory: 1G                                                                  # 어플리케이션에 할당할 메모리
  instances: 1                                                                # 어플리케이션 인스턴스 수
  buildpacks:
  - java_buildpack
  path: build/libs/paas-ta-portal-log-api.jar
  env:
    server_port: 80

    spring_application_name: PortalLogApi

    spring_security_username: admin
    spring_security_password: openpaasta

    tailsocket_port: 5555

    ### paasta info
    cloudfoundry_cc_api_url: https://api.<DOMAIN>
    cloudfoundry_cc_api_uaaUrl: https://uaa.<DOMAIN>
    cloudfoundry_cc_api_sslSkipValidation: true
    cloudfoundry_user_admin_username: <CF_USER_ADMIN_USERNAME>                # PaasTA_INFO (e.g. admin)
    cloudfoundry_user_admin_password: <CF_USER_ADMIN_PASSWORD>                # PaasTA_INFO (e.g. admin)
    cloudfoundry_user_uaaClient_clientId: <UAA_CLIENT_ID>                     # PaasTA_INFO (e.g. login)
    cloudfoundry_user_uaaClient_clientSecret: <UAA_CLIENT_SECRET>             # PaasTA_INFO (e.g. login-secret)
    cloudfoundry_user_uaaClient_adminClientId: <UAA_ADMIN_CLIENT_ID>          # PaasTA_INFO (e.g. admin)
    cloudfoundry_user_uaaClient_adminClientSecret: <UAA_ADMIN_CLIENT_SECRET>  # PaasTA_INFO (e.g. admin-secret)
    cloudfoundry_user_uaaClient_loginClientId: <UAA_LOGIN_CLIENT_ID>          # PaasTA_INFO (e.g. login)
    cloudfoundry_user_uaaClient_loginClientSecret: <UAA_LOGIN_CLIENT_SECRET>  # PaasTA_INFO (e.g. login-secret)
    cloudfoundry_user_uaaClient_skipSSLValidation: true
    cloudfoundry_authorization: cf-Authorization

    eureka_client_serviceUrl_defaultZone: http://portal-registration.<DOMAIN>/eureka/  # System Domain
    eureka_instance_hostname: ${vcap.application.uris[0]}
    eureka_instance_nonSecurePort: 80
    
    paasta_portal_api_authorization_base64: Basic YWRtaW46b3BlbnBhYXN0YQ==
    paasta_portal_api_zuulUrl_cfapi: http://portal-gateway.<DOMAIN>/portalapi          # System Domain
    paasta_portal_api_zuulUrl_commonapi: http://portal-gateway.<DOMAIN>/commonapi      # System Domain
    paasta_portal_api_zuulUrl_storageapi: http://portal-gateway.<DOMAIN>/storageapi    # System Domain
    paasta_portal_storageapi_type: swift
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-log-api --recent

#### <div id="2-3-9"/> 2.3.9. Portal SSH 배포
해당 소스를 빌드 방법은 소스의 [readme.md](https://github.com/PaaS-TA/PAAS-TA-PORTAL-SSH/blob/master/README.md) 파일을 참고한다.


배포 대상 소스 파일의 manifest.yml 파일을 확인 후 수정이 필요한 부분은 수정한다.
```
---
applications:
- name: paasta-ssh      # 어플리케이션 이름
  buildpacks: 
   - nodejs_buildpack
  memory: 1024M         # 어플리케이션에 할당할 메모리
  instances: 1          # 어플리케이션 인스턴스 수
  path: .
```

manifest.yml 파일 확인 후 CF 에 배포한다
> $ cf push

배포 완료 후 앱의 배포 상태를 확인 한다.
> $ cf apps

배포가 완료되면 로그를 확인 할 수 있다.
> $ cf logs portal-log-api --recent

### <div id="2-4"/> 2.4. rule
Common Api 혹은 Storage Api 구동시 서비스 접속 에러로 App 구동이 안될 경우 보안 그룹을 추가한다.
> $ cf create-security-group ui-infra rule.json <br>
>![paas-ta-portal-security-01]<br>
> $ cf bind-staging-security-group ui-infra<br>
>![paas-ta-portal-security-02]<br>
> $ cf bind-running-security-group ui-infra<br>
>![paas-ta-portal-security-03]<br>

완료 후 App을 재 기동 한다.<br>
> $ cf restart portal-common-api
> $ cf restart portal-storage-api


## <div id="3"/>3. PaaS-TA Portal 운영

### <div id="3-1"/> 3.1. 사용자의 조직 생성 Flag 활성화

PaaS-TA는 기본적으로 일반 사용자는 조직을 생성할 수 없도록 설정되어 있다. 포털 배포를 위해 조직 및 공간을 생성해야 하고 또 테스트를 구동하기 위해서도 필요하므로 사용자가 조직을 생성할 수 있도록 user_org_creation FLAG를 활성화 한다. FLAG 활성화를 위해서는 PaaS-TA 운영자 계정으로 로그인이 필요하다.

```
$ cf enable-feature-flag user_org_creation
```
```
Setting status of user_org_creation as admin...
OK

Feature user_org_creation Enabled.
```

### <div id="3-2"/> 3.2. 사용자포탈 UAA페이지 오류
>![paas-ta-portal-31]
1. uaac portalclient가 등록이 되어있지 않다면 해당 화면과 같이 redirect오류가 발생한다.
2. uaac client add를 통해 potalclient를 추가시켜주어야 한다.
    > $ uaac target\
    $ uaac token client get\
        Client ID:  admin\
        Client secret:  *****
        
3. uaac client add portalclient –s “portalclient Secret” 
>--redirect_uri "사용자포탈 Url, 사용자포탈 Url/callback"\
$ uaac client add portalclient -s xxxxx --redirect_uri "http://portal-web-user.xxxx.xip.io, http://portal-web-user.xxxx.xip.io/callback" \
--scope "cloud_controller_service_permissions.read , openid , cloud_controller.read , cloud_controller.write , cloud_controller.admin" \
--authorized_grant_types "authorization_code , client_credentials , refresh_token" \
--authorities="uaa.resource" \
--autoapprove="openid , cloud_controller_service_permissions.read"

 >![paas-ta-portal-32]
1. uaac portalclient가 url이 잘못 등록되어있다면 해당 화면과 같이 redirect오류가 발생한다. 
2. uaac client update를 통해 url을 수정해야한다.
   > $ uaac target\
    $ uaac token client get\
   Client ID:  admin\
   Client secret:  *****
3. uaac client update portalclient --redirect_uri "사용자포탈 Url, 사용자포탈 Url/callback"
    >$ uaac client update portalclient --redirect_uri "http://portal-web-user.xxxx.xip.io, http://portal-web-user.xxxx.xip.io/callback"

### <div id="3-3"/> 3.3. 카탈로그 적용
##### 1. Catalog 빌드팩, 서비스팩 추가
Paas-TA Portal 설치 후에 관리자 포탈에서 빌드팩, 서비스팩을 등록해야 사용자 포탈에서 사용이 가능하다.
 
 1. 관리자 포탈에 접속한다.(portal-web-admin.[public ip].xip.io)
    >![paas-ta-portal-15]
 2. 운영관리를 누른다.
    >![paas-ta-portal-16]
 2. 카탈로그 페이지에 들어간다.
    >![paas-ta-portal-17]
 3. 빌드팩, 서비스팩 상세화면에 들어가서 각 항목란에 값을 입력후에 저장을 누른다.
    >![paas-ta-portal-18]
 4. 사용자포탈에서 변경된값이 적용되어있는지 확인한다.
    >![paas-ta-portal-19] 
    
### <div id="3-4"/> 3.4. 모니터링 및 오토스케일링 적용
##### 1. 포탈 설치 이전 모니터링 설정 적용
###### 1.PaaS-TA 에서 제공하고있는 모니터링을 미리 설치를 한 후에 진행해야 한다.
 1. Paas-TA Portal 설치 시 공통 변수 파일과 Deployment 변수 파일의 monitoring_api_url=<모니터링 API URL>, webuser_monitoring=true로 적용 한 후 설치 하면 정상적으로 모니터링 페이지 및 오토스케일링을 사용 할 수 있다.

##### 2. 포탈 설치 이후 모니터링 설정 적용
 1. 사용자 포탈의 앱 상세 페이지로 이동한다.
    >![paas-ta-portal-30]
 2. ① 상세페이지 레이아웃 하단의 모니터링 버튼을 누른다.
    
 3. ② 모니터링 오토 스케일링 화면
    
 4. ③ 모니터링 알람 설정 화면
    
 5. 추이차트 탭에서 디스크 메모리 네트워크 사용량을 인스턴스 별로 확인이 가능하다.        
    
[paas-ta-portal-01]:../../install-guide/portal/images/Paas-TA-Portal_01.png
[paas-ta-portal-01-b]:../../install-guide/portal/images/Paas-TA-Portal_01_b.png
[paas-ta-portal-02]:../../install-guide/portal/images/Paas-TA-Portal_02.png
[paas-ta-portal-03]:../../install-guide/portal/images/Paas-TA-Portal_03.png
[paas-ta-portal-04]:../../install-guide/portal/images/Paas-TA-Portal_04.png
[paas-ta-portal-05]:../../install-guide/portal/images/Paas-TA-Portal_05.png
[paas-ta-portal-06]:../../install-guide/portal/images/Paas-TA-Portal_06.png
[paas-ta-portal-07]:../../install-guide/portal/images/Paas-TA-Portal_07.png
[paas-ta-portal-08]:../../install-guide/portal/images/Paas-TA-Portal_08.png
[paas-ta-portal-09]:../../install-guide/portal/images/Paas-TA-Portal_09.png
[paas-ta-portal-10]:../../install-guide/portal/images/Paas-TA-Portal_10.png
[paas-ta-portal-11]:../../install-guide/portal/images/Paas-TA-Portal_11.png
[paas-ta-portal-12]:../../install-guide/portal/images/Paas-TA-Portal_12.png
[paas-ta-portal-13]:../../install-guide/portal/images/Paas-TA-Portal_13.png
[paas-ta-portal-14]:../../install-guide/portal/images/Paas-TA-Portal_14.png
[paas-ta-portal-15]:../../install-guide/portal/images/Paas-TA-Portal_15.png
[paas-ta-portal-16]:../../install-guide/portal/images/Paas-TA-Portal_16.png
[paas-ta-portal-17]:../../install-guide/portal/images/Paas-TA-Portal_17.png
[paas-ta-portal-18]:../../install-guide/portal/images/Paas-TA-Portal_18.png
[paas-ta-portal-19]:../../install-guide/portal/images/Paas-TA-Portal_19.png
[paas-ta-portal-20]:../../install-guide/portal/images/Paas-TA-Portal_20.png
[paas-ta-portal-21]:../../install-guide/portal/images/Paas-TA-Portal_21.png
[paas-ta-portal-22]:../../install-guide/portal/images/Paas-TA-Portal_22.png
[paas-ta-portal-23]:../../install-guide/portal/images/Paas-TA-Portal_23.png
[paas-ta-portal-24]:../../install-guide/portal/images/Paas-TA-Portal_24.png
[paas-ta-portal-25]:../../install-guide/portal/images/Paas-TA-Portal_25.png
[paas-ta-portal-26]:../../install-guide/portal/images/Paas-TA-Portal_26.png
[paas-ta-portal-27]:../../install-guide/portal/images/Paas-TA-Portal_27.PNG
[paas-ta-portal-28]:../../install-guide/portal/images/Paas-TA-Portal_28.PNG
[paas-ta-portal-29]:../../install-guide/portal/images/Paas-TA-Portal_29.png
[paas-ta-portal-30]:../../install-guide/portal/images/Paas-TA-Portal_30.png
[paas-ta-portal-31]:../../install-guide/portal/images/Paas-TA-Portal_27.jpg
[paas-ta-portal-32]:../../install-guide/portal/images/Paas-TA-Portal_28.jpg
[paas-ta-portal-security-01]:../../install-guide/portal/images/Paas-TA-Portal-Security_01.png
[paas-ta-portal-security-02]:../../install-guide/portal/images/Paas-TA-Portal-Security_02.png
[paas-ta-portal-security-03]:../../install-guide/portal/images/Paas-TA-Portal-Security_03.png
