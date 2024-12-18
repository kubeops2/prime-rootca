# prime-rootca  

This is the repository to deploy the public key of Prime rootCA.  
Prime rootCA.pem 파일을 제공하는 Git Repo 입니다.  

<br>

Prime 의 Certificates 파일들은, 이 공개된 rootCA.pem 파일의 비밀키로 서명하여 만들어 집니다.  
하여, Prime 에 구성된 서비스에 접근하는 랩탑이나, 다른 접근하는 Endpoint 의 Machine 은 Prime rootCA 로 만들어진 Certificate 을 신뢰하도록 설정하기 위해, 


<B>이 rootCA.pem 공개키를 신뢰 하도록 구성되어 져야 합니다.</B>

<br>

> Prime RootCA 를 Trust 하기 위해서는, openssl 이나, mkcert 가 필요합니다.  
이 문서는 오직 mkcert 로 하는 방법 만을 설명하며, 아래 방법을 시도 하기 전에 mkcert 가 반드시 설치 되어 있어야 합니다.

<br><br>

### 이 rootCA 를 신뢰하면 어떻게 되나요?

Prime 에서 생성하는 서비스들의 Ingress 들은 모두 TLS 가 구성되어 있습니다.
기본 Prime 구성에 사용되는 rootCA 는 Self-singed 이므로, 당연히 웹브라우져 접근 시 `Warning` 메시지가 뜹니다.  

<br>

별도의 certificates 구성 없이 기본 prime 설치 시 생성되는 모든 `K8s Ingress` 에 접근 하기 위해서 Prime 의 rootCA 공개키를 신뢰 해주셔야 합니다.

<br><br>

## Requirements

| Tools | Desc |
| - | - |
| curl | rootCA 를 가져옵니다. |
| mkcert | 가져온 rootCA 를 신뢰하도록 OS 종류에 따라 자동 구성해 줍니다. |

<br>

mkcert 설치 방법은 OS 에 따라 다양 합니다.  
`brew`, `choco`, `apt` 와 `tarball` 등 다양한 패키지를 제공 하므로, 홈페이지를 참고하여 구성 바랍니다.  
<br>
[mkcert 깃허브 페이지 ](https://github.com/FiloSottile/mkcert)

<br><br>

## Get Prime rootCA.Pem and Access Services

<br>

### 1. curl 명령으로 rootCA 파일을 가져와서 mkcert 로 Trust 를 구성합니다.

```bash
curl -sSL -O https://raw.githubusercontent.com/kubeops2/prime-rootca/main/rootCA.pem && CAROOT=. mkcert -install

```
<br>

그럼 다음과 같은 메시지를 볼 수 있습니다.
```bash
The local CA is installed in the system trust store! 👍
```


<br>

### 2. /etc/hosts 파일에 접근할 서비스의 <IP> <domain> 매핑을 설정 합니다.  

현재는 별도의 dns 가 없으므로, 정적 Domain Resolving 설정을 해야 합니다.  
/etc/hosts 파일의 형식은 다음과 같습니다.

```bash
127.0.0.1	localhost
255.255.255.255	broadcasthost
::1             localhost
1.1.1.10   argocd.getprime.ai
1.1.1.10   longhorn.getprime.ai
1.1.1.10   harbor.getprime.ai
```

도메인과 IP 는 담당자에게 확인하여 설정 하시기 바랍니다.

<br>

> 이러한 rootCA.pem 설정 및 도메인 정적 구성은 내부 개발망에서 테스트 시 Self-Signed 된 `tls warning` 을 해결하기 위한 목적 입니다.
